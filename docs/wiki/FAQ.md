# Frequently Asked Questions (FAQ)

This FAQ addresses common questions about the ESP32-CAM Face Recognition Door Lock System.

## 📋 General Questions

### What is the ESP32-CAM Face Recognition Door Lock System?
The ESP32-CAM Face Recognition Door Lock System is an IoT security solution that uses computer vision and embedded systems to provide face-based access control. It combines an ESP32-CAM module with face recognition algorithms to automatically unlock doors when authorized users are detected.

### What are the main features?
- **Real-time Face Recognition**: LBP algorithm with 88-95% accuracy
- **Web Interface**: Complete control and monitoring via web browser
- **Mobile Responsive**: Works on smartphones and tablets
- **Access Logging**: Comprehensive audit trail of all access attempts
- **Power Management**: Battery backup and intelligent sleep modes
- **Remote Control**: Unlock doors and manage settings remotely

### What hardware do I need?
**Essential Components:**
- ESP32-CAM module (AI-Thinker recommended)
- FTDI USB programmer
- 5V 2A power supply
- Servo motor or solenoid lock
- Relay module (5V, 10A)
- Breadboard and jumper wires

**Optional Enhancements:**
- PIR motion sensor
- LED indicators
- Backup battery
- Weatherproof enclosure

### How much does it cost to build?
- **Essential components**: $40-60
- **Recommended setup**: $60-80
- **Full-featured system**: $80-100

### How long does it take to set up?
- **Hardware assembly**: 1-2 hours
- **Software installation**: 30-60 minutes
- **Initial configuration**: 30-60 minutes
- **Testing and calibration**: 1-2 hours
- **Total**: 3-6 hours for complete setup

## 🛠️ Installation & Setup

### Which ESP32-CAM model should I use?
**Recommended**: AI-Thinker ESP32-CAM
- Most widely supported
- Good camera quality
- Reliable WiFi performance
- Available from multiple vendors

**Avoid**: Generic or unbranded modules that may have different pinouts or quality issues.

### Why won't my ESP32-CAM connect to WiFi?
**Common causes:**
1. **Wrong frequency**: ESP32-CAM only supports 2.4GHz WiFi
2. **Signal strength**: Weak signal or interference
3. **Hidden SSID**: Some ESP32 versions don't support hidden networks
4. **Special characters**: Avoid special characters in SSID/password
5. **Network congestion**: Too many devices on the network

**Solutions:**
- Ensure 2.4GHz network
- Move closer to router
- Try different WiFi channel
- Temporarily disable 5GHz network
- Use simple SSID and password

### Why does the camera initialization fail?
**Common causes:**
1. **Flash mode**: GPIO0 grounded during normal operation
2. **Power supply**: Insufficient current or unstable voltage
3. **Pin connections**: Loose or incorrect camera connections
4. **Board selection**: Wrong board selected in Arduino IDE

**Solutions:**
- Disconnect GPIO0 from ground
- Use 5V 2A+ power supply
- Double-check camera pin connections
- Select "AI Thinker ESP32-CAM" in Arduino IDE

### How do I access the web interface?
1. **Find IP address**: Check serial monitor after boot
2. **Open browser**: Navigate to `http://[IP-ADDRESS]`
3. **Default port**: Uses port 80 (standard HTTP)
4. **Network**: Must be on same WiFi network

**Example**: If IP is 192.168.1.100, go to `http://192.168.1.100`

## 🔐 Face Recognition

### How accurate is the face recognition?
- **Optimal conditions**: 88-95% accuracy
- **Typical indoor lighting**: 85-92% accuracy
- **Challenging conditions**: 75-85% accuracy

**Factors affecting accuracy:**
- Lighting quality and consistency
- Face positioning and angle
- Camera quality and resolution
- User enrollment quality

### How many users can be enrolled?
- **Maximum**: 8 users per system
- **Recommended**: 5-6 users for best performance
- **Storage**: Each user template uses 15-25KB

### How do I enroll a new user?
1. **Access web interface**: Go to `/enroll` page
2. **Enter user details**: Provide user ID and name
3. **Follow instructions**: System guides through capture process
4. **Multiple captures**: System takes 5-10 images for training
5. **Processing**: Wait for template generation (2-5 seconds)

### Why is recognition not working?
**Common issues:**
1. **Poor lighting**: Insufficient or uneven lighting
2. **Wrong distance**: Face too far or too close (optimal: 30-80cm)
3. **Angle issues**: Face not relatively straight-on
4. **Enrollment quality**: Poor quality enrollment images

**Solutions:**
- Improve lighting conditions
- Position face properly
- Re-enroll users with better images
- Adjust confidence threshold

### What is the confidence threshold?
- **Default**: 0.7 (70% confidence required)
- **Range**: 0.5-0.9 (lower = more permissive, higher = more strict)
- **Adjustment**: Can be changed in web interface settings

**Guidelines:**
- **High security**: Use 0.8-0.9
- **Balanced**: Use 0.7 (default)
- **Convenience**: Use 0.6-0.7

## 🚪 Door Control

### What type of door lock can I use?
**Servo Motor (Recommended):**
- SG90 or MG90S servos
- 180-degree rotation
- Easy to control and calibrate
- Lower power consumption

**Solenoid Lock:**
- 5V or 12V solenoid
- Requires relay module
- More secure for some applications
- Higher power consumption

**Electric Strike:**
- Professional door hardware
- Requires 12V-24V power
- Most secure option
- Higher cost and complexity

### How does the auto-lock feature work?
- **Default delay**: 5 seconds after unlock
- **Configurable**: 1-60 seconds via web interface
- **Override**: Can be disabled in settings
- **Manual control**: Can lock/unlock manually anytime

### Can I control the door manually?
**Yes, multiple ways:**
1. **Web interface**: Manual unlock button
2. **API**: POST request to `/door/unlock`
3. **Physical override**: Direct access to lock mechanism
4. **Emergency access**: Backup power and manual controls

## 🌐 Web Interface & API

### What browsers are supported?
**Fully Supported:**
- Chrome 60+
- Firefox 55+
- Safari 12+
- Edge 79+

**Mobile Browsers:**
- Chrome Mobile
- Safari Mobile
- Firefox Mobile

**Requirements:**
- WebSocket support
- JavaScript enabled
- Modern ES6+ features

### How do I access the system remotely?
**Options:**
1. **Port forwarding**: Forward port 80 on your router
2. **VPN**: Connect via VPN to home network
3. **Reverse proxy**: Use nginx or Apache as reverse proxy
4. **Cloud service**: Deploy to cloud IoT platform

**Security considerations:**
- Use HTTPS encryption
- Implement authentication
- Monitor access logs
- Use strong passwords

### What API endpoints are available?
**Main endpoints:**
- `GET /status` - System status
- `GET /cam` - Camera stream
- `POST /door/unlock` - Unlock door
- `GET /logs` - Access logs
- `POST /recognition/enroll` - Enroll user

**Full API reference**: See [API Reference](API-Reference) documentation

## ⚡ Performance & Power

### How much power does it consume?
**Active Mode:**
- Camera streaming: 200-300mA
- Face recognition: 150-250mA
- WiFi connected: 100-200mA

**Sleep Mode:**
- Light sleep: 15-25mA
- Deep sleep: 1-5mA
- Power off: <1mA

**Battery Backup:**
- 18650 battery (4000mAh): 8-12 hours
- Motion-activated: 24-48 hours
- Sleep mode: 3-7 days

### How can I reduce power consumption?
**Strategies:**
1. **Sleep modes**: Use light/deep sleep between operations
2. **Motion detection**: Wake up only on motion
3. **Camera settings**: Lower resolution and frame rate
4. **WiFi optimization**: Use WiFi modem sleep
5. **Peripheral control**: Power down unused components

### What is the recognition speed?
**Processing time:**
- **Face detection**: 100-300ms
- **Recognition**: 400-800ms
- **Total**: 600-900ms average

**Factors affecting speed:**
- Camera resolution (lower = faster)
- Image quality (lower = faster)
- Number of enrolled users (fewer = faster)
- Processing power available

## 🔒 Security & Privacy

### Is the system secure?
**Security features:**
- Network-level security (WiFi encryption)
- Optional API authentication
- Access logging and monitoring
- Configurable confidence thresholds
- Anti-spoofing measures

**Security considerations:**
- Use WPA2/3 encryption
- Change default passwords
- Monitor access logs regularly
- Keep firmware updated
- Physical security of the device

### How is user data stored?
**Data storage:**
- Face templates stored locally on ESP32
- No cloud storage by default
- Optional microSD card storage
- Encrypted templates (AES-256)

**Privacy features:**
- Local processing only
- No internet upload of face data
- Configurable data retention
- User consent mechanisms

### Does it comply with privacy regulations?
**GDPR/CCPA compliance:**
- Data protection by design
- User consent management
- Right to deletion
- Data portability
- Privacy policy documentation

**Features:**
- Local data processing
- No third-party data sharing
- Configurable retention policies
- Audit logging

## 🐛 Troubleshooting

### Why is the system slow or unresponsive?
**Common causes:**
1. **Power supply**: Insufficient current or unstable voltage
2. **Memory usage**: High memory consumption
3. **Network issues**: WiFi connectivity problems
4. **Overheating**: Poor ventilation or high ambient temperature

**Solutions:**
- Check power supply (5V, 2A+)
- Monitor memory usage
- Verify WiFi signal strength
- Ensure adequate ventilation

### Why does recognition fail frequently?
**Common causes:**
1. **Lighting**: Poor or inconsistent lighting
2. **Positioning**: Incorrect face position or distance
3. **Enrollment**: Poor quality enrollment images
4. **Settings**: Wrong confidence threshold

**Solutions:**
- Improve lighting conditions
- Position face properly (30-80cm, straight-on)
- Re-enroll users with better images
- Adjust confidence threshold

### How do I reset the system?
**Factory reset options:**
1. **Web interface**: Settings → Factory Reset
2. **API call**: POST to `/system/reset`
3. **Serial command**: Send reset command via serial
4. **Hardware**: Power cycle with GPIO0 grounded

**What gets reset:**
- User enrollments
- System settings
- Access logs
- Network settings (optional)

## 📞 Support & Community

### Where can I get help?
**Resources:**
1. **Documentation**: Complete guides and tutorials
2. **GitHub Issues**: Bug reports and feature requests
3. **Community**: Discussions and user support
4. **Email**: Direct support for complex issues

### How do I contribute to the project?
**Ways to contribute:**
1. **Bug reports**: Report issues with detailed information
2. **Feature requests**: Suggest new features
3. **Code contributions**: Submit pull requests
4. **Documentation**: Improve guides and tutorials
5. **Testing**: Help test new features

### Where can I find more information?
**Documentation:**
- [Installation Guide](Installation-Guide)
- [Hardware Setup](Hardware-Setup)
- [API Reference](API-Reference)
- [Troubleshooting](Troubleshooting)

**Community:**
- [GitHub Repository](https://github.com/qzdevz/esp32-face-door-lock)
- [Discussions](https://github.com/qzdevz/esp32-face-door-lock/discussions)
- [Issues](https://github.com/qzdevz/esp32-face-door-lock/issues)

## 📈 Advanced Topics

### Can I integrate with home automation systems?
**Integration options:**
1. **Home Assistant**: MQTT or REST API integration
2. **OpenHAB**: HTTP binding support
3. **SmartThings**: Custom device handlers
4. **IFTTT**: Webhook triggers

### Can I add additional sensors?
**Supported sensors:**
- PIR motion sensors
- Temperature/humidity sensors
- Light sensors
- Door contact sensors
- Keypad for PIN entry

### Can I use different cameras?
**Camera compatibility:**
- OV2640 (default, recommended)
- OV7725 (lower resolution)
- OV5640 (higher resolution, more power)
- Custom camera modules (may require code changes)

### How do I update the firmware?
**Update methods:**
1. **OTA updates**: Over-the-air via web interface
2. **USB flashing**: Using Arduino IDE or PlatformIO
3. **Web uploader**: Upload new firmware via web interface
4. **Auto-update**: Scheduled automatic updates

---

*FAQ Version: 1.0.0*
*Last updated: 20 September 2025*

*Have a question not covered here? Check the [Troubleshooting](Troubleshooting) guide or open an issue on [GitHub](https://github.com/qzdevz/esp32-face-door-lock/issues).*
