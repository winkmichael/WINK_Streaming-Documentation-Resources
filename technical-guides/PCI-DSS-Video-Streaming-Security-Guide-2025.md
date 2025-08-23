# PCI-DSS Compliance for Video Streaming

## Security Best Practices for Payment Card Industry Environments

**WINK Streaming Technical Brief**  
**Version:** 1.0 - August 2025

---

## Table of Contents

1. [Executive Summary: Video Security in Payment Environments](#executive-summary-video-security-in-payment-environments)
2. [Understanding PCI-DSS Requirements for Video Systems](#understanding-pci-dss-requirements-for-video-systems)
3. [The Protocol Security Matrix](#the-protocol-security-matrix)
4. [Secure Video Streaming Protocols](#secure-video-streaming-protocols)
5. [Implementation Architecture](#implementation-architecture)
6. [Network Security Requirements](#network-security-requirements)
7. [Access Control and Authentication](#access-control-and-authentication)
8. [Monitoring and Compliance Validation](#monitoring-and-compliance-validation)
9. [Common PCI-DSS Video Violations](#common-pci-dss-video-violations)
10. [Implementation Checklist](#implementation-checklist)

---

## Executive Summary: Video Security in Payment Environments

> **The Bottom Line:** Video streaming systems in PCI-DSS environments must encrypt all data in transit, implement strong authentication, and maintain comprehensive audit trails. Traditional unencrypted protocols like RTSP, RTP, and standard RTMP are non-compliant and create significant security risks.

This document provides comprehensive guidance for implementing PCI-DSS compliant video streaming systems. Whether you're monitoring retail locations, securing payment processing facilities, or enabling video surveillance in cardholder data environments (CDEs), every component must meet stringent security requirements.

> **Critical Requirement:** PCI-DSS mandates that ALL sensitive data in transit must be encrypted using strong cryptography. This includes video feeds that could potentially expose cardholder data environments, payment terminals, or sensitive business operations.

---

## Understanding PCI-DSS Requirements for Video Systems

### Applicable PCI-DSS Requirements

| Requirement | Video System Impact | Implementation Need |
|-------------|-------------------|-------------------|
| **4.1** - Strong Cryptography | All video streams must be encrypted | TLS, SRT, SRTP, or equivalent |
| **7.1** - Access Control | Role-based video access permissions | Authentication and authorization systems |
| **8.2** - User Authentication | Strong passwords, MFA for video access | Multi-factor authentication required |
| **10.1** - Audit Logging | Log all video access and configuration changes | Comprehensive logging infrastructure |
| **11.4** - Network Monitoring | Monitor video network traffic for anomalies | Intrusion detection systems |

### Scope Determination

> **Key Principle:** Video systems are in PCI-DSS scope if they can view, access, or potentially compromise cardholder data environments. This includes cameras monitoring POS terminals, payment processing areas, or network infrastructure supporting payment systems.

---

## The Protocol Security Matrix

### Protocol Compliance Status

| Protocol | Default Security | PCI-DSS Status | Compliant Alternative |
|----------|-----------------|----------------|----------------------|
| ❌ RTSP (Standard) | Unencrypted | NON-COMPLIANT | RTSPS + SRTP |
| ❌ RTP (Standard) | Unencrypted | NON-COMPLIANT | SRTP |
| ❌ RTMP (Standard) | Unencrypted | NON-COMPLIANT | RTMPS (RTMP over TLS) |
| ❌ HTTP | Unencrypted | NON-COMPLIANT | HTTPS |
| ✅ SRT | AES Encrypted | COMPLIANT | - |
| ✅ RTSPS + SRTP | TLS + SRTP | COMPLIANT | - |
| ✅ RTMPS | TLS Encrypted | COMPLIANT | - |
| ✅ HLS over HTTPS | HTTPS Transport | COMPLIANT | - |
| ✅ WebRTC | DTLS/SRTP | COMPLIANT | - |

> **Critical Warning:** Using any unencrypted protocol (RTSP, RTP, RTMP, HTTP) in a PCI-DSS environment is an automatic compliance failure. These protocols transmit video data in clear text, making them vulnerable to interception and analysis.

---

## Secure Video Streaming Protocols

### SRT (Secure Reliable Transport)

> **Best Choice for Security:** SRT provides encryption by default with AES-128, AES-192, or AES-256. It's specifically designed for secure, low-latency video transport and includes built-in authentication mechanisms.

#### SRT Security Features:

- **Encryption:** AES encryption with configurable key lengths (128, 192, 256-bit)
- **Authentication:** Passphrase and StreamID-based authentication
- **Key Exchange:** Secure key exchange protocol
- **Replay Protection:** Built-in protection against replay attacks
- **Perfect Forward Secrecy:** Session keys are unique and temporary

### RTSPS with SRTP

For environments requiring RTSP compatibility, the secure variant uses:

- **RTSPS:** RTSP over TLS for signaling encryption
- **SRTP:** Secure RTP for media stream encryption
- **Certificate Validation:** X.509 certificate-based authentication

### RTMPS (RTMP over TLS)

Secure RTMP implementation providing:

- **TLS Encryption:** All RTMP traffic encrypted using TLS 1.2 or higher
- **Certificate Authentication:** Server certificate validation
- **Perfect Forward Secrecy:** Ephemeral key exchange

### WebRTC

Browser-native security with:

- **DTLS:** Datagram Transport Layer Security for signaling
- **SRTP:** Secure RTP for media transport
- **Identity Verification:** Built-in identity assertion mechanisms

---

## Implementation Architecture

### Secure Video Streaming Architecture

```
[IP Cameras] 
     │ SRT or RTSPS+SRTP
     ↓
[Video Ingestion Server]
- TLS certificate validation
- Strong authentication
- Encrypted storage
     │
     ├─ HTTPS → [CDN] → [Public Access]
     ├─ SRT → [Secure Distribution]
     └─ WebRTC → [Real-time Monitoring]

Network Security:
- Firewall rules (specific ports only)
- VPN access for management
- Network segmentation (DMZ)
- IDS/IPS monitoring

Access Control:
- Multi-factor authentication
- Role-based permissions
- Session timeout enforcement
- Certificate-based authentication
```

### Certificate Management

#### TLS Certificate Requirements:

- Use certificates from trusted Certificate Authorities (CAs)
- Implement certificate pinning where possible
- Set up automated certificate renewal
- Use at least 2048-bit RSA or 256-bit ECC keys
- Enable Certificate Transparency monitoring
- Maintain certificate inventory and expiration tracking

---

## Network Security Requirements

### Network Segmentation

| Network Zone | Purpose | Security Controls |
|-------------|---------|-------------------|
| Camera VLAN | IP camera network | Isolated, encrypted protocols only |
| Video Server DMZ | Video processing servers | Firewall rules, IDS monitoring |
| Management Network | Administrative access | VPN required, MFA enforcement |
| Public Distribution | External video access | CDN, HTTPS only, rate limiting |

### Firewall Configuration

**Recommended Firewall Rules:**

```bash
# Inbound - Video Ingestion (SRT)
ALLOW TCP/UDP 1935 FROM camera_network TO video_servers

# Inbound - Secure RTSP (RTSPS)
ALLOW TCP 322 FROM camera_network TO video_servers

# Inbound - HTTPS Distribution
ALLOW TCP 443 FROM any TO distribution_servers

# Management Access (VPN only)
ALLOW TCP 22,443 FROM vpn_network TO video_servers

# DENY all other traffic
DENY all FROM any TO video_infrastructure
```

---

## Access Control and Authentication

### Multi-Factor Authentication (MFA)

> **PCI-DSS Requirement:** All administrative access to video systems must implement multi-factor authentication. This includes web interfaces, API access, and direct system administration.

#### MFA Implementation Options:

- **TOTP:** Time-based One-Time Passwords (Google Authenticator, Authy)
- **Hardware Tokens:** FIDO2/WebAuthn security keys
- **SMS/Voice:** Phone-based verification (least secure option)
- **Push Notifications:** Mobile app-based approval

### Role-Based Access Control (RBAC)

| Role | Permissions | MFA Required |
|------|-------------|--------------|
| Viewer | Live video viewing only | Yes |
| Operator | PTZ control, preset management | Yes |
| Administrator | System configuration, user management | Yes (Hardware token required) |
| Auditor | Log access, compliance reporting | Yes |

### Session Management

#### Session Security Requirements:

- Maximum session timeout: 15 minutes of inactivity
- Automatic re-authentication after timeout
- Secure session token generation (cryptographically random)
- Session invalidation on logout
- Prevent session fixation attacks
- Secure cookie flags (HttpOnly, Secure, SameSite)

---

## Monitoring and Compliance Validation

### Required Audit Logging

> **PCI-DSS Requirement 10:** All access to video systems must be logged with sufficient detail to support forensic analysis. Logs must be protected against tampering and retained according to compliance requirements.

#### Events That Must Be Logged:

- **Authentication Events:** Login attempts (successful and failed), MFA challenges
- **Authorization Events:** Permission grants/denials, role changes
- **System Events:** Configuration changes, service starts/stops
- **Access Events:** Video stream access, camera control actions
- **Network Events:** Connection attempts, protocol violations

### Log Format Requirements

```
Required Log Fields:
- Timestamp (UTC, synchronized with NTP)
- User ID and source IP address
- Type of event and outcome (success/failure)
- System component affected
- Details of the event

Example Log Entry:
2025-08-03T14:30:15Z user=jdoe@company.com src_ip=192.168.1.100 
event=camera_access camera_id=CAM001 action=ptz_control 
direction=left outcome=success session_id=a1b2c3d4
```

### Real-Time Monitoring

| Alert Type | Trigger Condition | Response Action |
|-----------|-------------------|-----------------|
| Authentication Failure | 3+ failed login attempts in 5 minutes | Account lockout, security team notification |
| Unusual Access Pattern | Access from new location/device | Additional authentication challenge |
| Protocol Violation | Unencrypted traffic detected | Immediate connection termination |
| Configuration Change | Any system configuration modification | Change approval verification |

---

## Common PCI-DSS Video Violations

### Frequently Encountered Violations

> **Violation #1: Unencrypted Video Streams**
> 
> **Problem:** Using standard RTSP, RTP, or RTMP protocols without encryption.  
> **Solution:** Implement SRT, RTSPS+SRTP, or RTMPS for all video transport.  
> **Impact:** Automatic PCI-DSS failure, potential data breach exposure.

> **Violation #2: Weak Authentication**
> 
> **Problem:** Using default passwords, single-factor authentication, or shared accounts.  
> **Solution:** Implement MFA, unique user accounts, and strong password policies.  
> **Impact:** Requirement 8 violation, unauthorized access risk.

> **Violation #3: Insufficient Logging**
> 
> **Problem:** Not logging video access events or storing logs insecurely.  
> **Solution:** Comprehensive logging with secure, tamper-evident storage.  
> **Impact:** Requirement 10 violation, inability to detect breaches.

> **Violation #4: Network Segmentation Failures**
> 
> **Problem:** Video systems directly accessible from corporate networks.  
> **Solution:** Proper VLAN segmentation and firewall controls.  
> **Impact:** Expanded PCI-DSS scope, lateral movement risk.

---

## Implementation Checklist

### Pre-Implementation Assessment

#### Planning Phase:

- Conduct PCI-DSS scope assessment
- Document all video system components
- Identify data flows and network paths
- Select appropriate secure protocols
- Design network segmentation architecture

### Technical Implementation

#### Protocol Security:

- Deploy SRT or RTSPS+SRTP for camera connections
- Configure strong encryption (AES-256 minimum)
- Implement certificate-based authentication
- Disable all unencrypted protocols
- Test encryption effectiveness

#### Access Control:

- Implement multi-factor authentication
- Configure role-based access control
- Set up session management and timeouts
- Create unique accounts for all users
- Establish password complexity requirements

#### Network Security:

- Configure network segmentation (VLANs/subnets)
- Implement firewall rules (default deny)
- Deploy intrusion detection systems
- Set up VPN access for management
- Configure network monitoring and alerting

#### Monitoring and Logging:

- Configure comprehensive audit logging
- Set up secure log storage and retention
- Implement real-time monitoring and alerting
- Test log integrity and tamper detection
- Document incident response procedures

### Compliance Validation

#### Testing and Validation:

- Conduct vulnerability scans of video infrastructure
- Perform penetration testing on video systems
- Validate encryption implementation
- Test access controls and authentication
- Verify audit logging completeness
- Document all security controls
- Schedule regular compliance assessments

### Ongoing Maintenance

**Monthly Tasks:**
- Review access logs for anomalies
- Update security patches
- Validate certificate expiration dates
- Test backup and recovery procedures

**Quarterly Tasks:**
- Conduct access reviews
- Update risk assessments
- Review and test incident response plans
- Perform security control testing

**Annual Tasks:**
- Complete PCI-DSS compliance assessment
- Conduct penetration testing
- Review and update security policies
- Evaluate new security technologies

---

## About WINK Streaming

WINK Streaming provides PCI-DSS compliant video streaming solutions for payment environments. Our platform supports secure protocols including SRT, RTSPS+SRTP, and RTMPS, with built-in compliance features and comprehensive audit capabilities.

### PCI-DSS Compliance Features:

- **Encrypted-by-Default:** All video transport uses strong encryption
- **Built-in MFA:** Multi-factor authentication for all access
- **Comprehensive Logging:** Complete audit trail with tamper protection
- **Network Segmentation:** Secure architecture design and implementation
- **Compliance Reporting:** Automated compliance status reporting
- **Professional Services:** PCI-DSS assessment and implementation support

Ensure your video streaming infrastructure meets PCI-DSS requirements from day one. Contact our compliance specialists at [wink.co](https://www.wink.co).

---

*© 2025 WINK Streaming. All rights reserved.*  
*This document contains proprietary information and is subject to change without notice.*  
*Version 1.0 - August 2025*