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

*[Content continues with remaining sections...]*

---

This documentation provides comprehensive guidance for deploying and managing WINK Forge in enterprise environments. For additional support, contact WINK Streaming technical support.

**© 2025 WINK Streaming, Inc. All rights reserved.**