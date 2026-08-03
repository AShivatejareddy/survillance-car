# 🚗 IoT Surveillance Car Using ESP32-CAM

An IoT-based smart surveillance vehicle that provides **real-time video streaming** and **wireless remote control** using the ESP32-CAM module. The vehicle is controlled through a web browser over Wi-Fi and is designed for surveillance, security monitoring, and remote inspection.

---

## 📸 Project Photos

<p align="center">
  <img src="Images/Project_Photos/car_front.jpeg" width="300" alt="Front View">
  <img src="Images/Project_Photos/car_side.jpeg" width="300" alt="Side View">
  <img src="Images/Project_Photos/car_top.jpeg" width="300" alt="Top View">
</p>
---
## 🔌 Circuit Diagram

<p align="center">
  <img src="Images/Circuit_Diagram/Camera Car.png" width="700" alt="Circuit Diagram">
</p>
---
---

## ✨ Features

- 📷 Real-time live video streaming using ESP32-CAM
- 🚗 Wireless car movement control (Forward, Backward, Left, Right, Stop)
- 🌐 Web-based control interface accessible through any browser
- ⚡ Adjustable motor speed using PWM
- 💡 LED flash control for low-light surveillance
- 📡 Wi-Fi communication without additional hardware
- 🔋 Portable battery-powered system
- 🛡️ Suitable for surveillance and remote monitoring applications

---

## 🛠️ Hardware Components

| Component | Quantity |
|-----------|---------:|
| ESP32-CAM Module | 1 |
| L298N Motor Driver | 1 |
| DC Motors | 4 |
| Robot Chassis | 1 |
| Wheels | 4 |
| Battery Pack | 1 |
| Jumper Wires | As Required |
| Power Switch | 1 |

---

## 💻 Software Used

- Arduino IDE
- ESP32 Board Package
- C/C++
- HTML
- CSS
- JavaScript
- Wi-Fi Library
- ESP32 Camera Library

---

## ⚙️ Working Principle

The ESP32-CAM creates a Wi-Fi web server that hosts a control dashboard. A user connects to the ESP32-CAM through a web browser to view the live camera stream and control the vehicle. The L298N motor driver receives movement commands from the ESP32-CAM to drive the motors, while the camera continuously streams video for real-time surveillance.
---
---

## 📂 Folder Structure

```
survillance-car/
│
├── Arduino_Code/              # ESP32-CAM source code
├── Images/
│   ├── Project_Photos/
│   ├── Circuit_Diagram/
│   ├── Block_Diagram/
│   └── Web_Interface/
├── README.md
└── code.docx
```
---

## 🚀 Installation

1. Clone this repository.
2. Open the project in Arduino IDE.
3. Install the ESP32 Board Package.
4. Select **AI Thinker ESP32-CAM**.
5. Connect the ESP32-CAM using an FTDI programmer.
6. Upload the code.
7. Connect to the ESP32-CAM Wi-Fi network.
8. Open the IP address shown in the Serial Monitor.
9. Control the vehicle through the web interface.
---
---

## 🎯 Applications

- 🏠 Home Security
- 🏭 Industrial Surveillance
- 🚨 Disaster Area Inspection
- 🌾 Agricultural Monitoring
- 🏢 Warehouse Monitoring
- 🎓 Robotics Learning
- 🔬 Research Projects
- ---

## 🔮 Future Enhancements

- Face Recognition
- Object Detection using AI
- Mobile App Control
- Cloud Video Storage
- GPS Tracking
- Obstacle Avoidance
- Night Vision Camera
---

## 👨‍💻 Author

**A. Shiva Teja Reddy**

Electronics and Communication Engineering (ECE)

CVR College of Engineering

GitHub: https://github.com/AShivatejareddy
## ✨ Features

- 📷 Live video streaming
- 🚗 Wireless vehicle control
- 🌐 Web-based interface
- ⚡ PWM motor speed control
- 💡 LED Flash support
- 📡 Wi-Fi communication
- 🔋 Battery powered
