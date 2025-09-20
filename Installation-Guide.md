# Installation Guide

This comprehensive guide will walk you through the complete installation and setup process for the ESP32-CAM Face Recognition Door Lock System.

## 📋 Prerequisites

### Hardware Requirements
- ESP32-CAM module (AI-Thinker recommended)
- FTDI USB programmer (USB to TTL converter)
- 5V 2A power supply
- Servo motor (SG90/MG90S) or solenoid lock
- Relay module (5V, 10A)
- Breadboard and jumper wires
- microSD card (optional, for data storage)

### Software Requirements
- Arduino IDE (latest version)
- USB cable for programming
- Computer with USB port
- WiFi network access

## 🛠️ Step 1: Hardware Assembly

### Component Connections

#### ESP32-CAM Pinout Reference
```
ESP32-CAM Pinout:
┌─────────────────────────────────────┐
│ GPIO  | Function      | Connected To │
├─────────────────────────────────────┤
│ GPIO 2 | Flash LED     | Built-in LED │
│ GPIO 4 | PIR Sensor    | Motion input │
│ GPIO 12| Relay Control | Door relay   │
│ GPIO 13| Servo Control | Servo motor  │
│ GPIO 14| Status LED    | Status light │
│ GPIO 16| Camera Data   | OV2640 D0    │
│ GPIO 17| Camera Enable | OV2640 EN    │
│ GPIO 18| Camera Reset  | OV2640 RST   │
│ GPIO 19| Camera PCLK   | OV2640 PCLK  │
│ GPIO 21| Camera VSYNC  | OV2640 VSYNC │
│ GPIO 22| Camera HSYNC  | OV2640 HSYNC │
│ GPIO 23| Camera SDATA  | OV2640 SDA   │
│ GPIO 25| Camera SCLK   | OV2640 SCK   │
│ GPIO 26| Camera D6     | OV2640 D6    │
│ GPIO 27| Camera D7     | OV2640 D7    │
│ GPIO 32| Battery ADC   | Voltage mon. │
│ GPIO 33| Current Sensor| Current mon. │
└─────────────────────────────────────┘
```

#### Wiring Diagram
```
ESP32-CAM          │   Connected To
───────────────────┼─────────────────────────
5V Pin            ─┼─ 5V Power Supply (+)
GND Pin           ─┼─ Power Supply (GND)
GPIO 12           ─┼─ Relay Module (IN)
GPIO 13           ─┼─ Servo Motor (Signal)
GPIO 4            ─┼─ PIR Sensor (OUT)
GPIO 14           ─┼─ LED (Anode) + 220Ω Res.
GPIO 32           ─┼─ Battery Voltage Divider
GPIO 33           ─┼─ Current Sensor (OUT)
───────────────────┼─────────────────────────
Relay NO          ─┼─ Door Lock (+)
Relay COM         ─┼─ Power Supply (+)
Servo VCC         ─┼─ 5V Power Supply
Servo GND         ─┼─ Common Ground
```

### Assembly Steps
1. **Prepare the ESP32-CAM**
   - Mount on breadboard
   - Connect power supply (5V to 5V, GND to GND)
   - Connect FTDI programmer to TX, RX, GND pins

2. **Connect the Door Mechanism**
   - Wire relay module to GPIO 12
   - Connect door lock to relay NO and COM terminals
   - Connect servo to GPIO 13 (if using servo lock)

3. **Add Sensors (Optional)**
   - Connect PIR motion sensor to GPIO 4
   - Add status LED to GPIO 14 with current limiting resistor

4. **Power Connections**
   - Ensure stable 5V power supply
   - Add decoupling capacitors (100nF) near ESP32-CAM
   - Test voltage stability before proceeding

## 💻 Step 2: Software Setup

### Install Arduino IDE
1. Download Arduino IDE from [arduino.cc](https://www.arduino.cc/en/software)
2. Install on your computer
3. Launch Arduino IDE

### Add ESP32 Board Support
1. Open Arduino IDE
2. Go to **File** → **Preferences**
3. In "Additional Board Manager URLs" add:
   ```
   https://dl.espressif.com/dl/package_esp32_index.json
   ```
4. Go to **Tools** → **Board** → **Boards Manager**
5. Search for "ESP32" and install the latest version

### Install Required Libraries
Go to **Tools** → **Manage Libraries** and install:
- `esp32-camera` by Espressif Systems
- `ArduinoJson` by Benoit Blanchon
- `ESP32Servo` by Kevin Harrington
- `WiFi` (usually included with ESP32 package)

### Download Project Code
1. Clone or download the project from GitHub
2. Extract to your Arduino projects folder
3. Open the main sketch file

## ⚙️ Step 3: Configuration

### WiFi Configuration
Edit the configuration section in the main sketch:
```cpp
// WiFi Settings
const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

// System Configuration
#define MAX_ENROLLED_USERS 8
#define RECOGNITION_CONFIDENCE 0.7
#define AUTO_LOCK_DELAY 5000  // 5 seconds
```

### Camera Configuration
```cpp
// Camera Settings
#define CAMERA_MODEL_AI_THINKER
#define XCLK_FREQ 20000000
#define PIXFORMAT JPEG
#define FRAMESIZE UXGA
#define JPEG_QUALITY 12
```

### Face Recognition Settings
```cpp
// Recognition Parameters
#define LBP_RADIUS 1
#define LBP_SAMPLES 8
#define MIN_FACE_SIZE 80
#define MAX_RECOGNITION_TIME 2000  // 2 seconds
```

## 📝 Step 4: Programming

### Prepare for Flashing
1. Connect FTDI programmer to ESP32-CAM
2. Set connections:
   - FTDI GND → ESP32 GND
   - FTDI 5V → ESP32 5V
   - FTDI TX → ESP32 RX
   - FTDI RX → ESP32 TX
   - FTDI GND → ESP32 GPIO 0 (for flash mode)

### Flash the Firmware
1. Select board: **Tools** → **Board** → **ESP32** → **AI Thinker ESP32-CAM**
2. Select port: Choose the FTDI COM port
3. Set upload speed: 115200 baud
4. Click **Upload** button
5. Release GPIO 0 after upload starts

### Monitor Serial Output
1. Open **Tools** → **Serial Monitor**
2. Set baud rate to 115200
3. Watch for boot messages and IP address

## 🌐 Step 5: Initial Setup

### Access Web Interface
1. Note the IP address from serial monitor
2. Open web browser
3. Navigate to `http://[IP_ADDRESS]`
4. You should see the main dashboard

### Initial Configuration
1. Go to **Settings** page
2. Configure system parameters
3. Set up user enrollment
4. Test door mechanism

### Enroll First User
1. Navigate to **Enroll** page
2. Enter user name
3. Follow face capture instructions
4. Wait for processing completion
5. Test recognition

## 🧪 Step 6: Testing

### Basic Functionality Test
1. **Camera Test**: Check live stream at `/cam`
2. **WiFi Test**: Verify network connectivity
3. **Recognition Test**: Test face detection
4. **Door Test**: Test unlock mechanism

### System Integration Test
1. **Motion Detection**: Test PIR sensor (if installed)
2. **Auto-lock**: Verify automatic locking
3. **Web Interface**: Test all controls
4. **Mobile Access**: Test on different devices

### Performance Test
1. **Recognition Speed**: Measure response time
2. **Accuracy Test**: Test with different users
3. **Power Consumption**: Monitor battery usage
4. **Network Stability**: Test under load

## 🔧 Troubleshooting

### Common Issues

#### Camera Not Working
- Check GPIO 0 is not grounded during normal operation
- Verify camera connections are secure
- Try different frame size settings
- Check power supply stability

#### WiFi Connection Issues
- Verify WiFi credentials
- Check signal strength
- Try moving closer to router
- Reset router if necessary

#### Recognition Not Working
- Ensure proper lighting
- Check face positioning
- Verify enrollment quality
- Adjust confidence threshold

#### Door Not Unlocking
- Check relay connections
- Verify power to lock mechanism
- Test manual control
- Check GPIO pin configuration

### Debug Mode
Enable debug output in the sketch:
```cpp
#define DEBUG_MODE true
#define SERIAL_BAUD 115200
```

## 📊 Post-Installation

### System Optimization
1. **Power Settings**: Configure sleep modes
2. **Camera Settings**: Optimize for your environment
3. **Network Settings**: Set static IP if needed
4. **Security Settings**: Configure access controls

### Monitoring Setup
1. **Access Logs**: Review recognition history
2. **System Status**: Monitor performance metrics
3. **Battery Level**: Set up low battery alerts
4. **Network Health**: Monitor connectivity

### Maintenance Schedule
- **Daily**: Check system status and logs
- **Weekly**: Test recognition accuracy
- **Monthly**: Clean camera lens, check connections
- **Quarterly**: Update firmware, review security

## 📞 Support

If you encounter issues during installation:
1. Check the [Troubleshooting](Troubleshooting) section
2. Review the [FAQ](FAQ) for common questions
3. Open an issue on [GitHub](https://github.com/yourusername/esp32-face-door-lock/issues)

---

*Installation Guide Version: 1.0.0*
*Last updated: 20 September 2025*
