# WINK Virtual Appliance Hardware Requirements 2025

*Comprehensive Infrastructure Sizing Guide for WINK Streaming Products*

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Hardware Sizing Overview](#hardware-sizing-overview)
3. [WINK Forge Requirements](#wink-forge-requirements)
4. [WINK Media Router Requirements](#wink-media-router-requirements)
5. [WINK Analytics Requirements](#wink-analytics-requirements)
6. [WINK AI Traffic Requirements](#wink-ai-traffic-requirements)
7. [Combined Deployment Scenarios](#combined-deployment-scenarios)
8. [Virtualization Considerations](#virtualization-considerations)
9. [Network Infrastructure](#network-infrastructure)
10. [Storage Requirements](#storage-requirements)
11. [Deployment Examples](#deployment-examples)
12. [Monitoring and Maintenance](#monitoring-and-maintenance)

---

## Executive Summary

WINK Streaming products are designed to scale from small pilot deployments to massive enterprise installations managing thousands of video streams. This document provides comprehensive hardware requirements and sizing guidelines for virtual appliance deployments across VMware, Hyper-V, KVM, and cloud platforms.

### Quick Sizing Reference

| Deployment Scale | Streams | CPU Cores | RAM | Storage | Network |
|------------------|---------|-----------|-----|---------|---------|
| Small (1-50) | 50 | 4-8 | 16-32 GB | 500 GB | 1 Gbps |
| Medium (51-500) | 500 | 8-16 | 32-64 GB | 1-2 TB | 10 Gbps |
| Large (501-2000) | 2000 | 16-32 | 64-128 GB | 2-5 TB | 10-40 Gbps |
| Enterprise (2000+) | 5000+ | 32+ | 128+ GB | 5+ TB | 40+ Gbps |

### Platform Compatibility

- **VMware vSphere:** 6.5+ (7.0+ recommended)
- **Microsoft Hyper-V:** 2016+ (2019+ recommended)  
- **KVM/QEMU:** RHEL 8+, Ubuntu 20.04+
- **Public Cloud:** AWS, Azure, GCP (specific instance recommendations included)
- **Container:** Docker, Kubernetes (resource limits apply)

---

## Hardware Sizing Overview

### CPU Requirements

WINK products are CPU-intensive applications that perform real-time video processing, transcoding, and analysis. CPU requirements scale approximately linearly with the number of concurrent streams.

#### CPU Scaling Factors

| Processing Type | CPU Cores per Stream | Notes |
|-----------------|---------------------|--------|
| Pass-through (no transcoding) | 0.02 | Minimal processing |
| H.264 Transcode | 0.1-0.15 | Standard definition |
| H.264 HD Transcode | 0.2-0.3 | 1080p processing |
| H.264 4K Transcode | 0.8-1.2 | Ultra-high definition |
| Analytics Processing | 0.3-0.5 | Object detection |
| AI Traffic Analysis | 0.4-0.6 | FHWA classification |

#### Recommended CPU Specifications

- **Architecture:** x86_64 (Intel/AMD)
- **Minimum Frequency:** 2.4 GHz base clock
- **Recommended:** 3.0+ GHz with boost/turbo
- **Features Required:** SSE4.2, AVX (AVX2 preferred)
- **Hardware Acceleration:** Intel Quick Sync or AMD VCE beneficial but not required

### Memory Requirements

Memory usage scales with concurrent streams and processing complexity. WINK products use efficient memory management with configurable caching.

#### Memory Scaling
```
Base OS + Application: 4-8 GB
Per Stream Buffer: 15-25 MB
Analytics Models: 2-8 GB (per AI module)
Cache and Buffers: 20-40% of total streams
```

#### Memory Calculation Formula
```
Total RAM = Base (8 GB) + (Streams × 20 MB) + Analytics Memory + 25% overhead
```

### Storage Requirements

Storage needs vary significantly based on caching, logging, and analytics data retention requirements.

#### Storage Categories
- **Operating System:** 50-100 GB
- **Application Files:** 100-200 GB  
- **Stream Cache:** 1-5 GB per concurrent stream
- **Logs and Analytics:** 10-50 GB per day
- **Database:** 100 MB - 10 GB depending on metadata

---

## WINK Forge Requirements

WINK Forge is the universal video transcoder that handles protocol conversion, quality optimization, and stream distribution.

### Minimum Requirements
- **CPU:** 4 cores, 2.4 GHz
- **RAM:** 16 GB
- **Storage:** 500 GB SSD
- **Network:** 1 Gbps
- **Streams:** Up to 25 concurrent

### Recommended Small Deployment (1-50 Streams)
- **CPU:** 8 cores, 3.0 GHz (Intel Xeon or AMD EPYC)
- **RAM:** 32 GB DDR4
- **Storage:** 1 TB NVMe SSD
- **Network:** 10 Gbps (or dual 1 Gbps bonded)
- **GPU:** Optional Intel Quick Sync or NVIDIA T4

### Medium Deployment (51-250 Streams)
- **CPU:** 16 cores, 3.0+ GHz
- **RAM:** 64 GB DDR4
- **Storage:** 2 TB NVMe SSD
- **Network:** 10 Gbps minimum
- **GPU:** NVIDIA T4 or better recommended

### Large Deployment (251-1000 Streams)
- **CPU:** 32 cores, 3.2+ GHz
- **RAM:** 128 GB DDR4  
- **Storage:** 4 TB NVMe SSD (or NVMe RAID)
- **Network:** 25-40 Gbps
- **GPU:** NVIDIA RTX A4000 or better

### Performance Benchmarks

#### H.264 Transcoding Performance
| Hardware Configuration | 720p Streams | 1080p Streams | 4K Streams |
|------------------------|--------------|---------------|-------------|
| 8c/32GB (CPU only) | 45-60 | 25-35 | 6-8 |
| 8c/32GB + Quick Sync | 80-120 | 50-70 | 12-18 |
| 16c/64GB (CPU only) | 90-120 | 50-70 | 12-16 |
| 16c/64GB + T4 GPU | 200+ | 120+ | 30+ |

#### Protocol Overhead
- **RTSP to HLS:** 10-15% CPU overhead
- **RTSP to WebRTC:** 20-25% CPU overhead  
- **Multiple Outputs:** +5-10% per additional protocol

---

## WINK Media Router Requirements

Media Router manages video distribution, access control, and multi-agency sharing without transcoding overhead.

### Minimum Requirements
- **CPU:** 4 cores, 2.4 GHz
- **RAM:** 8 GB
- **Storage:** 250 GB SSD
- **Network:** 1 Gbps
- **Cameras:** Up to 100

### Recommended Small Deployment (1-500 Cameras)
- **CPU:** 8 cores, 2.8 GHz
- **RAM:** 16 GB DDR4
- **Storage:** 500 GB SSD
- **Network:** 10 Gbps
- **Concurrent Viewers:** 50-100

### Medium Deployment (501-2000 Cameras)
- **CPU:** 12 cores, 3.0 GHz
- **RAM:** 32 GB DDR4
- **Storage:** 1 TB SSD
- **Network:** 10-25 Gbps
- **Concurrent Viewers:** 100-500

### Large Deployment (2001-10000 Cameras)
- **CPU:** 16-24 cores, 3.0+ GHz
- **RAM:** 64 GB DDR4
- **Storage:** 2 TB NVMe SSD
- **Network:** 25-100 Gbps
- **Concurrent Viewers:** 500-2000

### Massive Deployment (10000+ Cameras)
- **CPU:** 32+ cores, 3.2+ GHz  
- **RAM:** 128+ GB DDR4
- **Storage:** 4+ TB NVMe SSD
- **Network:** 100+ Gbps (multiple interfaces)
- **Concurrent Viewers:** 2000+

### Performance Characteristics

#### Stream Management Overhead
```
Per Camera Registration: ~1 MB RAM
Per Active Stream: ~15 MB RAM  
Per Concurrent Viewer: ~5 MB RAM
Database Growth: ~1 KB per camera per day
```

#### Network Bandwidth Calculation
```
Required Bandwidth = (Cameras × Avg Stream Bitrate × Simultaneous Viewers) + 20% overhead

Example:
1000 cameras × 1.5 Mbps × 0.1 viewing ratio = 150 Mbps + 30 Mbps overhead = 180 Mbps
```

---

## WINK Analytics Requirements

WINK Analytics adds computer vision and behavioral analysis capabilities to video streams.

### Minimum Requirements (Development)
- **CPU:** 8 cores, 3.0 GHz
- **RAM:** 32 GB
- **GPU:** NVIDIA GTX 1660 or better
- **Storage:** 500 GB SSD
- **Concurrent Streams:** 5-10

### Recommended Small Deployment (1-25 Streams)
- **CPU:** 12 cores, 3.0+ GHz
- **RAM:** 64 GB DDR4
- **GPU:** NVIDIA RTX 3070 or RTX A4000
- **Storage:** 1 TB NVMe SSD
- **Network:** 10 Gbps

### Medium Deployment (26-100 Streams)
- **CPU:** 16 cores, 3.2+ GHz
- **RAM:** 128 GB DDR4
- **GPU:** NVIDIA RTX 4080 or RTX A5000
- **Storage:** 2 TB NVMe SSD
- **Network:** 25 Gbps

### Large Deployment (100+ Streams)
- **CPU:** 24+ cores, 3.5+ GHz
- **RAM:** 256+ GB DDR4
- **GPU:** NVIDIA RTX 4090 or A6000 (multiple GPUs)
- **Storage:** 4+ TB NVMe SSD
- **Network:** 40+ Gbps

### GPU Requirements

#### Supported GPU Architectures
- **NVIDIA:** Pascal, Turing, Ampere, Ada Lovelace (RTX 20/30/40 series)
- **Minimum VRAM:** 6 GB
- **Recommended VRAM:** 12+ GB for large deployments
- **CUDA Capability:** 6.0 or higher

#### GPU Performance Benchmarks
| GPU Model | 720p Streams | 1080p Streams | Analytics Types |
|-----------|--------------|---------------|-----------------|
| RTX 3070 | 15-20 | 8-12 | Person, Vehicle |
| RTX 4080 | 25-35 | 15-25 | Full object detection |
| RTX 4090 | 45-60 | 30-40 | All analytics + behavior |
| A6000 | 50-70 | 35-50 | Enterprise deployment |

#### Analytics Processing Overhead
- **Person Detection:** 0.3-0.4 GPU cores per 1080p stream
- **Vehicle Detection:** 0.2-0.3 GPU cores per 1080p stream
- **Behavior Analysis:** 0.5-0.7 GPU cores per 1080p stream
- **Facial Recognition:** 0.8-1.2 GPU cores per 1080p stream

---

## WINK AI Traffic Requirements

WINK AI Traffic provides vehicle classification, speed detection, and traffic pattern analysis.

### Minimum Requirements
- **CPU:** 8 cores, 3.0 GHz
- **RAM:** 32 GB
- **GPU:** NVIDIA RTX 3060 or better
- **Storage:** 500 GB SSD
- **Concurrent Streams:** 5-8

### Recommended Small Deployment (1-20 Streams)
- **CPU:** 12 cores, 3.2+ GHz
- **RAM:** 64 GB DDR4
- **GPU:** NVIDIA RTX 4070 or RTX A4000
- **Storage:** 1 TB NVMe SSD
- **Network:** 10 Gbps

### Medium Deployment (21-75 Streams)  
- **CPU:** 16 cores, 3.5+ GHz
- **RAM:** 128 GB DDR4
- **GPU:** NVIDIA RTX 4080 or RTX A5000
- **Storage:** 2 TB NVMe SSD
- **Network:** 25 Gbps

### Large Deployment (75+ Streams)
- **CPU:** 24+ cores, 3.5+ GHz
- **RAM:** 256+ GB DDR4
- **GPU:** Multiple RTX 4090 or A6000
- **Storage:** 4+ TB NVMe SSD
- **Network:** 40+ Gbps

### Traffic AI Performance Requirements

#### Resolution vs Processing Requirements
| Resolution | Min GPU VRAM | Streams per GPU | Accuracy Level |
|------------|-------------|-----------------|----------------|
| 480p | 4 GB | 8-12 | 85-90% |
| 720p | 6 GB | 6-10 | 95%+ |
| 1080p | 8 GB | 4-8 | 98%+ |

#### FHWA 13-Class Vehicle Detection
- **Processing:** 0.4-0.6 GPU cores per 1080p stream
- **Memory:** 200-300 MB GPU RAM per stream
- **CPU Overhead:** 0.2-0.3 CPU cores per stream (post-processing)

#### Speed Detection Accuracy
- **Minimum Frame Rate:** 15 FPS (accuracy degrades below)
- **Optimal Frame Rate:** 30 FPS
- **Calibration Requirements:** Manual setup per camera angle
- **Accuracy:** ±2 mph at proper calibration and 30 FPS

---

## Combined Deployment Scenarios

### Scenario 1: City Traffic Management
**Requirements:** 200 traffic cameras with public viewing and analytics

#### Infrastructure:
- **Primary Server:** WINK Forge + Media Router + AI Traffic
- **CPU:** 32 cores, 3.5 GHz (AMD EPYC 7542)
- **RAM:** 256 GB DDR4
- **GPU:** 2x NVIDIA RTX A5000
- **Storage:** 8 TB NVMe SSD RAID 1
- **Network:** Dual 25 Gbps

#### Processing Distribution:
- **50 cameras:** Full AI Traffic analysis (vehicle counting, classification, speed)
- **150 cameras:** Basic transcoding for public 511 website  
- **200 cameras:** Media Router distribution to agencies
- **Estimated Load:** 70% CPU, 60% GPU, 180 GB RAM

### Scenario 2: Multi-Agency Emergency Operations
**Requirements:** 1000 cameras across 5 agencies, real-time sharing

#### Infrastructure:
- **Primary:** WINK Media Router cluster (2 servers)
- **CPU:** 24 cores each, 3.2 GHz (Intel Xeon Gold 6348)
- **RAM:** 128 GB each
- **Storage:** 4 TB NVMe SSD each
- **Network:** Dual 40 Gbps per server
- **High Availability:** VRRP failover

#### Secondary Services:
- **Analytics Server:** WINK Analytics for critical cameras
- **CPU:** 16 cores, 3.5 GHz
- **GPU:** NVIDIA RTX 4080
- **RAM:** 128 GB
- **Processing:** 25 priority cameras with full analytics

### Scenario 3: Federal Facility Network
**Requirements:** 50 facilities, 100 cameras each, centralized monitoring

#### Central Hub:
- **WINK Media Router:** Master aggregation point
- **CPU:** 32 cores, 3.8 GHz
- **RAM:** 256 GB DDR4  
- **Storage:** 10 TB NVMe SSD
- **Network:** 100 Gbps fiber

#### Remote Sites (each):
- **WINK Forge:** Local transcoding and relay
- **CPU:** 8 cores, 3.0 GHz
- **RAM:** 32 GB
- **Storage:** 1 TB SSD
- **Network:** 1-10 Gbps per site

---

## Virtualization Considerations

### VMware vSphere

#### Recommended Settings
```
CPU Configuration:
- Cores per Socket: Match physical CPU
- CPU Hot Add: Enabled
- CPU Reservation: 50% of allocated
- Hardware MMU: Enabled

Memory Configuration:
- Memory Reservation: 75% of allocated
- Memory Hot Add: Enabled
- Large Page Support: Enabled
- NUMA Topology: Expose to guest

Storage Configuration:
- Disk Type: Thick Provision Eager Zeroed
- SCSI Controller: LSI Logic SAS or VMware Paravirtual
- Queue Depth: 32-64 for high IOPS

Network Configuration:  
- Adapter Type: VMXNET3
- SR-IOV: Enabled if available
- Multiple NICs: For bandwidth aggregation
```

#### GPU Passthrough (for Analytics)
```
Prerequisites:
- ESXi 7.0+ with GPU support
- Compatible NVIDIA Tesla/RTX cards
- VT-d/IOMMU enabled in BIOS

Configuration:
- Reserve all GPU memory
- Enable hardware MMU virtualization
- Assign entire GPU to single VM
```

### Microsoft Hyper-V

#### Generation 2 VM Settings
```
Processor:
- Virtual Processors: Match required cores
- Processor Compatibility: Disabled (for same hardware)
- NUMA Spanning: Disabled
- Resource Control: Weight 200-500

Memory:
- Dynamic Memory: Disabled for production
- Memory Buffer: 20%
- Maximum RAM: 110% of allocated
- Enable Hot Add Memory: Yes

Storage:
- VHDX Format: Fixed size for production
- Block Size: 32 MB for high IOPS
- Write-through Cache: Disabled
```

#### RemoteFX/GPU-PV (Legacy)
**Note:** Microsoft deprecated RemoteFX. For GPU acceleration, use:
- **DDA (Discrete Device Assignment)** for dedicated GPU
- **GPU-PV** for shared GPU (Windows Server 2022+)
- **Third-party solutions** (NVIDIA Grid, AMD MxGPU)

### KVM/QEMU

#### Optimal Configuration
```xml
<domain type='kvm'>
  <vcpu placement='static' cpuset='0-31'>32</vcpu>
  <cpu mode='host-passthrough' check='none'>
    <topology sockets='2' cores='16' threads='1'/>
    <cache level='3' mode='emulate'/>
    <feature policy='require' name='invtsc'/>
  </cpu>
  <memory unit='GiB'>256</memory>
  <memoryBacking>
    <hugepages>
      <page size='1048576' unit='KiB'/>
    </hugepages>
    <nosharepages/>
    <locked/>
  </memoryBacking>
</domain>
```

#### GPU Passthrough (VFIO)
```bash
# Enable IOMMU in kernel
echo 'GRUB_CMDLINE_LINUX="intel_iommu=on iommu=pt"' >> /etc/default/grub

# Bind GPU to VFIO
echo "nvidia" > /sys/bus/pci/devices/0000:01:00.0/driver/unbind
echo "10de 1e04" > /sys/bus/pci/drivers/vfio-pci/new_id
```

### Cloud Deployments

#### AWS Instance Recommendations
| Scale | Instance Type | vCPU | RAM | Network | GPU |
|-------|---------------|------|-----|---------|-----|
| Small | c5.2xlarge | 8 | 16 GB | 10 Gbps | N/A |
| Medium | c5.4xlarge | 16 | 32 GB | 10 Gbps | N/A |
| Large | c5.12xlarge | 48 | 96 GB | 25 Gbps | N/A |
| Analytics | p3.2xlarge | 8 | 61 GB | 10 Gbps | V100 |
| AI Traffic | g4dn.4xlarge | 16 | 64 GB | 25 Gbps | T4 |

#### Azure Instance Recommendations
| Scale | Instance Type | vCPU | RAM | Network | GPU |
|-------|---------------|------|-----|---------|-----|
| Small | Standard_D8s_v3 | 8 | 32 GB | 4 Gbps | N/A |
| Medium | Standard_D16s_v3 | 16 | 64 GB | 8 Gbps | N/A |
| Large | Standard_D32s_v3 | 32 | 128 GB | 16 Gbps | N/A |
| Analytics | Standard_NC6s_v3 | 6 | 112 GB | 8 Gbps | V100 |

#### Google Cloud Recommendations  
| Scale | Instance Type | vCPU | RAM | Network | GPU |
|-------|---------------|------|-----|---------|-----|
| Small | n2-standard-8 | 8 | 32 GB | 16 Gbps | N/A |
| Medium | n2-standard-16 | 16 | 64 GB | 32 Gbps | N/A |
| Large | n2-standard-32 | 32 | 128 GB | 32 Gbps | N/A |
| Analytics | n1-standard-8 + T4 | 8 | 30 GB | 16 Gbps | T4 |

---

## Network Infrastructure

### Bandwidth Planning

#### Per-Stream Bandwidth Requirements
| Quality Level | Resolution | Bitrate | Comments |
|---------------|------------|---------|----------|
| Low | 480p | 500 Kbps | Mobile/cellular |
| Standard | 720p | 1.5 Mbps | Most deployments |
| High | 1080p | 2.5 Mbps | High detail required |
| Ultra | 4K | 8 Mbps | Special applications |

#### Total Bandwidth Calculation
```
Input Bandwidth = Cameras × Average Bitrate
Output Bandwidth = Input × Concurrent Viewers × Protocol Overhead
Total = Input + Output + 25% safety margin

Example:
500 cameras × 1.5 Mbps = 750 Mbps input
750 × 0.2 viewers × 1.1 overhead = 165 Mbps output
Total required: (750 + 165) × 1.25 = 1,144 Mbps ≈ 1.2 Gbps
```

### Network Interface Recommendations

#### Single Interface Deployments
- **1 Gbps:** Up to 300 cameras (720p) or 100 viewers
- **10 Gbps:** Up to 3,000 cameras or 1,000 viewers  
- **25 Gbps:** Up to 7,500 cameras or 2,500 viewers
- **40 Gbps:** Up to 12,000 cameras or 4,000 viewers

#### Multi-Interface Configurations
```
Recommended Configurations:
- 2× 10 Gbps bonded (LACP)
- 4× 10 Gbps bonded for high availability
- 2× 25 Gbps bonded for maximum throughput
- Dedicated management interface (1 Gbps)
```

#### Network Redundancy
- **Primary/Secondary NICs:** Automatic failover
- **Link Aggregation:** Increased bandwidth + redundancy
- **Multiple Switches:** Avoid single points of failure
- **Different Cable Paths:** Physical redundancy

### Quality of Service (QoS)

#### Recommended QoS Configuration
```
Priority 1 (Highest): Management traffic, SSH, HTTPS
Priority 2: Real-time video streams (WebRTC, SRT)
Priority 3: Standard video streams (HLS, RTMP)
Priority 4: File transfers, backups
Priority 5 (Lowest): Bulk data, logs
```

#### DSCP Markings
- **AF41 (34):** Real-time video
- **AF31 (26):** Standard video  
- **AF21 (18):** Management traffic
- **CS1 (8):** Bulk data

---

## Storage Requirements

### Operating System Storage
- **Base OS:** 50-100 GB (Ubuntu/CentOS)
- **Application:** 100-200 GB
- **Updates/Patches:** 50 GB reserved
- **Logs:** 20-100 GB (configurable retention)

### Stream Caching Storage

#### Cache Size Planning
```
Cache Per Stream = Bitrate × Buffer Duration
Buffer Duration = 30-300 seconds (configurable)

Example:
1000 streams × 1.5 Mbps × 60 seconds = 11.25 GB active cache
Plus 2× buffer for rotation = 22.5 GB total cache
```

#### Cache Performance Requirements
- **IOPS:** 100-500 per concurrent stream
- **Latency:** <10ms average, <50ms 99th percentile
- **Bandwidth:** 2-5× total stream bitrate for burst writes

### Analytics Data Storage

#### Data Types and Retention
| Data Type | Size per Stream per Day | Typical Retention |
|-----------|-------------------------|-------------------|
| Detection Events | 10-50 MB | 30-90 days |
| Object Metadata | 5-25 MB | 30-365 days |
| Thumbnails | 50-200 MB | 7-30 days |
| Video Clips | 1-10 GB | 7-30 days |

#### Database Storage
- **Configuration:** 100-500 MB
- **User Management:** 10-100 MB  
- **Analytics Metadata:** 1-50 GB
- **Audit Logs:** 100 MB - 5 GB per month

### Storage Technology Recommendations

#### SSD Requirements
```
Minimum:
- SATA SSD for OS and applications
- 500+ MB/s sequential read/write
- 5,000+ IOPS random 4K

Recommended:
- NVMe SSD for cache and high-IOPS workloads
- 2,000+ MB/s sequential read/write  
- 50,000+ IOPS random 4K
```

#### RAID Configurations
- **RAID 1:** OS and applications (reliability)
- **RAID 10:** Cache and database (performance + reliability)
- **RAID 5/6:** Analytics data (capacity with protection)
- **No RAID:** Acceptable for cache with application-level redundancy

#### Enterprise Storage Integration
- **iSCSI:** Good performance, easy management
- **Fibre Channel:** Highest performance, more complex
- **NFS:** Acceptable for analytics data, not cache
- **Object Storage:** Long-term analytics data archival

---

## Deployment Examples

### Small Agency: 100 Cameras, Basic Sharing

#### Single Server Configuration
```
Hardware:
- Dell PowerEdge R440 or similar
- 2× Intel Xeon Silver 4208 (8c/16t each)
- 64 GB DDR4 ECC RAM
- 2× 1 TB NVMe SSD (RAID 1)
- 2× 10GbE ports (bonded)

Software:
- WINK Media Router
- Ubuntu 22.04 LTS
- Basic analytics on 10 priority cameras

Capacity:
- 100 cameras @ 1.5 Mbps average
- 25 concurrent viewers
- 150 Mbps total bandwidth
- 30-day analytics retention
```

### Medium City: 500 Cameras, Multi-Agency

#### Two-Server Configuration
```
Server 1 (Media Router + Light Analytics):
- HPE ProLiant DL360 or similar  
- 2× Intel Xeon Gold 6242 (16c/32t each)
- 128 GB DDR4 ECC RAM
- 4× 2 TB NVMe SSD (RAID 10)
- 4× 10GbE ports (bonded pairs)
- NVIDIA RTX A4000 (analytics)

Server 2 (Backup + Transcoding):
- Identical hardware configuration
- VRRP failover setup
- Load sharing for transcoding

Capacity:
- 500 cameras @ 1.5 Mbps average
- 100 concurrent viewers across agencies
- 750 Mbps input, 150 Mbps output
- 50 cameras with full analytics
```

### Large State: 2000+ Cameras, Public Access

#### Cluster Configuration
```
Media Router Cluster (3 servers):
- HPE Apollo or Dell EMC PowerEdge
- 2× AMD EPYC 7543 (32c/64t each)
- 256 GB DDR4 ECC RAM each
- 8× 4 TB NVMe SSD (RAID 10) each
- 2× 25GbE + 2× 10GbE each

Analytics Cluster (2 servers):
- 2× Intel Xeon Gold 6348 (28c/56t each)  
- 256 GB DDR4 ECC RAM
- 4× 2 TB NVMe SSD (RAID 10)
- 4× NVIDIA RTX A6000
- 4× 10GbE ports

Load Balancer:
- F5 BIG-IP or HAProxy cluster
- SSL termination
- Geographic traffic routing
- DDoS protection

Capacity:  
- 2000+ cameras @ 1.2 Mbps average
- 500+ concurrent viewers
- 2.4+ Gbps input bandwidth
- 200+ cameras with AI analytics
- Full 511 public website integration
```

### Enterprise: 5000+ Cameras, Global Deployment

#### Distributed Architecture
```
Regional Hubs (4 locations):
- Primary processing and caching
- 2-server clusters per region
- 1000-1500 cameras each
- Local analytics processing

Central Management:
- Global Media Router coordinator
- User management and authentication
- Cross-regional stream sharing
- Centralized reporting

Edge Locations (50+ sites):
- Local WINK Forge instances
- 10-100 cameras each
- Basic transcoding and relay
- Automatic failover to regional hubs

Network:
- MPLS backbone between regions
- Internet2/ESnet for educational institutions
- Dedicated fiber for critical links
- Satellite backup for remote locations
```

---

## Monitoring and Maintenance

### Performance Monitoring

#### Key Metrics to Track
```
System Metrics:
- CPU utilization (target: <80% average)
- Memory usage (target: <85% allocated)  
- Storage IOPS and bandwidth
- Network utilization and packet loss

Application Metrics:
- Active streams and viewers
- Stream quality and bitrate
- Transcoding queue depth
- Database response times

Analytics Metrics (if applicable):
- GPU utilization and memory
- Detection accuracy and confidence
- Processing latency per stream
- False positive/negative rates
```

#### Monitoring Tools Integration
- **SNMP:** Built-in support for network monitoring
- **Prometheus:** Metrics export for modern monitoring stacks
- **Grafana:** Real-time dashboards and alerting
- **ELK Stack:** Log aggregation and analysis
- **Nagios/Zabbix:** Traditional infrastructure monitoring

### Maintenance Windows

#### Regular Maintenance Tasks
```
Daily:
- Log rotation and cleanup
- Stream health verification
- Backup status checks
- Security event review

Weekly:  
- Performance trend analysis
- Storage usage review
- Software update checks
- Hardware health monitoring

Monthly:
- Full system health review
- Capacity planning assessment
- Security patch evaluation
- Disaster recovery testing

Quarterly:
- Hardware lifecycle review
- Software upgrade planning
- Performance optimization
- Business continuity validation
```

### Upgrade Planning

#### Hardware Refresh Cycles
- **Servers:** 3-5 years typical lifecycle
- **Storage:** 3-4 years (monitor warranty and wear)
- **Network:** 5-7 years (technology refresh driven)
- **GPUs:** 2-3 years (technology advancement rapid)

#### Capacity Growth Planning
```
Annual Growth Estimation:
- Camera Count: 15-25% typical growth
- Viewer Count: 20-30% typical growth
- Analytics Usage: 50-100% growth (adoption driven)
- Storage: 30-50% growth (retention policy dependent)

Scaling Triggers:
- >80% CPU utilization sustained
- >85% memory allocation
- >90% storage capacity
- >80% network bandwidth
```

### Troubleshooting Common Issues

#### Performance Problems
```
High CPU Usage:
1. Check transcoding load and quality settings
2. Verify GPU acceleration is working
3. Review concurrent stream counts
4. Check for memory leaks or runaway processes

High Memory Usage:
1. Verify cache size configuration
2. Check for memory leaks in long-running processes
3. Review analytics model memory usage
4. Monitor database connection pooling

Storage Issues:
1. Monitor IOPS and latency metrics
2. Check cache hit ratios and effectiveness
3. Verify RAID health and disk status
4. Review storage allocation and growth trends

Network Problems:
1. Monitor interface utilization and errors
2. Check QoS configuration and effectiveness
3. Verify link aggregation and failover
4. Analyze packet loss and jitter statistics
```

#### Application-Specific Issues
```
Stream Quality Problems:
1. Check source camera settings and network
2. Verify transcoding parameters
3. Monitor packet loss and retransmission
4. Review adaptive bitrate behavior

Authentication Issues:
1. Verify user permissions and group membership
2. Check LDAP/AD integration status
3. Review OTP generation and expiration
4. Monitor failed login attempts and patterns

Analytics Accuracy:
1. Verify camera calibration and positioning
2. Check lighting conditions and image quality
3. Review detection confidence thresholds
4. Monitor model performance and updates
```

---

*This hardware requirements guide is based on extensive real-world deployments and performance testing. Requirements may vary based on specific use cases, network conditions, and quality requirements. Always conduct pilot testing with your specific environment and workload before final sizing.*

*© 2025 WINK Streaming. All rights reserved.*