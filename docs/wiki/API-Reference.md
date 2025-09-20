# API Reference

This comprehensive API reference documents all REST endpoints, WebSocket events, and integration methods for the ESP32-CAM Face Recognition Door Lock System.

## 📋 Overview

The system provides both REST API endpoints for control and monitoring, as well as WebSocket connections for real-time updates. All APIs use JSON for data exchange and standard HTTP status codes.

### Base URL
```
http://[ESP32-IP-ADDRESS]/
```

### Authentication
Currently, the API uses network-level security (WiFi password). For enhanced security, consider implementing:
- API key authentication
- HTTPS encryption
- User session management

### Response Format
All API responses follow this structure:
```json
{
  "status": "success|error",
  "message": "Human-readable message",
  "data": {},  // Response data
  "timestamp": "2025-01-20T10:30:00Z"
}
```

## 🌐 REST API Endpoints

### System Information

#### GET /status
Get current system status and health information.

**Response:**
```json
{
  "status": "success",
  "data": {
    "system": {
      "uptime": 3600,
      "version": "1.0.0",
      "ip_address": "192.168.1.100",
      "mac_address": "A1:B2:C3:D4:E5:F6"
    },
    "camera": {
      "initialized": true,
      "resolution": "320x240",
      "fps": 15,
      "quality": 12
    },
    "recognition": {
      "engine": "LBP",
      "enrolled_users": 3,
      "last_recognition": "2025-01-20T10:25:30Z",
      "average_confidence": 0.85
    },
    "door": {
      "state": "locked",
      "last_unlock": "2025-01-20T10:20:15Z",
      "auto_lock_enabled": true,
      "auto_lock_delay": 5000
    },
    "power": {
      "battery_level": 85,
      "charging": false,
      "voltage": 4.1,
      "current": 120
    },
    "network": {
      "wifi_ssid": "MyNetwork",
      "signal_strength": -45,
      "connected_clients": 1
    }
  }
}
```

#### GET /info
Get basic system information and capabilities.

**Response:**
```json
{
  "status": "success",
  "data": {
    "name": "ESP32-CAM Face Recognition Door Lock",
    "version": "1.0.0",
    "description": "IoT door access control system",
    "features": [
      "face_recognition",
      "web_interface",
      "camera_streaming",
      "access_logging",
      "power_management"
    ],
    "api_version": "1.0",
    "supported_languages": ["en"],
    "max_users": 8
  }
}
```

### Camera Control

#### GET /cam
Stream live camera feed in MJPEG format.

**Usage:**
```html
<img src="http://192.168.1.100/cam" alt="Live Camera Feed">
```

**Parameters:**
- `resolution`: qvga, vga, svga, xga, uxga (default: qvga)
- `quality`: 1-63 (default: 12, lower = better quality)
- `fps`: 1-30 (default: 15)

**Example:**
```
http://192.168.1.100/cam?resolution=vga&quality=8&fps=20
```

#### POST /cam/capture
Capture a single frame from the camera.

**Request:**
```json
{
  "format": "jpeg|bmp",
  "quality": 12,
  "resolution": "qvga"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "image_data": "base64-encoded-image-data",
    "timestamp": "2025-01-20T10:30:00Z",
    "size": 15360
  }
}
```

### Face Recognition

#### GET /recognition/status
Get face recognition system status.

**Response:**
```json
{
  "status": "success",
  "data": {
    "engine": "LBP",
    "initialized": true,
    "enrolled_users": 3,
    "max_users": 8,
    "last_activity": "2025-01-20T10:25:30Z",
    "average_confidence": 0.85,
    "processing_time": 650
  }
}
```

#### POST /recognition/enroll
Enroll a new user in the face recognition database.

**Request:**
```json
{
  "user_id": "john_doe",
  "user_name": "John Doe",
  "capture_count": 5,
  "capture_delay": 1000
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "user_id": "john_doe",
    "enrolled": true,
    "template_size": 18432,
    "enrollment_time": 2500,
    "message": "User enrolled successfully"
  }
}
```

#### POST /recognition/recognize
Manually trigger face recognition.

**Request:**
```json
{
  "timeout": 5000,
  "confidence_threshold": 0.7
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "recognized": true,
    "user_id": "john_doe",
    "confidence": 0.89,
    "processing_time": 650,
    "timestamp": "2025-01-20T10:30:00Z"
  }
}
```

#### DELETE /recognition/users/{user_id}
Remove a user from the recognition database.

**Response:**
```json
{
  "status": "success",
  "data": {
    "user_id": "john_doe",
    "deleted": true,
    "remaining_users": 2
  }
}
```

#### GET /recognition/users
List all enrolled users.

**Response:**
```json
{
  "status": "success",
  "data": {
    "users": [
      {
        "user_id": "john_doe",
        "user_name": "John Doe",
        "enrolled_date": "2025-01-15T14:30:00Z",
        "template_size": 18432,
        "last_recognized": "2025-01-20T10:25:30Z"
      },
      {
        "user_id": "jane_smith",
        "user_name": "Jane Smith",
        "enrolled_date": "2025-01-16T09:15:00Z",
        "template_size": 19200,
        "last_recognized": "2025-01-20T08:45:15Z"
      }
    ],
    "total_users": 2,
    "max_users": 8
  }
}
```

### Door Control

#### GET /door/status
Get current door lock status.

**Response:**
```json
{
  "status": "success",
  "data": {
    "state": "locked|unlocked",
    "last_change": "2025-01-20T10:20:15Z",
    "last_user": "john_doe",
    "auto_lock_enabled": true,
    "auto_lock_delay": 5000,
    "manual_override": false
  }
}
```

#### POST /door/unlock
Unlock the door manually.

**Request:**
```json
{
  "user_id": "admin",
  "reason": "manual_override",
  "duration": 5000
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "unlocked": true,
    "duration": 5000,
    "auto_lock_time": "2025-01-20T10:30:05Z",
    "message": "Door unlocked successfully"
  }
}
```

#### POST /door/lock
Lock the door manually.

**Response:**
```json
{
  "status": "success",
  "data": {
    "locked": true,
    "timestamp": "2025-01-20T10:30:00Z",
    "message": "Door locked successfully"
  }
}
```

#### POST /door/settings
Update door control settings.

**Request:**
```json
{
  "auto_lock_enabled": true,
  "auto_lock_delay": 5000,
  "manual_override": false
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "settings_updated": true,
    "new_settings": {
      "auto_lock_enabled": true,
      "auto_lock_delay": 5000,
      "manual_override": false
    }
  }
}
```

### Access Logs

#### GET /logs
Get access log entries.

**Parameters:**
- `limit`: Number of entries (default: 50, max: 1000)
- `offset`: Starting offset (default: 0)
- `start_time`: Filter from timestamp (ISO format)
- `end_time`: Filter to timestamp (ISO format)
- `user_id`: Filter by user
- `result`: Filter by result (success|failure)

**Response:**
```json
{
  "status": "success",
  "data": {
    "logs": [
      {
        "id": 1234,
        "timestamp": "2025-01-20T10:25:30Z",
        "user_id": "john_doe",
        "user_name": "John Doe",
        "action": "unlock",
        "result": "success",
        "confidence": 0.89,
        "method": "face_recognition",
        "ip_address": "192.168.1.50",
        "user_agent": "Mozilla/5.0..."
      },
      {
        "id": 1233,
        "timestamp": "2025-01-20T10:20:15Z",
        "user_id": "unknown",
        "user_name": "Unknown",
        "action": "unlock",
        "result": "failure",
        "confidence": 0.45,
        "method": "face_recognition",
        "ip_address": "192.168.1.50",
        "user_agent": "ESP32-CAM/1.0"
      }
    ],
    "total_count": 2,
    "limit": 50,
    "offset": 0
  }
}
```

#### GET /logs/summary
Get access log summary statistics.

**Parameters:**
- `period`: day|week|month|year (default: day)

**Response:**
```json
{
  "status": "success",
  "data": {
    "period": "day",
    "start_time": "2025-01-20T00:00:00Z",
    "end_time": "2025-01-20T23:59:59Z",
    "summary": {
      "total_attempts": 15,
      "successful_attempts": 12,
      "failed_attempts": 3,
      "unique_users": 2,
      "average_confidence": 0.82,
      "most_active_user": "john_doe",
      "most_active_hour": 9
    },
    "hourly_breakdown": [
      {"hour": 8, "attempts": 3, "successes": 2},
      {"hour": 9, "attempts": 5, "successes": 4},
      {"hour": 10, "attempts": 7, "successes": 6}
    ]
  }
}
```

#### DELETE /logs
Clear all access logs.

**Response:**
```json
{
  "status": "success",
  "data": {
    "deleted": true,
    "previous_count": 1234,
    "message": "All logs cleared successfully"
  }
}
```

### System Configuration

#### GET /settings
Get current system settings.

**Response:**
```json
{
  "status": "success",
  "data": {
    "system": {
      "name": "ESP32-Face-Door-Lock",
      "location": "Front Door",
      "timezone": "UTC-5"
    },
    "camera": {
      "resolution": "qvga",
      "quality": 12,
      "fps": 15,
      "night_mode": false
    },
    "recognition": {
      "engine": "LBP",
      "confidence_threshold": 0.7,
      "max_processing_time": 2000,
      "anti_spoofing": true
    },
    "door": {
      "auto_lock_enabled": true,
      "auto_lock_delay": 5000,
      "manual_override": false
    },
    "notifications": {
      "email_enabled": false,
      "telegram_enabled": false,
      "push_enabled": true
    },
    "power": {
      "sleep_mode": "motion",
      "wake_up_time": 30,
      "battery_threshold": 20
    }
  }
}
```

#### POST /settings
Update system settings.

**Request:**
```json
{
  "camera": {
    "resolution": "vga",
    "quality": 8
  },
  "recognition": {
    "confidence_threshold": 0.8
  },
  "notifications": {
    "email_enabled": true,
    "email_address": "admin@example.com"
  }
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "updated": true,
    "changes": {
      "camera.resolution": "qvga -> vga",
      "camera.quality": "12 -> 8",
      "recognition.confidence_threshold": "0.7 -> 0.8",
      "notifications.email_enabled": "false -> true"
    },
    "message": "Settings updated successfully"
  }
}
```

#### POST /system/restart
Restart the system.

**Response:**
```json
{
  "status": "success",
  "data": {
    "restarting": true,
    "message": "System restart initiated"
  }
}
```

#### POST /system/reset
Factory reset the system.

**Request:**
```json
{
  "confirm": true,
  "keep_network_settings": false
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "reset": true,
    "message": "Factory reset completed. System will restart."
  }
}
```

## 🔌 WebSocket Events

The system provides real-time updates via WebSocket connections.

### Connection
```
ws://[ESP32-IP-ADDRESS]/ws
```

### Event Types

#### recognition
Fired when face recognition occurs.

```json
{
  "event": "recognition",
  "data": {
    "user_id": "john_doe",
    "confidence": 0.89,
    "processing_time": 650,
    "timestamp": "2025-01-20T10:30:00Z",
    "action": "unlock"
  }
}
```

#### door_status
Fired when door status changes.

```json
{
  "event": "door_status",
  "data": {
    "state": "unlocked",
    "user_id": "john_doe",
    "timestamp": "2025-01-20T10:30:00Z",
    "auto_lock_time": "2025-01-20T10:30:05Z"
  }
}
```

#### system_status
Fired when system status changes.

```json
{
  "event": "system_status",
  "data": {
    "battery_level": 85,
    "wifi_signal": -45,
    "memory_usage": 45,
    "temperature": 42,
    "timestamp": "2025-01-20T10:30:00Z"
  }
}
```

#### access_log
Fired when new access log entry is created.

```json
{
  "event": "access_log",
  "data": {
    "id": 1234,
    "timestamp": "2025-01-20T10:30:00Z",
    "user_id": "john_doe",
    "action": "unlock",
    "result": "success",
    "confidence": 0.89
  }
}
```

### Client Example
```javascript
const ws = new WebSocket('ws://192.168.1.100/ws');

ws.onmessage = function(event) {
  const data = JSON.parse(event.data);
  console.log('Event:', data.event, data.data);
};

ws.onopen = function() {
  console.log('WebSocket connected');
};

ws.onclose = function() {
  console.log('WebSocket disconnected');
};
```

## 📱 Integration Examples

### Python Client
```python
import requests
import json

class ESP32FaceDoorLock:
    def __init__(self, base_url):
        self.base_url = base_url

    def get_status(self):
        response = requests.get(f"{self.base_url}/status")
        return response.json()

    def unlock_door(self, user_id="admin"):
        data = {"user_id": user_id}
        response = requests.post(f"{self.base_url}/door/unlock", json=data)
        return response.json()

    def enroll_user(self, user_id, user_name):
        data = {
            "user_id": user_id,
            "user_name": user_name,
            "capture_count": 5
        }
        response = requests.post(f"{self.base_url}/recognition/enroll", json=data)
        return response.json()

# Usage
client = ESP32FaceDoorLock("http://192.168.1.100")
status = client.get_status()
print(json.dumps(status, indent=2))
```

### Node.js Client
```javascript
const axios = require('axios');

class ESP32FaceDoorLock {
  constructor(baseURL) {
    this.client = axios.create({ baseURL });
  }

  async getStatus() {
    const response = await this.client.get('/status');
    return response.data;
  }

  async unlockDoor(userId = 'admin') {
    const response = await this.client.post('/door/unlock', {
      user_id: userId
    });
    return response.data;
  }

  async enrollUser(userId, userName) {
    const response = await this.client.post('/recognition/enroll', {
      user_id: userId,
      user_name: userName,
      capture_count: 5
    });
    return response.data;
  }
}

// Usage
const client = new ESP32FaceDoorLock('http://192.168.1.100');
client.getStatus().then(status => console.log(status));
```

### Bash Script Integration
```bash
#!/bin/bash

ESP32_IP="192.168.1.100"

# Get system status
curl -s "http://${ESP32_IP}/status" | jq '.'

# Unlock door
curl -s -X POST "http://${ESP32_IP}/door/unlock" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "admin"}' | jq '.'

# Get access logs
curl -s "http://${ESP32_IP}/logs?limit=10" | jq '.'
```

## 📊 Error Codes

### HTTP Status Codes
- `200 OK` - Request successful
- `201 Created` - Resource created successfully
- `400 Bad Request` - Invalid request parameters
- `401 Unauthorized` - Authentication required
- `404 Not Found` - Endpoint not found
- `500 Internal Server Error` - Server error

### Application Error Codes
```json
{
  "status": "error",
  "error_code": "INVALID_USER_ID",
  "message": "User ID must be alphanumeric and 3-32 characters",
  "details": {
    "field": "user_id",
    "provided": "invalid@user",
    "expected": "alphanumeric, 3-32 chars"
  }
}
```

### Common Error Codes
- `CAMERA_NOT_INITIALIZED` - Camera failed to initialize
- `INVALID_USER_ID` - User ID format is invalid
- `USER_ALREADY_EXISTS` - User is already enrolled
- `USER_NOT_FOUND` - User not found in database
- `INSUFFICIENT_CONFIDENCE` - Recognition confidence too low
- `SYSTEM_BUSY` - System is processing another request
- `DOOR_ALREADY_UNLOCKED` - Door is already unlocked
- `BATTERY_LOW` - Battery level too low for operation

## 🔒 Security Considerations

### API Security Best Practices
1. **Network Security**: Use WPA2/3 encryption on WiFi
2. **Access Control**: Implement API key authentication
3. **HTTPS**: Use SSL/TLS for encrypted communication
4. **Rate Limiting**: Implement request rate limiting
5. **Input Validation**: Validate all input parameters
6. **Audit Logging**: Log all API access attempts

### Authentication Example
```bash
# With API key
curl -H "Authorization: Bearer YOUR_API_KEY" \
  "http://192.168.1.100/status"

# With basic auth
curl -u username:password \
  "http://192.168.1.100/status"
```

## 📞 Support

For API issues or questions:
- Check the [Troubleshooting](Troubleshooting) guide
- Review the [FAQ](FAQ) for common questions
- Open an issue on [GitHub](https://github.com/yourusername/esp32-face-door-lock/issues)

---

*API Reference Version: 1.0.0*
*Last updated: 20 September 2025*
