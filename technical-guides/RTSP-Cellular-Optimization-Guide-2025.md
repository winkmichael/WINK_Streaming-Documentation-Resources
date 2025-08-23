# RTSP Stream Optimization for Cellular Networks

*A Field Engineer's Guide to Reliable Video Streaming Over 4G/5G Networks*

---

## Table of Contents

1. [Quick Takeaway](#quick-takeaway)
2. [The Cellular Challenge](#the-cellular-challenge)
3. [TCP vs UDP: The Eternal Debate](#tcp-vs-udp-the-eternal-debate)  
4. [IPv6 Considerations](#ipv6-considerations)
5. [Connection Bonding and MPTCP](#connection-bonding-and-mptcp)
6. [Dealing with Packet Loss](#dealing-with-packet-loss)
7. [Optimizing for Cellular Carriers](#optimizing-for-cellular-carriers)
8. [Real-World Configuration Examples](#real-world-configuration-examples)
9. [Monitoring and Troubleshooting](#monitoring-and-troubleshooting)
10. [Cost Optimization](#cost-optimization)
11. [Integration with WINK Products](#integration-with-wink-products)
12. [Future Considerations](#future-considerations)

---

## Quick Takeaway

For cellular RTSP streaming, always use TCP mode, enable persistent connections, optimize for IPv6, and choose H.264 with error resilience. This guide explains why and how. For detailed camera settings, see our [Security Camera Optimization guide](Security-Camera-Optimization-KFI-FPS-Bitrate-Guide-2025.md).

---

## The Cellular Challenge

Streaming RTSP from fixed cameras over cellular networks presents unique challenges. Unlike fiber or fixed wireless, cellular networks are:

- **Inherently lossy** - Packet loss rates of 0.5-3% are normal, 5%+ during congestion
- **IPv6 native** - Most carriers use IPv6 with NAT64 for IPv4 compatibility  
- **Asymmetric** - Upload speeds typically 1/10th of download
- **Variable latency** - 20ms to 200ms+ depending on tower load
- **Behind CGNAT** - No public IP, complicating inbound connections

```
┌─────────┐     4G/5G Tower      ┌──────────────┐     ┌─────────┐
│ Camera  │ ═══════════════════> │ Carrier Core │ ──> │   VMS   │
│ + Modem │   Variable Quality    │  (CGNAT)     │     │ Server  │
└─────────┘                       └──────────────┘     └─────────┘
  Field Site                        IPv6 Native          Data Center
```

---

## TCP vs UDP: The Eternal Debate

For cellular RTSP streaming, **TCP is almost always the right choice**. Here's why:

| Aspect | UDP over Cellular | TCP over Cellular |
|--------|------------------|-------------------|
| Packet Loss Handling | ❌ Lost packets = corrupted frames | ✅ Automatic retransmission |
| Firewall Traversal | ❌ Often blocked by carriers | ✅ Works everywhere |
| NAT Persistence | ❌ NAT timeouts common (30-60s) | ✅ Persistent connections |
| Latency | ✅ Lower baseline (no ACKs) | ⚠️ Higher but predictable |
| Congestion Response | ❌ Continues sending at full rate | ✅ TCP congestion control |

> **Best Practice:** Configure your camera for RTSP-over-TCP (sometimes called "RTP over RTSP" or "Interleaved mode"). In most cameras, this is a simple dropdown selection.

---

## IPv6 Considerations

Modern cellular networks are IPv6-first. Your camera sees an IPv6 address like `2607:fb90:1234:5678::1` while your VMS might still be IPv4-only. Here's how to handle it:

### MTU and Fragmentation

**IPv6 Minimum MTU:** 1280 bytes (vs 576 for IPv4)  
**Typical Cellular MTU:** 1420-1430 bytes  
**Overhead:** IPv6 header (40 bytes) + TCP (20 bytes) + RTSP (~16 bytes)

To avoid fragmentation:

```bash
# On Linux-based camera or gateway:
ip link set dev wwan0 mtu 1400

# In camera settings, if available:
RTP Maximum Packet Size: 1200 bytes
```

### NAT64/DNS64 Traversal

When your IPv6 camera needs to reach an IPv4 VMS:

```
IPv6 Camera                    NAT64 Gateway              IPv4 VMS
2607:fb90::1 ──────────────> 64:ff9b::192.0.2.1 ──────> 192.0.2.1
            Synthesized IPv6 Address
```

> **Gotcha:** Some cameras fail DNS64 lookups. Solution: Use IP addresses or ensure your VMS has an AAAA (IPv6) record.

---

## Connection Bonding and MPTCP

For critical deployments, cellular bonding provides redundancy and increased bandwidth. Three main approaches:

### 1. Hardware Bonding Devices

Dedicated appliances that combine multiple cellular connections:

- **Peplink (SpeedFusion)** - Excellent for fixed installations, $800-3000
- **Mushroom Networks** - True packet-level bonding, $1500+
- **Cradlepoint** - Enterprise-grade, good management, $1000+

> **Pro Tip:** These devices work best when you disable their "optimization" features for video. Raw TCP pass-through often performs better than their video-specific modes.

### 2. MPTCP (Multipath TCP)

MPTCP allows a single TCP connection to use multiple network paths:

```bash
# Enable MPTCP on Linux (kernel 5.6+)
sysctl -w net.mptcp.enabled=1
sysctl -w net.mptcp.mptcp_path_manager=ndiffports

# Configure for redundancy (not aggregation)
ip mptcp endpoint add 10.0.0.1 dev wwan0 subflow
ip mptcp endpoint add 10.0.0.2 dev wwan1 subflow backup
```

| MPTCP Mode | Use Case | Cellular Impact |
|------------|----------|-----------------|
| Default (Aggregate) | Maximum bandwidth | Double data usage |
| Redundant | Reliability | ~110% data usage |
| Backup | Failover only | Single connection usage |

### 3. Application-Level Bonding

Some modern devices implement bonding + redundancy simultaneously:

```
┌─────────────┐     Modem 1 ────────┐
│   Camera    │────────────────────>├──> Primary Stream
│  Bonding    │     Modem 2 ────────┘
│   Device    │────────────────────>───> Redundant Stream
└─────────────┘     Modem 2
```

This approach sends the full stream over bonded connections PLUS a complete copy over a single modem. Data usage: ~200% but maximum reliability.

---

## Dealing with Packet Loss

Even with TCP, understanding packet loss helps optimize your setup:

### TCP Checksum Offloading

> **Controversial Opinion:** For live video over cellular, consider disabling TCP checksum validation at the receiver. Bad frames are better than frozen video.

```bash
# Disable checksum validation (Linux receiver)
ethtool -K eth0 rx off tx off

# In practice, this is rarely needed with modern H.264 error resilience
```

### H.264 Error Resilience

As detailed in our [H.264 guide](Why-H264-Is-Almost-Always-The-Answer.md), proper encoder settings handle packet loss gracefully:

- Enable **Flexible Macroblock Ordering (FMO)**
- Use **shorter GOP sizes** (1-2 seconds max)  
- Enable **Intra Refresh** instead of periodic I-frames
- Use multiple **slice partitions** per frame

---

## Optimizing for Cellular Carriers

Each carrier has quirks. Here's what we've learned:

### Verizon
- Best IPv6 implementation
- MTU typically 1428 bytes
- Aggressive timeout on idle connections (use keep-alives every 30s)

### AT&T
- Good FirstNet priority options for public safety  
- MTU varies by region (1420-1430)
- Less aggressive traffic shaping than others

### T-Mobile
- Excellent 5G coverage but aggressive video optimization
- May need to use business APN to avoid throttling
- MTU typically 1420 bytes

---

## Real-World Configuration Examples

### Axis Camera Configuration

```
Stream Profile: H.264
Resolution: 1920x1080
Frame Rate: 15 fps (not 30!)
Bitrate Mode: Variable (VBR)
Max Bitrate: 2 Mbps
GOP Length: 15 (1 second at 15fps)
Transport: RTP over RTSP (TCP)
```

### Hikvision Configuration

```
Video Encoding: H.264
Resolution: 1080P
Frame Rate: 15 fps
Bitrate Type: Variable
Max Bitrate: 2048 Kbps
I-Frame Interval: 15
Transport Protocol: TCP
```

### Linux Gateway Optimization

```bash
# TCP optimization for cellular
echo "net.ipv4.tcp_congestion_control = bbr" >> /etc/sysctl.conf
echo "net.core.default_qdisc = fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_time = 30" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_intvl = 10" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_probes = 6" >> /etc/sysctl.conf

# Buffer tuning for video streaming
echo "net.core.rmem_max = 134217728" >> /etc/sysctl.conf
echo "net.core.wmem_max = 134217728" >> /etc/sysctl.conf
echo "net.ipv4.tcp_rmem = 4096 87380 134217728" >> /etc/sysctl.conf
echo "net.ipv4.tcp_wmem = 4096 65536 134217728" >> /etc/sysctl.conf

sysctl -p
```

---

## Monitoring and Troubleshooting

### Key Metrics to Watch

**RSRP (Reference Signal Received Power):**
- Excellent: > -80 dBm
- Good: -80 to -90 dBm
- Fair: -90 to -100 dBm  
- Poor: < -100 dBm

**SINR (Signal to Interference & Noise Ratio):**
- Excellent: > 20 dB
- Good: 13 to 20 dB
- Fair: 0 to 13 dB
- Poor: < 0 dB

### Diagnostic Commands

```bash
# Check cellular signal (varies by modem)
mmcli -m 0 --signal-get
qmicli -d /dev/cdc-wdm0 --nas-get-signal-info

# Monitor TCP retransmissions
netstat -s | grep -i retrans

# Watch for packet loss
ping6 -c 100 your-vms-ipv6 | grep loss

# Check MPTCP status
ip mptcp monitor
ss -i | grep mptcp
```

---

## Cost Optimization

Cellular data isn't cheap. Here's how to minimize usage while maintaining quality:

| Resolution | FPS | Bitrate | Monthly Data (24/7) | Quality Assessment |
|------------|-----|---------|---------------------|-------------------|
| 1080p | 30 | 4 Mbps | 1,296 GB | Overkill for cellular |
| 1080p | 15 | 2 Mbps | 648 GB | Good balance |
| 720p | 15 | 1 Mbps | 324 GB | Recommended |
| 720p | 10 | 768 Kbps | 248 GB | Budget option |

> **Money-Saving Tip:** Use motion detection to reduce streaming. A camera streaming only during motion events uses 80-90% less data than continuous streaming.

---

## Integration with WINK Products

[WINK Forge](../manuals/WINK-Forge-Manual-2025.md) and Media Router are optimized for cellular delivery:

- **Adaptive bitrate** - Automatically adjusts to cellular conditions
- **Connection persistence** - Handles cellular interruptions gracefully  
- **IPv6 native** - No translation overhead
- **Error resilience** - Optimized H.264 encoding for lossy networks

---

## Future Considerations

### 5G Network Slicing

Coming soon: Dedicated network slices for video surveillance with guaranteed QoS. Early trials show:

- Guaranteed 5 Mbps upload
- Sub-50ms latency  
- 0.01% packet loss SLA

### Private 5G/CBRS

For large deployments, consider private cellular networks:

- No monthly data costs
- Complete control over QoS
- No carrier throttling
- Coverage where you need it

---

## Conclusion

Successful RTSP streaming over cellular requires understanding the unique characteristics of mobile networks. Key takeaways:

1. **Use TCP** - The reliability benefits outweigh the minimal latency cost
2. **Optimize for IPv6** - It's not the future, it's the present
3. **Consider bonding for critical feeds** - But understand the cost implications  
4. **Configure H.264 properly** - Error resilience is more important than compression efficiency
5. **Monitor continuously** - Cellular conditions change throughout the day

> **Remember:** The goal isn't perfection - it's reliable, usable video that works within cellular constraints. A slightly degraded but consistent stream beats a perfect stream that freezes every time a cloud passes over the tower.

---

*© 2025 WINK Streaming | [wink.co](https://wink.co) | [support@wink.co](mailto:support@wink.co)*