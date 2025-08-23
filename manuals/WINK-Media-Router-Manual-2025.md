# WINK Media Router Manual 2025

*Firmware Version 3.x - The Universal Video Sharing Platform for Government and Enterprise*

## Table of Contents

1. [Overview](#overview)
2. [System Requirements](#system-requirements)
3. [Installation](#installation)
4. [Initial Configuration](#initial-configuration)
5. [Camera Management](#camera-management)
6. [User Management & Access Control](#user-management--access-control)
7. [Stream Management](#stream-management)
8. [One-Time Password (OTP) System](#one-time-password-otp-system)
9. [VMS Integration](#vms-integration)
10. [Analytics Integration](#analytics-integration)
11. [High Availability](#high-availability)
12. [Monitoring & Troubleshooting](#monitoring--troubleshooting)
13. [API Reference](#api-reference)
14. [Appendix](#appendix)

---

## Overview

WINK Media Router is the universal video sharing platform designed specifically for government agencies and enterprises managing thousands of cameras across multiple systems. Unlike traditional VMS platforms that excel at internal management but fail at external sharing, Media Router bridges ALL video infrastructure - from 30-year-old analog systems to cutting-edge IP cameras.

### Key Features

- **Massive Scale:** Manage 20,000+ cameras in a single interface
- **Universal Compatibility:** Every streaming protocol from analog to 2025 standards
- **Five-Tier Permissions:** Granular access control for multi-agency sharing
- **OTP Authentication:** Secure temporary access without permanent accounts
- **Native VMS Integration:** Direct SDK integration with Genetec and Milestone
- **Protocol Translation:** Any input to any output seamlessly
- **24/7 Health Monitoring:** Real-time monitoring and alerting

### Target Applications

- State Departments of Transportation (DOT)
- Multi-agency emergency operations centers  
- Federal facility networks
- City surveillance systems
- Critical infrastructure protection
- Public safety intelligence sharing
- 511 traveler information systems

---

## System Requirements

### Minimum Hardware Requirements

| Component | Minimum | Recommended | High-Scale (10k+ cameras) |
|-----------|---------|-------------|---------------------------|
| CPU | 4 cores, 2.4 GHz | 8 cores, 3.0 GHz | 16+ cores, 3.2 GHz |
| RAM | 8 GB | 16 GB | 32+ GB |
| Storage | 250 GB SSD | 500 GB SSD | 1+ TB NVMe SSD |
| Network | 1 Gbps | 10 Gbps | 40+ Gbps |
| Operating System | Ubuntu 20.04+ | Ubuntu 22.04 LTS | Ubuntu 22.04 LTS |

### Network Requirements

- **Bandwidth:** 100 Kbps per camera stream (minimum)
- **Latency:** <200ms to cameras and clients
- **Ports:** 80/443 (HTTP/HTTPS), 1935 (RTMP), 8554 (RTSP), custom ports as needed
- **Protocols:** Support for RTSP, HLS, WebRTC, SRT, RTMP output

### VMS Compatibility

| VMS Platform | Version Support | Integration Type |
|--------------|-----------------|------------------|
| Genetec Security Center | 5.8+ | Native SDK |
| Milestone XProtect | 2019+ | Management API |
| Avigilon Control Center | 7.0+ | RTSP Export |
| Bosch BVMS | 10.0+ | HTTP API |
| Hanwha WAVE | 4.0+ | REST API |

---

## Installation

### Quick Start Installation

For most deployments, the automated installer handles all configuration:

```bash
# Download and run installer
curl -O https://downloads.wink.co/media-router/install-mr3.sh
sudo bash install-mr3.sh

# Follow interactive prompts for:
# - Network interface selection
# - SSL certificate setup
# - Initial admin account
# - VMS integration (optional)
```

### Manual Installation

For custom deployments or air-gapped environments:

```bash
# Install dependencies
sudo apt update
sudo apt install -y docker.io docker-compose nginx redis-server postgresql

# Download Media Router package
wget https://downloads.wink.co/media-router/wink-mr-3.0.tar.gz
tar -xzf wink-mr-3.0.tar.gz
cd wink-media-router

# Configure environment
cp config/example.env .env
nano .env  # Edit configuration as needed

# Deploy containers
docker-compose up -d

# Initialize database
docker exec -it wink-mr-db psql -U postgres -d wink_mr -f /sql/init.sql
```

### Installation Verification

```bash
# Check service status
sudo systemctl status wink-media-router

# Test web interface
curl -k https://localhost/api/health

# Expected response:
{
  "status": "healthy",
  "version": "3.0.1",
  "cameras": 0,
  "active_streams": 0,
  "uptime": "0h 2m 15s"
}
```

---

## Initial Configuration

### First-Time Setup Wizard

1. **Access Web Interface**
   - Navigate to `https://[server-ip]/setup`
   - Use temporary credentials provided during installation

2. **Basic System Configuration**
   ```
   System Name: [Your Organization] Media Router
   Time Zone: [Select appropriate timezone]
   NTP Server: pool.ntp.org (or internal NTP server)
   ```

3. **Network Configuration**
   ```
   Primary Interface: eth0
   IP Address: [Static IP recommended]
   Gateway: [Network gateway]
   DNS Servers: [Primary, Secondary]
   ```

4. **SSL Certificate Setup**
   - **Self-signed:** Auto-generated (development only)
   - **Let's Encrypt:** Automatic (requires public domain)
   - **Custom:** Upload your certificate files

5. **Administrator Account**
   ```
   Username: [admin username]
   Password: [strong password - min 12 chars]
   Email: [admin email for notifications]
   ```

### Essential Configuration Settings

#### Stream Settings
```json
{
  "default_protocol": "HLS",
  "segment_duration": 4,
  "playlist_length": 6,
  "adaptive_bitrate": true,
  "max_concurrent_streams": 1000
}
```

#### Security Settings
```json
{
  "require_https": true,
  "session_timeout": 3600,
  "max_failed_logins": 5,
  "password_complexity": "high",
  "otp_default_duration": 3600
}
```

#### Performance Settings
```json
{
  "max_cameras": 20000,
  "health_check_interval": 30,
  "stream_timeout": 60,
  "cache_duration": 300,
  "worker_processes": "auto"
}
```

---

## Camera Management

### Camera Discovery and Import

#### Automatic VMS Discovery

For integrated VMS systems:

```bash
# Genetec Discovery
POST /api/cameras/discover
{
  "type": "genetec",
  "server": "192.168.1.100",
  "username": "admin",
  "password": "password",
  "import_all": true
}

# Milestone Discovery  
POST /api/cameras/discover
{
  "type": "milestone",
  "management_server": "192.168.1.101",
  "username": "admin",
  "password": "password",
  "recording_servers": ["192.168.1.102", "192.168.1.103"]
}
```

#### ONVIF Discovery

For direct camera connections:

```bash
# Network scan for ONVIF cameras
POST /api/cameras/scan
{
  "network": "192.168.1.0/24",
  "timeout": 10,
  "onvif_only": true
}

# Manual ONVIF camera addition
POST /api/cameras/add
{
  "name": "Front Gate Camera",
  "ip_address": "192.168.1.50",
  "onvif_port": 80,
  "username": "admin", 
  "password": "camera_password",
  "location": "Building A - Main Entrance"
}
```

#### Bulk Import via CSV

CSV format for bulk camera import:

```csv
name,ip_address,username,password,location,tags,rtsp_url
"Camera 1","192.168.1.50","admin","pass123","Building A","entrance,security",""
"Camera 2","192.168.1.51","admin","pass123","Building B","parking,outdoor",""
"Legacy Cam","192.168.1.20","","","Building C","analog,legacy","rtsp://encoder:554/stream1"
```

Upload via web interface or API:

```bash
curl -X POST -F "file=@cameras.csv" https://server/api/cameras/bulk-import
```

### Camera Configuration

#### Individual Camera Settings

```json
{
  "camera_id": "cam_001",
  "name": "Main Entrance Camera",
  "description": "Primary entrance monitoring",
  "location": {
    "building": "Building A",
    "floor": "Ground Floor",
    "coordinates": {"lat": 40.7128, "lng": -74.0060}
  },
  "stream_settings": {
    "resolution": "1920x1080",
    "framerate": 15,
    "bitrate": 2000,
    "keyframe_interval": 15
  },
  "access_control": {
    "public": false,
    "agencies": ["POLICE", "FIRE"],
    "permission_level": 3
  },
  "tags": ["entrance", "priority", "ptz"],
  "ptz_enabled": true,
  "recording_enabled": true,
  "analytics_enabled": false
}
```

#### Bulk Configuration Templates

Create templates for similar cameras:

```json
{
  "template_name": "Standard Traffic Camera",
  "stream_settings": {
    "resolution": "1280x720", 
    "framerate": 15,
    "bitrate": 1500,
    "keyframe_interval": 15
  },
  "access_control": {
    "public": true,
    "agencies": ["DOT", "POLICE"],
    "permission_level": 1
  },
  "tags": ["traffic", "public"],
  "apply_to": {
    "tag_filter": "traffic",
    "location_filter": "highway"
  }
}
```

### Camera Organization

#### Hierarchical Groups

```
Organization Root
├── Buildings
│   ├── Building A
│   │   ├── Floor 1
│   │   ├── Floor 2
│   │   └── Parking
│   └── Building B
├── Perimeter
│   ├── North Gate
│   ├── South Gate  
│   └── Fence Line
└── Traffic
    ├── Highway Cameras
    ├── Intersection Cameras
    └── Parking Cameras
```

#### Smart Tagging System

```json
{
  "auto_tags": {
    "by_location": {
      "indoor": ["building", "interior"],
      "outdoor": ["weather", "exterior"],  
      "parking": ["vehicle", "security"]
    },
    "by_camera_type": {
      "ptz": ["controllable", "priority"],
      "fixed": ["static", "monitoring"],
      "thermal": ["specialty", "night"]
    },
    "by_function": {
      "security": ["surveillance", "recording"],
      "traffic": ["counting", "public"],
      "safety": ["emergency", "restricted"]
    }
  }
}
```

---

## User Management & Access Control

### Five-Tier Permission System

| Tier | Level | Capabilities | Typical Users |
|------|-------|-------------|---------------|
| 1 | View Only | Live view, no controls | Public, media |
| 2 | Basic Operator | View + snapshots | Partner agencies |
| 3 | Advanced Operator | View + PTZ control | Emergency responders |
| 4 | Supervisor | All operations + user management | Agency supervisors |
| 5 | Administrator | Full system control | IT administrators |

### User Management

#### Creating User Accounts

```json
{
  "username": "john.doe",
  "email": "john.doe@agency.gov",
  "first_name": "John",
  "last_name": "Doe",
  "agency": "City Police Department",
  "department": "Traffic Division",
  "permission_tier": 3,
  "camera_access": {
    "groups": ["traffic_cameras", "downtown"],
    "individual": ["cam_001", "cam_045"],
    "restrictions": {
      "time_based": {
        "days": ["monday", "tuesday", "wednesday", "thursday", "friday"],
        "hours": "06:00-18:00"
      },
      "location_based": {
        "allowed_zones": ["zone_downtown", "zone_traffic"],
        "denied_zones": ["zone_classified"]
      }
    }
  },
  "enabled": true,
  "password_reset_required": true
}
```

#### Agency-Based Organization

```json
{
  "agencies": [
    {
      "name": "State Police",
      "code": "SP",
      "tier": 5,
      "camera_access": "all",
      "can_create_users": true,
      "can_manage_cameras": true
    },
    {
      "name": "City Police", 
      "code": "CPD",
      "tier": 4,
      "camera_access": ["city_cameras", "shared_cameras"],
      "can_create_users": false,
      "can_manage_cameras": false
    },
    {
      "name": "Fire Department",
      "code": "FD", 
      "tier": 3,
      "camera_access": ["emergency_only"],
      "can_create_users": false,
      "can_manage_cameras": false
    }
  ]
}
```

### Advanced Access Controls

#### Time-Based Access

```json
{
  "time_restrictions": {
    "business_hours": {
      "days": ["monday", "tuesday", "wednesday", "thursday", "friday"],
      "hours": "08:00-17:00",
      "timezone": "America/New_York"
    },
    "emergency_override": {
      "enabled": true,
      "escalation_required": true,
      "max_duration": 3600
    }
  }
}
```

#### Geographic Restrictions

```json
{
  "geo_restrictions": {
    "allowed_ip_ranges": [
      "192.168.1.0/24",
      "10.0.0.0/8", 
      "172.16.0.0/12"
    ],
    "country_restrictions": {
      "allowed": ["US"],
      "denied": []
    },
    "vpn_detection": {
      "block_vpn": false,
      "require_known_vpn": true,
      "allowed_vpn_ips": ["vpn.agency.gov"]
    }
  }
}
```

---

## Stream Management

### Protocol Configuration

#### HLS (HTTP Live Streaming)
```json
{
  "hls_settings": {
    "segment_duration": 4,
    "playlist_length": 6,
    "allow_cache": true,
    "encryption": {
      "enabled": false,
      "method": "AES-128",
      "key_rotation": 3600
    },
    "adaptive_bitrate": {
      "enabled": true,
      "profiles": [
        {"bitrate": 500, "resolution": "640x360"},
        {"bitrate": 1000, "resolution": "1280x720"},
        {"bitrate": 2000, "resolution": "1920x1080"}
      ]
    }
  }
}
```

#### WebRTC (Ultra-Low Latency)
```json
{
  "webrtc_settings": {
    "stun_servers": ["stun:stun.l.google.com:19302"],
    "turn_servers": [
      {
        "url": "turn:turn.example.com:3478",
        "username": "user",
        "credential": "password"
      }
    ],
    "codec_preference": ["VP8", "H264"],
    "max_bitrate": 2500,
    "enable_simulcast": true
  }
}
```

#### SRT (Secure Reliable Transport)
```json
{
  "srt_settings": {
    "mode": "listener",
    "port": 9999,
    "encryption": {
      "passphrase": "your-secure-passphrase",
      "key_length": 256
    },
    "latency": 1000,
    "recovery_window": 0,
    "bandwidth_overhead": 25
  }
}
```

### Stream Quality Management

#### Adaptive Bitrate Profiles

```json
{
  "abr_profiles": {
    "public_access": {
      "max_bitrate": 1000,
      "resolutions": ["640x360", "1280x720"],
      "fallback_enabled": true
    },
    "agency_standard": {
      "max_bitrate": 2000,
      "resolutions": ["1280x720", "1920x1080"],
      "fallback_enabled": true  
    },
    "investigation": {
      "max_bitrate": 5000,
      "resolutions": ["1920x1080", "2560x1440"],
      "fallback_enabled": false
    }
  }
}
```

#### Stream Health Monitoring

```json
{
  "health_monitoring": {
    "check_interval": 30,
    "thresholds": {
      "bitrate_deviation": 20,
      "frame_drop_rate": 5,
      "connection_timeout": 60
    },
    "actions": {
      "restart_stream": true,
      "send_alert": true,
      "fallback_source": true
    },
    "recovery": {
      "max_restarts": 3,
      "restart_delay": 30,
      "escalation_timeout": 300
    }
  }
}
```

---

## One-Time Password (OTP) System

The OTP system enables secure, temporary access without creating permanent user accounts - perfect for emergency situations and inter-agency cooperation.

### OTP Generation

#### Creating OTP Tokens

```bash
# Via API
POST /api/otp/generate
{
  "description": "Emergency Response - Highway Incident",
  "cameras": ["cam_highway_001", "cam_highway_002", "cam_highway_003"],
  "permission_level": 2,
  "duration_hours": 4,
  "max_uses": 10,
  "allowed_ips": ["192.168.10.0/24"],
  "requester": {
    "name": "Chief Johnson",
    "agency": "State Police",
    "contact": "johnson@statepolice.gov"
  }
}

# Response
{
  "token": "OTP-2025-0123-A7B9C2E4",
  "url": "https://media-router.agency.gov/otp/OTP-2025-0123-A7B9C2E4", 
  "expires": "2025-01-23T20:00:00Z",
  "cameras_included": 3,
  "permission_level": 2
}
```

#### OTP Templates

```json
{
  "otp_templates": {
    "emergency_response": {
      "description": "Emergency Response Access",
      "duration_hours": 8,
      "permission_level": 3,
      "cameras": "@group:emergency_cameras",
      "auto_approve": true
    },
    "media_event": {
      "description": "Media Event Coverage",
      "duration_hours": 2,
      "permission_level": 1,
      "cameras": "@group:public_cameras",
      "auto_approve": false
    },
    "partner_agency": {
      "description": "Partner Agency Access",
      "duration_hours": 24,
      "permission_level": 2,
      "cameras": "@group:shared_cameras",
      "auto_approve": false
    }
  }
}
```

### OTP Usage Tracking

#### Access Monitoring

```json
{
  "otp_tracking": {
    "token": "OTP-2025-0123-A7B9C2E4",
    "created": "2025-01-23T16:00:00Z",
    "expires": "2025-01-23T20:00:00Z",
    "usage_stats": {
      "total_accesses": 15,
      "unique_ips": 3,
      "cameras_viewed": ["cam_highway_001", "cam_highway_002"],
      "last_access": "2025-01-23T18:45:00Z",
      "bandwidth_used": "2.5 GB"
    },
    "access_log": [
      {
        "timestamp": "2025-01-23T16:05:00Z",
        "ip": "192.168.10.15",
        "user_agent": "Chrome/96.0",
        "camera": "cam_highway_001",
        "duration": 300
      }
    ]
  }
}
```

### OTP Security Features

#### Restrictions and Limits

```json
{
  "otp_security": {
    "max_concurrent_sessions": 5,
    "ip_whitelist": ["192.168.10.0/24"],
    "geolocation_required": false,
    "user_agent_tracking": true,
    "screenshot_prevention": true,
    "watermarking": {
      "enabled": true,
      "include_timestamp": true,
      "include_token": true,
      "position": "bottom_right"
    }
  }
}
```

---

## VMS Integration

### Genetec Security Center Integration

#### Initial Setup

```bash
# Configure Genetec connection
POST /api/vms/genetec/configure
{
  "server_ip": "192.168.1.100",
  "server_port": 443,
  "username": "media_router_service",
  "password": "secure_password",
  "certificate_validation": true,
  "auto_discovery": true,
  "sync_interval": 300
}
```

#### Camera Discovery and Import

```json
{
  "genetec_import": {
    "import_settings": {
      "include_offline": false,
      "include_archived": true,
      "metadata_sync": true,
      "ptz_support": true
    },
    "filter_criteria": {
      "camera_types": ["IP", "Analog"],
      "locations": ["Building A", "Perimeter"],
      "exclude_patterns": ["test*", "*backup*"]
    },
    "grouping": {
      "by_location": true,
      "by_camera_type": true,
      "custom_groups": {
        "Critical": ["front_gate*", "*entrance*"],
        "Public": ["lobby*", "*public*"]
      }
    }
  }
}
```

#### Advanced Genetec Features

```json
{
  "genetec_advanced": {
    "federation": {
      "enabled": true,
      "federated_servers": [
        "genetec-server-2.local",
        "genetec-server-3.local"  
      ],
      "credential_sharing": true
    },
    "archiver_integration": {
      "enabled": true,
      "playback_support": true,
      "export_support": true,
      "bookmark_sync": true
    },
    "event_integration": {
      "alarms": true,
      "motion_events": true,
      "analytics_events": true,
      "custom_events": ["door_open", "access_denied"]
    }
  }
}
```

### Milestone XProtect Integration

#### Configuration

```json
{
  "milestone_config": {
    "management_server": {
      "address": "milestone-mgmt.local",
      "port": 80,
      "use_https": false
    },
    "recording_servers": [
      {
        "name": "RecServer-1",
        "address": "milestone-rec1.local", 
        "cameras": ["*building_a*"],
        "priority": 1
      },
      {
        "name": "RecServer-2", 
        "address": "milestone-rec2.local",
        "cameras": ["*building_b*"],
        "priority": 2
      }
    ],
    "authentication": {
      "type": "windows",
      "domain": "COMPANY",
      "username": "media_router",
      "password": "secure_password"
    }
  }
}
```

#### Smart Client Integration

```json
{
  "smart_client_integration": {
    "plugin_enabled": true,
    "shared_views": true,
    "bookmark_sync": true,
    "export_integration": true,
    "evidence_lock": {
      "respect_locks": true,
      "create_locks": false,
      "lock_duration": 3600
    }
  }
}
```

### Universal Camera Support

#### ONVIF Integration

```json
{
  "onvif_support": {
    "profiles": ["S", "G", "T"],
    "discovery": {
      "multicast": true,
      "port_scan": true,
      "network_ranges": ["192.168.1.0/24", "10.0.0.0/24"]
    },
    "features": {
      "ptz_control": true,
      "preset_management": true,
      "event_handling": true,
      "metadata_extraction": true
    }
  }
}
```

#### Manufacturer-Specific APIs

```json
{
  "manufacturer_apis": {
    "axis": {
      "vapix_enabled": true,
      "parameter_management": true,
      "event_notifications": true
    },
    "hikvision": {
      "isapi_enabled": true,
      "smart_events": true,
      "face_detection": true
    },
    "dahua": {
      "http_api": true,
      "ivs_events": true,
      "alarm_integration": true
    }
  }
}
```

---

## Analytics Integration

### WINK Analytics Add-on

#### Configuration

```json
{
  "wink_analytics": {
    "enabled": true,
    "license_key": "your-analytics-license-key",
    "processing_mode": "gpu",
    "max_concurrent_streams": 50,
    "analytics_types": [
      "object_detection",
      "behavior_analysis", 
      "crowd_analytics",
      "perimeter_protection",
      "search"
    ]
  }
}
```

#### Object Detection Settings

```json
{
  "object_detection": {
    "enabled_objects": [
      "person", "vehicle", "bicycle", "weapon", "package", "face"
    ],
    "confidence_threshold": 0.7,
    "track_objects": true,
    "max_track_duration": 300,
    "zone_based": {
      "enabled": true,
      "zones": [
        {
          "name": "entrance_zone",
          "coordinates": [[100,100], [500,100], [500,400], [100,400]],
          "objects": ["person", "vehicle"],
          "alerts": ["zone_entry", "zone_exit", "loitering"]
        }
      ]
    }
  }
}
```

#### Behavior Analysis

```json
{
  "behavior_analysis": {
    "behaviors": {
      "loitering": {
        "enabled": true,
        "threshold_seconds": 60,
        "zones": ["entrance_zone", "parking_zone"]
      },
      "running": {
        "enabled": true,
        "speed_threshold": 8.0,
        "zones": ["all"]
      },
      "crowd_formation": {
        "enabled": true,
        "person_threshold": 10,
        "density_threshold": 0.8
      },
      "fighting": {
        "enabled": true,
        "confidence_required": 0.85,
        "immediate_alert": true
      }
    }
  }
}
```

### WINK AI Traffic Add-on

#### Traffic Analytics Configuration

```json
{
  "ai_traffic": {
    "enabled": true,
    "license_key": "your-traffic-ai-license-key",
    "detection_modes": [
      "vehicle_counting",
      "speed_detection",
      "classification",
      "incident_detection",
      "wrong_way",
      "license_plates"
    ],
    "vehicle_classes": "fhwa_13_class",
    "speed_units": "mph"
  }
}
```

#### Vehicle Classification

```json
{
  "vehicle_classification": {
    "fhwa_classes": {
      "1": "motorcycles",
      "2": "passenger_cars", 
      "3": "other_two_axle",
      "4": "buses",
      "5": "two_axle_six_tire",
      "6": "three_axle",
      "7": "four_or_more_axle",
      "8": "four_or_fewer_axle",
      "9": "five_axle",
      "10": "six_or_more_axle",
      "11": "five_or_fewer_axle_multi",
      "12": "six_axle_multi", 
      "13": "seven_or_more_axle_multi"
    },
    "confidence_threshold": 0.8,
    "track_across_frames": true
  }
}
```

#### Speed Detection

```json
{
  "speed_detection": {
    "method": "optical_flow",
    "calibration": {
      "reference_distance_feet": 50,
      "pixel_distance": 200,
      "camera_height_feet": 20,
      "camera_angle_degrees": 15
    },
    "speed_limits": {
      "zones": [
        {
          "name": "highway_zone",
          "speed_limit": 65,
          "enforcement_threshold": 10
        },
        {
          "name": "residential_zone", 
          "speed_limit": 25,
          "enforcement_threshold": 5
        }
      ]
    }
  }
}
```

### Analytics Data Export

#### Real-time Events

```json
{
  "event_export": {
    "formats": ["json", "xml", "csv"],
    "destinations": [
      {
        "type": "webhook",
        "url": "https://agency.gov/api/analytics",
        "authentication": "bearer_token",
        "events": ["person_detected", "vehicle_speeding", "weapon_detected"]
      },
      {
        "type": "database",
        "connection": "postgresql://user:pass@db:5432/analytics",
        "table": "detection_events",
        "events": ["all"]
      }
    ]
  }
}
```

#### Historical Reports

```json
{
  "analytics_reports": {
    "scheduled_reports": [
      {
        "name": "Daily Traffic Summary",
        "schedule": "0 6 * * *",
        "cameras": ["traffic_*"],
        "metrics": ["vehicle_count", "average_speed", "classification_breakdown"],
        "format": "pdf",
        "recipients": ["traffic@agency.gov"]
      }
    ],
    "on_demand_reports": {
      "max_duration_days": 90,
      "formats": ["pdf", "csv", "json"],
      "visualizations": ["charts", "heatmaps", "timelines"]
    }
  }
}
```

---

## High Availability

### VRRP Configuration

#### Primary/Secondary Setup

```json
{
  "vrrp_config": {
    "enabled": true,
    "virtual_ip": "192.168.1.200",
    "interface": "eth0",
    "priority": {
      "primary": 110,
      "secondary": 100
    },
    "advertisement_interval": 1,
    "authentication": {
      "type": "password",
      "password": "vrrp-secure-password"
    }
  }
}
```

#### Failover Configuration

```json
{
  "failover": {
    "health_checks": {
      "interval_seconds": 5,
      "timeout_seconds": 3,
      "failure_threshold": 3,
      "checks": [
        {"type": "service", "name": "wink-media-router"},
        {"type": "port", "port": 443},
        {"type": "database", "timeout": 5},
        {"type": "disk_space", "threshold": 90}
      ]
    },
    "transition": {
      "takeover_delay": 10,
      "preempt_enabled": true,
      "preempt_delay": 60,
      "notification_enabled": true
    }
  }
}
```

### Load Balancing

#### Multi-Instance Deployment

```yaml
# docker-compose.yml for load-balanced deployment
version: '3.8'
services:
  media-router-1:
    image: wink/media-router:3.0
    ports:
      - "8001:80"
    environment:
      - INSTANCE_ID=mr1
      - DATABASE_URL=postgresql://user:pass@db:5432/wink_mr
      - REDIS_URL=redis://redis:6379
      
  media-router-2:
    image: wink/media-router:3.0  
    ports:
      - "8002:80"
    environment:
      - INSTANCE_ID=mr2
      - DATABASE_URL=postgresql://user:pass@db:5432/wink_mr
      - REDIS_URL=redis://redis:6379
      
  nginx:
    image: nginx:alpine
    ports:
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/ssl
    depends_on:
      - media-router-1
      - media-router-2
```

#### Load Balancer Configuration

```nginx
# nginx.conf
upstream media_routers {
    least_conn;
    server media-router-1:80 max_fails=3 fail_timeout=30s;
    server media-router-2:80 max_fails=3 fail_timeout=30s;
}

server {
    listen 443 ssl;
    server_name media-router.agency.gov;
    
    ssl_certificate /etc/ssl/server.crt;
    ssl_certificate_key /etc/ssl/server.key;
    
    location / {
        proxy_pass http://media_routers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support for real-time updates
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
    
    # HLS streams
    location ~* \.(m3u8|ts)$ {
        proxy_pass http://media_routers;
        expires -1;
        add_header Cache-Control no-cache;
    }
}
```

### Data Backup and Recovery

#### Automated Backup

```bash
#!/bin/bash
# backup-media-router.sh

BACKUP_DIR="/backup/media-router"
DATE=$(date +%Y%m%d-%H%M%S)

# Database backup
pg_dump -h localhost -U postgres wink_mr > "$BACKUP_DIR/db-$DATE.sql"

# Configuration backup  
tar -czf "$BACKUP_DIR/config-$DATE.tar.gz" /opt/wink-media-router/config

# Certificates and keys
tar -czf "$BACKUP_DIR/ssl-$DATE.tar.gz" /opt/wink-media-router/ssl

# Cleanup old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.sql" -mtime +30 -delete
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $DATE"
```

#### Disaster Recovery

```bash
#!/bin/bash
# restore-media-router.sh

BACKUP_DIR="/backup/media-router"
RESTORE_DATE="20250123-120000"

# Stop services
sudo systemctl stop wink-media-router

# Restore database
psql -h localhost -U postgres -d wink_mr < "$BACKUP_DIR/db-$RESTORE_DATE.sql"

# Restore configuration
tar -xzf "$BACKUP_DIR/config-$RESTORE_DATE.tar.gz" -C /

# Restore certificates
tar -xzf "$BACKUP_DIR/ssl-$RESTORE_DATE.tar.gz" -C /

# Start services
sudo systemctl start wink-media-router

echo "Restore completed from: $RESTORE_DATE"
```

---

## Monitoring & Troubleshooting

### System Health Monitoring

#### Health Check Endpoints

```bash
# Overall system health
GET /api/health
{
  "status": "healthy",
  "version": "3.0.1", 
  "uptime": "15d 4h 23m",
  "cameras": {
    "total": 15847,
    "online": 15234,
    "offline": 613,
    "health_score": 96.1
  },
  "streams": {
    "active": 2341,
    "concurrent_limit": 5000,
    "utilization": 46.8
  },
  "resources": {
    "cpu_usage": 34.2,
    "memory_usage": 67.8,
    "disk_usage": 45.3,
    "network_throughput": "1.2 Gbps"
  }
}

# Detailed camera health
GET /api/cameras/health
[
  {
    "camera_id": "cam_001", 
    "status": "online",
    "last_seen": "2025-01-23T15:30:00Z",
    "stream_quality": 98.5,
    "bitrate": 1987,
    "framerate": 14.8,
    "issues": []
  },
  {
    "camera_id": "cam_002",
    "status": "degraded",
    "last_seen": "2025-01-23T15:29:45Z", 
    "stream_quality": 76.2,
    "bitrate": 1456,
    "framerate": 11.2,
    "issues": ["packet_loss", "low_framerate"]
  }
]
```

#### Performance Metrics

```json
{
  "performance_metrics": {
    "stream_metrics": {
      "total_bandwidth": "15.2 Gbps",
      "average_bitrate": 1847,
      "peak_concurrent_viewers": 3421,
      "stream_start_time": 2.3,
      "buffering_events": 0.02
    },
    "system_metrics": {
      "cpu_cores_used": 12,
      "memory_committed": "24.5 GB", 
      "disk_iops": 2847,
      "network_packets_sec": 125847,
      "database_connections": 45
    },
    "error_metrics": {
      "stream_failures": 23,
      "authentication_failures": 3,
      "api_errors": 12,
      "system_errors": 1
    }
  }
}
```

### Alerting and Notifications

#### Alert Configuration

```json
{
  "alerts": {
    "camera_offline": {
      "enabled": true,
      "threshold": "1_minute",
      "recipients": ["admin@agency.gov"],
      "severity": "warning"
    },
    "high_cpu": {
      "enabled": true,
      "threshold": 85,
      "duration": "5_minutes",
      "recipients": ["admin@agency.gov"],
      "severity": "warning"
    },
    "stream_failure": {
      "enabled": true,
      "threshold": 5,
      "timeframe": "1_hour",
      "recipients": ["admin@agency.gov", "oncall@agency.gov"],
      "severity": "critical"
    },
    "disk_space": {
      "enabled": true,
      "threshold": 90,
      "recipients": ["admin@agency.gov"],
      "severity": "critical"
    },
    "authentication_failures": {
      "enabled": true,
      "threshold": 10,
      "timeframe": "5_minutes",
      "recipients": ["security@agency.gov"],
      "severity": "high"
    }
  }
}
```

#### Notification Channels

```json
{
  "notification_channels": {
    "email": {
      "smtp_server": "smtp.agency.gov",
      "port": 587,
      "username": "media-router@agency.gov", 
      "password": "smtp_password",
      "from_address": "media-router@agency.gov"
    },
    "slack": {
      "webhook_url": "https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK",
      "channel": "#it-alerts",
      "username": "Media Router"
    },
    "sms": {
      "provider": "twilio",
      "account_sid": "your_account_sid",
      "auth_token": "your_auth_token",
      "from_number": "+1234567890"
    }
  }
}
```

### Common Troubleshooting

#### Camera Connection Issues

```bash
# Check camera connectivity
curl -v "rtsp://camera-ip:554/stream1"

# Test ONVIF discovery
curl -X POST http://camera-ip/onvif/device_service \
  -H "Content-Type: text/xml" \
  -d '<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><GetDeviceInformation xmlns="http://www.onvif.org/ver10/device/wsdl"/></s:Body></s:Envelope>'

# Check Media Router camera status
curl -k "https://media-router/api/cameras/cam_001/test"
```

#### Stream Quality Issues

```bash
# Analyze stream quality
GET /api/streams/cam_001/analyze
{
  "stream_id": "cam_001",
  "quality_metrics": {
    "bitrate_stability": 87.3,
    "framerate_consistency": 94.1, 
    "packet_loss_rate": 0.3,
    "jitter": 12.4,
    "latency": 156
  },
  "recommendations": [
    "Consider reducing bitrate during peak hours",
    "Check network connection for packet loss",
    "Verify camera keyframe interval settings"
  ]
}
```

#### Performance Optimization

```bash
# CPU optimization
echo 'performance' | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Network optimization for video streaming
echo 'net.core.rmem_max = 134217728' >> /etc/sysctl.conf
echo 'net.core.wmem_max = 134217728' >> /etc/sysctl.conf  
echo 'net.ipv4.tcp_rmem = 4096 87380 134217728' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_wmem = 4096 65536 134217728' >> /etc/sysctl.conf
sysctl -p

# Database optimization
echo "shared_buffers = 4GB" >> /etc/postgresql/14/main/postgresql.conf
echo "effective_cache_size = 12GB" >> /etc/postgresql/14/main/postgresql.conf
sudo systemctl restart postgresql
```

### Log Analysis

#### Application Logs

```bash
# View recent logs
sudo journalctl -u wink-media-router -f

# Search for specific errors
sudo journalctl -u wink-media-router | grep -i "error\|failed\|timeout"

# Export logs for analysis
sudo journalctl -u wink-media-router --since "1 hour ago" --output=json > /tmp/mr-logs.json
```

#### Stream Access Logs

```bash
# Analyze stream access patterns
tail -f /var/log/wink-media-router/access.log | grep -E "\.m3u8|\.ts"

# Top viewed cameras
awk '{print $7}' /var/log/wink-media-router/access.log | grep -E "cam_" | sort | uniq -c | sort -nr | head -10

# Bandwidth usage by camera
awk '{bytes+=$10} END {print "Total bytes:", bytes}' /var/log/wink-media-router/access.log
```

---

## API Reference

### Authentication

#### API Key Authentication

```bash
# Create API key
POST /api/auth/keys
{
  "name": "Integration Key",
  "permissions": ["cameras:read", "streams:read", "users:write"],
  "expires": "2025-12-31T23:59:59Z"
}

# Use API key
curl -H "Authorization: Bearer your-api-key" \
     https://media-router.agency.gov/api/cameras
```

#### Session Authentication

```bash
# Login
POST /api/auth/login
{
  "username": "admin",
  "password": "your-password"
}

# Response includes session token
{
  "token": "session-token-here",
  "expires": "2025-01-23T20:00:00Z",
  "user": {
    "username": "admin",
    "tier": 5,
    "permissions": ["*"]
  }
}
```

### Core API Endpoints

#### Camera Management

```bash
# List all cameras
GET /api/cameras
GET /api/cameras?limit=100&offset=0&status=online&tag=traffic

# Get specific camera
GET /api/cameras/{camera_id}

# Add camera
POST /api/cameras
{
  "name": "New Camera",
  "ip_address": "192.168.1.100",
  "username": "admin",
  "password": "password", 
  "location": "Building A",
  "tags": ["indoor", "security"]
}

# Update camera
PUT /api/cameras/{camera_id}
{
  "name": "Updated Camera Name",
  "tags": ["indoor", "security", "priority"]
}

# Delete camera
DELETE /api/cameras/{camera_id}

# Bulk operations
POST /api/cameras/bulk
{
  "operation": "update",
  "filter": {"tags": ["traffic"]},
  "changes": {"permission_level": 1}
}
```

#### Stream Management

```bash
# Get stream info
GET /api/streams/{camera_id}

# Start stream
POST /api/streams/{camera_id}/start
{
  "protocol": "hls",
  "quality": "720p",
  "bitrate": 1500
}

# Stop stream  
POST /api/streams/{camera_id}/stop

# Get stream URL
GET /api/streams/{camera_id}/url?protocol=hls&quality=720p
{
  "url": "https://media-router.agency.gov/hls/cam_001/stream.m3u8",
  "expires": "2025-01-23T20:00:00Z",
  "protocol": "hls",
  "bitrate": 1500
}
```

#### User Management

```bash
# List users
GET /api/users
GET /api/users?agency=police&tier=3

# Create user
POST /api/users
{
  "username": "john.doe",
  "email": "john@agency.gov",
  "agency": "Police",
  "tier": 3,
  "camera_access": ["group:traffic_cameras"]
}

# Update user
PUT /api/users/{user_id}
{
  "tier": 4,
  "camera_access": ["group:all_cameras"]
}

# Delete user
DELETE /api/users/{user_id}
```

#### OTP Management

```bash
# Generate OTP
POST /api/otp/generate
{
  "description": "Emergency Access",
  "cameras": ["cam_001", "cam_002"],
  "duration_hours": 4,
  "permission_level": 2
}

# List active OTPs
GET /api/otp/active

# Revoke OTP
DELETE /api/otp/{token}

# OTP usage statistics
GET /api/otp/{token}/stats
```

### WebSocket API

#### Real-time Updates

```javascript
// Connect to WebSocket
const ws = new WebSocket('wss://media-router.agency.gov/ws');

// Authenticate
ws.send(JSON.stringify({
  type: 'auth',
  token: 'your-session-token'
}));

// Subscribe to camera events
ws.send(JSON.stringify({
  type: 'subscribe',
  channel: 'cameras',
  cameras: ['cam_001', 'cam_002']
}));

// Handle messages
ws.onmessage = function(event) {
  const data = JSON.parse(event.data);
  
  switch(data.type) {
    case 'camera_status':
      console.log(`Camera ${data.camera_id} is now ${data.status}`);
      break;
      
    case 'stream_quality':
      console.log(`Stream quality for ${data.camera_id}: ${data.quality}%`);
      break;
      
    case 'alert':
      console.log(`Alert: ${data.message}`);
      break;
  }
};
```

---

## Appendix

### Default Port Configuration

| Service | Port | Protocol | Purpose |
|---------|------|----------|---------|
| Web Interface | 443 | HTTPS | Management interface |
| API | 443 | HTTPS | REST API |
| WebSocket | 443 | WSS | Real-time updates |
| HLS Streaming | 443 | HTTPS | HTTP Live Streaming |
| RTMP Input | 1935 | TCP | RTMP stream ingestion |
| RTSP Input | 8554 | TCP | RTSP stream ingestion | 
| SRT Input | 9999 | UDP | SRT stream ingestion |
| WebRTC | 3478 | UDP | STUN/TURN |

### Configuration File Locations

```
/opt/wink-media-router/
├── config/
│   ├── main.conf          # Primary configuration
│   ├── cameras.json       # Camera definitions  
│   ├── users.json         # User accounts
│   ├── vms.conf          # VMS integration settings
│   └── analytics.conf     # Analytics configuration
├── ssl/
│   ├── server.crt        # SSL certificate
│   ├── server.key        # SSL private key  
│   └── ca-bundle.crt     # Certificate authority bundle
├── logs/
│   ├── application.log   # Application logs
│   ├── access.log        # Stream access logs
│   ├── error.log         # Error logs
│   └── audit.log         # Security audit logs
└── data/
    ├── database/         # Embedded database files
    ├── cache/           # Stream cache
    └── exports/         # Report exports
```

### Environment Variables

```bash
# Core settings
WINK_MR_MODE=production
WINK_MR_INTERFACE=0.0.0.0
WINK_MR_PORT=443
WINK_MR_SSL_ENABLED=true

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/wink_mr
REDIS_URL=redis://localhost:6379

# Security  
SESSION_SECRET=your-secret-key-here
JWT_SECRET=your-jwt-secret-here
ENCRYPTION_KEY=your-encryption-key-here

# External integrations
GENETEC_SDK_PATH=/opt/genetec-sdk
MILESTONE_SDK_PATH=/opt/milestone-sdk

# Performance
MAX_STREAMS=1000
WORKER_PROCESSES=auto
CACHE_SIZE=1GB

# Logging
LOG_LEVEL=INFO
LOG_FORMAT=json
AUDIT_ENABLED=true
```

### Sample Configuration Files

#### main.conf
```ini
[system]
name = Agency Media Router
timezone = America/New_York
max_cameras = 20000
max_streams = 5000

[network]
interface = eth0
port = 443  
ssl_enabled = true
ssl_cert = /opt/wink-media-router/ssl/server.crt
ssl_key = /opt/wink-media-router/ssl/server.key

[database]
type = postgresql
host = localhost
port = 5432
database = wink_mr
username = wink_user
password = secure_password
max_connections = 100

[streaming]
default_protocol = hls
segment_duration = 4
adaptive_bitrate = true
cache_enabled = true
cache_duration = 300

[security]
session_timeout = 3600
max_failed_logins = 5
password_complexity = high
require_2fa = false
audit_enabled = true

[monitoring]
health_check_interval = 30
alert_email = admin@agency.gov
smtp_server = smtp.agency.gov
```

### License Information

WINK Media Router requires proper licensing for full functionality:

- **Base License:** Included with purchase, supports up to 100 cameras
- **Scale License:** Required for 100+ cameras, contact sales for pricing
- **Analytics License:** Required for WINK Analytics integration
- **HA License:** Required for high availability features

License validation occurs during startup and periodically during operation. For air-gapped environments, offline license files are available.

### Support and Resources

- **Documentation:** https://docs.wink.co/media-router
- **Support Portal:** https://support.wink.co
- **Emergency Support:** +1-312-281-5433
- **Community Forums:** https://community.wink.co
- **Training Resources:** https://training.wink.co

### Changelog

#### Version 3.0.1 (Current)
- Improved OTP token generation security
- Enhanced Genetec SDK compatibility  
- Fixed WebRTC connection issues on mobile devices
- Added bulk camera import validation
- Performance improvements for 10,000+ camera deployments

#### Version 3.0.0
- Complete platform rewrite for scalability
- New five-tier permission system
- OTP authentication system
- Native analytics integration
- VRRP high availability support
- Enhanced VMS integration

For complete changelog and upgrade instructions, visit: https://docs.wink.co/media-router/changelog

---

*© 2025 WINK Streaming. All rights reserved. This document contains proprietary information and is subject to change without notice.*