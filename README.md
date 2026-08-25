# 🤖 EcoBot: Wireless Smart Waste Collection Robot

A wireless-controlled mobile robotic vehicle integrated with a **4-DOF (4-Axis) Robotic Arm** designed for collecting waste and garbage from public spaces. The system is powered by an **ESP32** microcontroller and controlled via a **PS5 Gamepad** over Bluetooth using the **Bluepad32** framework.

---

## 📌 System Overview
* **Microcontroller:** ESP32 Development Board (Bluetooth & Logic Control)
* **Robotic Arm:** 4-DOF Arm powered by 4× SG90 Micro Servos (Base, Shoulder, Elbow, Gripper)
* **Servo Controller:** PCA9685 16-Channel 12-bit PWM Driver (via I2C)
* **Drive System:** 4× DC Gear Motors driven by an **L298N** Motor Driver
* **Controller:** Sony PlayStation 5 (DualSense) Controller via Bluetooth Low Energy
* **Chassis:** Custom 4WD Robotic Platform

---

## ⚡ Power Distribution & Electrical Architecture
To prevent motor electrical noise and brownouts from resetting the microcontroller, a dual-isolated power design is implemented:
* **Main Battery (11.1V LiPo):** Powers DC Drive Motors via L298N VMS input and feeds the LM2596 Buck Converter.
* **Secondary Battery (7.4V Li-Ion):** Exclusively powers the PCA9685 Servo Driver `V+` power rail.
* **LM2596 Step-Down Buck Converter (5V):** Steps down voltage to provide a regulated 5V logic supply to ESP32, L298N logic, and PCA9685 logic.
* **Common Ground (GND):** All system grounds (Batteries, Buck Converter, ESP32, L298N, and PCA9685) are tied to a common ground rail.

---

## 🎮 PS5 Controller Mapping

### Vehicle Chassis Movement
| Input Control | Functionality |
| :--- | :--- |
| **Left Joystick (Y-Axis)** | Variable Speed Forward / Backward |
| **Triangle Button ($\triangle$)** | Fixed-Speed Spin Left |
| **Circle Button ($\bigcirc$)** | Fixed-Speed Spin Right |

### 4-DOF Robotic Arm Control
| Input Control | Joint / Action |
| :--- | :--- |
| **L2 / R2 Triggers** | Base Rotation (Left / Right) |
| **Right Joystick (Y-Axis)** | Shoulder Joint (Up / Down) |
| **L1 / R1 Bumpers** | Elbow Joint (Forward / Backward) |
| **Square ($\square$) / Cross ($\times$)** | Gripper Claw (Open / Close) |

---

## 🛠️ Pinout Configuration

### ESP32 to L298N Motor Driver
* **ENA (PWM Channel A):** GPIO 16
* **IN1 / IN2 (Left Motors):** GPIO 17 / GPIO 18
* **ENB (PWM Channel B):** GPIO 25
* **IN3 / IN4 (Right Motors):** GPIO 26 / GPIO 27

### ESP32 to PCA9685 Servo Driver (I2C)
* **SDA:** GPIO 4
* **SCL:** GPIO 15

---

## 🚀 Software & Libraries
* **Framework:** Arduino IDE (C++)
* **Core Libraries:**
  * `Bluepad32.h` (Bluetooth controller communication)
  * `Adafruit_PWMServoDriver.h` (16-channel PWM servo management)
  * `Wire.h` (I2C hardware bus)

---

## 🔮 Future Enhancements
1. **Autonomous Waste Detection:** Adding an ESP32-CAM module with Edge AI / TinyML object detection (using Edge Impulse) to classify waste (plastic, metal, paper).
2. **Structural Upgrade:** 3D-printed custom lightweight chassis and high-torque metal gear servos.

---

## 👥 Project Information
* **Developer:** **Muhammad Yasser Al-Hamad**
* **Academic Supervisors:** Dr. Mohammed Marei, Eng. Ahmed Abdellatif
* **Institution:** Faculty of Information Technology, Misr University for Science and Technology
