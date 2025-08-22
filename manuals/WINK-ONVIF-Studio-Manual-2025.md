---
title: "WINK ONVIF Studio - Professional Camera Management Manual"
description: "Complete guide for WINK ONVIF Studio - professional ONVIF camera management software. Learn camera discovery, PTZ control, live streaming setup, and advanced features."
keywords: ["ONVIF", "camera management", "IP camera", "PTZ control", "video surveillance", "camera discovery", "RTSP streaming", "security camera", "camera configuration", "network camera"]
category: "manuals"
product: "WINK ONVIF Studio"
version: "1.0 - 2025"
last_updated: "August 2025"
author: "WINK Streaming, Inc."
---

# WINK ONVIF Studio

## Professional Camera Management Manual

**Complete Guide for Security Professionals and System Integrators**

**Version 1.0 | 2025**  
**WINK Streaming**

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Getting Started](#2-getting-started)
   - 2.1 [System Requirements](#21-system-requirements)
   - 2.2 [Installation](#22-installation)
   - 2.3 [First Launch](#23-first-launch)
3. [User Interface Overview](#3-user-interface-overview)
   - 3.1 [Main Window](#31-main-window)
   - 3.2 [Camera List Panel](#32-camera-list-panel)
   - 3.3 [Tab Structure](#33-tab-structure)
4. [Camera Discovery](#4-camera-discovery)
   - 4.1 [Automatic Discovery](#41-automatic-discovery)
   - 4.2 [Manual Discovery Methods](#42-manual-discovery-methods)
   - 4.3 [Understanding ONVIF Discovery](#43-understanding-onvif-discovery)
5. [Connecting to Cameras](#5-connecting-to-cameras)
   - 5.1 [Authentication Methods](#51-authentication-methods)
   - 5.2 [Connection Process](#52-connection-process)
   - 5.3 [Troubleshooting Connections](#53-troubleshooting-connections)
6. [Live Video Streaming](#6-live-video-streaming)
   - 6.1 [Setting Up Streams](#61-setting-up-streams)
   - 6.2 [PTZ Controls](#62-ptz-controls)
   - 6.3 [Snapshots and Recording](#63-snapshots-and-recording)
7. [Camera Configuration](#7-camera-configuration)
   - 7.1 [Network Settings](#71-network-settings)
   - 7.2 [Video Settings](#72-video-settings)
   - 7.3 [Image Settings](#73-image-settings)
   - 7.4 [Date & Time Configuration](#74-date--time-configuration)
8. [Diagnostics & Testing](#8-diagnostics--testing)
   - 8.1 [Quick Test](#81-quick-test)
   - 8.2 [Detailed Diagnostics](#82-detailed-diagnostics)
   - 8.3 [Batch Testing](#83-batch-testing)
9. [Advanced Features](#9-advanced-features)
   - 9.1 [Batch Operations](#91-batch-operations)
   - 9.2 [User Management](#92-user-management)
   - 9.3 [Event Monitoring](#93-event-monitoring)
10. [Troubleshooting Guide](#10-troubleshooting-guide)
11. [Appendix](#11-appendix)
    - 11.1 [Keyboard Shortcuts](#111-keyboard-shortcuts)
    - 11.2 [Common ONVIF Ports](#112-common-onvif-ports)
    - 11.3 [Supported Features Matrix](#113-supported-features-matrix)

---

## 1. Introduction

WINK ONVIF Studio is a comprehensive, professional-grade camera management tool designed for security professionals, system integrators, and IT administrators. This software provides complete control over ONVIF-compliant IP cameras, including discovery, configuration, streaming, and diagnostics.

### About WINK ONVIF Studio

WINK ONVIF Studio represents the culmination of years of experience in video surveillance and camera integration. As a completely free tool, it democratizes access to professional camera management capabilities that were previously only available in expensive enterprise solutions.

### Key Features

#### Universal Compatibility
Supports all ONVIF-compliant cameras from any manufacturer, with automatic detection of authentication methods and protocol versions.

#### Professional Tools
Advanced diagnostics, batch operations, and comprehensive configuration capabilities designed for professional deployments.

#### Cross-Platform Support
Native applications for Windows, macOS, and Linux ensure you can manage cameras from any workstation.

### Target Audience

- **Security Integrators**: Quickly configure and test multiple cameras during installation
- **IT Administrators**: Manage corporate surveillance infrastructure efficiently
- **Security Professionals**: Monitor and maintain camera systems
- **System Engineers**: Diagnose and troubleshoot camera connectivity issues

### Document Overview

This manual provides comprehensive guidance on using WINK ONVIF Studio effectively. Each chapter builds upon previous concepts, taking you from basic installation through advanced features and troubleshooting.

> **Note:** This manual covers WINK ONVIF Studio version 1.0. Features may vary in different versions. Check www.wink.co for the latest documentation.

---

## 2. Getting Started

### 2.1 System Requirements

#### Minimum Requirements

| Component | Windows | macOS | Linux |
|-----------|---------|-------|-------|
| Operating System | Windows 10 64-bit | macOS 10.14 (Mojave) | Ubuntu 20.04 LTS |
| Processor | Intel Core i3 or equivalent | Intel Core i3 or equivalent | Intel Core i3 or equivalent |
| Memory | 4 GB RAM | 4 GB RAM | 4 GB RAM |
| Storage | 500 MB available space | 500 MB available space | 500 MB available space |
| Network | Gigabit Ethernet recommended | Gigabit Ethernet recommended | Gigabit Ethernet recommended |

#### Recommended Requirements

- Intel Core i5 or better processor
- 8 GB RAM or more
- SSD storage for better performance
- Multiple network interfaces for complex deployments
- 4K display for managing multiple camera streams

### 2.2 Installation

#### Windows Installation

1. Download the WINK ONVIF Studio installer from [www.wink.co/wink-onvif-studio](https://wink.co/wink-onvif-studio)
2. Run the installer as Administrator
3. Follow the installation wizard:
   - Accept the license agreement
   - Choose installation directory (default recommended)
   - Select Start Menu folder
   - Create desktop shortcut (optional)
4. Click "Install" to begin installation
5. Launch WINK ONVIF Studio when installation completes

#### macOS Installation

1. Download the appropriate version:
   - Intel Macs: WINK-ONVIF-Studio-Intel.dmg
   - Apple Silicon Macs: WINK-ONVIF-Studio-ARM.dmg
2. Open the .dmg file
3. Drag WINK ONVIF Studio to the Applications folder
4. Launch the application from Applications
5. If prompted by Gatekeeper, go to System Preferences > Security & Privacy and click "Open Anyway"

#### Linux Installation

1. Download the appropriate package:
   - Debian/Ubuntu: wink-onvif-studio_1.0_amd64.deb
   - Red Hat/CentOS: wink-onvif-studio-1.0.x86_64.rpm
   - Generic: wink-onvif-studio-1.0-linux-x64.tar.gz

2. Install using your package manager:

   **Debian/Ubuntu:**
   ```bash
   sudo dpkg -i wink-onvif-studio_1.0_amd64.deb
   sudo apt-get install -f  # Fix dependencies if needed
   ```

   **Red Hat/CentOS:**
   ```bash
   sudo rpm -i wink-onvif-studio-1.0.x86_64.rpm
   ```

   **Generic:**
   ```bash
   tar -xzf wink-onvif-studio-1.0-linux-x64.tar.gz
   cd wink-onvif-studio
   ./install.sh
   ```

### 2.3 First Launch

#### Initial Setup

1. Launch WINK ONVIF Studio
2. The application will automatically scan for cameras on the local network
3. Grant network access permissions when prompted by your firewall
4. Review the interface layout and available features

#### Network Configuration

WINK ONVIF Studio automatically detects network interfaces. For complex network configurations:

1. Go to **Settings** > **Network**
2. Select preferred network interfaces for camera discovery
3. Configure multicast settings if needed
4. Set timeout values for slow networks

---

## 3. User Interface Overview

### 3.1 Main Window

The main window consists of several key components:

- **Menu Bar**: Access to all application functions
- **Toolbar**: Quick access to common operations
- **Camera List Panel**: Displays discovered cameras
- **Main Content Area**: Tabbed interface for different functions
- **Status Bar**: Network and operation status

### 3.2 Camera List Panel

The camera list panel shows all discovered cameras with:

- **Camera Icon**: Visual indicator of camera type and status
- **IP Address**: Camera network address
- **Manufacturer**: Detected camera brand
- **Model**: Camera model information
- **Status**: Connection and operational status

#### Status Indicators

| Icon | Status | Description |
|------|--------|-------------|
| 🟢 | Connected | Camera is accessible and responding |
| 🟡 | Discovering | Camera discovery in progress |
| 🔴 | Disconnected | Camera not accessible |
| 🔒 | Authentication Required | Camera requires login credentials |

### 3.3 Tab Structure

The main content area is organized into functional tabs:

- **Discovery**: Find and add cameras
- **Live View**: Stream video from cameras
- **Configuration**: Modify camera settings
- **Diagnostics**: Test and troubleshoot cameras
- **Batch Operations**: Perform bulk operations

---

## 4. Camera Discovery

### 4.1 Automatic Discovery

WINK ONVIF Studio automatically discovers ONVIF-compliant cameras using several methods:

#### WS-Discovery
The primary method uses WS-Discovery multicast announcements. Cameras broadcast their presence and services automatically.

#### Network Scanning
Systematic scanning of IP ranges to locate cameras that don't support WS-Discovery.

#### UPnP Discovery
Universal Plug and Play detection for consumer and prosumer cameras.

### 4.2 Manual Discovery Methods

#### Direct IP Entry

1. Click the **Add Camera** button in the Discovery tab
2. Enter the camera's IP address
3. Specify the ONVIF port (default: 80 or 8080)
4. Click **Discover** to establish connection

#### IP Range Scanning

1. Select **Discovery** > **Scan IP Range**
2. Enter the network range (e.g., 192.168.1.1-192.168.1.254)
3. Configure scan parameters:
   - **Port Range**: Common ONVIF ports (80, 8080, 8081)
   - **Timeout**: Response timeout in seconds
   - **Threads**: Concurrent scan threads
4. Start the scan process

### 4.3 Understanding ONVIF Discovery

#### ONVIF Profiles

- **Profile S**: Streaming and media configuration
- **Profile G**: Video recording and playback
- **Profile T**: Advanced video streaming and imaging
- **Profile C**: Access control integration
- **Profile A**: Analytics and metadata

#### Discovery Process

1. **Multicast Query**: Send WS-Discovery probe
2. **Response Analysis**: Parse camera capabilities
3. **Service Enumeration**: Discover available services
4. **Capability Detection**: Determine supported features
5. **Authentication Check**: Verify access requirements

---

## 5. Connecting to Cameras

### 5.1 Authentication Methods

#### No Authentication
Some cameras allow anonymous access for basic functions.

#### Username/Password
Standard HTTP authentication using camera credentials.

#### Digest Authentication
Secure authentication method preferred by most modern cameras.

#### Windows Authentication
Integrated Windows authentication for domain-joined cameras.

### 5.2 Connection Process

1. **Select Camera**: Choose camera from the discovered list
2. **Enter Credentials**: Provide username and password if required
3. **Test Connection**: Verify authentication and accessibility
4. **Save Credentials**: Optionally store credentials for future use
5. **Connect**: Establish full connection to the camera

### 5.3 Troubleshooting Connections

#### Common Connection Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| Camera not discovered | Network configuration | Check network connectivity and ONVIF port |
| Authentication failed | Wrong credentials | Verify username/password with camera admin |
| Timeout errors | Network latency | Increase timeout values in settings |
| Service unavailable | Camera overloaded | Reduce concurrent connections |

#### Network Troubleshooting

```bash
# Test network connectivity
ping [camera_ip]

# Check ONVIF port accessibility
telnet [camera_ip] [onvif_port]

# Verify ONVIF service
curl -s http://[camera_ip]:[onvif_port]/onvif/device_service
```

---

## 6. Live Video Streaming

### 6.1 Setting Up Streams

#### Stream Configuration

1. Select camera from the camera list
2. Navigate to the **Live View** tab
3. Choose from available stream profiles:
   - **Main Stream**: High resolution (1080p, 4K)
   - **Sub Stream**: Lower resolution for preview
   - **Third Stream**: Mobile/remote viewing

#### Stream Parameters

| Parameter | Description | Typical Values |
|-----------|-------------|----------------|
| Resolution | Video dimensions | 1920x1080, 1280x720, 640x480 |
| Frame Rate | Frames per second | 30, 25, 15, 10, 5 fps |
| Bitrate | Data rate | 2-8 Mbps (main), 512-1024 Kbps (sub) |
| Codec | Video compression | H.264, H.265, MJPEG |

### 6.2 PTZ Controls

#### Pan-Tilt-Zoom Operations

- **Directional Pad**: Arrow keys or on-screen controls
- **Zoom Control**: Mouse wheel or +/- buttons
- **Focus Control**: Auto-focus or manual adjustment
- **Speed Control**: Variable movement speed

#### Preset Positions

1. **Set Preset**: Position camera and click "Set Preset"
2. **Go to Preset**: Select preset from dropdown and click "Go"
3. **Delete Preset**: Remove unwanted preset positions

#### Advanced PTZ Features

- **Auto Pan**: Automatic scanning between preset positions
- **Pattern Recording**: Record and replay movement patterns
- **Home Position**: Return to default position
- **PTZ Tours**: Programmed sequences of preset positions

### 6.3 Snapshots and Recording

#### Snapshot Capture

- **Single Snapshot**: Click snapshot button or press Space
- **Continuous Snapshots**: Automatic capture at set intervals
- **High-Resolution Snapshots**: Full-resolution image capture

#### Local Recording

- **Manual Recording**: Start/stop recording manually
- **Scheduled Recording**: Time-based recording schedules
- **Event-Triggered Recording**: Motion or alarm-based recording

---

## 7. Camera Configuration

### 7.1 Network Settings

#### IP Configuration

- **Static IP**: Manually assigned IP address
- **DHCP**: Automatic IP assignment
- **DNS Settings**: Primary and secondary DNS servers
- **Gateway Configuration**: Network gateway settings

#### Port Configuration

- **HTTP Port**: Web interface access
- **RTSP Port**: Video streaming port
- **ONVIF Port**: Device management port

### 7.2 Video Settings

#### Encoding Parameters

| Setting | Description | Range |
|---------|-------------|-------|
| Resolution | Video dimensions | 320x240 to 4096x2160 |
| Frame Rate | Frames per second | 1-60 fps |
| Bitrate Control | Rate control method | CBR, VBR, ABR |
| GOP Size | Group of Pictures | 1-300 frames |
| Quality | Compression quality | 1-100 |

#### Stream Profiles

Configure multiple stream profiles for different use cases:

- **Profile 1**: Main stream for recording
- **Profile 2**: Sub stream for live viewing
- **Profile 3**: Mobile stream for remote access

### 7.3 Image Settings

#### Image Enhancement

- **Brightness**: Overall image brightness (-100 to +100)
- **Contrast**: Image contrast ratio (-100 to +100)
- **Saturation**: Color saturation (-100 to +100)
- **Hue**: Color hue adjustment (-180 to +180)
- **Sharpness**: Edge enhancement (-100 to +100)

#### Exposure Control

- **Exposure Mode**: Auto, Manual, Shutter Priority, Iris Priority
- **Exposure Time**: Shutter speed (1/10000 to 1 second)
- **Gain Control**: Sensor gain (0-100)
- **Iris Control**: Aperture setting (F1.0 to F22)

### 7.4 Date & Time Configuration

#### Time Settings

- **Time Zone**: Local time zone selection
- **Date Format**: Display format for date
- **Time Format**: 12-hour or 24-hour format
- **Daylight Saving**: Automatic DST adjustment

#### NTP Synchronization

```bash
# Configure NTP server
NTP Server: pool.ntp.org
Update Interval: 24 hours
Time Zone: America/New_York
```

---

## 8. Diagnostics & Testing

### 8.1 Quick Test

The Quick Test feature provides rapid assessment of camera functionality:

- **Connectivity Test**: Verify network connection
- **Authentication Test**: Validate credentials
- **Stream Test**: Check video stream availability
- **PTZ Test**: Test pan-tilt-zoom operations (if supported)

### 8.2 Detailed Diagnostics

#### Network Diagnostics

- **Ping Test**: Network connectivity verification
- **Port Scan**: Service availability check
- **Bandwidth Test**: Network throughput measurement
- **Latency Analysis**: Response time evaluation

#### Camera Health Check

- **Temperature Monitoring**: Hardware temperature sensors
- **Power Status**: Power supply status
- **Storage Status**: SD card or NAS storage health
- **Firmware Version**: Current firmware information

### 8.3 Batch Testing

Perform diagnostics on multiple cameras simultaneously:

1. Select cameras for batch testing
2. Choose diagnostic tests to perform
3. Configure test parameters
4. Execute batch diagnostics
5. Review comprehensive results report

---

## 9. Advanced Features

### 9.1 Batch Operations

#### Configuration Synchronization

Apply settings to multiple cameras:

- **Network Configuration**: IP ranges, DNS, gateway
- **Video Settings**: Resolution, frame rate, bitrate
- **User Management**: Create/modify user accounts
- **Time Synchronization**: NTP server configuration

#### Firmware Updates

- **Update Check**: Verify current firmware versions
- **Batch Download**: Download firmware for multiple cameras
- **Scheduled Updates**: Plan firmware update deployment
- **Rollback Support**: Revert to previous firmware version

### 9.2 User Management

#### User Account Operations

- **Create Users**: Add new user accounts
- **Modify Permissions**: Adjust user access levels
- **Password Management**: Change or reset passwords
- **Group Management**: Organize users into groups

#### Access Control

- **Role-Based Access**: Admin, Operator, Viewer roles
- **Feature Restrictions**: Limit access to specific functions
- **Time-Based Access**: Schedule user access windows
- **IP Restrictions**: Limit access by IP address range

### 9.3 Event Monitoring

#### Event Types

- **Motion Detection**: Movement in video frame
- **Tampering Alerts**: Camera obstruction or movement
- **Network Events**: Connection status changes
- **System Events**: Hardware or software issues

#### Event Handling

- **Real-time Notifications**: Immediate event alerts
- **Event Logging**: Comprehensive event history
- **Email Notifications**: Automated alert emails
- **SNMP Traps**: Integration with network monitoring

---

## 10. Troubleshooting Guide

### Common Issues and Solutions

#### Camera Discovery Problems

**Issue**: Cameras not appearing in discovery list

**Solutions**:
1. Verify network connectivity
2. Check firewall settings
3. Enable ONVIF in camera settings
4. Use manual IP discovery
5. Verify ONVIF port configuration

#### Authentication Failures

**Issue**: Cannot connect to camera despite correct credentials

**Solutions**:
1. Verify username and password
2. Check authentication method (Basic vs Digest)
3. Reset camera to factory defaults if necessary
4. Update camera firmware
5. Check for special characters in credentials

#### Streaming Issues

**Issue**: No video stream or poor quality

**Solutions**:
1. Verify network bandwidth
2. Adjust stream resolution and bitrate
3. Check camera lens and focus
4. Test different stream profiles
5. Verify codec compatibility

#### PTZ Control Problems

**Issue**: PTZ commands not working

**Solutions**:
1. Verify PTZ support in camera
2. Check PTZ protocol configuration
3. Test manual PTZ operation
4. Verify preset positions
5. Check PTZ speed settings

### Advanced Troubleshooting

#### Network Analysis

Use built-in tools for network troubleshooting:

```bash
# Network connectivity test
Tools > Network Diagnostics > Ping Test

# Port availability check
Tools > Network Diagnostics > Port Scanner

# Bandwidth measurement
Tools > Network Diagnostics > Bandwidth Test
```

#### Log File Analysis

Access detailed logs for troubleshooting:

- **Application Logs**: General application events
- **Network Logs**: Communication with cameras
- **Error Logs**: System and camera errors
- **Debug Logs**: Detailed diagnostic information

---

## 11. Appendix

### 11.1 Keyboard Shortcuts

| Function | Windows/Linux | macOS |
|----------|---------------|-------|
| Refresh Discovery | F5 | Cmd+R |
| Quick Connect | Ctrl+Q | Cmd+Q |
| Take Snapshot | Space | Space |
| Start/Stop Recording | Ctrl+R | Cmd+R |
| Full Screen | F11 | Cmd+F |
| Settings | Ctrl+, | Cmd+, |
| Exit | Alt+F4 | Cmd+Q |

### 11.2 Common ONVIF Ports

| Service | Default Port | Alternative Ports |
|---------|--------------|-------------------|
| ONVIF Device | 80 | 8080, 8081, 8000 |
| RTSP Streaming | 554 | 8554, 7554 |
| HTTP Web Interface | 80 | 8080, 8081 |
| HTTPS Web Interface | 443 | 8443 |

### 11.3 Supported Features Matrix

#### Camera Manufacturers

| Manufacturer | Discovery | Live View | PTZ | Config | Notes |
|--------------|-----------|-----------|-----|--------|-------|
| Axis | ✓ | ✓ | ✓ | ✓ | Full support |
| Hikvision | ✓ | ✓ | ✓ | ✓ | Full support |
| Dahua | ✓ | ✓ | ✓ | ✓ | Full support |
| Bosch | ✓ | ✓ | ✓ | ✓ | Full support |
| Hanwha | ✓ | ✓ | ✓ | Partial | Limited config |
| Panasonic | ✓ | ✓ | ✓ | ✓ | Full support |
| Sony | ✓ | ✓ | ✓ | ✓ | Full support |

#### ONVIF Profile Support

- **Profile S**: Streaming ✓
- **Profile G**: Recording ✓  
- **Profile T**: Advanced Imaging ✓
- **Profile C**: Access Control ✓
- **Profile A**: Analytics Partial

---

This manual provides comprehensive guidance for using WINK ONVIF Studio effectively. For technical support or additional information, visit [www.wink.co](https://wink.co) or contact our support team.

**© 2025 WINK Streaming, Inc. All rights reserved.**