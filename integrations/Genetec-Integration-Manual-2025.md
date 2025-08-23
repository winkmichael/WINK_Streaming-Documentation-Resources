---
title: "WINK Streaming - Genetec Integration Manual"
description: "Complete integration guide for WINK platform with Genetec Security Center. SDK implementation, authentication setup, PTZ control, and multi-agency sharing."
keywords: ["genetec", "security center", "integration", "virtual encoder", "virtual decoder", "video management", "WINK Forge", "APIBridge.Metro", "SDK", "surveillance"]
version: "2025.1"
compatible_versions: "Security Center 5.2 - 5.12"
document_id: "AB4401-2025"
last_updated: "January 2025"
---

# WINK Streaming - Genetec Integration Manual

## Technical Configuration Manual - Virtual Encoder and Decoder Setup Guide

**Version:** 2025.1  
**Compatible with:** Security Center 5.2 - 5.12  
**Date:** January 2025  
**Company:** WINK Streaming - Professional Video Management Solutions

---

## Table of Contents

1. [Overview](#overview)
2. [Compatibility & Requirements](#compatibility--requirements)
3. [Configuration](#configuration)
4. [WINK Forge Settings](#wink-forge-settings)
5. [APIBridge.Metro Integration](#apibridgemetro-integration)
6. [Troubleshooting](#troubleshooting)
7. [Best Practices](#best-practices)
8. [Appendix](#appendix)

---

## 1. Overview

This document provides comprehensive instructions for configuring and debugging Genetec Virtual Encoders and Virtual Decoders with WINK Streaming solutions. It covers all recent versions of Omnicast and Security Center (5.2 through 5.12), focusing on the integration between WINK Forge and Genetec systems.

### Purpose

The WINK Forge uses the Genetec Virtual Decoder interface for video ingress and egress. Supplemental features are made available via the WINK Streaming H263/H264/H265 Genetec SDK component. This guide covers:

- Virtual Decoder configuration for video ingress
- Virtual Encoder setup for video egress
- Transport mode configuration (UDP, Multicast, TCP)
- Integration with APIBridge.Metro for automated management
- SDK-based helper applications

### Document Scope

This manual does not cover the SDK helper applications provided by WINK Streaming. Information about these SDK-based helper applications (including APIBridge.Metro and WINKSCData) can be found in their respective documentation.

> **Note:** For APIBridge.Metro specific configuration and analog monitor management, refer to the APIBridge.Metro Complete User Manual. For a complete overview of WINK's Genetec integration capabilities, see our Genetec solution page.

---

## 2. Compatibility & Requirements

### Supported Genetec Versions

| Genetec Product | Supported Versions | Notes |
|-----------------|-------------------|--------|
| Security Center | 5.2 - 5.12 | Full compatibility with all features |
| Omnicast | 4.8 and later | Legacy support available |
| Genetec SDK | 5.9, 5.10, 5.11, 5.12 | For SDK-based applications |

### Network Requirements

- **Ports:**
  - 8000-8031 (Default decoder ports)
  - 8100-8131 (Alternate decoder ports)
  - 5500 (Genetec SDK communication)
- **Protocols:** UDP (unicast/multicast), TCP (for SDK)
- **Bandwidth:** Varies by stream count and quality

### Licensing

The WINK Forge can use the built-in virtual encoder and decoder licenses associated with your Genetec product. The total number of virtual decoder connections is constrained by the available monitor licenses.

> **Important:** Ensure you have sufficient Genetec monitor licenses for the number of virtual decoders you plan to deploy.

---

## 3. Configuration

### 3.1 Virtual Decoder Setup

Virtual Decoders allow WINK Forge to receive video streams from Genetec Security Center. This is the primary method for integrating Genetec cameras with WINK streaming infrastructure.

#### Step-by-Step Configuration

**Step 1: Add Virtual Decoder in Security Center**

1. Open Genetec Config Tool
2. Navigate to the Video section
3. Right-click and select "Add Video Unit"
4. Choose "Virtual" as the unit type
5. Select "Genetec Protocol"
6. Enter the WINK Forge IP address and port

**Step 2: Configure Decoder Properties**

| Property | Recommended Value | Description |
|----------|------------------|-------------|
| Name | WINK-Decoder-[Port] | Descriptive name for identification |
| IP Address | WINK Forge Server IP | Target server address |
| Port | 8000-8031 | Default range for decoders |
| Protocol | Genetec | Must be Genetec protocol |

#### Transport Mode Configuration

**Unicast UDP**
For unicast UDP configuration:

1. Open the monitor interface in a view window
2. Drag a camera to the monitor interface
3. Load the WINK Forge logs and determine the assigned port
4. Create an input in the WINK Forge with the assigned unicast port

**Multicast UDP**
For multicast UDP configuration:

1. Open the monitor interface in a view window
2. Drag a camera to the monitor interface
3. Create an input in the WINK Forge with the camera's multicast address and port

> **Note:** Multicast requires that the WINK Forge is in the same LAN or multicast region as the camera. Example multicast address: 239.100.100.100

### 3.2 Virtual Encoder Setup

Virtual Encoders allow WINK Forge to send video streams into Genetec Security Center, appearing as standard IP cameras.

#### Adding a Virtual Encoder

To add a virtual encoder, follow these steps:

1. **Add Video Unit**
   - In Config Tool, navigate to Video section
   - Right-click and select "Add Video Unit"

2. **Select Genetec Protocol**
   - Choose "Virtual" as unit type
   - Select "Genetec" as protocol

3. **Configure Connection**
   - Enter WINK Forge IP address
   - Specify the encoder port
   - Set appropriate credentials if required

From this point on, the Virtual Encoder should be treated as a camera within Security Center.

#### Encoder Properties

| Property | Description | Default |
|----------|-------------|---------|
| Resolution | Video resolution (matches source) | 1920x1080 |
| Frame Rate | Frames per second | 30 fps |
| Codec | H.264, H.263, or H.265 | H.264 (Recommended) |
| Bitrate | Target bitrate in Kbps | 4000 |

> **Codec Recommendation:** While H.263, H.264, and H.265 are all supported, H.264 is preferred as it provides better resilience in packet loss situations. H.264's error resilience features make it the optimal choice for network environments where packet loss may occur.

### 3.3 Associating Cameras with Decoders

Once virtual decoders are configured, you need to associate cameras with them to establish video streams.

#### Manual Association

Using the Security Desk live viewer:

1. Open Security Desk
2. Navigate to the monitoring task
3. Drag a camera from the entity tree
4. Drop it on an open monitor interface

#### Automated Association with APIBridge.Metro

For large-scale deployments, use APIBridge.Metro to automate camera-monitor associations:

1. Connect APIBridge.Metro to Security Center
2. Navigate to "Associate Monitors Evenly"
3. Configure distribution settings
4. Apply associations in batch

> **SDK Limitation:** The Genetec SDK 5.12 does not support programmatic video routing. APIBridge.Metro updates monitor names to indicate intended associations, but actual video routing must be configured manually in Security Desk.

---

## 4. WINK Forge Settings

Each WINK Forge offers additional settings related to the Genetec Protocol configuration. These settings can be found in the Variables page of the WINK Forge interface.

### 4.1 Encoder & Decoder Settings

#### Genetec Mode
*Default: 0*

| Mode | Name | Description | Use Case |
|------|------|-------------|----------|
| 0 | Default | Works with all standard versions | Recommended for Security Center 5.x |
| 1 | Gen_Omni | Fallback for older Omnicast | Use if Mode 0 fails with Omnicast |
| 2 | Gen_SC | Fallback for older Security Center | Use if Mode 0 fails with SC < 5.7 |
| 3 | Debug | Additional auditing and logging | Troubleshooting only |

> **Note:** Debug mode logs are exposed in the WINK Forge under Support → ||| Logs.

#### Monitor Configuration
*Default: 10*

This value defines the default number of monitors per port. Configuration details:

- **Default ports:** 8000-8031
- **Alternate ports:** 8100-8131
- **Maximum cameras per monitor:** 10 (recommended)

> **Warning:** Increasing this value beyond 10 has been known to cause issues with some versions of Omnicast and may result in performance problems. Performance issues have not been observed in Security Center greater than 5.7.

#### Transport Protocol Settings

**UDP**
*Default: Enabled*
Enable or disable support to receive UDP Unicast. WINK Forges will be able to receive the video stream in UDP.

**Multicast**
*Default: Enabled*
Enable or disable support to receive UDP Multicast. This is the default transport type for Genetec products; however, it requires that the WINK Forge is in the same LAN or multicast region of the camera.

**TCP**
*Default: Disabled*
TCP transport mode is currently not supported for virtual decoders.

### 4.2 Decoder Specific Settings

#### Hold Time
*Default: 20*
Number of seconds to hold the current camera image in the event of disconnection. This helps prevent black screens during temporary network interruptions.

#### Generate Stop and Start
*Default: Enabled*
Enable or disable stop-start messages sent by Omnicast or Security Center.

> **Note:** Disabling this value will disable blocking unless the blocking Windows SDK application provided by WINK Streaming is in use.

#### Buffering
*Default: 500ms*
Amount of video buffering to maintain smooth playback. Adjust based on network conditions:

- **LAN:** 200-500ms
- **WAN:** 1000-2000ms
- **Internet:** 2000-5000ms

### 4.3 Encoder Specific Settings

#### HTTP Tunneling
*Default: Enabled*
Enable or disable tunneling over HTTP. This allows video streams to traverse firewalls and proxies that block standard protocols.

#### Inline Communication
*Default: Disabled*
Enable or disable RTP communication inline over the control channel. This reduces the number of ports required but may impact performance.

#### Always Available
*Default: Disabled*
Enable or disable sending an online state even when the camera is offline. Useful for maintaining consistent entity states in Security Center.

#### Force HTTP Tunnel
*Default: Disabled*
Enable or disable forcing all communication over HTTP tunnel. Only to be used for debugging or in restrictive network environments.

#### Absolute Path
*Default: (empty)*
Only to be used for debugging when coordinating with WINK Streaming technical support staff.

---

## 5. APIBridge.Metro Integration

APIBridge.Metro provides enhanced management capabilities for WINK-Genetec integrations, offering automated configuration and monitoring features.

### 5.1 SDK Components

The WINK Streaming H263/H264/H265 Genetec SDK component provides supplemental features beyond the basic virtual encoder/decoder functionality:

#### Key Features

- **Automated Discovery:** Finds all virtual decoders in the system
- **Batch Configuration:** Update multiple decoders simultaneously
- **Association Management:** Distribute cameras across monitors evenly
- **Export Capabilities:** Generate XML reports of configurations
- **Real-time Monitoring:** Track decoder status and connections

#### System Requirements for SDK Components

- Windows 2012 Server or Windows 8.1 or greater
- .NET Framework 4.5.1 or later (4.8 recommended)
- Genetec SDK 5.9 or later installed
- Administrative access to Security Center

### 5.2 Monitor Associations

APIBridge.Metro simplifies the process of associating cameras with virtual decoders, especially in large deployments.

#### Association Workflow

1. **Connect to Security Center**
   - Use SDK credentials with appropriate permissions
   - Verify connection status

2. **List Decoders**
   - View all virtual decoders
   - Check current associations
   - Identify unconnected monitors

3. **Configure Associations**
   - Use "Associate Monitors Evenly" feature
   - Preview distribution before applying
   - Apply naming conventions

4. **Export Configuration**
   - Generate XML documentation
   - Create association reports

#### Naming Conventions

APIBridge.Metro uses standardized naming to indicate associations:

- **Format:** `[Camera Name] - [Monitor Prefix]-[UDP Port]`
- **Example:** `Front Entrance - WF301-8001`

> **Integration Note:** While APIBridge.Metro updates monitor names to reflect associations, the actual video routing must still be configured in Security Desk due to SDK limitations.

---

## 6. Troubleshooting

### Common Issues and Solutions

#### Virtual Decoder Not Connecting

| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Decoder shows offline | Network connectivity | • Verify WINK Forge is accessible<br>• Check firewall rules for ports 8000-8031<br>• Test with telnet to port |
| Connection timeout | Wrong Genetec mode | • Try different Genetec modes (0, 1, or 2)<br>• Check Security Center version<br>• Enable debug mode for logs |
| Authentication failed | Protocol mismatch | • Ensure "Genetec" protocol is selected<br>• Verify no authentication is set<br>• Check WINK Forge license |

#### No Video Stream

| Symptom | Possible Cause | Solution |
|---------|----------------|----------|
| Black screen on monitor | No camera associated | • Drag camera to monitor in Security Desk<br>• Verify camera is online<br>• Check monitor license availability |
| Intermittent video | Network issues | • Check multicast routing<br>• Verify UDP traffic not blocked<br>• Increase buffer time |
| Poor video quality | Bandwidth limitations | • Check network bandwidth<br>• Reduce stream quality<br>• Enable unicast instead of multicast |

#### Debug Mode Logging

To enable detailed logging:

1. Set Genetec Mode to 3 (Debug)
2. Reproduce the issue
3. Access logs via WINK Forge Support → ||| Logs
4. Look for connection attempts and error messages

#### APIBridge.Metro Issues

- **Cannot see decoders:** Verify SDK user has "View analog monitors" permission
- **Cannot update names:** Check "Modify analog monitor properties" permission
- **Missing IP addresses:** Enable "View video units" permission

---

## 7. Best Practices

### Network Configuration

- **Use dedicated VLANs** for video traffic
- **Enable QoS** for video streams
- **Configure multicast properly** with IGMP snooping
- **Monitor bandwidth usage** regularly

### Decoder Management

- **Naming Convention:** Use consistent naming like WF[Number]-[Port]
- **Port Allocation:** Document port assignments
- **License Planning:** Ensure sufficient monitor licenses
- **Regular Testing:** Verify decoder connections weekly

### Performance Optimization

- **Limit cameras per monitor** to 10 or fewer
- **Use appropriate buffer sizes** based on network type
- **Enable only required transport modes**
- **Monitor WINK Forge CPU and memory** usage

### Security Considerations

- **Firewall Rules:** Only open required ports
- **Network Isolation:** Separate video traffic from corporate network
- **Access Control:** Limit who can modify decoder configurations
- **Regular Updates:** Keep WINK Forge and Genetec updated

### Documentation

- **Maintain decoder mapping** spreadsheet
- **Document custom configurations**
- **Export configurations regularly** using APIBridge.Metro
- **Keep troubleshooting logs** for pattern analysis

---

## 8. Appendix

### Port Reference

| Port Range | Usage | Protocol |
|------------|-------|----------|
| 8000-8031 | Default decoder ports | UDP |
| 8100-8131 | Alternate decoder ports | UDP |
| 5500 | Genetec SDK communication | TCP |
| 554 | Standard streaming | TCP/UDP |
| 80/443 | HTTP tunneling | TCP |

### Genetec Mode Reference

| Mode | Best For | Notes |
|------|----------|--------|
| 0 (Default) | Security Center 5.x | Recommended starting point |
| 1 (Gen_Omni) | Omnicast 4.x | Legacy support |
| 2 (Gen_SC) | Security Center < 5.7 | Older versions |
| 3 (Debug) | Troubleshooting | Verbose logging |

### Contact Information

For technical support and assistance:

- **WINK Streaming Support:** tickets@wink.co

### Related Documentation

- APIBridge.Metro Complete User Manual
- WINKSCData Console Application Guide
- Genetec Security Center Administrator Guide
- WINK Forge Installation Manual

---

© 2025 WINK Streaming. All rights reserved.  
WINK-Genetec Interface Manual - Version 2025.1  
Document Revision: AB4401-2025