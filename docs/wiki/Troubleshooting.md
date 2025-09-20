# Troubleshooting Guide

This comprehensive troubleshooting guide helps you diagnose and resolve common issues with the ESP32-CAM Face Recognition Door Lock System.

## 📋 Quick Diagnostic Checklist

Before diving into specific issues, run through this checklist:

- [ ] Check power supply (5V, stable output)
- [ ] Verify WiFi connection and signal strength
- [ ] Confirm ESP32-CAM is not in flash mode (GPIO0 floating)
- [ ] Check serial monitor for error messages
- [ ] Verify all hardware connections are secure
- [ ] Test with basic example sketches first
- [ ] Check system logs for error patterns

## 🔧 Hardware Issues

### Power Supply Problems

#### Symptom: ESP32-CAM won't boot or is unstable
**Diagnosis:**
- Measure voltage at ESP32-CAM 5V and 3.3V pins
- Check for voltage drops under load
- Verify power supply current rating (minimum 2A)

**Solutions:**
1. **Insufficient Current**: Upgrade to 5V 2A+ power supply
2. **Voltage Drop**: Use shorter, thicker wires
3. **Brown-out Reset**: Add large capacitor (1000μF) across power rails
4. **Ground Loop**: Ensure single ground point

**Test Command:**
```cpp
// Add to setup() for power monitoring
Serial.print("Voltage: ");
Serial.println(analogRead(35) * 3.3 / 4095 * 2); // Voltage divider
```

#### Symptom: Camera initialization fails
**Diagnosis:**
- Check camera connections (especially OV2640 pins)
- Verify camera module is properly seated
- Test with different camera settings

**Solutions:**
1. **Loose Connections**: Reseat camera module carefully
2. **Wrong Pinout**: Verify AI-Thinker pinout vs other models
3. **Power Issue**: Ensure 3.3V rail is stable
4. **I2C Conflict**: Check for I2C address conflicts

### Camera Issues

#### Symptom: Camera stream not working
**Diagnosis:**
- Check serial monitor for camera initialization messages
- Verify camera example sketch works
- Test different resolution settings

**Solutions:**
1. **Flash Mode**: Ensure GPIO0 is not grounded during normal operation
2. **Wrong Board Selection**: Select "AI Thinker ESP32-CAM" in Arduino IDE
3. **Memory Issue**: Use lower resolution (QVGA instead of UXGA)
4. **Power Supply**: Camera needs stable 5V supply

**Test Sketch:**
```cpp
#include "esp_camera.h"

// Camera configuration
camera_config_t config = {
  .pin_pwdn = 32,
  .pin_reset = -1,
  .pin_xclk = 0,
  .pin_sscb_sda = 26,
  .pin_sscb_scl = 27,
  .pin_d7 = 35,
  .pin_d6 = 34,
  .pin_d5 = 39,
  .pin_d4 = 36,
  .pin_d3 = 21,
  .pin_d2 = 19,
  .pin_d1 = 18,
  .pin_d0 = 5,
  .pin_vsync = 25,
  .pin_href = 23,
  .pin_pclk = 22,
  .xclk_freq_hz = 20000000,
  .ledc_timer = LEDC_TIMER_0,
  .ledc_channel = LEDC_CHANNEL_0,
  .pixel_format = PIXFORMAT_JPEG,
  .frame_size = FRAMESIZE_QVGA,
  .jpeg_quality = 12,
  .fb_count = 1
};

void setup() {
  Serial.begin(115200);
  if(psramInit() == ESP_OK) {
    Serial.println("PSRAM initialized");
  }

  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return;
  }
  Serial.println("Camera initialized successfully");
}
```

#### Symptom: Poor image quality
**Solutions:**
1. **Clean Lens**: Gently clean camera lens with microfiber cloth
2. **Adjust Settings**: Use lower resolution for better frame rate
3. **Lighting**: Ensure adequate lighting (300-1000 lux)
4. **Focus**: Some modules allow manual focus adjustment

### WiFi and Network Issues

#### Symptom: Cannot connect to WiFi
**Diagnosis:**
- Check WiFi credentials (SSID and password)
- Verify 2.4GHz network (ESP32-CAM doesn't support 5GHz)
- Check signal strength and interference

**Solutions:**
1. **Wrong Frequency**: Ensure 2.4GHz network
2. **Signal Strength**: Move closer to router or add WiFi extender
3. **Hidden SSID**: Some ESP32 versions don't support hidden networks
4. **Special Characters**: Avoid special characters in SSID/password

**Test Command:**
```cpp
// Add to setup() for WiFi debugging
WiFi.begin(ssid, password);
Serial.print("Connecting to WiFi");
int attempts = 0;
while (WiFi.status() != WL_CONNECTED && attempts < 30) {
  delay(1000);
  Serial.print(".");
  attempts++;
}
if (WiFi.status() == WL_CONNECTED) {
  Serial.println("\nConnected!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
} else {
  Serial.println("\nConnection failed!");
}
```

#### Symptom: Web interface not accessible
**Solutions:**
1. **Check IP**: Verify IP address from serial monitor
2. **Firewall**: Disable firewall temporarily for testing
3. **Port**: Ensure port 80 is accessible
4. **Network**: Test from different devices on same network

### Face Recognition Issues

#### Symptom: Face not detected
**Diagnosis:**
- Check lighting conditions
- Verify face positioning
- Test with face detection example first

**Solutions:**
1. **Lighting**: Ensure adequate, even lighting (300-1000 lux)
2. **Distance**: Position face 30-80cm from camera
3. **Angle**: Keep face relatively straight-on (±15 degrees)
4. **Size**: Ensure face occupies significant portion of frame

#### Symptom: Poor recognition accuracy
**Solutions:**
1. **Re-enrollment**: Delete and re-enroll users
2. **Multiple Samples**: Use 5-10 enrollment images
3. **Varied Conditions**: Enroll under different lighting
4. **Confidence Threshold**: Adjust threshold in settings

#### Symptom: False positives/negatives
**Solutions:**
1. **Threshold Tuning**: Adjust confidence threshold (0.7-0.9)
2. **User Management**: Limit enrolled users to 5-8
3. **Anti-spoofing**: Enable basic anti-spoofing measures
4. **Template Quality**: Check template sizes in logs

### Door Control Issues

#### Symptom: Door won't unlock
**Diagnosis:**
- Test relay/servo independently
- Check GPIO pin configuration
- Verify power to lock mechanism

**Solutions:**
1. **Relay Test**: Use LED to test relay switching
2. **Servo Power**: Ensure separate power supply for servo
3. **GPIO Test**: Use digitalWrite to test pin directly
4. **Lock Mechanism**: Verify lock compatibility

**Test Code:**
```cpp
// Test door mechanism
#define RELAY_PIN 12
#define SERVO_PIN 13

void setup() {
  Serial.begin(115200);
  pinMode(RELAY_PIN, OUTPUT);
  // Test relay
  digitalWrite(RELAY_PIN, HIGH);
  Serial.println("Relay ON");
  delay(2000);
  digitalWrite(RELAY_PIN, LOW);
  Serial.println("Relay OFF");
}

void loop() {}
```

#### Symptom: Door won't lock
**Solutions:**
1. **Auto-lock**: Check auto-lock settings and timing
2. **Mechanism**: Verify lock returns to locked position
3. **Power**: Ensure consistent power to lock mechanism
4. **Calibration**: Check servo angles for lock/unlock

### Software and Firmware Issues

#### Symptom: Upload fails
**Solutions:**
1. **Flash Mode**: Ground GPIO0 during upload
2. **Driver**: Install CH340/CH341 drivers for FTDI
3. **Port Selection**: Choose correct COM port
4. **Board Selection**: Select "AI Thinker ESP32-CAM"

#### Symptom: System crashes or resets
**Diagnosis:**
- Check for memory leaks
- Monitor power supply stability
- Look for infinite loops

**Solutions:**
1. **Watchdog**: Implement hardware watchdog timer
2. **Memory Management**: Use dynamic allocation carefully
3. **Error Handling**: Add try-catch blocks
4. **Power Supply**: Ensure stable 5V supply

#### Symptom: WebSocket connection fails
**Solutions:**
1. **Browser Support**: Use modern browser with WebSocket support
2. **Network**: Check firewall and proxy settings
3. **Code**: Verify WebSocket implementation
4. **Memory**: Monitor ESP32 memory usage

## 🔍 Debug Tools and Techniques

### Serial Monitor Debugging
1. **Enable Debug Mode**: Set `DEBUG true` in code
2. **Monitor Startup**: Watch boot messages for errors
3. **Check WiFi**: Verify connection process
4. **Camera Init**: Monitor camera initialization

### LED Status Indicators
```cpp
// Status LED meanings
#define STATUS_LED 14

void setup() {
  pinMode(STATUS_LED, OUTPUT);
  digitalWrite(STATUS_LED, LOW); // Off = booting
}

void indicateStatus(int status) {
  switch(status) {
    case 0: digitalWrite(STATUS_LED, LOW); break;    // Booting
    case 1: blinkLED(1, 1000); break;                // WiFi connecting
    case 2: blinkLED(2, 500); break;                 // WiFi connected
    case 3: blinkLED(3, 250); break;                 // Camera init
    case 4: digitalWrite(STATUS_LED, HIGH); break;   // Ready
  }
}
```

### Network Debugging
1. **Ping Test**: Test network connectivity
2. **Port Scan**: Verify web server is listening
3. **WiFi Analyzer**: Check signal strength and channels
4. **Packet Capture**: Use Wireshark for detailed analysis

### Memory Debugging
```cpp
// Memory monitoring
void printMemoryInfo() {
  Serial.printf("Free heap: %d\n", ESP.getFreeHeap());
  Serial.printf("Heap fragmentation: %d%%\n",
    100 - (ESP.getMaxAllocHeap() * 100 / ESP.getFreeHeap()));
  Serial.printf("PSRAM: %d/%d\n",
    ESP.getFreePsram(), ESP.getPsramSize());
}
```

## 📊 Performance Issues

### Slow Recognition
**Solutions:**
1. **Resolution**: Use QVGA instead of higher resolutions
2. **Quality**: Lower JPEG quality setting
3. **Algorithm**: Optimize LBP parameters
4. **Processing**: Use PSRAM for larger buffers

### High Power Consumption
**Solutions:**
1. **Sleep Modes**: Implement light/deep sleep between operations
2. **Camera Settings**: Use lower frame rates and resolution
3. **WiFi**: Use WiFi modem sleep mode
4. **Peripherals**: Power down unused components

### Memory Issues
**Solutions:**
1. **Buffer Management**: Reuse buffers instead of allocating new ones
2. **PSRAM**: Utilize PSRAM for camera buffers
3. **Cleanup**: Free memory after processing
4. **Monitoring**: Track memory usage patterns

## 🌐 Web Interface Issues

### Cannot Access Web Interface
**Solutions:**
1. **IP Address**: Check serial monitor for correct IP
2. **Port**: Ensure accessing port 80 (not 81)
3. **Firewall**: Temporarily disable firewall for testing
4. **Browser Cache**: Clear browser cache and cookies

### WebSocket Connection Issues
**Solutions:**
1. **Browser Compatibility**: Use Chrome, Firefox, or Safari
2. **Network**: Check for proxy or firewall blocking WebSocket
3. **Code**: Verify WebSocket server implementation
4. **SSL**: Some browsers require secure WebSocket (wss://)

### Mobile Access Issues
**Solutions:**
1. **Network**: Ensure phone connected to same WiFi
2. **Browser**: Use mobile browser with WebSocket support
3. **PWA**: Install as Progressive Web App for better performance
4. **Permissions**: Allow camera access if needed

## 📝 Log Analysis

### Understanding Log Messages
```
[INFO] System started successfully
[WARN] WiFi signal strength low: -75dBm
[ERROR] Camera initialization failed: 0x105
[DEBUG] Recognition confidence: 0.85
```

### Common Log Patterns
1. **Boot Issues**: Look for early boot failures
2. **Network Problems**: Check WiFi connection logs
3. **Camera Errors**: Monitor camera initialization sequence
4. **Recognition Failures**: Review confidence scores and timing

### Log Export and Analysis
1. **Serial Monitor**: Copy logs for analysis
2. **Web Interface**: Use /logs endpoint for structured data
3. **External Logging**: Implement syslog or cloud logging
4. **Log Rotation**: Implement automatic log cleanup

## 🆘 Emergency Procedures

### System Recovery
1. **Safe Mode**: Boot with minimal configuration
2. **Factory Reset**: Restore default settings
3. **Manual Override**: Physical door control if needed
4. **Backup Power**: Ensure battery backup is functional

### Data Recovery
1. **Settings Backup**: Export settings before major changes
2. **User Database**: Backup enrolled users regularly
3. **Access Logs**: Export logs for security analysis
4. **Configuration Files**: Keep copies of working configurations

## 📞 Getting Help

### Self-Help Resources
1. **Documentation**: Review installation and setup guides
2. **Examples**: Test with provided example sketches
3. **Community**: Search GitHub issues for similar problems
4. **Logs**: Analyze system logs for error patterns

### Community Support
- **GitHub Issues**: [Report bugs and request features](https://github.com/yourusername/esp32-face-door-lock/issues)
- **Discussions**: [Community discussions](https://github.com/yourusername/esp32-face-door-lock/discussions)
- **Stack Overflow**: Tag questions with `esp32-cam` and `face-recognition`

### Professional Support
- **Email**: support@yourdomain.com
- **Documentation**: Complete guides and tutorials
- **Remote Assistance**: Screen sharing for complex issues

## 📋 Maintenance Checklist

### Daily Checks
- [ ] System status and uptime
- [ ] WiFi connectivity
- [ ] Camera functionality
- [ ] Access logs review

### Weekly Maintenance
- [ ] Battery level check
- [ ] Recognition accuracy test
- [ ] Log file cleanup
- [ ] Security scan

### Monthly Tasks
- [ ] Firmware update check
- [ ] Hardware inspection
- [ ] Performance optimization
- [ ] Backup verification

---

*Troubleshooting Guide Version: 1.0.0*
*Last updated: 20 September 2025*
