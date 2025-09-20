# Power Management & Battery Backup Strategy

## 📋 Overview
Comprehensive power management strategy for ESP32-CAM Face Recognition Door Lock System, focusing on 24/7 operation, battery backup requirements, and power consumption optimization.

## 🎯 Power Requirements Analysis

### System Power Profile
- **Operating Voltage**: 5V DC (stable)
- **Peak Current**: 400-500 mA (during face recognition + WiFi)
- **Average Current**: 15-25 mA (24h average with sleep modes)
- **Idle Current**: 20-30 mA (WiFi connected, camera off)
- **Sleep Current**: 0.8 mA (light sleep mode)

### Component Power Breakdown

#### ESP32-CAM Module
- **Active Mode**: 120-180 mA
- **WiFi TX**: 100-150 mA
- **Camera Active**: 80-120 mA
- **Light Sleep**: 0.8 mA
- **Deep Sleep**: 10 μA

#### Camera Module (OV2640)
- **Active**: 80-120 mA
- **Standby**: 5-10 mA
- **Power Down**: <1 mA

#### Servo Motor (SG90)
- **Operating**: 100-200 mA
- **Stall Current**: 300-400 mA
- **Idle**: 5-10 mA

#### Relay Module
- **Active**: 50-70 mA
- **Inactive**: <1 mA

#### LED Indicators
- **Per LED**: 10-20 mA
- **RGB LED**: 30-60 mA

## 🔋 Battery Backup Strategy

### Battery Requirements
- **Capacity Needed**: 2000-3000 mAh minimum
- **Voltage**: 3.7V nominal (lithium-ion)
- **Backup Duration**: >4 hours (as per project requirements)
- **Charging**: USB 5V charging capability
- **Protection**: Over-discharge and over-charge protection

### Battery Selection Options

#### Option 1: 18650 Lithium-ion Battery
- **Capacity**: 2000-3000 mAh
- **Voltage**: 3.7V
- **Size**: 18mm x 65mm
- **Cost**: $3-8
- **Backup Time**: 4-6 hours
- **Pros**: High capacity, readily available
- **Cons**: Requires boost converter to 5V

#### Option 2: LiPo Battery Pack
- **Capacity**: 2000 mAh
- **Voltage**: 3.7V
- **Size**: Compact (50x30x10mm)
- **Cost**: $5-12
- **Backup Time**: 4-5 hours
- **Pros**: Compact size, lightweight
- **Cons**: Lower capacity options

#### Option 3: Power Bank Solution
- **Capacity**: 3000-5000 mAh
- **Voltage**: 5V output
- **Size**: 90x60x20mm
- **Cost**: $8-15
- **Backup Time**: 6-10 hours
- **Pros**: Built-in protection, easy integration
- **Cons**: Larger size, higher cost

### Recommended: 18650 Battery Pack
- **Configuration**: 2x 18650 batteries in parallel
- **Total Capacity**: 4000-6000 mAh
- **Backup Time**: 8-12 hours
- **Cost**: $10-20 (including holder and protection circuit)

## ⚡ Power Management Architecture

### Power Supply Design

#### Primary Power Supply
```
AC Power (110-240V)
     ↓
Switching Power Supply (5V 2A)
     ↓
Voltage Regulator (LM2596/LM1117)
     ↓
ESP32-CAM System (5V)
```

#### Battery Backup System
```
Battery Pack (3.7V)
     ↓
Boost Converter (MT3608)
     ↓
Charge Controller (TP4056)
     ↓
Power Multiplexer
     ↓
ESP32-CAM System (5V)
```

### Power Modes Implementation

#### Mode 1: Deep Sleep Mode
- **Trigger**: No motion detected for 30 seconds
- **Power Draw**: 10 μA
- **Wake-up**: PIR motion sensor
- **Duration**: Most of the time (95%+)
- **Components Off**: Camera, WiFi, Servo

#### Mode 2: Light Sleep Mode
- **Trigger**: Motion detected, no face in view
- **Power Draw**: 0.8 mA
- **Wake-up**: Camera interrupt
- **Duration**: Brief periods between detections
- **Components Off**: Camera (mostly), WiFi (intermittent)

#### Mode 3: Active Detection Mode
- **Trigger**: Face detected in camera view
- **Power Draw**: 150-200 mA
- **Duration**: 1-3 seconds per recognition attempt
- **Components On**: Camera, WiFi (if needed), Processing

#### Mode 4: Recognition Mode
- **Trigger**: Valid face detected
- **Power Draw**: 300-400 mA
- **Duration**: 0.5-2 seconds
- **Components On**: Camera, WiFi, Processing, Servo (if unlocking)

## 🔧 Implementation Strategy

### Hardware Components

#### Power Supply Module
- **Switching Regulator**: MP1584EN (5V 3A)
- **Boost Converter**: MT3608 (for battery backup)
- **Charge Controller**: TP4056 (for battery charging)
- **Power Multiplexer**: Automatic switching between AC and battery

#### Voltage Monitoring
- **ADC Monitoring**: ESP32 ADC for battery voltage
- **Voltage Divider**: 10kΩ + 4.7kΩ for battery monitoring
- **Thresholds**: Low battery warning at 3.4V, shutdown at 3.2V

#### Current Monitoring
- **Current Sensor**: INA219 (I2C current sensor)
- **Monitoring Points**: Total current, camera current, servo current
- **Alerts**: High current warnings, power consumption logging

### Software Implementation

#### Power Management Library
```cpp
class PowerManager {
private:
    enum PowerMode {
        DEEP_SLEEP = 0,
        LIGHT_SLEEP = 1,
        ACTIVE_DETECTION = 2,
        RECOGNITION = 3
    };

    PowerMode currentMode;
    unsigned long lastMotionTime;
    float batteryVoltage;
    float currentDraw;

public:
    void initialize();
    void setPowerMode(PowerMode mode);
    void monitorBattery();
    void handleLowBattery();
    bool isBatteryCharging();
    float getBatteryLevel();
    void enablePeripherals(bool camera, bool wifi, bool servo);
};
```

#### Sleep/Wake Logic
```cpp
void managePowerStates() {
    unsigned long currentTime = millis();

    // Check for motion timeout
    if (currentTime - lastMotionTime > MOTION_TIMEOUT) {
        enterDeepSleep();
        return;
    }

    // Check for face detection timeout
    if (currentTime - lastFaceTime > FACE_TIMEOUT) {
        enterLightSleep();
        return;
    }

    // Active detection mode
    if (motionDetected && !faceDetected) {
        enterActiveDetection();
        return;
    }

    // Recognition mode
    if (faceDetected) {
        enterRecognitionMode();
        return;
    }
}
```

### Power Optimization Techniques

#### Dynamic Frequency Scaling
- **Normal Mode**: 240 MHz (full speed)
- **Light Sleep**: 80 MHz (reduced speed)
- **Deep Sleep**: 0 MHz (clock stopped)

#### Peripheral Control
- **Camera Power**: GPIO control for camera enable pin
- **WiFi Management**: Turn off WiFi between operations
- **Servo Control**: Power down servo when not in use
- **LED Control**: Turn off status LEDs during sleep

#### Motion-Based Activation
- **PIR Sensor**: Wake system from deep sleep
- **Motion Timeout**: 30 seconds without motion → deep sleep
- **Face Timeout**: 5 seconds without face → light sleep
- **Recognition Timeout**: 2 seconds → return to detection

## 📊 Power Consumption Calculations

### 24-Hour Power Budget

#### Scenario 1: Low Traffic (Residential)
- **Deep Sleep**: 22 hours × 10 μA = 220 μAh
- **Light Sleep**: 1.5 hours × 0.8 mA = 1,200 μAh
- **Active Detection**: 0.4 hours × 150 mA = 60 mAh
- **Recognition**: 0.1 hours × 300 mA = 30 mAh
- **Total**: ~90 mAh per day
- **Battery Life**: 4000 mAh ÷ 90 mAh = 44 days

#### Scenario 2: Medium Traffic (Office)
- **Deep Sleep**: 18 hours × 10 μA = 180 μAh
- **Light Sleep**: 4 hours × 0.8 mA = 3,200 μAh
- **Active Detection**: 1.5 hours × 150 mA = 225 mAh
- **Recognition**: 0.5 hours × 300 mA = 150 mAh
- **Total**: ~380 mAh per day
- **Battery Life**: 4000 mAh ÷ 380 mAh = 10 days

#### Scenario 3: High Traffic (Public Area)
- **Deep Sleep**: 12 hours × 10 μA = 120 μAh
- **Light Sleep**: 8 hours × 0.8 mA = 6,400 μAh
- **Active Detection**: 3 hours × 150 mA = 450 mAh
- **Recognition**: 1 hour × 300 mA = 300 mAh
- **Total**: ~760 mAh per day
- **Battery Life**: 4000 mAh ÷ 760 mAh = 5 days

## 🔍 Monitoring & Analytics

### Power Monitoring Features
- **Real-time Current**: Display current power draw
- **Battery Level**: Percentage and voltage display
- **Power History**: 24-hour consumption graph
- **Alerts**: Low battery, high consumption warnings

### Web Interface Integration
- **Power Dashboard**: Real-time power statistics
- **Battery Status**: Voltage, percentage, charging status
- **Consumption Reports**: Daily/weekly power usage
- **Efficiency Metrics**: Average power per recognition

### Logging System
```json
{
  "timestamp": "2025-09-20T10:30:00",
  "battery_voltage": 3.85,
  "battery_percentage": 78,
  "current_draw": 45.2,
  "power_mode": "ACTIVE_DETECTION",
  "wifi_status": "connected",
  "camera_status": "active",
  "servo_status": "idle"
}
```

## 🛡️ Safety & Protection

### Battery Protection
- **Over-discharge Protection**: Cutoff at 3.2V
- **Over-charge Protection**: Cutoff at 4.2V
- **Short Circuit Protection**: Hardware fuse
- **Temperature Monitoring**: Thermal cutoff

### System Protection
- **Brown-out Detection**: ESP32 built-in protection
- **Voltage Monitoring**: Continuous voltage checking
- **Current Limiting**: Software limits on power draw
- **Thermal Protection**: Temperature monitoring

### Emergency Features
- **Low Battery Mode**: Disable non-essential features
- **Graceful Shutdown**: Safe system shutdown
- **Manual Override**: Mechanical door release
- **Status Indication**: LED battery level indicator

## 📋 Implementation Checklist

### Phase 1: Basic Power Management
- [ ] Implement power mode switching
- [ ] Add battery voltage monitoring
- [ ] Create basic sleep/wake logic
- [ ] Add power status reporting

### Phase 2: Battery Backup System
- [ ] Build battery backup circuit
- [ ] Implement automatic switching
- [ ] Add charging capability
- [ ] Create battery management system

### Phase 3: Advanced Power Optimization
- [ ] Implement motion-based activation
- [ ] Add power consumption analytics
- [ ] Optimize sleep cycles
- [ ] Create power efficiency testing

### Phase 4: Monitoring & Safety
- [ ] Add comprehensive monitoring
- [ ] Implement safety features
- [ ] Create emergency protocols
- [ ] Add user notifications

## 🎯 Performance Targets

### Power Efficiency
- **Average Current**: <25 mA (24h average)
- **Peak Current**: <500 mA
- **Sleep Time**: >90% of operation time
- **Battery Life**: >4 hours backup (as required)

### System Reliability
- **Brown-out Recovery**: Automatic recovery from power dips
- **Battery Monitoring**: Real-time battery health tracking
- **Power Fail Safety**: Graceful handling of power failures
- **Emergency Access**: Manual override capability

---

*Strategy Date: 20 September 2025*
*Recommended Battery: 2x 18650 (4000 mAh)*
*Expected Backup Time: 8-12 hours*
*Implementation Priority: High*
