# Documentation Folder

## system_architecture.md

# System Architecture

## Overview
The IoT-based Efficient Waste Collection System consists of several interconnected components that work together to provide real-time waste management solutions.

## Components
1. Smart Bins
   - Ultrasonic sensors for fill-level detection
   - IR sensors for presence detection
   - Servo motors for lid control

2. IoT Device (Raspberry Pi)
   - Processes sensor data
   - Controls bin operations
   - Transmits data to the cloud

3. Cloud Infrastructure (Firebase)
   - Stores real-time and historical data
   - Facilitates data analysis and reporting

4. Mobile Application
   - Provides user interface for waste management personnel
   - Displays bin status and collection routes

## Data Flow
1. Sensors collect data on bin fill levels and user presence
2. Raspberry Pi processes this data and controls bin operations
3. Processed data is sent to Firebase in real-time
4. Mobile application retrieves data from Firebase and displays it to users
5. Waste management personnel use the app to optimize collection routes

## Communication Protocols
- Sensors to Raspberry Pi: GPIO
- Raspberry Pi to Firebase: HTTPS
- Firebase to Mobile App: Firebase Realtime Database SDK

## system_architecture.md

# Hardware Setup

## Components Required
- Raspberry Pi (3 Model B+ or later)
- Ultrasonic Sensor (HC-SR04)
- IR Sensor
- Servo Motor
- Jumper wires
- Breadboard (optional)
- Power supply for Raspberry Pi

## Wiring Instructions
1. Ultrasonic Sensor (HC-SR04)
   - VCC to Raspberry Pi 5V
   - GND to Raspberry Pi GND
   - TRIG to Raspberry Pi GPIO 23
   - ECHO to Raspberry Pi GPIO 24

2. IR Sensor
   - VCC to Raspberry Pi 3.3V
   - GND to Raspberry Pi GND
   - OUT to Raspberry Pi GPIO 27

3. Servo Motor
   - VCC to Raspberry Pi 5V
   - GND to Raspberry Pi GND
   - Signal to Raspberry Pi GPIO 17

## Assembly Instructions
1. Mount the ultrasonic sensor at the top of the bin, ensuring it has a clear view of the bin's contents.
2. Place the IR sensor near the bin's opening to detect when someone approaches.
3. Attach the servo motor to the bin's lid mechanism.
4. Secure all wiring and ensure there are no loose connections.

## Power Considerations
- Ensure your power supply can provide sufficient current for the Raspberry Pi and all connected components.
- Consider using a UPS or battery backup for uninterrupted operation.

## Troubleshooting
- If sensors aren't detected, double-check all wiring connections.
- Ensure the Raspberry Pi GPIO pins are correctly configured in the software.

## software_setup.md

# Software Setup

## Raspberry Pi Setup
1. Install Raspbian OS on the Raspberry Pi
2. Update and upgrade the system:
   ```
   sudo apt update
   sudo apt upgrade
   ```
3. Enable I2C and SPI interfaces if not already enabled:
   ```
   sudo raspi-config
   ```
   Navigate to "Interfacing Options" and enable I2C and SPI.

## Python Environment Setup
1. Install Python 3 (if not already installed):
   ```
   sudo apt install python3 python3-pip
   ```
2. Install required Python libraries:
   ```
   pip3 install RPi.GPIO firebase-admin requests
   ```

## Firebase Setup
1. Create a new project in the Firebase Console (https://console.firebase.google.com/)
2. Set up a Realtime Database in your Firebase project
3. Generate a private key for your service account:
   - Go to Project Settings > Service Accounts
   - Click "Generate New Private Key"
   - Save the JSON file securely
4. Place the JSON file in your project directory

## Project Setup
1. Clone the project repository:
   ```
   git clone https://github.com/yourusername/iot-waste-collection.git
   cd iot-waste-collection
   ```
2. Update the `smart_bin_controller.py` script with your Firebase credentials and database URL
3. Set up IFTTT for notifications:
   - Create an IFTTT account
   - Create a new applet with a Webhook trigger
   - Update the `smart_bin_controller.py` script with your IFTTT webhook URL

## Running the System
1. Navigate to the project directory
2. Run the main script:
   ```
   python3 smart_bin_controller.py
   ```

## Autostart on Boot (Optional)
To make the script run automatically when the Raspberry Pi boots:
1. Edit the rc.local file:
   ```
   sudo nano /etc/rc.local
   ```
2. Add the following line before `exit 0`:
   ```
   python3 /path/to/your/smart_bin_controller.py &
   ```
3. Save and exit the file

## api_documentation.md

# API Documentation

## Firebase Realtime Database API

### Data Structure
```
/bin_data
  /current
    fill_level: number
    last_updated: timestamp
  /history
    /{timestamp}
      fill_level: number
```

### Reading Data
To read the current bin status:
```python
ref = db.reference('/bin_data/current')
current_status = ref.get()
```

To read historical data:
```python
history_ref = db.reference('/bin_data/history')
historical_data = history_ref.order_by_child('timestamp').limit_to_last(10).get()
```

### Writing Data
To update the current bin status:
```python
ref = db.reference('/bin_data/current')
ref.set({
    'fill_level': 75,
    'last_updated': firebase.server_timestamp()
})
```

To add a historical data point:
```python
history_ref = db.reference('/bin_data/history')
history_ref.push({
    'fill_level': 75,
    'timestamp': firebase.server_timestamp()
})
```

## IFTTT Webhook API

To send a notification when the bin is full:
```python
requests.post('https://maker.ifttt.com/trigger/{event}/with/key/{your_key}', 
              json={"value1": "Bin is full", "value2": "Current fill level: 90%"})
```

Replace `{event}` with your IFTTT event name and `{your_key}` with your IFTTT Webhook key.

## troubleshooting_guide.md

# Troubleshooting Guide

## Common Issues and Solutions

### 1. Sensor Not Detecting Accurately
- Ensure the ultrasonic sensor is clean and unobstructed
- Check wiring connections
- Verify the MAX_CAPACITY constant in the code matches your bin's actual depth

### 2. Lid Not Opening/Closing
- Check servo motor connections
- Ensure the servo is receiving enough power
- Verify the GPIO pin for the servo in the code

### 3. Data Not Updating in Firebase
- Check internet connectivity on the Raspberry Pi
- Verify Firebase credentials and database URL in the code
- Ensure the Firebase service account key JSON file is in the correct location

### 4. IFTTT Notifications Not Working
- Verify the IFTTT webhook URL in the code
- Check if the IFTTT applet is active
- Ensure internet connectivity on the Raspberry Pi

### 5. Raspberry Pi Not Booting
- Check power supply
- Verify SD card is properly inserted and not corrupted
- Try re-flashing the Raspbian OS

### 6. Script Not Running on Boot
- Check the path in rc.local is correct
- Ensure the script has execute permissions

## Debugging Tips
- Use print statements in the code to track execution flow
- Check system logs: `sudo journalctl -u your-service-name`
- Monitor CPU and memory usage: `top` or `htop`

## Getting Help
If you're unable to resolve an issue, please open a GitHub issue with:
- A clear description of the problem
- Steps to reproduce the issue
- Relevant log outputs or error messages

## user_manual.md

# User Manual

## Introduction
Welcome to the IoT-based Efficient Waste Collection System. This manual will guide you through the use of the smart bin and the associated mobile application.

## Smart Bin Usage
1. Approach the bin - the lid will open automatically
2. Dispose of your waste
3. Step away from the bin - the lid will close automatically

## Mobile Application

### Installation
1. Download the app from the [App Store/Google Play Store]
2. Install and open the app
3. Log in with your provided credentials

### Features
1. Real-time Bin Status
   - View current fill levels of all connected bins
   - Color-coded for easy identification (Green: Empty, Yellow: Half-full, Red: Full)

2. Collection Route Optimization
   - View suggested collection routes based on bin fill levels
   - Optimize routes manually if needed

3. Notifications
   - Receive alerts when bins are nearing capacity
   - Get notifications for any system issues

4. Historical Data
   - View trends in waste generation
   - Access reports on collection efficiency

5. Map View
   - See all bin locations on an interactive map
   - Click on bins for detailed information

### Troubleshooting
- If the app is not updating, check your internet connection
- For login issues, contact your system administrator
- If bin data seems incorrect, inform maintenance through the app's reporting feature

## Maintenance
- Clean sensors monthly for optimal performance
- Check battery levels of bins quarterly
- Update the mobile app when new versions are available

## Safety Precautions
- Do not overfill bins
- Report any physical damage to bins immediately
- Do not attempt to manually open bin lids

For further assistance, please contact our support team at support@smartwastemanagement.com

