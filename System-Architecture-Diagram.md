# System Architecture Diagram & Design

## 📋 Overview
Comprehensive system architecture design for ESP32-CAM Face Recognition Door Lock System, including component relationships, data flow, and integration patterns.

## 🏗️ High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        USER INTERACTION LAYER                          │
├─────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │   Web UI    │  │  Mobile App │  │  Admin Panel│  │  API Client │    │
│  │ (Browser)   │  │   (PWA)     │  │   (Web)     │  │ (REST API)  │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
                    │                 │                 │
                    │ HTTP/HTTPS      │ WebSocket       │ HTTP
                    │                 │                 │
                    ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      APPLICATION LAYER (ESP32-CAM)                     │
├─────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │  Web Server │  │ Face Recog  │  │  Door Ctrl  │  │  Power Mgr  │    │
│  │   Module    │  │  Engine     │  │  Module     │  │  Module     │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
├─────────────────────────────────────────────────────────────────────────┘
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │   Camera    │  │   WiFi      │  │   GPIO      │  │   Flash     │    │
│  │   Module    │  │  Module     │  │ Controller  │  │  Storage    │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
                    │                 │                 │
                    │                 │                 │
                    ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      HARDWARE INTERFACE LAYER                         │
├─────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │   OV2640    │  │   Servo/    │  │   Relay     │  │   Sensors   │    │
│  │   Camera    │  │   Motor     │  │  Module     │  │   (PIR)     │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
```

## 🔧 Component Architecture

### ESP32-CAM Core System

#### 1. Web Server Module
```cpp
class WebServerModule {
private:
    ESP32WebServer server;
    WebSocketHandler wsHandler;
    CameraStreamHandler camHandler;

public:
    void initialize();
    void handleClient();
    void sendWebSocketMessage(String message);
    void streamCameraFeed();
    void handleAPIRequest(String endpoint, String method);
};
```

#### 2. Face Recognition Engine
```cpp
class FaceRecognitionEngine {
private:
    LBPFeatureExtractor lbpExtractor;
    FaceTemplateMatcher matcher;
    FaceDatabase faceDB;
    AntiSpoofingDetector spoofDetector;

public:
    bool initialize();
    RecognitionResult recognizeFace(uint8_t* imageData);
    bool enrollFace(uint8_t* imageData, String userName);
    bool deleteFace(String userName);
    float getRecognitionConfidence();
};
```

#### 3. Door Control Module
```cpp
class DoorControlModule {
private:
    Servo doorServo;
    Relay doorRelay;
    DoorState currentState;
    Timer autoLockTimer;

public:
    void initialize();
    void unlockDoor();
    void lockDoor();
    DoorState getDoorState();
    void setAutoLockDelay(int seconds);
    void enableManualOverride();
};
```

#### 4. Power Management Module
```cpp
class PowerManagementModule {
private:
    PowerMode currentMode;
    BatteryMonitor batteryMonitor;
    MotionDetector motionDetector;
    SleepManager sleepManager;

public:
    void initialize();
    void setPowerMode(PowerMode mode);
    float getBatteryLevel();
    bool isBatteryCharging();
    void enableMotionWakeup();
    void enterSleepMode();
};
```

## 📊 Data Flow Architecture

### Face Recognition Flow
```
Motion Detected (PIR)
         ↓
Wake from Sleep Mode
         ↓
Initialize Camera
         ↓
Capture Image Frame
         ↓
Preprocess Image (resize, normalize)
         ↓
Extract LBP Features
         ↓
Compare with Database
         ↓
Calculate Confidence Score
         ↓
Decision (Accept/Reject)
         ↓
Trigger Door Control
         ↓
Log Access Event
         ↓
Return to Sleep Mode
```

### Web Interface Flow
```
User Opens Web Browser
         ↓
HTTP Request to ESP32
         ↓
Serve HTML/CSS/JS Files
         ↓
WebSocket Connection
         ↓
Real-time Camera Stream
         ↓
User Interaction (enroll, settings)
         ↓
AJAX API Calls
         ↓
Process Request
         ↓
Update Configuration
         ↓
WebSocket Response
         ↓
UI Update
```

### Power Management Flow
```
System Startup
         ↓
Initialize Power Monitoring
         ↓
Check Battery Level
         ↓
Set Initial Power Mode
         ↓
Monitor Motion/Activity
         ↓
Dynamic Mode Switching
         ↓
Battery Level Monitoring
         ↓
Low Battery Actions
         ↓
Sleep Mode Management
```

## 🔐 Security Architecture

### Authentication & Authorization
```
┌─────────────────────────────────────┐
│         Authentication Flow         │
├─────────────────────────────────────┤
│  User → Login Interface             │
│         ↓                           │
│  Credentials Validation             │
│         ↓                           │
│  Session Token Generation           │
│         ↓                           │
│  Access Control Enforcement         │
│         ↓                           │
│  Authorized Operations Only         │
└─────────────────────────────────────┘
```

### Data Protection Layers
```
┌─────────────────────────────────────┐
│        Data Protection Stack        │
├─────────────────────────────────────┤
│  Application Layer Encryption       │
│         ↓                           │
│  TLS/HTTPS Communication            │
│         ↓                           │
│  Network Access Controls            │
│         ↓                           │
│  Physical Security Measures         │
└─────────────────────────────────────┘
```

## 📡 Network Architecture

### Connectivity Options
```
┌─────────────────────────────────────┐
│        Network Configuration        │
├─────────────────────────────────────┤
│  ┌─────────┐  ┌─────────┐            │
│  │  WiFi   │  │  LAN    │            │
│  │ Station │  │  (RJ45) │            │
│  └─────────┘  └─────────┘            │
│         ↓          ↓                │
│  ┌─────────┐  ┌─────────┐            │
│  │  DHCP   │  │ Static  │            │
│  │ Client  │  │  IP     │            │
│  └─────────┘  └─────────┘            │
│         ↓          ↓                │
│  ┌─────────┐  ┌─────────┐            │
│  │  AP     │  │  Client │            │
│  │ Mode    │  │  Mode   │            │
│  └─────────┘  └─────────┘            │
└─────────────────────────────────────┘
```

### Communication Protocols
```
HTTP/HTTPS (Port 80/443)
    ↓
WebSocket (Port 81)
    ↓
MQTT (Port 1883)
    ↓
NTP (Port 123)
    ↓
mDNS (Port 5353)
```

## 💾 Storage Architecture

### Data Storage Hierarchy
```
┌─────────────────────────────────────┐
│           Storage Layers            │
├─────────────────────────────────────┤
│  ┌─────────┐  ┌─────────┐            │
│  │ Runtime │  │ Program │            │
│  │ Memory  │  │ Memory  │            │
│  │ (RAM)   │  │ (Flash) │            │
│  └─────────┘  └─────────┘            │
│         ↓          ↓                │
│  ┌─────────┐  ┌─────────┐            │
│  │ Face    │  │ System  │            │
│  │ Database│  │ Config  │            │
│  └─────────┘  └─────────┘            │
│         ↓          ↓                │
│  ┌─────────┐  ┌─────────┐            │
│  │ SD Card │  │ EEPROM  │            │
│  │ Storage │  │ Backup  │            │
│  └─────────┘  └─────────┘            │
└─────────────────────────────────────┘
```

### File System Structure
```
/ (Root)
├── /system/
│   ├── config.json          # System configuration
│   ├── wifi.json           # WiFi settings
│   └── security.json       # Security settings
├── /faces/
│   ├── user1.dat           # Face template 1
│   ├── user2.dat           # Face template 2
│   └── enrollment.log      # Enrollment history
├── /logs/
│   ├── access.log          # Access attempts
│   ├── system.log          # System events
│   └── error.log           # Error events
└── /www/
    ├── index.html          # Web interface
    ├── style.css           # Styling
    └── script.js           # Client-side logic
```

## 🔄 State Management

### System States
```cpp
enum SystemState {
    STATE_BOOTING = 0,        // System starting up
    STATE_CONFIGURING = 1,    // Loading configuration
    STATE_CONNECTING = 2,     // Connecting to WiFi
    STATE_READY = 3,          // Ready for operation
    STATE_ENROLLING = 4,      // Enrolling new face
    STATE_RECOGNIZING = 5,    // Processing recognition
    STATE_UNLOCKING = 6,      // Unlocking door
    STATE_ERROR = 7,          // Error state
    STATE_SLEEPING = 8        // Low power mode
};
```

### State Transition Diagram
```
BOOTING → CONFIGURING → CONNECTING → READY
   ↓           ↓           ↓          ↓
   └─────── ERROR ◄────────┴─────────┘
READY → ENROLLING → READY
READY → RECOGNIZING → UNLOCKING → READY
READY → SLEEPING → READY (motion detected)
ANY STATE → ERROR (critical error)
```

## 📱 User Interface Architecture

### Web Interface Components
```
┌─────────────────────────────────────┐
│         Main Dashboard              │
├─────────────────────────────────────┤
│  ┌─────────┬─────────┬─────────┐     │
│  │ Camera  │ Status  │ Control │     │
│  │ Stream  │ Panel   │ Panel   │     │
│  └─────────┴─────────┴─────────┘     │
│  ┌─────────────────────────────┐     │
│  │      Recognition Log        │     │
│  └─────────────────────────────┘     │
└─────────────────────────────────────┘
```

### Mobile-Responsive Design
```
Desktop Layout (>1024px)
    ↓
Tablet Layout (768px-1024px)
    ↓
Mobile Layout (<768px)
    ↓
Compact Layout (<480px)
```

## 🔧 Hardware Integration Architecture

### GPIO Pin Mapping
```
ESP32-CAM Pinout:
┌─────────────────────────────────────┐
│ GPIO Pin | Function | Connected To   │
├─────────────────────────────────────┤
│ GPIO 2   | Flash   | Built-in LED   │
│ GPIO 4   | Input   | PIR Sensor     │
│ GPIO 12  | Output  | Relay Control  │
│ GPIO 13  | Output  | Servo Control  │
│ GPIO 14  | Output  | Status LED     │
│ GPIO 15  | Output  | Camera XCLK    │
│ GPIO 16  | Input   | Camera Data    │
│ GPIO 17  | Output  | Camera Enable  │
│ GPIO 18  | Output  | Camera Reset   │
│ GPIO 19  | Output  | Camera PCLK    │
│ GPIO 21  | Output  | Camera VSYNC   │
│ GPIO 22  | Output  | Camera HSYNC   │
│ GPIO 23  | Output  | Camera SDATA   │
│ GPIO 25  | Output  | Camera SCLK    │
│ GPIO 26  | Output  | Camera D6      │
│ GPIO 27  | Output  | Camera D7      │
│ GPIO 32  | Input   | Battery ADC    │
│ GPIO 33  | Input   | Current Sensor │
└─────────────────────────────────────┘
```

### Peripheral Integration
```
┌─────────────────────────────────────┐
│        Hardware Integration         │
├─────────────────────────────────────┤
│  Camera Module (OV2640)             │
│  ├── Data Pins (D0-D7)              │
│  ├── Control Pins (XCLK, PCLK)      │
│  ├── Sync Pins (VSYNC, HSYNC)       │
│  └── I2C Pins (SDA, SCK)            │
├─────────────────────────────────────┤
│  Motion Sensor (PIR)                │
│  ├── Signal Pin                     │
│  ├── VCC (5V)                       │
│  └── GND                            │
├─────────────────────────────────────┤
│  Door Lock Mechanism                │
│  ├── Servo Motor (PWM Control)      │
│  ├── Relay Module (GPIO Control)    │
│  └── Position Feedback (Optional)   │
├─────────────────────────────────────┤
│  Power Management                   │
│  ├── Battery Voltage Monitor        │
│  ├── Current Sensor                 │
│  └── Power Mode Control             │
└─────────────────────────────────────┘
```

## 📊 Monitoring & Logging Architecture

### Event Logging System
```
┌─────────────────────────────────────┐
│         Event Categories            │
├─────────────────────────────────────┤
│  System Events                      │
│  ├── Boot sequence                  │
│  ├── Configuration changes          │
│  ├── Network status                 │
│  └── Power state changes            │
├─────────────────────────────────────┤
│  Security Events                    │
│  ├── Face recognition attempts      │
│  ├── Authentication failures        │
│  ├── Unauthorized access attempts   │
│  └── System integrity checks        │
├─────────────────────────────────────┤
│  Access Events                      │
│  ├── Door unlock events             │
│  ├── User enrollment                │
│  ├── Template updates               │
│  └── Access denials                 │
├─────────────────────────────────────┤
│  Error Events                       │
│  ├── Hardware failures              │
│  ├── Software exceptions            │
│  ├── Network errors                 │
│  └── Power issues                   │
└─────────────────────────────────────┘
```

### Performance Monitoring
```
┌─────────────────────────────────────┐
│       Performance Metrics           │
├─────────────────────────────────────┤
│  Recognition Performance            │
│  ├── Accuracy rate                  │
│  ├── Response time                  │
│  ├── False positive rate            │
│  └── Confidence scores              │
├─────────────────────────────────────┤
│  System Performance                 │
│  ├── CPU usage                      │
│  ├── Memory usage                   │
│  ├── Network latency                │
│  └── Power consumption              │
├─────────────────────────────────────┤
│  Hardware Performance               │
│  ├── Camera frame rate              │
│  ├── Servo response time            │
│  ├── WiFi signal strength           │
│  └── Battery level                  │
└─────────────────────────────────────┘
```

## 🔄 Integration Patterns

### Publish-Subscribe Pattern
```
Event Publisher (ESP32)
    ↓
Event Subscribers
├── Web Interface
├── Mobile App
├── Logging System
└── Notification Service
```

### Observer Pattern
```
Subject (Door Controller)
    ↓
Observers
├── Web UI (real-time updates)
├── Logging System (event recording)
├── Security Monitor (anomaly detection)
└── Power Manager (state changes)
```

### Factory Pattern
```
Hardware Factory
├── Camera Factory → OV2640 Camera
├── Servo Factory → SG90 Servo
├── Sensor Factory → PIR Sensor
└── Storage Factory → SD Card Storage
```

## 📋 Implementation Roadmap

### Phase 1: Core Architecture
- [ ] Implement basic web server
- [ ] Create face recognition engine
- [ ] Add door control module
- [ ] Implement power management

### Phase 2: Integration
- [ ] Connect hardware components
- [ ] Implement data flow
- [ ] Add security layers
- [ ] Create user interfaces

### Phase 3: Enhancement
- [ ] Add monitoring systems
- [ ] Implement logging
- [ ] Add notification services
- [ ] Performance optimization

---

*Architecture Design Date: 20 September 2025*
*Architecture Style: Layered Architecture with Microservices*
*Scalability: Designed for single-door deployment*
*Maintainability: Modular component design*
