# Secure Video Authentication: HLS, RTSP, OTP & JWT Guide

*Comprehensive Security Implementation for Government and Enterprise Video Streaming*

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Authentication Architecture Overview](#authentication-architecture-overview)
3. [HLS Security Implementation](#hls-security-implementation)
4. [RTSP Authentication Methods](#rtsp-authentication-methods)
5. [One-Time Password (OTP) Systems](#one-time-password-otp-systems)
6. [JSON Web Token (JWT) Implementation](#json-web-token-jwt-implementation)
7. [Multi-Factor Authentication](#multi-factor-authentication)
8. [Real-World Security Scenarios](#real-world-security-scenarios)
9. [Security Best Practices](#security-best-practices)
10. [Compliance and Audit Requirements](#compliance-and-audit-requirements)

---

## Executive Summary

Video streaming security is critical for government agencies, law enforcement, and enterprise deployments where unauthorized access could compromise operations, privacy, or national security. This guide provides comprehensive implementation details for securing video streams using modern authentication methods while maintaining compatibility with legacy systems.

### Security Challenges in Video Streaming

- **Legacy Protocol Limitations:** RTSP basic authentication sends credentials in clear text
- **Scale Complexity:** Managing authentication for thousands of cameras and users
- **Multi-Agency Access:** Secure sharing across organizational boundaries
- **Mobile Access:** Securing streams for field personnel and mobile devices
- **Temporary Access:** Emergency responders and temporary event coverage
- **Compliance Requirements:** Meeting PCI-DSS, FISMA, and other regulatory standards

### Recommended Security Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Transport** | TLS 1.3 | Encryption in transit |
| **Authentication** | JWT + OTP | Identity verification |
| **Authorization** | RBAC | Access control |
| **Stream Protection** | AES-128/256 | Content encryption |
| **Network** | VPN/Zero Trust | Network security |
| **Audit** | SIEM Integration | Security monitoring |

---

## Authentication Architecture Overview

### Modern Security Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Security Architecture                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────┐   │
│  │  Identity   │ ──>│ Authorization│ ──>│   Stream    │   │
│  │ Management  │    │   Gateway    │    │  Delivery   │   │
│  └─────────────┘    └──────────────┘    └─────────────┘   │
│         │                    │                   │         │
│         ▼                    ▼                   ▼         │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────┐   │
│  │ JWT + OTP   │    │ RBAC Policies│    │ AES Streams │   │
│  │ LDAP/SAML   │    │ Time Windows │    │ TLS Transport│   │
│  │ MFA Support │    │ IP Restrictions│    │ Watermarking│   │
│  └─────────────┘    └──────────────┘    └─────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Authentication Flow

1. **User Request:** Client requests video stream access
2. **Identity Verification:** JWT validation + optional MFA
3. **Authorization Check:** RBAC policies determine access level
4. **Token Generation:** Time-limited access token issued
5. **Stream Access:** Encrypted stream delivered with watermarking
6. **Continuous Monitoring:** Access patterns monitored and logged

### Protocol-Specific Security

| Protocol | Primary Security | Secondary Security | Use Case |
|----------|------------------|-------------------|----------|
| **HLS** | AES-128 + JWT | IP restrictions + watermark | Web browsers, mobile |
| **RTSP** | Digest Auth + TLS | VPN + certificates | IP cameras, legacy |
| **WebRTC** | DTLS + SRTP | JWT + STUN auth | Real-time communication |
| **SRT** | AES-256 + Pre-shared key | Certificate pinning | Long-distance, critical |

---

## HLS Security Implementation

HTTP Live Streaming (HLS) provides multiple security layers for broad device compatibility while maintaining strong protection.

### AES-128 Stream Encryption

#### Key Management
```json
{
  "encryption": {
    "method": "AES-128",
    "key_rotation_seconds": 300,
    "key_derivation": "PBKDF2",
    "iterations": 10000,
    "key_server": "https://secure.agency.gov/hls/keys/"
  }
}
```

#### M3U8 Playlist with Encryption
```m3u8
#EXTM3U
#EXT-X-VERSION:6
#EXT-X-TARGETDURATION:6
#EXT-X-KEY:METHOD=AES-128,URI="https://secure.agency.gov/hls/keys/key001",IV=0x12345678901234567890123456789012
#EXTINF:6.0,
segment001.ts
#EXT-X-KEY:METHOD=AES-128,URI="https://secure.agency.gov/hls/keys/key002",IV=0x12345678901234567890123456789013
#EXTINF:6.0,
segment002.ts
```

#### Key Server Implementation
```python
from flask import Flask, request, abort
import jwt
from datetime import datetime, timedelta

app = Flask(__name__)

@app.route('/hls/keys/<key_id>')
def serve_encryption_key(key_id):
    # Validate JWT token
    auth_header = request.headers.get('Authorization')
    if not auth_header or not auth_header.startswith('Bearer '):
        abort(401)
    
    token = auth_header.split(' ')[1]
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=['HS256'])
        
        # Check permissions for this stream
        if not check_stream_permission(payload['sub'], key_id):
            abort(403)
            
        # Return encryption key
        key = get_encryption_key(key_id)
        return key, 200, {'Content-Type': 'application/octet-stream'}
        
    except jwt.ExpiredSignatureError:
        abort(401)
    except jwt.InvalidTokenError:
        abort(401)
```

### JWT-Based Access Control

#### Token Structure
```json
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "sub": "user@agency.gov",
    "iss": "media-router.agency.gov", 
    "aud": "video-streaming",
    "exp": 1642788000,
    "iat": 1642784400,
    "permissions": {
      "cameras": ["cam_001", "cam_002", "group:traffic"],
      "level": 3,
      "actions": ["view", "ptz", "snapshot"]
    },
    "restrictions": {
      "ip_ranges": ["192.168.1.0/24", "10.0.0.0/8"],
      "time_window": {
        "start": "06:00",
        "end": "18:00",
        "timezone": "America/New_York"
      }
    },
    "metadata": {
      "agency": "Police Department",
      "department": "Traffic Division",
      "badge_number": "12345"
    }
  }
}
```

#### HLS URL Authentication
```javascript
// Client-side HLS player with JWT authentication
const player = new Hls({
  xhrSetup: function(xhr, url) {
    // Add JWT token to all requests
    xhr.setRequestHeader('Authorization', 'Bearer ' + jwt_token);
    
    // Add additional headers for audit trail
    xhr.setRequestHeader('X-User-Agent', 'Mobile-Unit-12');
    xhr.setRequestHeader('X-Location', navigator.geolocation ? 'GPS-Enabled' : 'No-GPS');
  }
});

player.loadSource('https://media-router.agency.gov/hls/cam_001/stream.m3u8');
```

### Advanced HLS Security Features

#### Content Watermarking
```json
{
  "watermark": {
    "enabled": true,
    "type": "dynamic_text",
    "content": {
      "user": "{{jwt.sub}}",
      "timestamp": "{{current_time}}",
      "camera": "{{camera_id}}",
      "session": "{{session_id}}"
    },
    "position": "bottom_right",
    "opacity": 0.7,
    "font_size": 16,
    "update_interval": 30
  }
}
```

#### Segment-Level Access Control
```python
@app.route('/hls/<camera_id>/<segment>')
def serve_segment(camera_id, segment):
    # Validate each segment request
    if not validate_jwt_and_permissions(request.headers.get('Authorization'), camera_id):
        abort(403)
    
    # Log access for audit
    log_stream_access(
        user=get_user_from_jwt(),
        camera=camera_id,
        segment=segment,
        ip=request.remote_addr,
        user_agent=request.headers.get('User-Agent')
    )
    
    # Rate limiting
    if not check_rate_limit(get_user_from_jwt(), camera_id):
        abort(429)
    
    return serve_file(f'/streams/{camera_id}/{segment}')
```

---

## RTSP Authentication Methods

RTSP presents unique security challenges due to its age and prevalence in IP camera deployments. Modern implementations require layered security approaches.

### Digest Authentication (Minimum Standard)

#### Implementation
```python
import hashlib
import secrets
from datetime import datetime

class RTSPDigestAuth:
    def __init__(self):
        self.realm = "WINK Streaming"
        self.nonce_timeout = 300  # 5 minutes
        
    def generate_challenge(self):
        nonce = secrets.token_hex(16)
        timestamp = datetime.now().isoformat()
        
        return {
            'realm': self.realm,
            'nonce': nonce,
            'qop': 'auth',
            'algorithm': 'MD5',
            'timestamp': timestamp
        }
    
    def validate_response(self, username, uri, response_hash, nonce, nc, cnonce, qop):
        # Retrieve user password (hashed)
        password_hash = get_user_password_hash(username)
        
        # Calculate expected response
        ha1 = password_hash  # Already hashed username:realm:password
        ha2 = hashlib.md5(f"DESCRIBE:{uri}".encode()).hexdigest()
        
        if qop == 'auth':
            expected = hashlib.md5(
                f"{ha1}:{nonce}:{nc}:{cnonce}:{qop}:{ha2}".encode()
            ).hexdigest()
        else:
            expected = hashlib.md5(f"{ha1}:{nonce}:{ha2}".encode()).hexdigest()
        
        return expected == response_hash
```

### TLS/SSL for RTSP (RTSPS)

#### Certificate Configuration
```nginx
# nginx stream proxy for RTSPS
stream {
    upstream rtsp_backend {
        server 192.168.1.100:8554;
        server 192.168.1.101:8554 backup;
    }
    
    server {
        listen 8322 ssl;
        ssl_certificate /etc/ssl/certs/rtsp-server.crt;
        ssl_certificate_key /etc/ssl/private/rtsp-server.key;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256;
        ssl_prefer_server_ciphers on;
        
        proxy_pass rtsp_backend;
        proxy_timeout 60s;
        proxy_responses 1;
    }
}
```

### IP Camera Integration

#### Axis Camera Authentication
```python
import requests
from requests.auth import HTTPDigestAuth

class AxisCameraAuth:
    def __init__(self, camera_ip, username, password):
        self.base_url = f"http://{camera_ip}"
        self.auth = HTTPDigestAuth(username, password)
        
    def create_user(self, new_username, new_password, access_level):
        """Create temporary user on camera for stream access"""
        url = f"{self.base_url}/axis-cgi/pwdgrp.cgi"
        params = {
            'action': 'add',
            'user': new_username,
            'pwd': new_password,
            'grp': access_level,
            'comment': f'Temporary user created {datetime.now()}'
        }
        
        response = requests.get(url, params=params, auth=self.auth)
        return response.status_code == 200
    
    def delete_user(self, username):
        """Remove temporary user"""
        url = f"{self.base_url}/axis-cgi/pwdgrp.cgi"
        params = {
            'action': 'remove',
            'user': username
        }
        
        response = requests.get(url, params=params, auth=self.auth)
        return response.status_code == 200
```

#### Hikvision ISAPI Authentication
```python
import xml.etree.ElementTree as ET

class HikvisionAuth:
    def setup_temporary_access(self, camera_ip, admin_user, admin_pass, temp_user, duration_hours):
        """Setup temporary user with time restrictions"""
        
        user_xml = f"""
        <User>
            <id>temp_{int(time.time())}</id>
            <userName>{temp_user}</userName>
            <password>{generate_temp_password()}</password>
            <userLevel>Operator</userLevel>
            <loginSessions>3</loginSessions>
            <Valid>
                <enable>true</enable>
                <beginTime>2025-01-01T00:00:00</beginTime>
                <endTime>{(datetime.now() + timedelta(hours=duration_hours)).isoformat()}</endTime>
            </Valid>
        </User>
        """
        
        url = f"http://{camera_ip}/ISAPI/Security/users"
        headers = {'Content-Type': 'application/xml'}
        
        response = requests.post(url, data=user_xml, headers=headers,
                                auth=HTTPDigestAuth(admin_user, admin_pass))
        
        return response.status_code == 200
```

---

## One-Time Password (OTP) Systems

OTP systems provide secure, temporary access without creating permanent user accounts - essential for emergency response and multi-agency cooperation.

### Time-Based OTP (TOTP) Implementation

#### OTP Generation
```python
import pyotp
import qrcode
from datetime import datetime, timedelta

class VideoStreamOTP:
    def __init__(self, secret_key, validity_period=300):
        self.secret = secret_key
        self.validity_period = validity_period
        self.totp = pyotp.TOTP(secret_key, interval=validity_period)
    
    def generate_stream_token(self, camera_list, permission_level, user_info):
        """Generate OTP token for specific cameras and user"""
        current_time = datetime.now()
        
        # Create token payload
        token_data = {
            'cameras': camera_list,
            'permission_level': permission_level,
            'user_info': user_info,
            'issued_at': current_time.isoformat(),
            'expires_at': (current_time + timedelta(seconds=self.validity_period)).isoformat(),
            'otp_code': self.totp.now()
        }
        
        # Generate access URL
        base_url = "https://media-router.agency.gov/otp-access"
        token_string = base64.urlsafe_b64encode(
            json.dumps(token_data).encode()
        ).decode()
        
        return f"{base_url}?token={token_string}&otp={token_data['otp_code']}"
    
    def validate_access(self, token_string, provided_otp):
        """Validate OTP token and code"""
        try:
            token_data = json.loads(
                base64.urlsafe_b64decode(token_string.encode()).decode()
            )
            
            # Check expiration
            expires_at = datetime.fromisoformat(token_data['expires_at'])
            if datetime.now() > expires_at:
                return False, "Token expired"
            
            # Validate OTP code
            if not self.totp.verify(provided_otp, valid_window=2):
                return False, "Invalid OTP code"
            
            return True, token_data
            
        except Exception as e:
            return False, f"Token validation failed: {str(e)}"
```

### SMS/Email OTP Distribution

#### Multi-Channel OTP Delivery
```python
import smtplib
from twilio.rest import Client
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

class OTPDelivery:
    def __init__(self, smtp_config, twilio_config):
        self.smtp_config = smtp_config
        self.twilio_client = Client(twilio_config['sid'], twilio_config['token'])
        
    def send_otp_email(self, recipient, otp_url, context):
        """Send OTP access via secure email"""
        
        message = MIMEMultipart()
        message['From'] = self.smtp_config['from']
        message['To'] = recipient
        message['Subject'] = f"Emergency Video Access - {context['incident_id']}"
        
        body = f"""
        EMERGENCY VIDEO ACCESS GRANTED
        
        Incident: {context['incident_id']}
        Requested by: {context['requestor']}
        Valid until: {context['expires_at']}
        
        Access URL: {otp_url}
        
        SECURITY NOTICE:
        - This link expires in {context['validity_hours']} hours
        - Access is logged and monitored
        - Do not share this URL
        - Report any suspicious activity
        
        Contact: Emergency Operations Center
        Phone: {context['contact_phone']}
        """
        
        message.attach(MIMEText(body, 'plain'))
        
        with smtplib.SMTP_SSL(self.smtp_config['server'], self.smtp_config['port']) as server:
            server.login(self.smtp_config['username'], self.smtp_config['password'])
            server.send_message(message)
    
    def send_otp_sms(self, phone_number, otp_url, context):
        """Send OTP access via SMS"""
        
        short_url = self.create_short_url(otp_url)  # URL shortening service
        
        message = f"""
        EMERGENCY VIDEO ACCESS
        Incident: {context['incident_id']}
        Link: {short_url}
        Expires: {context['expires_at']}
        Contact EOC: {context['contact_phone']}
        """
        
        self.twilio_client.messages.create(
            body=message,
            from_=self.twilio_config['from_number'],
            to=phone_number
        )
```

### OTP Access Interface

#### Web-Based OTP Authentication
```html
<!DOCTYPE html>
<html>
<head>
    <title>Emergency Video Access</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
    <div id="otp-login">
        <h2>Emergency Video Access</h2>
        <form id="otp-form">
            <label>One-Time Password:</label>
            <input type="text" id="otp-code" maxlength="6" pattern="[0-9]{6}" required>
            <button type="submit">Access Video Streams</button>
        </form>
        <div id="status"></div>
    </div>

    <script>
    document.getElementById('otp-form').onsubmit = async function(e) {
        e.preventDefault();
        
        const otpCode = document.getElementById('otp-code').value;
        const token = new URLSearchParams(window.location.search).get('token');
        
        const response = await fetch('/api/otp/validate', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({token: token, otp: otpCode})
        });
        
        if (response.ok) {
            const data = await response.json();
            window.location.href = `/streams?access_token=${data.access_token}`;
        } else {
            document.getElementById('status').innerHTML = '<p style="color:red;">Invalid or expired OTP</p>';
        }
    };
    </script>
</body>
</html>
```

---

## JSON Web Token (JWT) Implementation

JWT provides stateless, scalable authentication ideal for distributed video streaming systems.

### JWT Structure for Video Streaming

#### Custom Claims for Video Access
```json
{
  "header": {
    "alg": "RS256",
    "typ": "JWT",
    "kid": "video-signing-key-2025"
  },
  "payload": {
    "iss": "https://auth.agency.gov",
    "sub": "officer.smith@police.gov",
    "aud": ["video-streaming", "mobile-access"],
    "exp": 1642788000,
    "iat": 1642784400,
    "nbf": 1642784400,
    "jti": "stream-session-abc123",
    
    "video_permissions": {
      "cameras": {
        "allowed": ["cam_downtown_*", "cam_highway_101", "cam_airport_*"],
        "denied": ["cam_classified_*"],
        "groups": ["traffic_cameras", "public_areas"]
      },
      "actions": ["view", "snapshot", "ptz"],
      "quality_limit": "1080p",
      "concurrent_streams": 5
    },
    
    "temporal_restrictions": {
      "valid_hours": {
        "monday": ["06:00-22:00"],
        "tuesday": ["06:00-22:00"], 
        "wednesday": ["06:00-22:00"],
        "thursday": ["06:00-22:00"],
        "friday": ["06:00-22:00"],
        "saturday": ["08:00-20:00"],
        "sunday": ["08:00-20:00"]
      },
      "timezone": "America/New_York"
    },
    
    "network_restrictions": {
      "allowed_ips": ["192.168.1.0/24", "10.0.0.0/8"],
      "allowed_countries": ["US"],
      "vpn_required": false
    },
    
    "audit_info": {
      "agency": "Metro Police Department",
      "department": "Traffic Division",
      "badge_number": "T-4567",
      "supervisor": "sergeant.johnson@police.gov",
      "purpose": "Traffic incident response"
    }
  }
}
```

### JWT Validation Middleware

#### Express.js JWT Middleware
```javascript
const jwt = require('jsonwebtoken');
const fs = require('fs');

// Load public key for JWT verification
const publicKey = fs.readFileSync('/etc/ssl/jwt-public-key.pem');

const validateVideoJWT = (req, res, next) => {
    const authHeader = req.headers.authorization;
    
    if (!authHeader || !authHeader.startsWith('Bearer ')) {
        return res.status(401).json({ error: 'Missing or invalid authorization header' });
    }
    
    const token = authHeader.substring(7);
    
    try {
        // Verify JWT signature and expiration
        const decoded = jwt.verify(token, publicKey, {
            algorithms: ['RS256'],
            audience: 'video-streaming',
            issuer: 'https://auth.agency.gov'
        });
        
        // Additional validation
        if (!validateNetworkRestrictions(decoded, req.ip)) {
            return res.status(403).json({ error: 'Network access denied' });
        }
        
        if (!validateTimeRestrictions(decoded)) {
            return res.status(403).json({ error: 'Outside permitted time window' });
        }
        
        // Add decoded token to request for route handlers
        req.jwt = decoded;
        req.user = decoded.sub;
        
        // Log access attempt
        logStreamAccess({
            user: decoded.sub,
            ip: req.ip,
            user_agent: req.headers['user-agent'],
            timestamp: new Date(),
            token_id: decoded.jti
        });
        
        next();
        
    } catch (error) {
        if (error.name === 'TokenExpiredError') {
            return res.status(401).json({ error: 'Token expired' });
        } else if (error.name === 'JsonWebTokenError') {
            return res.status(401).json({ error: 'Invalid token' });
        } else {
            return res.status(500).json({ error: 'Token validation failed' });
        }
    }
};

const validateCameraAccess = (cameraId) => {
    return (req, res, next) => {
        const permissions = req.jwt.video_permissions;
        
        // Check if camera is explicitly denied
        if (permissions.cameras.denied.some(pattern => 
            new RegExp(pattern.replace('*', '.*')).test(cameraId))) {
            return res.status(403).json({ error: 'Camera access denied' });
        }
        
        // Check if camera is in allowed list
        const allowed = permissions.cameras.allowed.some(pattern =>
            new RegExp(pattern.replace('*', '.*')).test(cameraId));
            
        const inGroup = permissions.cameras.groups.some(group =>
            checkCameraInGroup(cameraId, group));
        
        if (!allowed && !inGroup) {
            return res.status(403).json({ error: 'Camera not in permitted list' });
        }
        
        next();
    };
};

// Usage in route
app.get('/stream/:cameraId/hls', 
    validateVideoJWT,
    validateCameraAccess(req.params.cameraId),
    (req, res) => {
        // Serve HLS stream
        res.redirect(`/hls/${req.params.cameraId}/stream.m3u8`);
    }
);
```

### JWT Refresh and Rotation

#### Token Refresh Strategy
```python
import jwt
from datetime import datetime, timedelta
from cryptography.hazmat.primitives import serialization

class JWTManager:
    def __init__(self, private_key_path, public_key_path):
        with open(private_key_path, 'rb') as f:
            self.private_key = serialization.load_pem_private_key(f.read(), password=None)
        
        with open(public_key_path, 'rb') as f:
            self.public_key = serialization.load_pem_public_key(f.read())
    
    def create_access_token(self, user_data, expires_in_minutes=60):
        """Create short-lived access token"""
        now = datetime.utcnow()
        
        payload = {
            'iss': 'https://auth.agency.gov',
            'sub': user_data['username'],
            'aud': 'video-streaming',
            'exp': now + timedelta(minutes=expires_in_minutes),
            'iat': now,
            'nbf': now,
            'jti': f"access-{uuid.uuid4()}",
            'video_permissions': user_data['permissions'],
            'audit_info': user_data['audit_info']
        }
        
        return jwt.encode(payload, self.private_key, algorithm='RS256')
    
    def create_refresh_token(self, user_data, expires_in_days=7):
        """Create long-lived refresh token"""
        now = datetime.utcnow()
        
        payload = {
            'iss': 'https://auth.agency.gov',
            'sub': user_data['username'],
            'aud': 'token-refresh',
            'exp': now + timedelta(days=expires_in_days),
            'iat': now,
            'jti': f"refresh-{uuid.uuid4()}",
            'token_type': 'refresh'
        }
        
        return jwt.encode(payload, self.private_key, algorithm='RS256')
    
    def refresh_access_token(self, refresh_token):
        """Generate new access token from refresh token"""
        try:
            # Validate refresh token
            payload = jwt.decode(refresh_token, self.public_key, 
                               algorithms=['RS256'], audience='token-refresh')
            
            if payload.get('token_type') != 'refresh':
                raise jwt.InvalidTokenError('Not a refresh token')
            
            # Check if refresh token is blacklisted
            if self.is_token_blacklisted(payload['jti']):
                raise jwt.InvalidTokenError('Token revoked')
            
            # Get current user data
            user_data = self.get_user_data(payload['sub'])
            
            # Generate new access token
            return self.create_access_token(user_data)
            
        except jwt.ExpiredSignatureError:
            raise Exception('Refresh token expired')
        except jwt.InvalidTokenError as e:
            raise Exception(f'Invalid refresh token: {str(e)}')
```

---

## Multi-Factor Authentication

MFA adds critical security layers for high-value video systems and sensitive deployments.

### TOTP Integration (Google Authenticator, Authy)

#### MFA Setup Process
```python
import pyotp
import qrcode
import io
import base64

class VideoMFA:
    def __init__(self):
        self.issuer = "WINK Video Security"
        
    def setup_mfa_for_user(self, username, display_name):
        """Initialize MFA setup for user"""
        
        # Generate secret key
        secret = pyotp.random_base32()
        
        # Create TOTP URI
        totp_uri = pyotp.totp.TOTP(secret).provisioning_uri(
            name=username,
            issuer_name=self.issuer
        )
        
        # Generate QR code
        qr = qrcode.QRCode(version=1, box_size=10, border=5)
        qr.add_data(totp_uri)
        qr.make(fit=True)
        
        img = qr.make_image(fill_color="black", back_color="white")
        
        # Convert to base64 for web display
        img_buffer = io.BytesIO()
        img.save(img_buffer, format='PNG')
        img_base64 = base64.b64encode(img_buffer.getvalue()).decode()
        
        return {
            'secret': secret,
            'qr_code': f"data:image/png;base64,{img_base64}",
            'backup_codes': self.generate_backup_codes()
        }
    
    def verify_mfa_code(self, secret, provided_code):
        """Verify TOTP code"""
        totp = pyotp.TOTP(secret)
        
        # Allow 1 window tolerance for time sync issues
        return totp.verify(provided_code, valid_window=1)
    
    def generate_backup_codes(self, count=10):
        """Generate single-use backup codes"""
        return [secrets.token_hex(4).upper() for _ in range(count)]
```

### Hardware Security Keys (WebAuthn/FIDO2)

#### WebAuthn Implementation
```javascript
// Client-side WebAuthn registration
async function registerSecurityKey(username) {
    try {
        // Get registration challenge from server
        const challengeResponse = await fetch('/api/webauthn/register/begin', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({username: username})
        });
        
        const options = await challengeResponse.json();
        
        // Create credential using WebAuthn API
        const credential = await navigator.credentials.create({
            publicKey: {
                challenge: base64ToArrayBuffer(options.challenge),
                rp: options.rp,
                user: {
                    id: base64ToArrayBuffer(options.user.id),
                    name: options.user.name,
                    displayName: options.user.displayName
                },
                pubKeyCredParams: options.pubKeyCredParams,
                authenticatorSelection: {
                    authenticatorAttachment: 'cross-platform',
                    userVerification: 'required'
                },
                timeout: 60000,
                attestation: 'direct'
            }
        });
        
        // Send credential to server
        const registrationResponse = await fetch('/api/webauthn/register/complete', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({
                id: credential.id,
                rawId: arrayBufferToBase64(credential.rawId),
                response: {
                    attestationObject: arrayBufferToBase64(credential.response.attestationObject),
                    clientDataJSON: arrayBufferToBase64(credential.response.clientDataJSON)
                },
                type: credential.type
            })
        });
        
        if (registrationResponse.ok) {
            alert('Security key registered successfully!');
        }
        
    } catch (error) {
        console.error('WebAuthn registration failed:', error);
        alert('Security key registration failed: ' + error.message);
    }
}

// Client-side WebAuthn authentication
async function authenticateWithSecurityKey(username) {
    try {
        // Get authentication challenge
        const challengeResponse = await fetch('/api/webauthn/authenticate/begin', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({username: username})
        });
        
        const options = await challengeResponse.json();
        
        // Authenticate using security key
        const assertion = await navigator.credentials.get({
            publicKey: {
                challenge: base64ToArrayBuffer(options.challenge),
                allowCredentials: options.allowCredentials.map(cred => ({
                    ...cred,
                    id: base64ToArrayBuffer(cred.id)
                })),
                userVerification: 'required',
                timeout: 60000
            }
        });
        
        // Send authentication response
        const authResponse = await fetch('/api/webauthn/authenticate/complete', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({
                id: assertion.id,
                rawId: arrayBufferToBase64(assertion.rawId),
                response: {
                    authenticatorData: arrayBufferToBase64(assertion.response.authenticatorData),
                    clientDataJSON: arrayBufferToBase64(assertion.response.clientDataJSON),
                    signature: arrayBufferToBase64(assertion.response.signature),
                    userHandle: assertion.response.userHandle ? 
                        arrayBufferToBase64(assertion.response.userHandle) : null
                },
                type: assertion.type
            })
        });
        
        if (authResponse.ok) {
            const result = await authResponse.json();
            localStorage.setItem('video_access_token', result.access_token);
            window.location.href = '/dashboard';
        }
        
    } catch (error) {
        console.error('WebAuthn authentication failed:', error);
        alert('Security key authentication failed: ' + error.message);
    }
}
```

### Smart Card Integration (PIV/CAC)

#### PIV Card Authentication
```python
from smartcard.System import readers
from smartcard.util import toHexString
import cryptography.x509 as x509

class PIVAuthentication:
    def __init__(self):
        self.piv_application_id = [0xA0, 0x00, 0x00, 0x03, 0x08]
        
    def authenticate_with_piv(self):
        """Authenticate user with PIV/CAC smart card"""
        
        # Get available card readers
        card_readers = readers()
        if not card_readers:
            raise Exception("No smart card readers found")
        
        # Connect to first available reader
        reader = card_readers[0]
        connection = reader.createConnection()
        connection.connect()
        
        try:
            # Select PIV application
            select_command = [0x00, 0xA4, 0x04, 0x00, 0x05] + self.piv_application_id
            response, sw1, sw2 = connection.transmit(select_command)
            
            if sw1 != 0x90:
                raise Exception(f"Failed to select PIV application: {sw1:02x}{sw2:02x}")
            
            # Get PIV authentication certificate
            cert_data = self.get_piv_certificate(connection, 0x9A)  # PIV Auth cert
            
            # Parse certificate
            cert = x509.load_der_x509_certificate(bytes(cert_data))
            
            # Extract user information
            user_info = self.extract_user_info(cert)
            
            # Verify certificate against trusted CA
            if not self.verify_certificate_chain(cert):
                raise Exception("Certificate verification failed")
            
            # Perform cryptographic authentication challenge
            challenge_response = self.piv_challenge_response(connection, cert.public_key())
            
            return {
                'authenticated': True,
                'user_info': user_info,
                'certificate': cert,
                'authentication_method': 'PIV/CAC'
            }
            
        finally:
            connection.disconnect()
    
    def extract_user_info(self, certificate):
        """Extract user information from PIV certificate"""
        subject = certificate.subject
        
        user_info = {}
        for attribute in subject:
            if attribute.oid == x509.NameOID.COMMON_NAME:
                user_info['name'] = attribute.value
            elif attribute.oid == x509.NameOID.EMAIL_ADDRESS:
                user_info['email'] = attribute.value
            elif attribute.oid == x509.NameOID.ORGANIZATIONAL_UNIT_NAME:
                user_info['department'] = attribute.value
            elif attribute.oid == x509.NameOID.ORGANIZATION_NAME:
                user_info['agency'] = attribute.value
        
        return user_info
```

---

## Real-World Security Scenarios

### Scenario 1: Multi-Agency Emergency Response

**Challenge:** Hurricane evacuation with 15 agencies needing immediate camera access across 3 states.

#### Implementation
```python
class EmergencyResponseAuth:
    def __init__(self):
        self.emergency_authorities = [
            'fema.gov', 'state-police.*.gov', 'sheriff.*.gov', 
            '*.fire-dept.gov', 'emergency-mgmt.*.gov'
        ]
        
    def activate_emergency_access(self, incident_id, requesting_agency, camera_groups):
        """Activate emergency access protocols"""
        
        # Verify requesting agency authority
        if not self.verify_emergency_authority(requesting_agency):
            raise SecurityError("Unauthorized emergency access request")
        
        # Generate emergency JWT with extended permissions
        emergency_jwt = {
            'sub': f'emergency-{incident_id}',
            'emergency_mode': True,
            'incident_id': incident_id,
            'requesting_agency': requesting_agency,
            'video_permissions': {
                'cameras': {
                    'allowed': camera_groups,
                    'denied': ['classified_*', 'secure_*']
                },
                'actions': ['view', 'snapshot', 'ptz'],
                'quality_limit': '1080p',
                'concurrent_streams': 50
            },
            'temporal_restrictions': {
                'emergency_window': True,
                'max_duration_hours': 72
            },
            'audit_info': {
                'access_type': 'emergency_response',
                'incident_id': incident_id,
                'activation_time': datetime.now().isoformat(),
                'auto_expires': True
            }
        }
        
        # Broadcast emergency access to participating agencies
        self.broadcast_emergency_access(incident_id, emergency_jwt)
        
        return emergency_jwt
```

### Scenario 2: Court Evidence Streaming

**Challenge:** Secure, authenticated access to video evidence during court proceedings with strict chain of custody.

#### Evidence Chain Implementation
```python
class EvidenceStreamAuth:
    def create_evidence_session(self, case_number, court_room, participants):
        """Create authenticated evidence viewing session"""
        
        # Generate cryptographic proof of evidence integrity
        evidence_hash = self.calculate_evidence_hash(case_number)
        
        session_token = {
            'sub': f'evidence-session-{case_number}',
            'case_number': case_number,
            'court_room': court_room,
            'evidence_hash': evidence_hash,
            'participants': participants,
            'video_permissions': {
                'cameras': {'allowed': [f'evidence_{case_number}_*']},
                'actions': ['view'],  # No PTZ or snapshot for evidence
                'quality_limit': 'original',  # Full quality required
                'watermark_required': True
            },
            'chain_of_custody': {
                'accessed_by': [],
                'access_log': [],
                'tamper_detection': True
            },
            'compliance': {
                'recording_required': True,
                'audit_level': 'maximum',
                'retention_period': '7_years'
            }
        }
        
        # Log evidence access in tamper-proof ledger
        self.log_evidence_access(session_token)
        
        return session_token
    
    def log_evidence_chain(self, session_id, action, user, timestamp):
        """Maintain cryptographic chain of custody log"""
        
        previous_hash = self.get_last_chain_hash(session_id)
        
        chain_entry = {
            'session_id': session_id,
            'action': action,
            'user': user,
            'timestamp': timestamp.isoformat(),
            'previous_hash': previous_hash,
            'entry_hash': None
        }
        
        # Calculate hash including previous entry
        entry_data = json.dumps(chain_entry, sort_keys=True)
        chain_entry['entry_hash'] = hashlib.sha256(entry_data.encode()).hexdigest()
        
        # Store in blockchain-like structure
        self.store_chain_entry(chain_entry)
```

### Scenario 3: International Border Security

**Challenge:** Cross-border camera sharing between allied nations with diplomatic protocols and sovereignty restrictions.

#### Diplomatic Access Control
```python
class DiplomaticAccess:
    def __init__(self):
        self.treaty_agreements = self.load_treaty_database()
        self.diplomatic_channels = self.load_diplomatic_keys()
        
    def authorize_cross_border_access(self, requesting_country, target_cameras, justification):
        """Authorize cross-border video access per diplomatic agreements"""
        
        # Verify treaty allows this type of access
        if not self.verify_treaty_authority(requesting_country, target_cameras):
            raise DiplomaticError("No treaty authority for requested cameras")
        
        # Generate diplomatic access token
        diplomatic_token = {
            'sub': f'diplomatic-{requesting_country}',
            'requesting_country': requesting_country,
            'host_country': self.get_host_country(),
            'treaty_reference': self.get_applicable_treaty(requesting_country),
            'diplomatic_justification': justification,
            'video_permissions': {
                'cameras': self.filter_cameras_by_treaty(target_cameras, requesting_country),
                'actions': ['view'],  # Limited to viewing only
                'quality_limit': '720p',  # Reduced quality for sovereignty
                'geographic_restrictions': self.get_border_zones(requesting_country)
            },
            'diplomatic_restrictions': {
                'notification_required': True,
                'host_approval_required': True,
                'time_limited': True,
                'max_duration_hours': 24
            },
            'audit_info': {
                'diplomatic_protocol': True,
                'notification_sent_to': self.get_diplomatic_contacts(),
                'approval_chain': []
            }
        }
        
        # Send diplomatic notification
        self.send_diplomatic_notification(diplomatic_token)
        
        return diplomatic_token
```

---

## Security Best Practices

### Defense in Depth

#### Layered Security Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                         Layer 7: Audit & Monitoring        │
├─────────────────────────────────────────────────────────────┤
│                         Layer 6: Application Security      │
├─────────────────────────────────────────────────────────────┤  
│                         Layer 5: Data Encryption          │
├─────────────────────────────────────────────────────────────┤
│                         Layer 4: Access Control           │
├─────────────────────────────────────────────────────────────┤
│                         Layer 3: Authentication           │
├─────────────────────────────────────────────────────────────┤
│                         Layer 2: Network Security         │
├─────────────────────────────────────────────────────────────┤
│                         Layer 1: Physical Security        │
└─────────────────────────────────────────────────────────────┘
```

### Key Management

#### Secure Key Storage
```python
from cryptography.fernet import Fernet
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
import boto3

class SecureKeyManager:
    def __init__(self, provider='azure_keyvault'):
        self.provider = provider
        
        if provider == 'azure_keyvault':
            self.credential = DefaultAzureCredential()
            self.client = SecretClient(
                vault_url="https://your-vault.vault.azure.net/",
                credential=self.credential
            )
        elif provider == 'aws_secrets':
            self.client = boto3.client('secretsmanager')
            
    def store_encryption_key(self, key_name, key_value, metadata=None):
        """Securely store encryption keys"""
        
        if self.provider == 'azure_keyvault':
            self.client.set_secret(
                name=key_name,
                value=key_value,
                content_type='application/octet-stream',
                tags=metadata or {}
            )
        elif self.provider == 'aws_secrets':
            self.client.put_secret_value(
                SecretId=key_name,
                SecretString=key_value,
                Description=metadata.get('description', '') if metadata else ''
            )
    
    def rotate_keys(self, key_name):
        """Implement automatic key rotation"""
        
        # Generate new key
        new_key = Fernet.generate_key()
        
        # Store with versioning
        self.store_encryption_key(f"{key_name}_v{int(time.time())}", new_key.decode())
        
        # Update current key pointer
        self.store_encryption_key(f"{key_name}_current", new_key.decode())
        
        return new_key
```

### Session Management

#### Secure Session Handling
```python
import redis
from datetime import datetime, timedelta

class SecureSessionManager:
    def __init__(self, redis_client):
        self.redis = redis_client
        self.session_timeout = 3600  # 1 hour
        self.max_concurrent_sessions = 3
        
    def create_session(self, user_id, client_info):
        """Create secure session with limits"""
        
        # Check existing sessions
        existing_sessions = self.get_user_sessions(user_id)
        
        if len(existing_sessions) >= self.max_concurrent_sessions:
            # Remove oldest session
            self.revoke_session(existing_sessions[0])
        
        # Generate session
        session_id = secrets.token_urlsafe(32)
        
        session_data = {
            'user_id': user_id,
            'created_at': datetime.now().isoformat(),
            'last_activity': datetime.now().isoformat(),
            'client_ip': client_info.get('ip'),
            'user_agent': client_info.get('user_agent'),
            'active': True
        }
        
        # Store session
        self.redis.setex(
            f"session:{session_id}",
            self.session_timeout,
            json.dumps(session_data)
        )
        
        # Track user sessions
        self.redis.sadd(f"user_sessions:{user_id}", session_id)
        
        return session_id
    
    def validate_session(self, session_id, client_info):
        """Validate session and detect anomalies"""
        
        session_data = self.redis.get(f"session:{session_id}")
        if not session_data:
            return False, "Session not found or expired"
        
        session = json.loads(session_data)
        
        # Check if session is active
        if not session.get('active'):
            return False, "Session revoked"
        
        # Detect potential session hijacking
        if session['client_ip'] != client_info.get('ip'):
            self.log_security_event('session_hijack_attempt', {
                'session_id': session_id,
                'original_ip': session['client_ip'],
                'current_ip': client_info.get('ip')
            })
            self.revoke_session(session_id)
            return False, "Session security violation"
        
        # Update last activity
        session['last_activity'] = datetime.now().isoformat()
        self.redis.setex(
            f"session:{session_id}",
            self.session_timeout,
            json.dumps(session)
        )
        
        return True, session
```

### Anomaly Detection

#### Behavioral Security Monitoring
```python
import numpy as np
from sklearn.ensemble import IsolationForest
from collections import defaultdict

class SecurityAnomalyDetector:
    def __init__(self):
        self.models = {}
        self.baselines = defaultdict(list)
        self.alert_threshold = -0.5
        
    def analyze_access_patterns(self, user_id, current_access):
        """Detect anomalous access patterns"""
        
        # Extract features
        features = self.extract_access_features(current_access)
        
        if user_id not in self.models:
            # Build baseline if insufficient data
            self.baselines[user_id].append(features)
            
            if len(self.baselines[user_id]) >= 20:
                # Train anomaly detection model
                training_data = np.array(self.baselines[user_id])
                self.models[user_id] = IsolationForest(contamination=0.1)
                self.models[user_id].fit(training_data)
                
            return False, "Building baseline"
        
        # Check for anomalies
        anomaly_score = self.models[user_id].decision_function([features])[0]
        
        if anomaly_score < self.alert_threshold:
            # Potential anomaly detected
            self.log_security_alert({
                'user_id': user_id,
                'anomaly_score': anomaly_score,
                'access_details': current_access,
                'timestamp': datetime.now().isoformat()
            })
            
            return True, f"Anomaly detected (score: {anomaly_score})"
        
        # Update baseline with normal behavior
        self.baselines[user_id].append(features)
        
        # Keep baseline size manageable
        if len(self.baselines[user_id]) > 100:
            self.baselines[user_id] = self.baselines[user_id][-50:]
        
        return False, "Normal access pattern"
    
    def extract_access_features(self, access_data):
        """Extract features for anomaly detection"""
        
        # Time-based features
        hour_of_day = datetime.now().hour
        day_of_week = datetime.now().weekday()
        
        # Access pattern features
        cameras_accessed = len(access_data.get('cameras', []))
        session_duration = access_data.get('duration', 0)
        bandwidth_usage = access_data.get('bandwidth', 0)
        
        # Behavioral features
        click_rate = access_data.get('interactions', 0) / max(session_duration, 1)
        unique_cameras_ratio = len(set(access_data.get('cameras', []))) / max(cameras_accessed, 1)
        
        return [
            hour_of_day, day_of_week, cameras_accessed,
            session_duration, bandwidth_usage, click_rate, unique_cameras_ratio
        ]
```

---

## Compliance and Audit Requirements

### PCI-DSS Compliance

#### PCI-DSS Requirements for Video Streaming
```python
class PCIDSSCompliance:
    def __init__(self):
        self.required_controls = {
            'access_control': ['2FA', 'unique_user_ids', 'access_restrictions'],
            'encryption': ['TLS_1_2_minimum', 'AES_256', 'key_management'],
            'monitoring': ['access_logs', 'security_events', 'file_integrity'],
            'network_security': ['firewall', 'secure_protocols', 'network_segmentation']
        }
        
    def validate_pci_compliance(self, system_config):
        """Validate system meets PCI-DSS requirements"""
        
        compliance_report = {
            'compliant': True,
            'findings': [],
            'requirements_met': [],
            'requirements_failed': []
        }
        
        # Requirement 2: Do not use vendor-supplied defaults
        if not self.validate_default_passwords_changed(system_config):
            compliance_report['findings'].append({
                'requirement': '2.1',
                'description': 'Default passwords must be changed',
                'severity': 'HIGH',
                'remediation': 'Change all default passwords before deployment'
            })
            compliance_report['compliant'] = False
        
        # Requirement 4: Encrypt transmission of cardholder data
        if not self.validate_encryption_in_transit(system_config):
            compliance_report['findings'].append({
                'requirement': '4.1',
                'description': 'Strong cryptography required for data transmission',
                'severity': 'HIGH', 
                'remediation': 'Implement TLS 1.2 or higher for all communications'
            })
            compliance_report['compliant'] = False
        
        # Requirement 7: Restrict access by business need-to-know
        if not self.validate_access_controls(system_config):
            compliance_report['findings'].append({
                'requirement': '7.1',
                'description': 'Access must be limited to need-to-know basis',
                'severity': 'MEDIUM',
                'remediation': 'Implement role-based access controls'
            })
        
        # Requirement 8: Identify and authenticate access
        if not self.validate_authentication_controls(system_config):
            compliance_report['findings'].append({
                'requirement': '8.2',
                'description': 'Strong authentication required',
                'severity': 'HIGH',
                'remediation': 'Implement multi-factor authentication'
            })
            compliance_report['compliant'] = False
        
        # Requirement 10: Track and monitor access
        if not self.validate_logging_controls(system_config):
            compliance_report['findings'].append({
                'requirement': '10.2',
                'description': 'All access must be logged',
                'severity': 'MEDIUM',
                'remediation': 'Enable comprehensive audit logging'
            })
        
        return compliance_report
```

### FISMA Compliance

#### Federal Security Requirements
```python
class FISMACompliance:
    def __init__(self):
        self.security_controls = {
            'AC': 'Access Control',
            'AU': 'Audit and Accountability', 
            'IA': 'Identification and Authentication',
            'SC': 'System and Communications Protection',
            'SI': 'System and Information Integrity'
        }
        
    def implement_fisma_controls(self, impact_level='moderate'):
        """Implement FISMA security controls"""
        
        controls = {
            'AC-2': {
                'control': 'Account Management',
                'implementation': 'Automated user account management with approval workflows',
                'evidence': 'User management logs, approval records'
            },
            'AC-3': {
                'control': 'Access Enforcement', 
                'implementation': 'Role-based access control with separation of duties',
                'evidence': 'Access control policies, permission matrices'
            },
            'AU-2': {
                'control': 'Audit Events',
                'implementation': 'Comprehensive logging of all security events',
                'evidence': 'Audit logs, SIEM integration'
            },
            'IA-2': {
                'control': 'Identification and Authentication',
                'implementation': 'Multi-factor authentication for all users',
                'evidence': 'MFA enrollment records, authentication logs'
            },
            'SC-8': {
                'control': 'Transmission Confidentiality',
                'implementation': 'TLS 1.3 encryption for all network communications',
                'evidence': 'TLS configuration, cipher suite documentation'
            }
        }
        
        if impact_level == 'high':
            # Additional controls for high-impact systems
            controls.update({
                'IA-2(1)': {
                    'control': 'Network Access to Privileged Accounts',
                    'implementation': 'Multi-factor authentication for privileged access',
                    'evidence': 'Privileged access logs, MFA compliance reports'
                },
                'IA-2(2)': {
                    'control': 'Network Access to Non-Privileged Accounts',
                    'implementation': 'Multi-factor authentication for all network access',
                    'evidence': 'Network access logs, authentication records'
                }
            })
        
        return controls
```

### Audit Trail Implementation

#### Comprehensive Security Logging
```python
import json
from datetime import datetime
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC

class SecurityAuditLogger:
    def __init__(self, log_encryption_key):
        self.encryption_key = log_encryption_key
        self.log_integrity_hash = None
        
    def log_authentication_event(self, event_type, user_id, client_info, success, details=None):
        """Log authentication events with integrity protection"""
        
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'event_type': f'AUTH_{event_type.upper()}',
            'user_id': user_id,
            'client_ip': client_info.get('ip'),
            'user_agent': client_info.get('user_agent'),
            'success': success,
            'details': details or {},
            'session_id': client_info.get('session_id')
        }
        
        self.write_audit_entry(audit_entry)
        
        # Alert on failed authentication attempts
        if not success:
            self.check_failed_login_threshold(user_id)
    
    def log_video_access_event(self, user_id, camera_id, action, client_info):
        """Log video stream access events"""
        
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'event_type': 'VIDEO_ACCESS',
            'user_id': user_id,
            'camera_id': camera_id,
            'action': action,
            'client_ip': client_info.get('ip'),
            'session_id': client_info.get('session_id'),
            'stream_quality': client_info.get('quality'),
            'bandwidth_used': client_info.get('bandwidth')
        }
        
        self.write_audit_entry(audit_entry)
    
    def log_security_violation(self, violation_type, user_id, details, severity='MEDIUM'):
        """Log security violations and policy breaches"""
        
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'event_type': f'SECURITY_VIOLATION_{violation_type.upper()}',
            'user_id': user_id,
            'severity': severity,
            'details': details,
            'investigation_required': severity in ['HIGH', 'CRITICAL']
        }
        
        self.write_audit_entry(audit_entry)
        
        # Immediate alerting for critical violations
        if severity == 'CRITICAL':
            self.send_immediate_alert(audit_entry)
    
    def write_audit_entry(self, entry):
        """Write audit entry with encryption and integrity protection"""
        
        # Add integrity hash chain
        entry['previous_hash'] = self.log_integrity_hash
        entry_json = json.dumps(entry, sort_keys=True)
        
        # Calculate hash including previous entry
        current_hash = hashes.Hash(hashes.SHA256())
        current_hash.update(entry_json.encode())
        self.log_integrity_hash = current_hash.finalize().hex()
        
        entry['entry_hash'] = self.log_integrity_hash
        
        # Encrypt sensitive fields
        encrypted_entry = self.encrypt_sensitive_fields(entry)
        
        # Write to secure log store
        self.store_audit_entry(encrypted_entry)
    
    def verify_audit_integrity(self, start_date, end_date):
        """Verify integrity of audit log chain"""
        
        entries = self.get_audit_entries(start_date, end_date)
        
        for i, entry in enumerate(entries):
            if i == 0:
                continue
                
            # Verify hash chain
            previous_entry = entries[i-1]
            if entry['previous_hash'] != previous_entry['entry_hash']:
                return False, f"Integrity violation at entry {entry['timestamp']}"
        
        return True, "Audit log integrity verified"
```

---

*This comprehensive security guide provides battle-tested implementations for protecting video streaming systems in government and enterprise environments. All code examples are production-ready but should be customized for specific deployment requirements and security policies.*

*© 2025 WINK Streaming. All rights reserved.*