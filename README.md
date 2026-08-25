# 🤖 EcoBot: Wireless Smart Waste Collection Robot

A wireless-controlled mobile robotic vehicle integrated with a **4-DOF (4-Axis) Robotic Arm** designed for collecting waste and garbage from public spaces[cite: 5]. The system is powered by an **ESP32** microcontroller and controlled via a **PS5 Gamepad** over Bluetooth using the **Bluepad32** framework[cite: 5].

---

## 📌 System Overview
* **Microcontroller:** ESP32 Development Board (Bluetooth & Logic Control)[cite: 5]
* **Robotic Arm:** 4-DOF Arm powered by 4× SG90 Micro Servos (Base, Shoulder, Elbow, Gripper)[cite: 5]
* **Servo Controller:** PCA9685 16-Channel 12-bit PWM Driver (via I2C)[cite: 5]
* **Drive System:** 4× DC Gear Motors driven by an **L298N** Motor Driver[cite: 5]
* **Controller:** Sony PlayStation 5 (DualSense) Controller via Bluetooth Low Energy[cite: 5]
* **Chassis:** Custom 4WD Robotic Platform[cite: 5]

---

## ⚡ Power Distribution & Electrical Architecture
To prevent motor electrical noise and brownouts from resetting the microcontroller, a dual-isolated power design is implemented[cite: 5]:
* **Main Battery (11.1V LiPo):** Powers DC Drive Motors via L298N VMS input and feeds the LM2596 Buck Converter[cite: 5].
* **Secondary Battery (7.4V Li-Ion):** Exclusively powers the PCA9685 Servo Driver `V+` power rail[cite: 5].
* **LM2596 Step-Down Buck Converter (5V):** Steps down voltage to provide a regulated 5V logic supply to ESP32, L298N logic, and PCA9685 logic[cite: 5].
* **Common Ground (GND):** All system grounds (Batteries, Buck Converter, ESP32, L298N, and PCA9685) are tied to a common ground rail[cite: 5].

---

## 🎮 PS5 Controller Mapping

### Vehicle Chassis Movement[cite: 5]
| Input Control | Functionality |
| :--- | :--- |
| **Left Joystick (Y-Axis)** | Variable Speed Forward / Backward[cite: 5] |
| **Triangle Button ($\triangle$)** | Fixed-Speed Spin Left[cite: 5] |
| **Circle Button ($\bigcirc$)** | Fixed-Speed Spin Right[cite: 5] |

### 4-DOF Robotic Arm Control[cite: 5]
| Input Control | Joint / Action |
| :--- | :--- |
| **L2 / R2 Triggers** | Base Rotation (Left / Right)[cite: 5] |
| **Right Joystick (Y-Axis)** | Shoulder Joint (Up / Down)[cite: 5] |
| **L1 / R1 Bumpers** | Elbow Joint (Forward / Backward)[cite: 5] |
| **Square ($\square$) / Cross ($\times$)** | Gripper Claw (Open / Close)[cite: 5] |

---

## 🛠️ Pinout Configuration

### ESP32 to L298N Motor Driver[cite: 4, 5]
* **ENA (PWM Channel A):** GPIO 16[cite: 4]
* **IN1 / IN2 (Left Motors):** GPIO 17 / GPIO 18[cite: 4, 5]
* **ENB (PWM Channel B):** GPIO 25[cite: 4]
* **IN3 / IN4 (Right Motors):** GPIO 26 / GPIO 27[cite: 4]

### ESP32 to PCA9685 Servo Driver (I2C)[cite: 4, 5]
* **SDA:** GPIO 4[cite: 4, 5]
* **SCL:** GPIO 15[cite: 4, 5]

---

## 🚀 Software & Libraries
* **Framework:** Arduino IDE (C++)[cite: 5]
* **Core Libraries:**
  * `Bluepad32.h` (Bluetooth controller communication)[cite: 4, 5]
  * `Adafruit_PWMServoDriver.h` (16-channel PWM servo management)[cite: 4, 5]
  * `Wire.h` (I2C hardware bus)[cite: 4, 5]

---

## 🔮 Future Enhancements
1. **Autonomous Waste Detection:** Adding an ESP32-CAM module with Edge AI / TinyML object detection (using Edge Impulse) to classify waste (plastic, metal, paper)[cite: 5].
2. **Structural Upgrade:** 3D-printed custom lightweight chassis and high-torque metal gear servos[cite: 5].

---

## 👥 Project Information
* **Developer:** **Muhammad Yasser Al-Hamad**[cite: 5]
* **Academic Supervisors:** Dr. Mohammed Marei, Eng. Ahmed Abdellatif[cite: 5]
* **Institution:** Faculty of Information Technology, Misr University for Science and Technology[cite: 3, 5]
