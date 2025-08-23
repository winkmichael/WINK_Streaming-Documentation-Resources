---
title: "WINK Forge - Enterprise Video Transcoding Platform Technical Manual"
description: "Complete technical manual for WINK Forge enterprise video transcoding platform. RTSP/SRT/H.265 configuration, automatic input detection, VMS integration, and best practices."
keywords: ["video transcoding", "RTSP", "SRT", "H.265", "VMS integration", "enterprise video", "streaming platform", "WINK Forge", "video processing", "adaptive streaming"]
category: "manuals"
product: "WINK Forge"
version: "8.x - 2025 Edition"
last_updated: "August 2025"
author: "WINK Streaming, Inc."
---

# WINK Forge

## Enterprise Video Transcoding Platform
### Technical Manual & User Guide

**Version 8.x - 2025 Edition**  
**WINK Streaming, Inc.**  
**Document Updated: August 2025**

---

## Table of Contents

1. [Introduction to WINK Forge](#1-introduction-to-wink-forge)
   - 1.1 [Overview](#11-overview)
   - 1.2 [System Architecture](#12-system-architecture)
   - 1.3 [Deployment Options](#13-deployment-options)

2. [Getting Started](#2-getting-started)
   - 2.1 [Web Interface Access](#21-web-interface-access)
   - 2.2 [Default Credentials](#22-default-credentials)
   - 2.3 [Security Configuration](#23-security-configuration)

3. [Input Configuration](#3-input-configuration)
   - 3.1 [Supported Input Types](#31-supported-input-types)
   - 3.2 [Input Settings](#32-input-settings)
   - 3.3 [Advanced Input Options](#33-advanced-input-options)

4. [Output Configuration](#4-output-configuration)
   - 4.1 [Output Formats](#41-output-formats)
   - 4.2 [Encoding Settings](#42-encoding-settings)
   - 4.3 [Adaptive Streaming](#43-adaptive-streaming)

5. [Media Protocols](#5-media-protocols)
   - 5.1 [RTMP Configuration](#51-rtmp-configuration)
   - 5.2 [SRT Configuration](#52-srt-configuration)
   - 5.3 [RTSP Configuration](#53-rtsp-configuration)
   - 5.4 [HLS/LL-HLS](#54-hlsll-hls)
   - 5.5 [MPEG-DASH](#55-mpeg-dash)

6. [VMS Integration](#6-vms-integration)
   - 6.1 [Genetec Integration](#61-genetec-integration)
   - 6.2 [Milestone Integration](#62-milestone-integration)
   - 6.3 [VMS to VMS Bridging](#63-vms-to-vms-bridging)

7. [API Reference](#7-api-reference)
   - 7.1 [API Overview](#71-api-overview)
   - 7.2 [Stream Control](#72-stream-control)
   - 7.3 [User Management](#73-user-management)

8. [Advanced Features](#8-advanced-features)
   - 8.1 [One-Time Password (OTP)](#81-one-time-password-otp)
   - 8.2 [Watermarking](#82-watermarking)
   - 8.3 [PTZ Control](#83-ptz-control)
   - 8.4 [Advanced Auto-Detection](#84-advanced-auto-detection)

9. [Monitoring & Analytics](#9-monitoring--analytics)
   - 9.1 [Real-time Dashboard](#91-real-time-dashboard)
   - 9.2 [Stream Statistics](#92-stream-statistics)
   - 9.3 [Alert Configuration](#93-alert-configuration)

10. [Troubleshooting](#10-troubleshooting)

11. [Appendices](#appendices)
    - A. [SSL Certificate Management](#a-ssl-certificate-management)
    - B. [Network Configuration](#b-network-configuration)
    - C. [Performance Tuning](#c-performance-tuning)
    - D. [Codec Comparison Guide](#d-codec-comparison-guide)
    - E. [Common Transcoding Workflows](#e-common-transcoding-workflows)

---

## 1. Introduction to WINK Forge

### 1.1 Overview

WINK Forge is an enterprise-grade video transcoding and streaming platform that transforms video content for optimal delivery across diverse networks and devices. As the cornerstone of the WINK Streaming ecosystem, WINK Forge provides robust video processing capabilities that enable organizations to deploy comprehensive video streaming solutions.

#### Key Capabilities

- **Multi-Protocol Support:** Ingest and deliver video using RTMP, RTSP, SRT, HLS, MPEG-DASH, WebRTC, and more
- **Enterprise Integration:** Native integration with Genetec, Milestone, and other major VMS platforms
- **Adaptive Streaming:** Automatic bitrate adaptation for optimal viewing experience
- **Security Features:** SSL/TLS encryption, access control lists, and authentication mechanisms
- **High Performance:** Optimized encoding supporting up to 8K resolution

> **Note:** WINK Forge is always paired with [WINK Media Router](/wink-router) for complete functionality. Together, they form the core platform for all WINK solutions.

[Learn more about WINK Forge features](https://wink.co/wink-forge) on our product page.

### 1.2 System Architecture

WINK Forge operates on a modular architecture designed for scalability and reliability:

| Component | Function | Description |
|-----------|----------|-------------|
| Input Manager | Video Ingestion | Handles multiple simultaneous video sources with automatic format detection |
| Transcoding Engine | Video Processing | Real-time encoding with H.264, H.265/HEVC, VP9, and AV1 support |
| Output Manager | Stream Distribution | Manages multiple output formats and destinations simultaneously |
| API Gateway | Control Interface | RESTful API for programmatic control and monitoring |
| Web Admin | Management UI | Browser-based configuration and monitoring interface |

### 1.3 Deployment Options

#### Hardware Appliance

Pre-configured hardware solutions optimized for video processing:

- 1U and 2U rackmount servers
- Enterprise-grade components
- Redundant power supplies and RAID storage
- Pre-installed and optimized firmware

#### Virtual Appliance (V-Forge)

Deploy on your existing virtualization infrastructure:

- VMware vSphere compatible
- Hyper-V support
- KVM/QEMU compatible
- OVF/OVA templates available
- Docker containers for microservices deployment
- Kubernetes orchestration support

#### Cloud Deployment (Cloud Forge)

Fully managed cloud instances:

- AWS EC2 optimized AMIs
- Azure marketplace images
- Google Cloud Platform support
- Auto-scaling capabilities

---

## 2. Getting Started

### 2.1 Web Interface Access

The WINK Forge web administration interface provides complete control over all system functions. Access methods vary by firmware version:

#### Firmware 8.x (Current)

- **Primary Access:** https://[IP-ADDRESS]:444
- **Alternative:** https://[IP-ADDRESS]:443/wink-admin/

#### Legacy Firmware

- **Primary Access:** https://[IP-ADDRESS]:7543
- **Alternative:** https://[IP-ADDRESS]/wink_main/

> **Security Note:** Always use HTTPS connections for admin access. HTTP connections are automatically redirected to HTTPS in production environments.

### 2.2 Default Credentials

#### Standard Installation

- **Username:** `admin`
- **Password:** `wink2024!`

#### Enterprise/Government Installation

- **Username:** `administrator`
- **Password:** `[Provided separately by WINK support]`

> **Important:** Change default credentials immediately after first login. Use strong passwords with minimum 12 characters including uppercase, lowercase, numbers, and symbols.

### 2.3 Security Configuration

#### SSL Certificate Management

WINK Forge supports multiple SSL certificate options:

1. **Self-Signed Certificates** (Default)
   - Automatically generated during installation
   - Suitable for internal/testing environments
   - Browser security warnings expected

2. **Commercial Certificates**
   - Upload PEM/PFX format certificates
   - Eliminates browser security warnings
   - Required for production deployments

3. **Let's Encrypt Integration**
   - Automatic certificate generation and renewal
   - Requires public domain name
   - Zero-cost solution for internet-facing deployments

#### Shared Key Authentication

Configure shared key authentication for API access:

```bash
# Generate secure shared key
openssl rand -hex 32

# Configure in Web Admin > Security > API Keys
```

---

## 3. Input Configuration

### 3.1 Supported Input Types

WINK Forge supports comprehensive input formats:

#### Network Streams

| Protocol | Description | Use Cases |
|----------|-------------|-----------|
| RTSP | Real Time Streaming Protocol | IP cameras, NVRs, encoders |
| RTMP | Real Time Messaging Protocol | Live streaming, broadcast |
| SRT | Secure Reliable Transport | Long-distance, unreliable networks |
| UDP | User Datagram Protocol | Broadcast reception, multicast |
| HTTP/HTTPS | Web-based streams | HLS inputs, file downloads |

#### File Inputs

- **Video Files:** MP4, AVI, MOV, MKV, FLV, WMV
- **Container Formats:** MPEG-TS, MPEG-PS, 3GP, ASF
- **Codec Support:** H.264, H.265/HEVC, VP8, VP9, AV1, MPEG-2

#### Hardware Inputs

- **SDI:** HD-SDI, 3G-SDI, 6G-SDI, 12G-SDI
- **HDMI:** HDMI 1.4, HDMI 2.0, HDMI 2.1
- **Composite:** NTSC/PAL analog video
- **USB:** UVC-compatible USB cameras

### 3.2 Input Settings

#### Basic Configuration

```json
{
  "input": {
    "type": "rtsp",
    "url": "rtsp://192.168.1.100:554/stream1",
    "username": "admin",
    "password": "password123",
    "timeout": 30000,
    "retry_attempts": 3
  }
}
```

#### Advanced Input Parameters

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `buffer_size` | Integer | Input buffer size (KB) | 1024 |
| `reconnect_delay` | Integer | Reconnection delay (ms) | 5000 |
| `max_analyze_duration` | Integer | Stream analysis time (ms) | 10000 |
| `probesize` | Integer | Probe buffer size (bytes) | 5000000 |

### 3.3 Advanced Input Options

#### Automatic Input Detection

WINK Forge can automatically detect and configure input streams:

```bash
# Enable auto-detection
curl -X POST "https://forge-ip:444/api/v2/inputs/detect" \
  -H "Authorization: Bearer {API_KEY}" \
  -d '{"scan_range": "192.168.1.1/24"}'
```

#### Multi-Input Aggregation

Configure multiple inputs for redundancy:

```json
{
  "input_group": {
    "primary": "rtsp://camera1:554/stream",
    "backup": "rtsp://camera2:554/stream",
    "failover_timeout": 10
  }
}
```

---

## 4. Output Configuration

### 4.1 Output Formats

WINK Forge supports extensive output format options:

#### Streaming Protocols

| Format | Description | Use Cases |
|--------|-------------|-----------|
| RTSP | Real-time streaming | VMS integration, IP cameras |
| RTMP | Live streaming | CDNs, streaming platforms |
| HLS | HTTP Live Streaming | Web players, mobile apps |
| MPEG-DASH | Dynamic Adaptive Streaming | Modern web browsers |
| WebRTC | Real-time communication | Ultra-low latency applications |

#### File Outputs

- **MP4:** Standard video container
- **MKV:** Matroska container  
- **AVI:** Audio Video Interleave
- **MOV:** QuickTime format
- **TS:** Transport Stream segments

### 4.2 Encoding Settings

#### Video Encoder Options

Configure video encoding parameters:

```json
{
  "video": {
    "codec": "h264",
    "profile": "high",
    "level": "4.1",
    "bitrate": "5000k",
    "fps": "30",
    "keyframe_interval": "2",
    "preset": "medium"
  }
}
```

#### Preset Options

| Preset | CPU Usage | Quality | Latency |
|--------|-----------|---------|---------|
| ultrafast | Lowest | Lowest | Lowest |
| superfast | Low | Low | Low |
| veryfast | Medium-Low | Medium-Low | Low |
| faster | Medium | Medium-Low | Medium |
| fast | Medium | Medium | Medium |
| medium | Medium-High | High | High |
| slow | High | Highest | High |
| slower | Highest | Highest | Highest |

#### Audio Configuration

Audio encoding settings:

```json
{
  "audio": {
    "codec": "aac",
    "bitrate": "128k",
    "sample_rate": "48000",
    "channels": "2"
  }
}
```

### 4.3 Adaptive Streaming

#### HLS Adaptive Bitrate

Create multiple quality levels for HLS:

```json
{
  "hls_variants": [
    {
      "name": "low",
      "video_bitrate": "800k",
      "resolution": "640x360",
      "audio_bitrate": "64k"
    },
    {
      "name": "medium", 
      "video_bitrate": "2500k",
      "resolution": "1280x720",
      "audio_bitrate": "128k"
    },
    {
      "name": "high",
      "video_bitrate": "5000k",
      "resolution": "1920x1080", 
      "audio_bitrate": "192k"
    }
  ]
}
```

---

## 5. Media Protocols

### 5.1 RTMP Configuration

#### RTMP Publishing

Configure RTMP output for CDN delivery:

```bash
# RTMP URL format
rtmp://cdn.example.com:1935/live/stream_key

# With authentication
rtmp://username:password@server.com/app/stream
```

#### RTMP Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `rtmp_live` | Live stream mode | "live" |
| `rtmp_buffer` | Output buffer (ms) | 3000 |
| `rtmp_timeout` | Connection timeout | 30000 |
| `rtmp_drop_threshold` | Frame drop threshold | 1000 |

### 5.2 SRT Configuration  

#### SRT Caller Mode

Connect to remote SRT listener:

```bash
# Basic SRT URL
srt://remote.server.com:9000

# With stream ID
srt://server.com:9000?streamid=stream1

# With passphrase
srt://server.com:9000?passphrase=mykey123
```

#### SRT Listener Mode

Accept incoming SRT connections:

```bash
# Listen on port 9000
srt://0.0.0.0:9000?mode=listener

# With passphrase protection
srt://0.0.0.0:9000?mode=listener&passphrase=secret
```

#### SRT Parameters

| Parameter | Description | Recommended |
|-----------|-------------|-------------|
| `latency` | Network latency (ms) | 200-2000 |
| `maxbw` | Max bandwidth limit | -1 (unlimited) |
| `pbkeylen` | Encryption key length | 32 |
| `passphrase` | Encryption passphrase | Required for security |

### 5.3 RTSP Configuration

#### RTSP Server Setup

WINK Forge can serve RTSP streams:

```bash
# RTSP URL format
rtsp://forge-server:554/stream/camera1

# With authentication
rtsp://user:pass@forge-server:554/stream/camera1
```

#### RTSP Parameters

Configure RTSP server settings:

```json
{
  "rtsp": {
    "port": 554,
    "authentication": true,
    "timeout": 60,
    "tcp_only": false,
    "rtp_port_range": "5000-5999"
  }
}
```

### 5.4 HLS/LL-HLS

#### Standard HLS Configuration

HTTP Live Streaming setup:

```json
{
  "hls": {
    "segment_duration": 6,
    "playlist_length": 5,
    "delete_threshold": 10,
    "hls_flags": "delete_segments"
  }
}
```

#### Low Latency HLS (LL-HLS)

Enable ultra-low latency streaming:

```json
{
  "ll_hls": {
    "segment_duration": 2,
    "part_duration": 0.5,
    "part_hold_back": 1.0,
    "segment_hold_back": 6.0
  }
}
```

### 5.5 MPEG-DASH

#### DASH Configuration

Dynamic Adaptive Streaming over HTTP:

```json
{
  "dash": {
    "segment_duration": 4,
    "window_size": 5,
    "use_template": true,
    "adaptation_sets": "id=0,streams=v id=1,streams=a"
  }
}
```

---

## 6. VMS Integration

### 6.1 Genetec Integration

#### SDK Integration

WINK Forge integrates directly with Genetec Security Center:

```xml
<!-- Genetec configuration -->
<genetec>
  <server_address>genetec.company.com</server_address>
  <directory_port>4500</directory_port>
  <username>wink_service</username>
  <password>secure_password</password>
  <certificate_path>/etc/ssl/genetec.pem</certificate_path>
</genetec>
```

#### Camera Discovery

Automatic discovery of Genetec cameras:

```bash
# API call for camera discovery
curl -X POST "https://forge:444/api/genetec/discover" \
  -H "Authorization: Bearer {API_KEY}" \
  -d '{"server": "genetec.local", "username": "admin"}'
```

### 6.2 Milestone Integration

#### Milestone XProtect Integration

Connect to Milestone VMS:

```xml
<milestone>
  <management_server>milestone.company.com</management_server>
  <username>integration_user</username>
  <password>milestone_pass</password>
  <basic_auth>true</basic_auth>
</milestone>
```

#### Stream Configuration

Configure Milestone camera streams:

```json
{
  "milestone_camera": {
    "guid": "12345678-1234-5678-9012-123456789abc",
    "stream_type": "live",
    "quality": "high",
    "fps": 30
  }
}
```

### 6.3 VMS to VMS Bridging

#### Bridge Configuration

Connect different VMS platforms:

```json
{
  "vms_bridge": {
    "source_vms": {
      "type": "genetec",
      "server": "genetec.local"
    },
    "destination_vms": {
      "type": "milestone", 
      "server": "milestone.local"
    },
    "transcoding": {
      "required": true,
      "target_codec": "h264"
    }
  }
}
```

---

## 7. API Reference

### 7.1 API Overview

WINK Forge provides a comprehensive XML-based API for remote control and automation. The API uses XML request/response format with authentication.

#### API Request Format

```xml
<wink_api user='username' pass='password' key='shared_key'>
    <req id='1' command='command_name'>payload</req>
</wink_api>
```

#### Response Format

```xml
<wink_api>
    <resp id="1" command="command_name" status="ok">response_data</resp>
</wink_api>
```

### 7.2 Stream Control

#### Start/Stop/Restart Streams

| Command | Description | Payload |
|---------|-------------|---------|
| start | Start transcoding | GUID or 'all' |
| stop | Stop transcoding | GUID or 'all' |
| restart | Restart transcoding | GUID or 'all' |

#### Example: Restart Stream

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='1' command='restart'>WF05-2657-1C04-7F1B-5EC0</req>
</wink_api>
```

#### Stream Status

Get GUID Status:

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='1' command='guidstatus'>WF05-1EDA-1C04-6E33-ABF9</req>
</wink_api>

Response values:
0 = Not publishing
1 = Publishing
2 = Blocked
3 = Unavailable
```

#### Block/Unblock Streams

| Command | Function | Usage |
|---------|----------|-------|
| blockguid | Block video output | Shows placeholder image |
| unblockguid | Restore video output | Resumes normal stream |
| blockip | Block by IP:Port | Block without GUID |

#### Input Management

Create/Update Input:

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='1' command='input_source'>
        <input title='Camera 1' 
               desc='Main entrance' 
               type='RTSP' 
               path='rtsp://192.168.1.100:554/stream1'
               enabled='1' />
    </req>
</wink_api>
```

#### Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| title | Yes (new) | Stream title |
| desc | Yes (new) | Description |
| path | Yes (new) | Source URI |
| type | Yes (new) | Protocol type |
| guid | Yes (update) | Existing GUID |
| fps | No | Frame rate |
| width | No | Video width |
| height | No | Video height |

### 7.3 User Management API

#### Create User

```xml
<wink_api user='admin' pass='adminpass'>
    <req id='1' command='createuser'>
        <name>John Smith</name>
        <username>jsmith</username>
        <password>SecurePass123!</password>
        <access>2</access>
    </req>
</wink_api>

Access Levels:
1 = Admin
2 = Operator  
3 = Viewer
4 = API Only
```

#### Change Password

```xml
<wink_api user='admin' pass='adminpass'>
    <req id='1' command='changepassword'>
        <username>jsmith</username>
        <password md5='false'>NewPass456!</password>
    </req>
</wink_api>
```

---

## 8. Advanced Features

### 8.1 One-Time Password (OTP) Authentication

OTP provides temporary access tokens for secure stream viewing, ideal for:

- Time-limited access to streams
- Integration with web applications  
- Secure sharing of live content

#### OTP Workflow

1. Application requests OTP token from WINK Media Router
2. Token generated with specified duration
3. Client uses token to access stream
4. Token expires after duration or single use

#### API Commands

Create OTP Token:

```bash
curl -d "apiuser=apiuser&apipass=apipass&action=create&duration=60&hash_type=alphanumeric&hash_length=32" \
     -X POST https://router.example.com/otp/api/

Response: 24814928371014572819abc123def456
```

#### OTP Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| duration | 60 | Token lifetime (minutes) |
| hash_type | alphanumeric | alpha, numeric, alphanumeric |
| hash_length | 32 | Token length (max 128) |
| expiry_url | None | Redirect on expiry |

#### Using OTP Tokens

```bash
# Append token to stream URL
https://forge.example.com/hls/stream1/playlist.m3u8?token=24814928371014572819abc123def456
```

### 8.2 Watermarking

#### Image Watermarks

Add logos or branding to video streams:

1. Upload watermark image (PNG with transparency recommended)
2. Configure in output settings:
   - **Watermark File:** Select uploaded image
   - **Position:** topleft, topright, bottomleft, bottomright  
   - **X-Pad:** Horizontal offset in pixels
   - **Y-Pad:** Vertical offset in pixels
   - **Opacity:** 0.0 (transparent) to 1.0 (opaque)

#### Text Overlays

Add dynamic text to streams:

| Variable | Output | Example |
|----------|--------|---------|
| %TIME% | Current time | 14:35:22 |
| %DATE% | Current date | 2025-08-02 |
| %TITLE% | Stream title | Main Entrance |
| %FPS% | Current FPS | 29.97 fps |

#### Timestamp Formats

```bash
# Predefined formats
YYYY-MM-DD HH:MM:SS
MM/DD/YYYY HH:MM:SS
DD/MM/YYYY HH:MM:SS

# Custom format (strftime)
%Y-%m-%d %I:%M:%S %p
```

### 8.3 PTZ Control

#### PTZ Passthrough

WINK Forge can relay PTZ commands between VMS systems and cameras:

| Protocol | Support | Features |
|----------|---------|----------|
| ONVIF | Full | Pan, Tilt, Zoom, Presets |
| VAPIX | Full | Axis camera control |
| Pelco-D | Basic | RS-485 control |
| VISCA | Basic | Sony cameras |

#### PTZ Configuration

1. Enable PTZ in input settings
2. Configure control protocol
3. Map PTZ URLs for passthrough
4. Test with VMS PTZ controls

> **PTZ Latency:** Expect 100-500ms latency for PTZ commands depending on network conditions and protocol.

### 8.4 Advanced Auto-Detection

WINK Forge's intelligent auto-detection system eliminates manual configuration for most streams:

#### Auto-Detection Process

1. **Protocol Analysis**
   - URL scheme detection (rtsp://, rtmp://, srt://, etc.)
   - Port scanning for common protocols
   - Protocol negotiation and handshake

2. **Stream Analysis**
   - SDP parsing for RTSP streams
   - Metadata extraction from RTMP
   - Container format identification

3. **Codec Detection**
   - H.264 profile and level detection
   - H.265 tier and profile identification  
   - Audio codec and channel configuration

4. **Parameter Optimization**
   - Buffer size based on bitrate
   - Keyframe interval detection
   - Network transport optimization

#### Supported Auto-Detection Scenarios

| Source Type | Auto-Detected Parameters | Success Rate |
|-------------|--------------------------|---------------|
| IP Cameras | Protocol, resolution, codec, FPS | 98% |
| Streaming Servers | All parameters + audio config | 95% |
| VMS Systems | Authentication method, streams | 90% |
| CDN Sources | Adaptive bitrate ladder | 85% |

#### Manual Override Options

When auto-detection needs assistance:

```bash
# Force specific codec detection
advanced_options="analyzeduration=5000000"

# Override detected parameters
force_codec="h265"
force_fps="30"
force_resolution="1920x1080"

# Custom probe size for difficult streams
probesize="10M"
```

> **Auto-Detection Tips:**
> - Allow 5-10 seconds for full analysis
> - Check "Auto-Detection Log" for details
> - Use manual mode for non-standard streams
> - Save successful configs as templates

---

## 9. Monitoring & Analytics

### 9.1 Real-time Dashboard

WINK Forge provides comprehensive monitoring through its web interface dashboard:

#### Dashboard Overview

- **Active Streams:** Live count and status of all streams
- **System Resources:** CPU, memory, and network utilization
- **Bandwidth Usage:** Input/output bandwidth graphs
- **Error Log:** Recent errors and warnings
- **Stream Health:** Color-coded status indicators

#### Stream Status Indicators

| Status | Color | Meaning |
|--------|-------|---------|
| Publishing | Green | Stream active and healthy |
| Starting | Yellow | Initialization in progress |
| Reconnecting | Orange | Temporary connection loss |
| Failed | Red | Stream error, check logs |
| Blocked | Gray | Administratively disabled |

### 9.2 Stream Statistics

#### Per-Stream Metrics

Click on any stream to view detailed statistics:

| Metric | Description | Healthy Range |
|--------|-------------|---------------|
| Input Bitrate | Incoming data rate | Within 10% of source |
| Output Bitrate | Encoded output rate | 90-110% of target |
| Frame Rate | Actual FPS | ±1 FPS of target |
| Dropped Frames | Frames not processed | <0.1% |
| Latency | End-to-end delay | Protocol dependent |
| Packet Loss | Network packets lost | <0.05% |

#### Export Statistics

Export historical data for analysis:

- **CSV Export:** Raw metrics for spreadsheet analysis
- **JSON API:** Programmatic access to statistics
- **SNMP:** Integration with network monitoring systems  
- **Prometheus:** Metrics endpoint for Grafana dashboards

### 9.3 Alert Configuration

#### Alert Types

Configure automatic notifications for critical events:

| Alert Type | Trigger | Default Action |
|------------|---------|----------------|
| Stream Failed | Input connection lost | Email + Log |
| High CPU | CPU > 85% for 5 min | Email + SNMP |
| Storage Full | Disk > 90% | Email + API call |
| License Limit | 90% streams used | Email admin |
| Network Issue | Packet loss > 1% | Log + SNMP |

#### Alert Delivery Methods

1. **Email:** SMTP configuration in Settings
2. **Webhook:** HTTP POST to your endpoint
3. **SNMP Trap:** To network monitoring system
4. **Syslog:** To centralized logging
5. **SMS:** Via configured gateway (optional)

> **Alert Best Practices:**
> - Set up escalation for critical alerts
> - Use hysteresis to prevent alert storms  
> - Test alert delivery during maintenance windows
> - Document response procedures for each alert type

---

## 10. Troubleshooting

### Common Issues and Solutions

#### Stream Not Starting

**Quick Checks:**

1. Verify source is accessible (ping/telnet test)
2. Check credentials are correct
3. Confirm firewall allows connection
4. Test source with VLC player
5. Review error logs for specific messages

| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Status: "Failed" | Invalid source URL | Verify URL with VLC player |
| Status: "Timeout" | Network connectivity | Check firewall, ping source |
| Status: "Auth Failed" | Wrong credentials | Verify username/password |
| No status change | Licensing issue | Check license limits |

#### Poor Video Quality

- **Pixelation:** Increase bitrate or reduce resolution
- **Stuttering:** Check CPU usage, reduce FPS
- **Color issues:** Verify color space settings
- **Artifacts:** Adjust encoder preset (faster = lower quality)

#### High CPU Usage

1. Check transcoding load:
   ```bash
   top -n 1 | grep ffmpeg
   ```

2. Reduce quality settings:
   - Lower resolution
   - Decrease FPS  
   - Use optimized encoder settings

3. Optimize encoder settings:
   ```
   Preset: ultrafast (lowest CPU)
   Profile: baseline (simpler encoding)
   Codec: Consider H.265 for better compression
   ```

#### Network Diagnostics

Built-in Tools (access via Tools menu in web interface):

| Tool | Purpose | Usage |
|------|---------|--------|
| Ping Test | Connectivity check | Test camera reachability |
| MTR | Route analysis | Diagnose network path |
| TCPDump | Packet capture | Debug protocol issues |
| RTSP Probe | Stream testing | Verify RTSP sources |

#### Log Analysis

Check system logs for errors:

```bash
# Web interface
Tools → Logs → System Logs

# Command line
tail -f /var/log/winkforge/stream.log
grep ERROR /var/log/winkforge/system.log
```

#### H.265 Specific Issues

| Issue | Cause | Solution |
|-------|--------|----------|
| H.265 not playing | Browser incompatibility | Add H.264 output for web viewers |
| High CPU with H.265 | Complex encoding | Use faster preset or reduce resolution |
| H.265 → H.264 quality loss | Bitrate too low | Increase H.264 bitrate by 40% |
| HDR lost in output | Profile mismatch | Use Main10 profile for HDR |

#### SRT Connection Issues

| Error | Meaning | Fix |
|-------|---------|-----|
| Connection timeout | Firewall blocking | Open UDP port (default 9000) |
| Authentication failed | Wrong passphrase | Verify passphrase matches |
| High latency warning | Network congestion | Increase latency parameter |
| Stream ID rejected | Invalid stream ID | Check streamid format |

#### Common Log Messages

| Message | Meaning | Action |
|---------|---------|--------|
| Connection refused | Source unreachable | Check IP and port |
| Invalid data found | Codec mismatch | Verify source format |
| Buffer underflow | Network congestion | Increase buffer size |
| License exceeded | Stream limit reached | Upgrade license |

---

## Appendices

### A. SSL Certificate Management

#### Exporting Certificates (Windows)

1. Open MMC (Win+R, type `mmc`)
2. Add Certificates snap-in
3. Navigate to Personal → Certificates
4. Right-click certificate → All Tasks → Export
5. Export with private key (PFX format)
6. Set strong password
7. Save PFX file

#### Uploading to WINK Forge

1. Navigate to Settings → System Options → SSL Certificate
2. Choose "Upload Certificate"
3. Select PFX file
4. Enter PFX password  
5. Apply and restart services

#### Let's Encrypt Integration

```bash
# Requirements
- Public domain name
- Port 80 accessible from internet
- DNS pointing to WINK Forge

# Automatic renewal
Certificates renew automatically every 60 days
```

### B. Network Configuration

#### Firewall Ports

| Port | Protocol | Direction | Purpose |
|------|----------|-----------|---------|
| 443 | TCP | Inbound | HTTPS (Web/API) |
| 444 | TCP | Inbound | Admin HTTPS |
| 554 | TCP | In/Out | RTSP |
| 1935 | TCP | In/Out | RTMP |
| 8080-8090 | TCP | Inbound | HTTP Streams |
| 5000-5999 | UDP | In/Out | RTP/RTCP |

#### Network Bonding

Configure multiple network interfaces for redundancy:

| Mode | Description | Use Case |
|------|-------------|----------|
| Active-Backup | Failover only | High availability |
| Balance-RR | Round-robin | Load distribution |
| 802.3ad | LACP | Switch support required |
| Balance-ALB | Adaptive load | No switch config needed |

### C. Performance Tuning

#### System Requirements

| Workload | CPU | RAM | Storage |
|----------|-----|-----|---------|
| 10 HD streams | 8 cores | 16 GB | 500 GB SSD |
| 50 HD streams | 16 cores | 32 GB | 1 TB SSD |
| 100+ HD streams | 32 cores | 64 GB | 2 TB NVMe |
| 4K streaming | Additional cores | +16 GB | +50% storage |

#### Performance Optimization

WINK Forge uses intelligent resource management:

- **Adaptive Threading:** Automatically scales threads based on workload
- **Smart Codec Selection:** Chooses optimal codec for each stream
- **Memory Pool Management:** Efficient buffer allocation
- **CPU Affinity:** Pins processes to specific cores for consistency

#### System Optimization

```bash
# Increase file descriptors
ulimit -n 65536

# Optimize network buffers
sysctl -w net.core.rmem_max=134217728
sysctl -w net.core.wmem_max=134217728

# CPU governor for performance
cpupower frequency-set -g performance
```

### D. Codec Comparison Guide

#### Video Codec Selection

| Use Case | Recommended Codec | Reason |
|----------|-------------------|--------|
| Traffic Cameras | H.264 | Proven reliability over cellular/wireless |
| Field Deployments | H.264 | Handles packet loss gracefully |
| Web Streaming | H.264 | Universal browser support |  
| Low Latency | H.264 (Baseline) | Minimal processing delay |
| Wireless/Cellular | H.264 | Error resilience + compatibility |
| Archive Only | H.265 (maybe) | Only for controlled storage |

#### Why H.264 Dominates Field Deployments

Real-world experience shows H.264's superiority for field cameras:

- **Error Resilience:** H.264 handles packet loss without catastrophic failure
- **Decoder Availability:** Every device can decode H.264 efficiently
- **Latency:** Simpler processing = lower end-to-end delay  
- **Bandwidth Adaptation:** Graceful degradation on congested networks
- **Field Testing:** 15+ years of proven reliability

> **Real-World Example:** A traffic camera using H.265 over LTE may save 30% bandwidth but will experience complete frame loss during network congestion. The same camera with H.264 will show minor artifacts but remain viewable. The bandwidth "savings" are meaningless if the stream fails.

#### Codec Feature Matrix

| Feature | H.264 | H.265 | VP9 | AV1 |
|---------|-------|-------|-----|-----|
| Max Resolution | 4K | 8K | 8K | 8K |
| 10-bit Support | Limited | Yes | Yes | Yes |
| HDR Support | No | Yes | Yes | Yes |
| Encoding Speed | Fast | 2-4x Slower | 5x Slower | 10x Slower |
| Error Recovery | Excellent | Poor | Poor | Poor |
| Wireless Performance | Excellent | Problematic | Problematic | Unsuitable |
| Patent Status | Licensed | Licensed | Open | Open |

### E. Common Transcoding Workflows

#### Workflow 1: Traffic Camera to Multi-Bitrate HLS

Standard configuration for field cameras (ALWAYS use H.264):

```
Input:
  Type: RTSP
  Path: rtsp://camera.local:554/stream1
  Auto-detect: Enabled

Outputs:
  1. HLS Low (360p)
     Resolution: 640x360
     Bitrate: 800 Kbps
     Path: /hls/camera1_low
  
  2. HLS Medium (720p)
     Resolution: 1280x720
     Bitrate: 2500 Kbps
     Path: /hls/camera1_med
  
  3. HLS High (1080p)
     Resolution: 1920x1080
     Bitrate: 5000 Kbps
     Path: /hls/camera1_high

Master Playlist: /hls/camera1/master.m3u8

CODEC NOTE: All outputs use H.264 - DO NOT use H.265 for field cameras!
```

#### Workflow 2: H.265 to H.264 Conversion

Fix problematic H.265 field cameras by converting to reliable H.264:

```
Input:
  Type: RTSP
  Path: rtsp://4k-camera.local/h265stream
  Codec: H.265 (auto-detected)

Output:
  Format: RTMP
  Path: rtmp://cdn.example.com/live/camera
  Codec: H.264
  Profile: High
  Bitrate: 8000 Kbps (40% higher than source)
  Preset: fast (balance quality/CPU)
  
NOTE: This is fixing a deployment mistake. New cameras should use H.264 from the start.
```

#### Workflow 3: SRT Remote Production

Reliable streaming over internet:

```
Input:
  Type: SRT (Listener)
  Path: srt://0.0.0.0:9000?mode=listener
  Passphrase: ProductionKey2025
  Latency: 500ms

Outputs:
  1. Program Feed
     Format: RTSP
     Path: rtsp://0.0.0.0:554/program
     
  2. Backup Stream
     Format: SRT (Caller)
     Path: srt://backup.site:9000?streamid=main
     
  3. Web Preview
     Format: HLS
     Path: /hls/preview
     Bitrate: 1000 Kbps
```

#### Workflow 4: VMS Bridge with Transcoding

Connect incompatible VMS systems:

```
Input:
  Type: Genetec
  Source: Via SDK integration
  Original: H.265 + G.711 audio

Outputs:
  1. Milestone Compatible
     Format: RTSP
     Codec: H.264 Baseline
     Audio: AAC
     Resolution: Maintain original
     
  2. Archive Copy
     Format: File (MP4)
     Codec: H.265 (preserve)
     Segment: 1 hour files
```

#### Workflow 5: Bandwidth Optimization

Reduce bandwidth for remote sites:

```
Input:
  Type: RTSP
  Original: 4K @ 15 Mbps

Outputs:
  1. Remote Site A (Limited bandwidth)
     Resolution: 1280x720
     Bitrate: 1500 Kbps
     FPS: 15
     Codec: H.265 (better compression)
     
  2. Remote Site B (Very limited)
     Resolution: 640x360
     Bitrate: 500 Kbps
     FPS: 10
     Codec: H.264  # Still H.264 for reliability!
     
  3. Local Recording
     Resolution: Original (4K)
     Codec: H.264  # H.265 only if on fiber/local network
     Quality: High
     
IMPORTANT: Even for "bandwidth optimization", H.264 is preferred.
The minor savings from H.265 don't justify the reliability risks.
```

#### Best Practices Summary

- **Default to H.264 for ALL field deployments** - Reliability trumps minor bandwidth savings
- **H.264 is mandatory for wireless/cellular cameras** - Other codecs WILL fail under packet loss
- **Test with actual network conditions** - Lab tests don't reflect field reality
- **Bandwidth is cheap, downtime is expensive** - Use H.264 with adequate bitrate
- **Only consider H.265 for:** Controlled environments, fiber connections, archival storage
- **Monitor packet loss** - Switch to H.264 if loss exceeds 0.1%

> **Field Deployment Golden Rule:** If you're unsure which codec to use, choose H.264. The minimal bandwidth savings from newer codecs are meaningless when the stream fails. H.264's error resilience and universal compatibility make it the only sensible choice for real-world deployments.

---

**© 2025 WINK Streaming, Inc. All rights reserved.**

This documentation provides comprehensive guidance for deploying and managing WINK Forge in enterprise environments. For additional support, contact WINK Streaming technical support.