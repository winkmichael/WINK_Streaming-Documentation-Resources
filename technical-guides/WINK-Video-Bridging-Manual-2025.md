# WINK Video Bridging Manual

## VMS Integration & Protocol Translation Guide

**Comprehensive Manual for Video System Integration**  
**Version:** 1.0 | 2025  
**Company:** WINK Streaming

---

## Table of Contents

1. [Overview](#overview)
2. [Integration of Diverse Video Sources](#integration-of-diverse-video-sources)
   - 2.1 [VMS to VMS Bridging](#21-vms-to-vms-bridging)
   - 2.2 [IPTV System Integration](#22-iptv-system-integration)
   - 2.3 [Unsupported Cameras](#23-unsupported-cameras)
   - 2.4 [Network Connectivity Challenges](#24-network-connectivity-challenges)
3. [Supported Protocols](#supported-protocols)
   - 3.1 [Camera Imitation](#31-camera-imitation)
   - 3.2 [Input Protocols](#32-input-protocols)
   - 3.3 [Output Protocols](#33-output-protocols)
4. [Security and Authentication](#security-and-authentication)
   - 4.1 [Access Control Lists](#41-access-control-lists)
   - 4.2 [Authentication Methods](#42-authentication-methods)
5. [PTZ Support](#ptz-support)
6. [Deployment Options](#deployment-options)
7. [Contact Information](#contact-information)

---

## Overview

WINK Streaming goes beyond traditional transcoding, utilizing the WINK Forge to transform and bridge live and recorded video feeds for enhanced compatibility with various Video Management Systems (VMS). This unique capability allows for the seamless integration of unconventional or unsupported camera sources, such as drones, into established VMS environments. Furthermore, WINK Forge can emulate well-known camera protocols, facilitating smooth integration and expanded functionality with diverse systems.

> **The Challenge of Video Bridging:**
> 
> Video bridging is inherently complex. While modern systems can often ingest standard protocols like RTSP, RTMP, and even HLS, many legacy systems and closed proprietary platforms do not support these open protocols. This creates significant integration challenges for organizations trying to unify their video infrastructure.

WINK Streaming has developed creative solutions to solve these complex bridging challenges. Rather than forcing incompatible systems to communicate directly, we take a different approach: **we make video feeds appear as native cameras**. Our platform can emulate various manufacturer protocols including Axis, Panasonic, Sony, and Hikvision. Most importantly, we are absolute experts in ONVIF protocol implementation and can present any video feed as a fully-compliant ONVIF camera.

### Key Capabilities

- Bridge video feeds between incompatible VMS platforms
- Integrate non-standard video sources (drones, industrial cameras)
- Emulate popular camera protocols (Axis, Panasonic, ONVIF)
- Transform protocols for maximum compatibility
- Provide secure, authenticated access to video streams

WINK Forge stands as a comprehensive solution for bridging, transcoding, and transforming video feeds across a variety of platforms and protocols, thus ensuring that integration challenges are minimized and system capabilities are extended. These integrations underscore the versatility of WINK Forge, which not only enhances existing systems but also bridges technological gaps between disparate systems.

> **Why Camera Emulation Works:**
> 
> Bridging between modern and legacy systems is complex because:
> 
> - Legacy VMS systems were designed to work only with specific camera models
> - Proprietary protocols often have undocumented features and quirks
> - Authentication methods vary wildly between manufacturers
> - Feature sets differ significantly between camera generations
> 
> This is where WINK Streaming excels - we've reverse-engineered and implemented these proprietary protocols, allowing us to make any video source appear as a trusted, native camera to your VMS.

> **Note:** This document covers the use cases for both WINK Forge (on-premises) and WINK V-Forge (cloud-based) solutions. The functionality described applies to both deployment models unless otherwise specified.

By providing robust support for a variety of protocols and by emulating well-known camera standards, WINK Forge ensures that your video management capabilities are both expansive and adaptable to the needs of complex and varied environments.

Through its ability to integrate diverse video sources, support a wide range of protocols, and prioritize security and authentication, WINK Forge positions itself as an essential tool for organizations looking to streamline their video management processes and optimize their surveillance capabilities. Whether it's integrating drone footage, bridging VMS systems, or adapting to industrial camera setups, WINK Forge provides the flexibility and reliability needed to navigate the ever-evolving landscape of video management technology.

---

## Integration of Diverse Video Sources

WINK Forge excels at integrating video sources that traditional VMS platforms struggle to accommodate. This section explores the most common use cases and implementation strategies.

### 2.1 VMS to VMS Bridging

Many Video Management Systems (VMS) are designed primarily to handle direct camera inputs and may not support direct integration with other VMS platforms. WINK Forge serves as a mediator in these instances, capturing video feeds from one VMS and translating them into formats recognizable by another VMS as standard camera outputs. This allows for seamless integration and management within systems that otherwise would not recognize the original VMS's output.

#### Supported VMS Platforms

WINK Forge has extensive experience integrating with leading VMS solutions:

- **Genetec Security Center** - Full integration with unified security platform
- **Genetec Omnicast** - Legacy system support maintained
- **Milestone XProtect** - All editions supported
- **OnSSI Ocularis** - Complete compatibility
- **Avigilon Control Center** - Seamless integration
- **exacqVision** - Full feature support
- And many others...

Our advanced compatibility ensures that users can leverage the power of WINK Forge to bridge video feeds between these systems and many others. By imitating camera protocols, WINK Forge can effectively connect disparate VMS platforms, enabling users to access and manage video feeds from one central location.

#### Multi-System Distribution

One of the key advantages of using WINK Forge for VMS to VMS bridging is its ability to share video feeds with multiple systems simultaneously. This means that a single video feed can be distributed to various VMS platforms, allowing different departments or even organizations to access the same live video without the need for separate camera installations or complex network configurations.

**Multi-Agency Sharing**
Share critical camera feeds between law enforcement agencies, emergency services, and municipal departments while maintaining separate VMS systems.
- Maintain agency autonomy
- Selective feed sharing
- Audit trail maintenance

**Corporate Integration**
Bridge legacy VMS systems with modern platforms during phased upgrades or after mergers and acquisitions.
- Gradual system migration
- Cost-effective transitions
- Minimal disruption

**Partner Access**
Provide secure access to specific camera feeds for partners, contractors, or temporary stakeholders.
- Time-limited access
- Granular permissions
- No VMS client required

#### Web-Based Access

WINK Streaming offers its own web-based player and viewers, providing an additional layer of flexibility and accessibility. Users can access live video feeds through a standard web browser, eliminating the need for specialized software or client installations. This feature is particularly beneficial for:

- Remote monitoring from any device
- Mobile access without dedicated apps
- Situations where installing a full VMS client is not feasible
- Quick access for executives or decision-makers

This functionality ties into a classic use case of the WINK Forge, sharing cameras on the web and mobile devices.

### 2.2 IPTV System Integration

IPTV systems are commonly used for distributing video content across localized networks. WINK Forge can bridge the gap between IPTV broadcasting and VMS systems, facilitating unique use cases such as video walls or channel-based surveillance monitoring.

#### VMS to IPTV Conversion

WINK Forge can take live feeds from a VMS and convert them into a format suitable for IPTV systems, allowing organizations to create a "channel" for each camera. This makes it possible to view live security camera feeds on any device connected to the IPTV network, including:

- Smart TVs in control rooms
- Set-top boxes in offices
- Digital signage displays
- Mobile devices on the network

#### IPTV to VMS Integration

Conversely, WINK Forge can capture broadcasts from an IPTV system and package them for use by a VMS, which is particularly useful in environments where live media needs to be closely monitored and recorded, such as:

- Media houses monitoring broadcast content
- Event venues recording live productions
- Educational institutions archiving lectures
- Corporate environments recording presentations

> **Tip:** Although IPTV systems are generally considered legacy technology, pairing an existing IPTV system with a new WINK Streaming deployment can bring new life to an aging system, maximizing your existing infrastructure investment.

### 2.3 Unsupported Cameras

Most VMS systems have limited camera make, model, and firmware support. With WINK Forge as a bridge, although not all features will be supported, we can enable users to at a minimum stream the live video and provide some features on a case-by-case basis.

#### Common Unsupported Camera Scenarios

- **Legacy Cameras:** Older models no longer supported by modern VMS
- **Proprietary Systems:** Cameras with manufacturer-specific protocols
- **Consumer Cameras:** Residential or prosumer devices in commercial settings
- **Specialized Cameras:** Scientific, medical, or industrial imaging devices
- **International Models:** Region-specific cameras not supported in your VMS

#### Feature Support Matrix

| Feature | Always Supported | Often Supported | Case-by-Case |
|---------|-----------------|------------------|--------------|
| Live Video Stream | ✓ | | |
| Multiple Resolutions | ✓ | | |
| Basic PTZ Control | | ✓ | |
| Preset Positions | | ✓ | |
| Two-Way Audio | | | ✓ |
| Analytics Metadata | | | ✓ |
| Camera Settings | | | ✓ |

### 2.4 Network Connectivity Challenges

Modern network architectures often create challenges for traditional VMS deployments. WINK Forge solves connectivity issues for cameras that cannot be directly accessed due to network limitations.

#### Common Network Challenges

**Carrier Grade NAT (CGNAT)**
Cameras behind cellular networks or ISP CGNAT cannot accept incoming connections. WINK Forge enables these cameras to push video to the cloud for VMS access.

**Private Subnets**
Cameras on isolated networks or DMZs can securely publish to WINK Forge without exposing the internal network structure.

**IPv6 Tunnels**
Bridge IPv6-only cameras to IPv4 VMS systems or vice versa, ensuring compatibility across network generations.

#### Drone Integration

Drones represent a special category of network-challenged devices. They frequently operate on networks without real IP addresses, complicating direct VMS integration. WINK Forge resolves this issue by enabling drones to publish their live video feeds to a dedicated cloud environment.

> **The Drone Integration Challenge:**
> 
> Integrating drone video feeds into traditional VMS platforms is particularly complex:
> 
> - Drones use cellular networks with dynamic IPs and CGNAT
> - Most drone platforms use proprietary streaming protocols
> - VMS systems expect static IP cameras, not mobile sources
> - Metadata (GPS, altitude, heading) needs special handling
> - Connection reliability varies with signal strength
> 
> WINK Streaming solves this by accepting the drone's outbound stream and re-presenting it as a standard IP camera. To the VMS, the drone appears as a fixed Axis, Panasonic, or ONVIF camera - complete with all expected features and behaviors.

The WINK Forge drone integration process:

1. Drone establishes outbound connection to WINK cloud
2. Video stream is received and processed in real-time
3. Stream is transcoded to VMS-compatible format
4. VMS connects to WINK Forge as if it were a standard IP camera
5. Full recording, analytics, and distribution capabilities are maintained

#### Industrial Camera Integration

In industrial sectors such as construction or energy, many cameras operate with limited connectivity options like RTP or multicast broadcasting, which are not natively supported by all VMS systems. WINK Forge captures these specific feeds and makes them universally accessible.

**Industrial Integration Examples**

- **Construction Sites:** Time-lapse cameras using FTP upload → Live streaming
- **Oil & Gas:** Explosion-proof cameras with proprietary protocols → Standard RTSP
- **Manufacturing:** Machine vision cameras → VMS integration
- **Mining Operations:** Ruggedized cameras with satellite uplink → Cloud VMS
- **Utilities:** SCADA-integrated cameras → Security operations center

WINK Forge captures these feeds, whether they are broadcast over RTP or multicast, and transcodes them into more widely accepted protocols such as RTSP or HLS, making them compatible with a broad range of VMS platforms. This not only ensures that the feeds can be integrated into more sophisticated monitoring systems but also enhances features such as recording, archiving, and remote viewing.

---

## Supported Protocols

WINK Forge's strength lies in its comprehensive protocol support, enabling it to act as a universal translator for video streams.

### 3.1 Camera Imitation

Imitation being the highest form of flattery, the WINK Forge can imitate or pretend to be a range of camera makes and models. Depending on the needs, the WINK Forge can make live feeds appear to be from popular manufacturers.

#### The Art of Camera Emulation

Making a video feed appear as a specific camera model is far more complex than simple protocol translation. Each manufacturer has unique:

- **Discovery mechanisms** - How cameras announce themselves on the network
- **Authentication sequences** - Proprietary handshakes and session management
- **Command structures** - Specific API calls and response formats
- **Feature presentations** - How capabilities are advertised to the VMS
- **Error behaviors** - How cameras respond to unsupported commands

WINK Streaming has spent years perfecting these emulations. We don't just translate protocols - we faithfully reproduce the entire camera behavior, ensuring seamless integration with even the most particular VMS platforms.

#### Supported Camera Protocols

**Axis Communications**
- Full VAPIX API support
- Event stream compatibility
- PTZ and preset control

**Panasonic**
- i-PRO protocol emulation
- CGI command support
- Privacy mask compatibility

**Sony**
- VISCA protocol support
- SNC API compatibility
- Image adjustment controls

**ONVIF - Our Specialty!**
- Profile S, G, T, M support
- Event handling & analytics
- Edge storage emulation
- Full WSDL compliance

This feature is crucial for integrating non-standard or unsupported camera systems into existing VMS setups. By emulating the protocols of popular camera brands, WINK Forge can masquerade as these devices. This allows users to manage and record from these "virtual" cameras as if they were natively supported by their VMS, expanding the range of compatible devices without necessitating extensive overhauls or upgrades to existing infrastructure.

> **ONVIF Excellence - Our Core Expertise:**
> 
> While we support many manufacturer protocols, ONVIF is where WINK Streaming truly shines. We are **absolute experts** in ONVIF implementation with:
> 
> - Complete implementation of all ONVIF profiles (S, G, T, M, A, C)
> - Perfect WSDL compliance for maximum compatibility
> - Support for advanced ONVIF features often missing in actual cameras
> - Ability to present any video source as a fully-compliant ONVIF device
> - Expert knowledge of ONVIF quirks and VMS-specific requirements
> 
> When bridging through ONVIF, you get the best of both worlds: universal compatibility and full feature support. This is why ONVIF is our recommended protocol for most bridging scenarios.

> **Note:** WINK Forge also supports emulation of Bosch, Hikvision, Dahua, Hanwha, Avigilon, and many other manufacturers. Contact our team for specific model compatibility.

### 3.2 Input Protocols

WINK Forge accepts video from virtually any source through its extensive input protocol support:

| Protocol | Common Use Cases | Features |
|----------|-----------------|----------|
| **RTMP** | Live streaming platforms, encoders | Low latency, widely supported |
| **RTSP** | IP cameras, NVRs | Bidirectional, PTZ support |
| **RTP** | Direct camera streams, multicast | Raw transport, low overhead |
| **HLS** | CDN delivery, web players | Adaptive bitrate, encrypted |
| **MPEG-DASH** | Modern streaming, 4K delivery | Standards-based, efficient |
| **SDP** | Session description, SIP integration | Codec negotiation |
| **UDP/TCP** | Raw streams, custom protocols | Direct socket connections |

These protocols are commonly used for streaming video over the internet. WINK Forge accepts these inputs, allowing for a wide range of video sources, from broadcast media to personal streaming.

### 3.3 Output Protocols

After processing, WINK Forge can output video in several protocols, ensuring compatibility with various downstream systems:

#### Advanced Output Capabilities

- **RTMP:** For CDN distribution and streaming servers
- **RTSP:** Universal VMS compatibility with full feature support
- **HLS:** Web and mobile viewing with adaptive quality
- **MPEG-DASH:** Next-generation streaming with DRM support
- **WebRTC:** Ultra-low latency browser-based viewing
- **WebSockets:** Real-time bidirectional communication
- **MJPEG:** Legacy system support and low-bandwidth scenarios
- **SRT:** Secure Reliable Transport for WAN connections

Advanced protocols like WebRTC and WebSockets enable real-time streaming capabilities to web browsers and applications, providing flexibility in how and where the video can be viewed.

---

## Security and Authentication

To ensure secure access and control over video feeds, WINK Forge incorporates multiple layers of security.

### 4.1 Access Control Lists

Define who can access which video feeds, ensuring that only authorized users can view or manipulate the video streams. ACL implementation includes:

#### ACL Configuration Options

- **IP-based restrictions:** Whitelist/blacklist by IP address or range
- **User-based permissions:** Individual user access rights
- **Group policies:** Department or role-based access
- **Time-based access:** Scheduled access windows
- **Geographic restrictions:** Geofencing and location-based controls
- **Stream-level permissions:** Different access levels for different quality streams

### 4.2 Authentication Methods

WINK Forge supports multiple authentication mechanisms to meet various security requirements:

#### One-Time Password (OTP)

Enhances security by requiring a time-sensitive password for access. This is particularly useful for:

- Temporary access grants
- High-security environments
- Audit trail requirements
- Two-factor authentication scenarios

#### Basic and Digest Authentication

These methods provide straightforward yet robust options for protecting access:

- **Basic Authentication:** Simple username/password over HTTPS
- **Digest Authentication:** Hashed credentials for enhanced security
- Both compatible with standard VMS authentication systems

#### Custom API Methods

Beyond standard authentication, WINK Forge supports custom API integrations that allow for bespoke authentication and authorization methods tailored to specific organizational requirements or security policies:

**SAML Integration**
- Enterprise SSO support
- Active Directory federation
- Multi-factor authentication

**OAuth 2.0**
- Token-based access
- Third-party authorization
- Scope-based permissions

**Custom Webhooks**
- Real-time validation
- External system integration
- Dynamic permissions

> **Security Best Practices:**
> 
> - Always use HTTPS/TLS for authentication
> - Implement least-privilege access principles
> - Regular security audits and access reviews
> - Enable comprehensive logging for all access attempts
> - Use strong password policies and rotation schedules

---

## PTZ Support

For cameras that support Pan-Tilt-Zoom (PTZ) functionality, WINK Forge can pass through PTZ controls from the VMS to the actual camera hardware, regardless of the original compatibility.

### PTZ Control Features

- **Full PTZ Control:** Pan, tilt, zoom with variable speeds
- **Preset Management:** Save and recall preset positions
- **Tour/Pattern Support:** Automated patrol sequences
- **Focus/Iris Control:** Manual and auto-focus options
- **Protocol Translation:** Convert between different PTZ protocols
- **Multi-user Coordination:** PTZ priority and queuing

### Supported PTZ Protocols

| Protocol | Cameras | Features |
|----------|---------|----------|
| ONVIF PTZ | Most modern IP cameras | Full feature set, standardized |
| Pelco-D/P | Legacy analog PTZ | Serial control, wide support |
| VISCA | Sony cameras | Professional features |
| Proprietary CGI | Manufacturer-specific | Full vendor features |

This feature ensures that users retain control over camera movements and settings directly from their VMS, providing a seamless user experience and enhanced operational flexibility.

> **PTZ Best Practice:** When bridging PTZ cameras, WINK Forge can implement PTZ queuing and priority systems to prevent conflicts when multiple users attempt to control the same camera simultaneously.

---

## Deployment Options

WINK Forge is available in multiple deployment models to suit different organizational needs:

### On-Premises Deployment
**WINK Forge**

- Complete data sovereignty
- No bandwidth to cloud required
- Physical or virtual appliance
- Direct integration with local systems
- Customizable hardware specifications

### Cloud Deployment
**WINK V-Forge**

- No hardware maintenance
- Instant scalability
- Geographic redundancy
- Pay-as-you-grow model
- Automatic updates and patches

### Hybrid Deployment
**Best of Both Worlds**

- On-prem for critical cameras
- Cloud for remote locations
- Failover capabilities
- Flexible resource allocation
- Optimized bandwidth usage

> **Scaling Guidelines:** Each WINK Forge instance can typically handle 50-100 concurrent HD streams or 20-40 4K streams, depending on transcoding requirements. Cloud deployments can scale automatically based on demand.

---

## Contact Information

Contact your WINK Streaming representative for additional details on how to get started with WINK Forge video bridging solutions.

### Get Started Today

Our solutions team is ready to help you design the perfect video bridging solution for your organization.

- **Sales:** sales@wink.co
- **Technical Support:** support@wink.co
- **Phone:** +1-312-281-5433
- **Web:** [https://wink.co](https://wink.co)

Request a demo or proof of concept to see how WINK Forge can solve your video integration challenges.

> **Professional Services Available:** Our team offers deployment assistance, custom integration development, and training services to ensure your success with WINK Forge.

---

*© 2025 WINK Streaming. All rights reserved.*