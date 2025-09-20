# Hardware Setup Guide

This guide provides detailed instructions for assembling and configuring the hardware components of the ESP32-CAM Face Recognition Door Lock System.

## 📋 Hardware Components List

### Essential Components
| Component | Model | Quantity | Purpose | Cost (USD) |
|-----------|-------|----------|---------|------------|
| ESP32-CAM Module | AI-Thinker | 1 | Main controller | $8-12 |
| FTDI Programmer | USB-TTL | 1 | Firmware flashing | $5-10 |
| Power Supply | 5V 2A | 1 | System power | $6-8 |
| Servo Motor | SG90 | 1 | Door mechanism | $3-5 |
| Relay Module | 5V 10A | 1 | Power switching | $2-4 |
| Breadboard | 400-point | 1 | Prototyping | $3-5 |
| Jumper Wires | M-M/F-F | 20 | Connections | $2-4 |
| microSD Card | 8GB+ | 1 | Data storage | $5-10 |

### Optional Components
| Component | Purpose | Cost (USD) |
|-----------|---------|------------|
| PIR Motion Sensor | Wake-up detection | $2-4 |
| LED Indicators | Status display | $1-3 |
| Buzzer | Audio feedback | $1-2 |
| Battery Pack | Backup power | $10-15 |
| Enclosure | Weather protection | $5-10 |

## 🔧 Assembly Instructions

### Step 1: ESP32-CAM Preparation

#### Camera Module Setup
1. **Inspect the Module**
   - Check for any visible damage
   - Verify all pins are straight
   - Ensure camera lens is clean

2. **Breadboard Mounting**
   ```
   ESP32-CAM Breadboard Layout:
   ┌─────────────────────────────────┐
   │ [ESP32-CAM Module]              │
   │                                 │
   │ GND  3.3V EN IO0 IO4 IO5 5V GND │
   │ IO12 IO13 IO14 IO15 IO16 IO17   │
   │ IO18 IO19 IO21 IO22 IO23 IO25   │
   │ IO26 IO27 IO32 IO33 TX  RX GND  │
   └─────────────────────────────────┘
   ```

3. **Power Connections**
   - Connect 5V pin to breadboard +5V rail
   - Connect GND pin to breadboard ground rail
   - Add 100nF decoupling capacitor between 5V and GND

### Step 2: FTDI Programmer Connection

#### Programming Interface
```
FTDI Programmer → ESP32-CAM
────────────────────────────
5V     → 5V     (Power)
GND    → GND    (Ground)
TX     → RX     (Data out)
RX     → TX     (Data in)
GND    → GPIO0  (Flash mode)
```

**Important**: Connect GPIO0 to GND only during flashing, disconnect for normal operation.

### Step 3: Door Control Mechanism

#### Option A: Servo Motor Setup
```cpp
// Servo Configuration
#define SERVO_PIN 13
#define SERVO_UNLOCK_ANGLE 90
#define SERVO_LOCK_ANGLE 0

// Wiring
ESP32 GPIO 13 → Servo Signal (Orange wire)
5V Rail       → Servo VCC (Red wire)
GND Rail      → Servo GND (Brown wire)
```

#### Option B: Solenoid Lock Setup
```cpp
// Relay Configuration
#define RELAY_PIN 12
#define RELAY_ON HIGH
#define RELAY_OFF LOW

// Wiring
ESP32 GPIO 12 → Relay IN pin
5V Rail       → Relay VCC
GND Rail      → Relay GND
Relay NO      → Lock +
Relay COM     → Power Supply +
```

### Step 4: Sensor Integration (Optional)

#### PIR Motion Sensor
```cpp
// PIR Configuration
#define PIR_PIN 4
#define PIR_WAKEUP_THRESHOLD 500  // ms

// Wiring
ESP32 GPIO 4  → PIR OUT
5V Rail       → PIR VCC
GND Rail      → PIR GND
```

#### Status LEDs
```cpp
// LED Configuration
#define STATUS_LED_PIN 14
#define RECOGNITION_LED_PIN 2

// Wiring
ESP32 GPIO 14 → LED Anode (+)
GND Rail      → LED Cathode (-) + 220Ω resistor
```

### Step 5: Power Management

#### Main Power Supply
- **Input**: 100-240V AC wall adapter
- **Output**: 5V DC, 2A minimum
- **Connector**: 5.5mm x 2.1mm barrel jack or USB
- **Stability**: ±5% voltage regulation

#### Battery Backup (Optional)
```cpp
// Battery Configuration
#define BATTERY_PIN 32
#define BATTERY_FULL 4.2  // 18650 full voltage
#define BATTERY_LOW 3.3   // Low battery threshold

// Wiring
Battery + → 5V Rail (via protection diode)
Battery - → GND Rail
ESP32 GPIO 32 → Voltage divider (R1=100k, R2=22k)
```

## 📐 Circuit Diagrams

### Basic System Schematic
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Power Supply  │    │   ESP32-CAM     │    │   Door Lock     │
│   5V 2A DC      │    │   Controller    │    │   Mechanism     │
│                 │    │                 │    │                 │
│  +──────┬───────┼────┼──────┬──────────┼────┼──────┬──────────┤
│  │      │       │    │      │          │    │      │          │
│  │  ┌───▼───┐   │    │  ┌───▼───┐      │    │  ┌───▼───┐      │
│  │  │Relay  │   │    │  │Servo  │      │    │  │Lock   │      │
│  │  │Module │◄──┼────┼──┤Motor  │◄─────┼────┼──┤Device │◄─────┤
│  │  └──────┘   │    │  └──────┘      │    │  └──────┘      │
│  │      │       │    │      │          │    │      │          │
│  └──────┴───────┘    └──────┴──────────┘    └──────┴──────────┘
│         │                       │                       │
│         └───────────────────────┼───────────────────────┘
│                                 │
└─────────────────────────────────┼──────────────────────────────┐
                                  │                              │
                    ┌─────────────┼─────────────┐                │
                    │   FTDI      │   Sensors   │                │
                    │ Programmer  │ (Optional)  │                │
                    │             │             │                │
                    │ USB Port    │ PIR, LEDs   │                │
                    └─────────────┴─────────────┘                │
                                                                  │
                    ┌───────────────────────────────────────────┘
                    │
                    │   WiFi Network
                    │   (2.4GHz 802.11 b/g/n)
                    │
                    └────────────────────────────────────────────
```

### Power Distribution
```
Power Supply (5V 2A)
        │
        ├─────────────────┬─────────────────┬─────────────────┐
        │                 │                 │                 │
    ┌───▼───┐         ┌───▼───┐         ┌───▼───┐         ┌───▼───┐
    │ESP32- │         │Relay  │         │Servo  │         │Sensors│
    │CAM    │         │Module │         │Motor  │         │       │
    │Module │         │       │         │       │         │       │
    └───┬───┘         └───┬───┘         └───┬───┘         └───┬───┘
        │                 │                 │                 │
        └─────────────────┼─────────────────┼─────────────────┘
                          │                 │
                    ┌─────▼─────┐     ┌─────▼─────┐
                    │Door Lock  │     │Status LEDs│
                    │Mechanism  │     │           │
                    └───────────┘     └───────────┘
```

## 🧪 Testing Hardware Components

### Component-by-Component Testing

#### 1. Power Supply Test
```cpp
// Test power stability
float voltage = analogRead(35) * 3.3 / 4095 * 2; // Voltage divider
Serial.print("Voltage: ");
Serial.println(voltage);
```

#### 2. ESP32-CAM Test
- Upload basic blink sketch
- Verify LED flashing
- Check serial communication
- Test WiFi connectivity

#### 3. Camera Test
- Upload camera example sketch
- Check serial monitor for camera initialization
- Verify frame capture
- Test different resolution settings

#### 4. Servo/Relay Test
```cpp
// Test servo movement
servo.write(0);   // Lock position
delay(1000);
servo.write(90);  // Unlock position
delay(1000);

// Test relay
digitalWrite(RELAY_PIN, HIGH);  // Activate
delay(1000);
digitalWrite(RELAY_PIN, LOW);   // Deactivate
```

#### 5. Sensor Test (if installed)
```cpp
// Test PIR sensor
int motion = digitalRead(PIR_PIN);
if (motion == HIGH) {
    Serial.println("Motion detected!");
}
```

### System Integration Test
1. **Power Test**: Verify all components receive power
2. **Communication Test**: Check ESP32-CAM to PC connection
3. **Camera Test**: Verify image capture and streaming
4. **WiFi Test**: Confirm network connectivity
5. **Door Test**: Test lock/unlock mechanism
6. **Sensor Test**: Verify motion detection (if installed)

## 🔧 Configuration and Calibration

### Camera Settings Optimization
```cpp
// Optimal settings for face recognition
#define XCLK_FREQ 20000000
#define PIXFORMAT JPEG
#define FRAMESIZE QVGA  // 320x240 for faster processing
#define JPEG_QUALITY 12  // Balance quality/speed
#define CONTRAST 0
#define BRIGHTNESS 0
#define SATURATION 0
```

### Servo Calibration
```cpp
// Calibrate servo angles
#define LOCK_ANGLE 0    // Adjust based on your servo
#define UNLOCK_ANGLE 90 // Adjust based on your mechanism
#define SERVO_SPEED 15  // Degrees per second
```

### Recognition Parameters
```cpp
// Tune recognition sensitivity
#define CONFIDENCE_THRESHOLD 0.7  // 70% confidence required
#define MIN_FACE_SIZE 80          // Minimum face size in pixels
#define MAX_RECOGNITION_TIME 2000 // 2 second timeout
```

## 📊 Performance Optimization

### Power Consumption Optimization
- Use QVGA resolution instead of UXGA
- Implement sleep modes between recognition attempts
- Use PIR sensor for wake-up to save power
- Optimize camera frame rate based on needs

### Recognition Speed Optimization
- Use smaller frame sizes for faster processing
- Implement region of interest (ROI) detection
- Use lower JPEG quality for faster transmission
- Optimize LBP parameters for your use case

### Memory Usage Optimization
- Limit number of enrolled users
- Use dynamic memory allocation carefully
- Implement memory cleanup routines
- Monitor heap usage during operation

## 🛠️ Troubleshooting Hardware Issues

### Common Problems and Solutions

#### Power Issues
- **Symptom**: ESP32-CAM won't boot
- **Solution**: Check 5V supply stability, add capacitors
- **Test**: Measure voltage at ESP32 pins

#### Camera Issues
- **Symptom**: Camera initialization fails
- **Solution**: Check camera connections, try different settings
- **Test**: Use camera example sketch

#### WiFi Issues
- **Symptom**: Cannot connect to network
- **Solution**: Check credentials, signal strength, try 2.4GHz
- **Test**: Use WiFi scan example

#### Servo Issues
- **Symptom**: Servo not moving or jittering
- **Solution**: Check power supply, use separate power for servo
- **Test**: Use servo sweep example

#### Recognition Issues
- **Symptom**: Poor recognition accuracy
- **Solution**: Improve lighting, adjust camera angle, retrain users
- **Test**: Use face detection example first

### Debug Tools
- **Serial Monitor**: Check system messages
- **Multimeter**: Test voltages and continuity
- **Logic Analyzer**: Debug digital signals
- **Oscilloscope**: Check signal quality (if available)

## 📋 Final Assembly Checklist

- [ ] All components securely connected
- [ ] Power supply stable and properly rated
- [ ] ESP32-CAM properly mounted and oriented
- [ ] Camera lens clean and unobstructed
- [ ] Door mechanism tested and calibrated
- [ ] Sensors (if any) properly positioned
- [ ] All wires organized and labeled
- [ ] System tested with basic sketches
- [ ] WiFi connectivity verified
- [ ] Camera functionality confirmed

## 📞 Support and Resources

- **Hardware Documentation**: [ESP32-CAM Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32_camera_web_server_user_guide_en.pdf)
- **Pinout Reference**: [ESP32-CAM Pinout Guide](https://randomnerdtutorials.com/esp32-cam-camera-pinout-arduino-ide/)
- **Troubleshooting**: See [Troubleshooting](Troubleshooting) wiki page
- **Community Support**: [GitHub Issues](https://github.com/yourusername/esp32-face-door-lock/issues)

---

*Hardware Setup Guide Version: 1.0.0*
*Last updated: 20 September 2025*
