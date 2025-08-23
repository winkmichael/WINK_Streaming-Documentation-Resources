# Camera Mounting & Analytics Guide

## Optimal Camera Placement for AI-Powered Traffic Analytics

**Version:** 2025  
**Company:** WINK Streaming

---

## Table of Contents

1. [Optimal Results - Camera Mounting and Placement](#optimal-results---camera-mounting-and-placement)
2. [Highway Camera Analytics - Technical Requirements](#highway-camera-analytics---technical-requirements)
3. [FHWA Vehicle Classification Guide](#fhwa-vehicle-classification-guide)

---

## Optimal Results - Camera Mounting and Placement

### Why Good Camera Mounting Matters

Proper camera mounting is critical for accurate traffic detection. When your cameras are mounted correctly:

- **Consistent Data:** Training data and inference data match, making it easier for models to generalize
- **Better Detection:** Minimized occlusions, overlaps, and shadow artifacts
- **Stable Tracking:** Reduced motion blur and false motion detection
- **Simplified Pipeline:** All cameras work similarly, greatly boosting overall system accuracy

### Optimal Mounting Angles

**Top-down (80-90°):**
- Minimal vehicle overlap
- Clear lane separation
- Reduced shadow interference
- Accurate vehicle counting

**High-angle (45-70°):**
- Vehicle counting
- Speed estimation
- Lane occupancy
- FHWA classification

**Mid-angle (20-45°):**
- Good lane separation
- License plate visibility
- Vehicle type classification
- Incident detection

**Side-view (0-20°):**
- Lane-specific analysis
- Vehicle identification
- Behavioral analysis
- Queue detection

### Mounting Height Guidelines

The optimal mounting height depends on the camera's viewing angle and the width of the roadway being monitored. Higher mounting positions generally provide better coverage but may reduce detail visibility.

### Camera Stability Requirements

**Critical Factors:**
- **Vibration Dampening:** Use shock-absorbing mounts to minimize camera shake
- **Wind Resistance:** Ensure mounts can withstand 80+ mph wind loads
- **Temperature Stability:** Account for thermal expansion/contraction
- **Rigid Mounting:** Avoid flexible poles or loose connections

**Best Practices:**
- **Guy Wires:** Use for tall poles in windy areas
- **Concrete Foundations:** Minimum 4-foot depth for pole mounts
- **Regular Maintenance:** Check mount tightness quarterly
- **Image Stabilization:** Enable electronic stabilization when available

### Impact on Detection Accuracy

Understanding how mounting affects detection accuracy helps set realistic expectations and optimize system performance.

### Understanding Margins of Error

**Occlusion Issues:**
When vehicles block other lanes or partially hide other vehicles:
- **Cross-lane occlusion:** Large trucks blocking adjacent lanes (±5-15% error)
- **Same-lane occlusion:** Tailgating vehicles appear as one (±3-8% error)
- **Merge/weave areas:** Complex overlapping patterns (±10-20% error)

**Environmental Factors:**
- **Heavy rain/snow:** Reduced visibility (±15-25% error)
- **Sun glare:** Dawn/dusk periods (±10-15% error)
- **Night conditions:** Headlight glare and shadows (±5-10% error)

**Technical Limitations:**
- **Frame rate:** Fast vehicles between frames (±2-5% error)
- **Resolution limits:** Distant lane detection (±3-7% error)
- **Processing latency:** Missed short-duration events (±1-3% error)

**Overall System Accuracy:**
- **Best Case:** Clear weather, optimal mounting: ±2-5% error
- **Average Case:** Mixed conditions: ±8-12% error
- **Worst Case:** Heavy traffic, poor weather: ±15-25% error

### Calibration Strategies

**Detection + Counting Camera Approach:**
Use a dedicated counting camera to calibrate detection accuracy:

- Position counting camera perpendicular to traffic flow
- Use simple line-crossing algorithm for ground truth
- Compare detection results with actual counts
- Calculate correction factors per lane/time of day

**Benefits:**
- Real-time accuracy validation
- Automatic bias correction
- Weather-specific calibration factors

**Multi-Camera Validation:**
Multiple cameras on same roadway for cross-validation:

**Overlap Validation:**
Cameras with 20-30% overlap zones for verification
- Compare counts in overlap areas
- Identify systematic biases
- Validate vehicle classifications

**Continuity Validation:**
Cameras at entry/exit points for continuity checks
- Track vehicle flow conservation
- Detect missing/phantom vehicles
- Calibrate travel time estimates

> **Note:** While multi-camera validation improves accuracy, the computational cost often outweighs benefits. Single detection + counting camera typically provides 90% of the accuracy at 50% of the cost.

### Technical Configuration Parameters

**Lane Geometry:**
- **Standard lane:** 12 feet (3.7m)
- **Tolerance:** ±1 foot (0.3m)
- **Shoulder detection:** >14 feet from centerline

**Motion Parameters:**
- **Max lateral speed:** 10 ft/sec (lane change)
- **Min dwell time:** 2 seconds in lane
- **Lane assignment confidence:** 0.85

**Occlusion Handling:**
- **Partial occlusion:** >40% visible = valid
- **Max occlusion time:** 3 seconds
- **Reacquisition distance:** 50 feet

### Camera Installation Checklist

**Pre-Installation:**
- Survey site for optimal viewing angle
- Check power and network availability
- Verify mounting structure stability
- Calculate required camera height
- Plan cable routing and protection

**Installation:**
- Use calibrated angle finder for precision
- Install vibration dampeners
- Secure all mounting hardware
- Apply thread-locking compound
- Test live view before finalizing

**Post-Installation:**
- Verify detection accuracy
- Check for image stability
- Test in various weather conditions
- Document final angles and height
- Schedule maintenance intervals

---

## Highway Camera Analytics - Technical Requirements

### Technical Foundation for Accurate Analytics

The success of AI-based traffic monitoring depends heavily on meeting technical requirements. This guide outlines the critical specifications needed for:

- **Video Quality:** Frame rate, resolution, and codec requirements for reliable tracking
- **Network Infrastructure:** Bandwidth, latency, and security considerations
- **Processing Power:** GPU and hardware specifications for real-time analytics
- **Environmental Resilience:** Operating conditions and reliability factors

### Video Capture Specifications

| Specification | Requirement | Notes |
|--------------|-------------|--------|
| **Frame Rate** | 25-30 fps minimum | Higher for fast traffic |
| **Resolution** | 1080p minimum, 4MP preferred | Balance detail vs bandwidth |
| **Codec** | H.264 (recommended), H.265 | H.264 for reliability |
| **Protocol** | RTSP, UDP, RTP, SRT | Not HLS |
| **Bitrate** | 4-8 Mbps for 1080p | Adjust for conditions |
| **WDR** | Required | High contrast scenes |
| **IR/Night** | Required | 24/7 operation |

**Streaming Protocols:**
- Direct UDP streaming in raw format or RTP is equivalent to RTSP for performance
- Supported protocols: RTSP (TCP or UDP), UDP, RTP, SRT, or RTMP
- For H.265: Keep keyframe intervals low (1-2 seconds) to minimize video loss from packet drops
- On unreliable networks: RTSP over TCP or SRT are preferred to prevent packet loss

> **⚠️ Important:** HLS (HTTP Live Streaming) is **not recommended** for real-time analytics due to high latency, chunked delivery, and unreliable frame arrival timing. Use RTSP or SRT for low-latency, frame-accurate video required by AI tracking and vehicle classification systems.

### Lighting & Visibility Requirements

**Camera Features:**
- **IR/Night Vision:** Required for dark conditions
- **WDR (Wide Dynamic Range):** Required for high contrast scenes
- **Minimum Illumination:** 0.01 lux (color), 0.001 lux (B&W)
- **Smart IR:** Prevents overexposure of nearby objects

**Environmental Considerations:**
- **External Lighting:** Adequate roadway illumination for night accuracy
- **Light Pollution:** Shield cameras from direct light sources
- **Headlight Compensation:** HLC or BLC features recommended
- **Dawn/Dusk Performance:** Auto-exposure adjustment critical

### Performance Impact by Configuration

| Configuration | Detection Accuracy | Use Case |
|--------------|-------------------|----------|
| **<15 fps** | 60-70% | Emergency backup only |
| **15–20 fps, 720p** | 80-85% | Basic monitoring |
| **25–30 fps, 1080p+** | 90-95% | Production systems |
| **30+ fps, 4MP+** | 95%+ | Premium deployments |

### Stream Quality Impact on Detection

**Poor Quality (Low resolution, <20fps):**
- Vehicles merge into single blobs
- Cannot distinguish vehicle types
- Tracking breaks frequently
- Unusable in rain/night

**Good Quality (720p, 20-25fps):**
- Basic vehicle counting works
- Simple classification possible
- Some tracking losses at high speed
- Reduced accuracy at night

**Excellent Quality (1080p+, 25-30fps):**
- Full vehicle classification
- Reliable multi-lane tracking
- Works in all weather conditions
- Accurate speed estimation

### Bandwidth vs Quality Trade-offs

**Limited Bandwidth (<5 Mbps):**
- Use H.265 codec for 50% bandwidth savings
- Reduce to 720p if needed
- Consider SRT for packet loss recovery
- Implement adaptive bitrate

**Standard Bandwidth (5-15 Mbps):**
- 1080p @ 25fps with H.264/H.265
- RTSP over TCP for reliability
- Enable WDR for lighting changes
- Standard keyframe intervals

**High Bandwidth (15+ Mbps):**
- 4K resolution for maximum detail
- 30+ fps for smooth tracking
- Raw UDP/RTP for lowest latency
- Multiple streams for redundancy

### System Integration Considerations

Estimate bandwidth requirements for your deployment based on the number of cameras, resolution, frame rate, and codec selection to ensure adequate network infrastructure.

---

## FHWA Vehicle Classification Guide

### Overview of FHWA Classification

The Federal Highway Administration (FHWA) defines 13 distinct vehicle classes for standardized traffic monitoring and reporting. Our system performs this classification as a background process after initial vehicle detection, ensuring accurate categorization without impacting real-time performance.

**Key Features:**
- **Two-Stage Process:** Initial vehicle detection followed by detailed classification
- **Background Processing:** Classification happens after vehicle crop extraction
- **Standardized Categories:** Full compliance with FHWA 13-class scheme
- **Flexible Implementation:** Can start with basic categories and refine over time

### How Classification Works

**Benefits of Background Processing:**
- Detection continues at full speed while classification happens asynchronously
- More computational resources available for detailed analysis of each vehicle
- Can use different models for different vehicle types without slowing detection
- Classification models can be updated without affecting core detection

### The 13 FHWA Vehicle Classes

The FHWA classification system provides standardized vehicle categories for traffic monitoring and analysis, ranging from motorcycles and passenger cars to various truck configurations and buses.

### Implementation Strategies

Start simple and add complexity as your system matures:

**Phased Approach:**
1. Start with Car/Truck/Bus/Motorcycle (4 classes)
2. Add small/medium/large truck categories (7 classes)
3. Implement axle counting for truck sub-classification
4. Complete 13-class implementation with validation

**Model Development:**
- Use lightweight models for initial detection
- Deploy specialized models for classification
- Consider ensemble approaches for difficult classes

**Data Collection:**
- Collect diverse samples for each class
- Account for regional vehicle variations
- Include different weather/lighting conditions

**Validation:**
- Cross-reference with manual counts
- Compare with loop detector data if available
- Regular accuracy assessments

### Integration with Traffic Systems

FHWA classification integrates seamlessly with other traffic monitoring components:

- **WINK AI Traffic:** Complete traffic analytics platform with FHWA classification
- **WINK Analytics:** Advanced video analytics with vehicle detection and tracking
- **API Integration:** Integration specifications and code examples

---

## Conclusion

This comprehensive guide combines best practices for camera mounting, technical specifications, and FHWA vehicle classification to help organizations deploy successful AI-powered traffic analytics systems. By following these guidelines and leveraging WINK AI Traffic and WINK Analytics, you can achieve:

- 95%+ vehicle detection accuracy in optimal conditions
- Full FHWA 13-class vehicle classification
- Reliable multi-lane tracking and speed estimation
- 24/7 operation in various weather conditions
- Seamless integration with existing traffic management systems

### For Technical Support

- **Email:** support@wink.co
- **Phone:** +1-312-281-5433
- **Documentation:** [wink.co/resources](https://www.wink.co/resources)

---

*© 2025 WINK Streaming. All rights reserved.*