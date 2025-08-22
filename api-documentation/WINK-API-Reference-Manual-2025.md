---
title: "WINK Streaming - Command API Reference Manual"
description: "Complete API reference documentation for WINK Streaming products. RESTful APIs for WINK Forge, Media Router, Analytics, and AI Traffic platforms."
keywords: ["API documentation", "WINK API", "video streaming API", "REST API", "XML API", "WINK Forge API", "Media Router API", "streaming platform API", "video transcoding API", "authentication API"]
category: "api-documentation"
product: "WINK Streaming Platform"
version: "1.4.9.1"
last_updated: "August 2025"
author: "WINK Streaming, Inc."
---

# WINK Streaming

## Command API Reference

**Version 1.4.9.1**  
**Document Updated: August 2025**  
**WINK Streaming, Inc.**

---

## Table of Contents

1. [Overview](#overview)
2. [Getting Started](#getting-started)
   - [Authentication](#authentication)
   - [SSL Requirements](#ssl-requirements)
   - [API Testing](#api-testing)
3. [WINK Forge API Commands](#wink-forge-api-commands)
   - [1. Version](#1-version)
   - [2. Input Control](#2-input-control)
   - [3. IP Based Control](#3-ip-based-control)
   - [4-7. GUID Operations](#4-7-guid-operations)
   - [8-9. IP Operations](#8-9-ip-operations)
   - [10-11. Stream Management](#10-11-stream-management)
   - [12-14. User Management](#12-14-user-management)
   - [15-17. System Operations](#15-17-system-operations)
4. [WINK Archive API Commands](#wink-archive-api-commands)
   - [1. Version](#archive-version)
   - [2. Archive Control](#archive-control)
   - [3. Archive Status](#archive-status)
   - [4. Archive Source](#archive-source)
   - [5. Archive Delete](#archive-delete)
5. [WINK Media Router API](#wink-media-router-api)
   - [OTP Overview](#otp-overview)
   - [1. Create OTP](#1-create-otp)
   - [2. Destroy/Extend OTP](#2-destroyextend-otp)
   - [3. Query Token](#3-query-token)
6. [Code Examples](#code-examples)
   - [JavaScript Integration](#javascript-integration)
   - [Python Integration](#python-integration)
   - [cURL Examples](#curl-examples)
7. [Notes & Best Practices](#notes--best-practices)
8. [Customer Support](#customer-support)

---

## Overview

This document provides a comprehensive reference for the WINK Streaming Command API, covering WINK Forge, WINK Archive, and WINK Media Router appliances. The API enables programmatic control of video streaming operations, user management, and system administration.

### Supported Products

- **WINK Forge:** Video transcoding and stream management (Firmware 1.6+)
- **WINK Archive:** Video recording and storage (Firmware 1.1+)
- **WINK Media Router:** Stream distribution and OTP authentication (Firmware 1.2+)

### API Architecture

The WINK API uses XML-based messaging over HTTPS, providing:

- Secure SSL/TLS encrypted communication
- XML request/response format for structured data
- Session-based authentication with optional shared keys
- RESTful design principles for predictable behavior

> **Note:** This document does not cover WINK Crossroad integration APIs or third-party integration commands, which are documented separately.

---

## Getting Started

### Authentication

All API calls require authentication using the following XML structure:

```xml
<wink_api user='username' pass='password' key='shared_key'>
    <req id='unique_id' command='command_name'>payload</req>
</wink_api>
```

#### Authentication Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| user | Yes | API username (must have API access level) |
| pass | Yes | User password |
| key | Optional | Shared key for enhanced security |

### SSL Requirements

All API endpoints require SSL/HTTPS connections. Configure SSL certificates through:

1. Navigate to **Settings → System Options → SSL Certificate**
2. Generate self-signed certificate or upload existing certificate
3. For development, use the `--insecure` flag with cURL to bypass certificate validation

#### Example Connection

```bash
curl -X POST https://10.130.90.188/api/ \
    --insecure \
    --data-urlencode "<wink_api user='apiuser' pass='apipass'>
        <req id='1' command='version'></req>
    </wink_api>" \
    -H 'Content-Type: application/xml'
```

### API Testing

WINK Forge includes a built-in API testing interface:

**Access via: Tools → API Tester**

Features:
- Test commands without external tools
- View formatted responses
- Save command history
- Export results

---

## WINK Forge API Commands

### 1. Version

| Field | Value |
|-------|-------|
| **Command** | `version` |
| **Description** | Returns the API version number |
| **Payload** | None |

#### Example Request

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='1' command='version'></req>
</wink_api>
```

#### Example Response

```xml
<wink_api>
    <resp id="1" command="version" status="ok">1.4</resp>
</wink_api>
```

**Return Value:** Numeric API version (e.g., "1.4")

### 2. Input Control

| Field | Value |
|-------|-------|
| **Command** | `input_control` |
| **Description** | Start or stop input processing |
| **Parameters** | `action` (start/stop), `input_id` |

#### Example Request - Start Input

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='2' command='input_control'>
        <action>start</action>
        <input_id>rtsp://192.168.1.100/stream1</input_id>
    </req>
</wink_api>
```

#### Example Response

```xml
<wink_api>
    <resp id="2" command="input_control" status="ok">
        <result>Input started successfully</result>
        <stream_guid>12345678-1234-1234-1234-123456789012</stream_guid>
    </resp>
</wink_api>
```

### 3. IP Based Control

| Field | Value |
|-------|-------|
| **Command** | `ip_control` |
| **Description** | Control streams by IP address |
| **Parameters** | `action` (start/stop), `ip_address`, `port` |

#### Example Request

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='3' command='ip_control'>
        <action>start</action>
        <ip_address>192.168.1.100</ip_address>
        <port>554</port>
    </req>
</wink_api>
```

### 4-7. GUID Operations

#### 4. Get GUID by IP

| Field | Value |
|-------|-------|
| **Command** | `get_guid_by_ip` |
| **Description** | Retrieve stream GUID using IP address |
| **Parameters** | `ip_address` |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='4' command='get_guid_by_ip'>
        <ip_address>192.168.1.100</ip_address>
    </req>
</wink_api>
```

#### 5. Get IP by GUID

| Field | Value |
|-------|-------|
| **Command** | `get_ip_by_guid` |
| **Description** | Retrieve IP address using stream GUID |
| **Parameters** | `guid` |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='5' command='get_ip_by_guid'>
        <guid>12345678-1234-1234-1234-123456789012</guid>
    </req>
</wink_api>
```

#### 6. Start Stream by GUID

| Field | Value |
|-------|-------|
| **Command** | `start_by_guid` |
| **Description** | Start streaming using GUID |
| **Parameters** | `guid` |

#### 7. Stop Stream by GUID

| Field | Value |
|-------|-------|
| **Command** | `stop_by_guid` |
| **Description** | Stop streaming using GUID |
| **Parameters** | `guid` |

### 8-9. IP Operations

#### 8. Start Stream by IP

| Field | Value |
|-------|-------|
| **Command** | `start_by_ip` |
| **Description** | Start streaming using IP address |
| **Parameters** | `ip_address`, `port` (optional) |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='8' command='start_by_ip'>
        <ip_address>192.168.1.100</ip_address>
        <port>554</port>
    </req>
</wink_api>
```

#### 9. Stop Stream by IP

| Field | Value |
|-------|-------|
| **Command** | `stop_by_ip` |
| **Description** | Stop streaming using IP address |
| **Parameters** | `ip_address` |

### 10-11. Stream Management

#### 10. List Active Streams

| Field | Value |
|-------|-------|
| **Command** | `list_streams` |
| **Description** | Get list of all active streams |
| **Parameters** | None |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='10' command='list_streams'></req>
</wink_api>
```

#### Example Response

```xml
<wink_api>
    <resp id="10" command="list_streams" status="ok">
        <stream_count>3</stream_count>
        <streams>
            <stream>
                <guid>12345678-1234-1234-1234-123456789012</guid>
                <ip>192.168.1.100</ip>
                <status>active</status>
                <bitrate>2048</bitrate>
                <resolution>1920x1080</resolution>
            </stream>
            <stream>
                <guid>87654321-4321-4321-4321-210987654321</guid>
                <ip>192.168.1.101</ip>
                <status>active</status>
                <bitrate>1024</bitrate>
                <resolution>1280x720</resolution>
            </stream>
        </streams>
    </resp>
</wink_api>
```

#### 11. Get Stream Statistics

| Field | Value |
|-------|-------|
| **Command** | `stream_stats` |
| **Description** | Get detailed statistics for a stream |
| **Parameters** | `guid` or `ip_address` |

### 12-14. User Management

#### 12. List Users

| Field | Value |
|-------|-------|
| **Command** | `list_users` |
| **Description** | Get list of all system users |
| **Parameters** | None |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='12' command='list_users'></req>
</wink_api>
```

#### 13. Add User

| Field | Value |
|-------|-------|
| **Command** | `add_user` |
| **Description** | Create new user account |
| **Parameters** | `username`, `password`, `access_level` |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='13' command='add_user'>
        <username>newuser</username>
        <password>securepass123</password>
        <access_level>operator</access_level>
    </req>
</wink_api>
```

#### 14. Delete User

| Field | Value |
|-------|-------|
| **Command** | `delete_user` |
| **Description** | Remove user account |
| **Parameters** | `username` |

### 15-17. System Operations

#### 15. System Status

| Field | Value |
|-------|-------|
| **Command** | `system_status` |
| **Description** | Get system health and status |
| **Parameters** | None |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='15' command='system_status'></req>
</wink_api>
```

#### 16. System Reboot

| Field | Value |
|-------|-------|
| **Command** | `system_reboot` |
| **Description** | Restart the system |
| **Parameters** | `confirm` (required: "yes") |

#### 17. Get Configuration

| Field | Value |
|-------|-------|
| **Command** | `get_config` |
| **Description** | Retrieve system configuration |
| **Parameters** | `section` (optional) |

---

## WINK Archive API Commands

### Archive Version

| Field | Value |
|-------|-------|
| **Command** | `version` |
| **Description** | Returns the Archive API version |
| **Endpoint** | `https://[archive-ip]/archive_api/` |

### Archive Control

| Field | Value |
|-------|-------|
| **Command** | `archive_control` |
| **Description** | Start/stop recording |
| **Parameters** | `action`, `stream_id`, `duration` |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='1' command='archive_control'>
        <action>start</action>
        <stream_id>camera_01</stream_id>
        <duration>3600</duration>
    </req>
</wink_api>
```

### Archive Status

| Field | Value |
|-------|-------|
| **Command** | `archive_status` |
| **Description** | Get recording status |
| **Parameters** | `stream_id` (optional) |

### Archive Source

| Field | Value |
|-------|-------|
| **Command** | `archive_source` |
| **Description** | Configure recording source |
| **Parameters** | `stream_id`, `source_url` |

### Archive Delete

| Field | Value |
|-------|-------|
| **Command** | `archive_delete` |
| **Description** | Delete archived recordings |
| **Parameters** | `stream_id`, `date_range` |

---

## WINK Media Router API

### OTP Overview

One-Time Password (OTP) tokens provide secure, time-limited access to video streams without exposing permanent credentials.

#### OTP Benefits

- **Enhanced Security**: Temporary access tokens
- **Access Control**: Fine-grained permissions
- **Audit Trail**: Complete access logging
- **Integration**: Easy third-party integration

### 1. Create OTP

| Field | Value |
|-------|-------|
| **Command** | `create_otp` |
| **Description** | Generate new OTP token |
| **Parameters** | `stream_id`, `duration`, `permissions` |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='1' command='create_otp'>
        <stream_id>camera_01</stream_id>
        <duration>3600</duration>
        <permissions>view,ptz</permissions>
    </req>
</wink_api>
```

#### Example Response

```xml
<wink_api>
    <resp id="1" command="create_otp" status="ok">
        <token>abc123def456ghi789</token>
        <expires>2025-08-22T15:30:00Z</expires>
        <stream_url>https://router.example.com/otp/abc123def456ghi789</stream_url>
    </resp>
</wink_api>
```

### 2. Destroy/Extend OTP

#### Destroy OTP

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='2' command='destroy_otp'>
        <token>abc123def456ghi789</token>
    </req>
</wink_api>
```

#### Extend OTP

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='3' command='extend_otp'>
        <token>abc123def456ghi789</token>
        <additional_duration>1800</additional_duration>
    </req>
</wink_api>
```

### 3. Query Token

| Field | Value |
|-------|-------|
| **Command** | `query_token` |
| **Description** | Get token information |
| **Parameters** | `token` |

```xml
<wink_api user='apiuser' pass='apipass'>
    <req id='4' command='query_token'>
        <token>abc123def456ghi789</token>
    </req>
</wink_api>
```

---

## Code Examples

### JavaScript Integration

```javascript
class WinkAPI {
    constructor(baseUrl, username, password, sharedKey = '') {
        this.baseUrl = baseUrl;
        this.username = username;
        this.password = password;
        this.sharedKey = sharedKey;
    }

    async sendCommand(command, payload = '', requestId = Date.now()) {
        const xmlRequest = `
            <wink_api user='${this.username}' pass='${this.password}' key='${this.sharedKey}'>
                <req id='${requestId}' command='${command}'>${payload}</req>
            </wink_api>
        `;

        const response = await fetch(`${this.baseUrl}/api/`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/xml',
            },
            body: xmlRequest,
        });

        return await response.text();
    }

    async getVersion() {
        return await this.sendCommand('version');
    }

    async startStream(ipAddress, port = 554) {
        const payload = `
            <ip_address>${ipAddress}</ip_address>
            <port>${port}</port>
        `;
        return await this.sendCommand('start_by_ip', payload);
    }

    async listStreams() {
        return await this.sendCommand('list_streams');
    }
}

// Usage example
const wink = new WinkAPI('https://forge.example.com', 'apiuser', 'apipass');

// Get API version
wink.getVersion().then(response => {
    console.log('API Version Response:', response);
});

// Start a stream
wink.startStream('192.168.1.100').then(response => {
    console.log('Stream Start Response:', response);
});
```

### Python Integration

```python
import requests
import xml.etree.ElementTree as ET
from urllib.parse import quote

class WinkAPI:
    def __init__(self, base_url, username, password, shared_key=''):
        self.base_url = base_url
        self.username = username
        self.password = password
        self.shared_key = shared_key
        self.session = requests.Session()
        
        # Disable SSL verification for development
        self.session.verify = False

    def send_command(self, command, payload='', request_id=None):
        if request_id is None:
            request_id = str(int(time.time()))
        
        xml_request = f"""
            <wink_api user='{self.username}' pass='{self.password}' key='{self.shared_key}'>
                <req id='{request_id}' command='{command}'>{payload}</req>
            </wink_api>
        """
        
        response = self.session.post(
            f"{self.base_url}/api/",
            data={'xml': xml_request},
            headers={'Content-Type': 'application/x-www-form-urlencoded'}
        )
        
        return response.text

    def get_version(self):
        return self.send_command('version')

    def start_stream(self, ip_address, port=554):
        payload = f"""
            <ip_address>{ip_address}</ip_address>
            <port>{port}</port>
        """
        return self.send_command('start_by_ip', payload)

    def list_streams(self):
        return self.send_command('list_streams')

    def parse_response(self, xml_response):
        """Parse XML response and extract data"""
        try:
            root = ET.fromstring(xml_response)
            responses = []
            
            for resp in root.findall('.//resp'):
                response_data = {
                    'id': resp.get('id'),
                    'command': resp.get('command'),
                    'status': resp.get('status'),
                    'content': resp.text
                }
                responses.append(response_data)
            
            return responses
        except ET.ParseError as e:
            return {'error': f'XML Parse Error: {e}'}

# Usage example
if __name__ == "__main__":
    wink = WinkAPI('https://forge.example.com', 'apiuser', 'apipass')
    
    # Get API version
    version_response = wink.get_version()
    print("API Version Response:", version_response)
    
    # Parse the response
    parsed = wink.parse_response(version_response)
    print("Parsed Response:", parsed)
    
    # Start a stream
    stream_response = wink.start_stream('192.168.1.100')
    print("Stream Start Response:", stream_response)
```

### cURL Examples

#### Basic Version Check

```bash
curl -X POST https://forge.example.com/api/ \
    --insecure \
    --data-urlencode "<wink_api user='apiuser' pass='apipass'>
        <req id='1' command='version'></req>
    </wink_api>"
```

#### Start Stream with Parameters

```bash
curl -X POST https://forge.example.com/api/ \
    --insecure \
    --data-urlencode "<wink_api user='apiuser' pass='apipass' key='sharedkey'>
        <req id='2' command='start_by_ip'>
            <ip_address>192.168.1.100</ip_address>
            <port>554</port>
        </req>
    </wink_api>" \
    -H 'Content-Type: application/xml'
```

#### List All Active Streams

```bash
curl -X POST https://forge.example.com/api/ \
    --insecure \
    --data-urlencode "<wink_api user='apiuser' pass='apipass'>
        <req id='3' command='list_streams'></req>
    </wink_api>"
```

#### Create OTP Token

```bash
curl -X POST https://router.example.com/api/ \
    --insecure \
    --data-urlencode "<wink_api user='apiuser' pass='apipass'>
        <req id='4' command='create_otp'>
            <stream_id>camera_01</stream_id>
            <duration>3600</duration>
            <permissions>view,ptz</permissions>
        </req>
    </wink_api>"
```

---

## Notes & Best Practices

### Security Considerations

1. **Always Use HTTPS**: Never send credentials over unencrypted connections
2. **Implement Shared Keys**: Add an extra layer of security for production deployments
3. **Limit API User Permissions**: Create dedicated API users with minimal required permissions
4. **Rotate Credentials**: Regularly change API passwords and shared keys
5. **Monitor API Usage**: Keep logs of all API calls for security auditing

### Performance Optimization

1. **Reuse Connections**: Use persistent HTTP connections when possible
2. **Batch Operations**: Group multiple commands when supported
3. **Implement Caching**: Cache responses that don't change frequently
4. **Handle Rate Limits**: Implement proper retry logic for rate-limited requests
5. **Use Async Operations**: Non-blocking calls for better performance

### Error Handling

Always check the `status` attribute in API responses:

```xml
<resp id="1" command="version" status="error">
    <error_code>401</error_code>
    <error_message>Authentication failed</error_message>
</resp>
```

Common error codes:
- **401**: Authentication failed
- **403**: Insufficient permissions
- **404**: Command or resource not found
- **500**: Internal server error
- **503**: Service temporarily unavailable

### Integration Tips

1. **Start Simple**: Begin with basic commands like `version` before complex operations
2. **Test Thoroughly**: Use the built-in API testing tool before implementing in production
3. **Handle Timeouts**: Set appropriate timeout values for network operations
4. **Document Your Integration**: Keep clear documentation of your API usage
5. **Stay Updated**: Check for API version updates and new features regularly

---

## Customer Support

### Technical Support

For technical assistance with the WINK API:

- **Email**: support@wink.co
- **Phone**: +1 (555) 123-WINK
- **Documentation**: [https://wink.co/documentation](https://wink.co/documentation)
- **Support Portal**: [https://support.wink.co](https://support.wink.co)

### Support Hours

- **Standard Support**: Monday-Friday, 9 AM - 5 PM EST
- **Premium Support**: 24/7 support available for enterprise customers
- **Emergency Support**: Critical issues supported 24/7

### Resources

- **Knowledge Base**: Searchable documentation and troubleshooting guides
- **Video Tutorials**: Step-by-step implementation guides
- **Community Forums**: Peer-to-peer support and discussion
- **Sample Code**: GitHub repository with integration examples

### Reporting Issues

When reporting API issues, please include:

1. **API Version**: Output from the `version` command
2. **Request Details**: Complete XML request that failed
3. **Response Details**: Full error response received
4. **Environment Info**: Firmware version, network configuration
5. **Steps to Reproduce**: Detailed reproduction steps

---

This API reference provides comprehensive documentation for integrating with WINK Streaming products. For the latest updates and additional resources, visit our documentation portal at [wink.co/documentation](https://wink.co/documentation).

**© 2025 WINK Streaming, Inc. All rights reserved.**