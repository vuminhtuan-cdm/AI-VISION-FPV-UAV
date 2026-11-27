# AI-VISION-FPV-UAV

> **Platform:** ESP32 (38-pin) | **Frame:** DJI F450 Quad-X | **Firmware:** Custom C++ / FreeRTOS

A custom-built F450 quadcopter project powered by an ESP32 microcontroller. The goal of this project is to build an open-source flight controller from the ground up, achieve rock-solid flight stability, and eventually integrate an FPV camera with onboard AI vision to detect, approach, and grasp objects.

---

### 📌 Project Goals
- **Current Focus:** Stabilizing the drone using custom dual-loop PID algorithms, sensor filtering (MPU-6050), and radio control decoding on an ESP32 (38-pin).
- **Next Step:** Altitude hold using a BMP280 barometric pressure sensor and flight data logging via MicroSD.
- **Final Mission:** Mounting an FPV camera and an onboard computer vision module to detect target objects and autonomously pick them up using a robotic gripper.

---

## 🏛️ System Architecture

```text
[ High-Level: Vision & AI ]
  - FPV Camera Video Stream
  - Object Detection & Tracking (Target Coordinates dx, dy)
         │
         │ UART / Serial Commands
         ▼
[ Low-Level: ESP32 Flight Controller (500Hz) ]
  - MPU-6050 IMU + Complementary Filter
  - Dual-Loop Cascaded PID (Rate & Angle)
  - RC Receiver Decoding (Microzone MC6RE) & Failsafe
  - Quad-X Motor Mixing
         │
         ├──► 4x 40A ESCs ──► 4x A2212 2450KV Brushless Motors
         ├──► MicroSD Card SPI (Blackbox Datalogger)
         └──► Servo Gripper Mechanism (Payload Grasping)
```

---

## 🛠️ Hardware Specifications (Bill of Materials)

| Subsystem | Component | Specifications | Role |
| :--- | :--- | :--- | :--- |
| **Airframe** | DJI F450 Quadcopter | 450mm wheelbase, integrated PDB | Main chassis & power distribution |
| **Flight Controller** | ESP32 DevKit (NodeMCU-32S) | 38-Pin version, Dual-Core 240MHz, CP2102 | Central flight computing & PID loop |
| **IMU** | GY-521 (MPU-6050) | 3-axis Accelerometer + 3-axis Gyroscope (I2C) | Attitude and angular rate estimation |
| **Barometer** | HW-611 (BMP280) | Barometric pressure sensor (I2C) | Altitude sensing & vertical hold |
| **Radio Receiver** | Microzone MC6RE Mini | 6-Channel 2.4GHz S-FHSS | Remote pilot control uplink |
| **Propulsion** | 4x A2212/5T 2450KV | Outrunner brushless motors (~27,000 RPM) | Thrust generation |
| **Speed Controllers** | 4x 40A ESC | 40A continuous rating per arm | 3-phase high-current motor control |
| **Power Supply** | 3S LiPo Battery (11.1V - 12.6V) | 4-wire balance plug, Deans T-Plug | Main power source |
| **Voltage Regulator** | DC-DC Buck (LM2596 / XL4015) | Step-down 12V LiPo to clean 5.0V | Isolated clean power for ESP32 & sensors |
| **Blackbox Telemetry** | Micro SD Card Adapter | SPI interface (CS, SCK, MOSI, MISO) | Flight telemetry & PID log recording |

---

## 🎯 Development Roadmap & Progress Tracker

### 📍 Phase 1: Hardware Bring-Up & Diagnostics
- [ ] **1.1.** Measure and verify 5.0V output from Buck converter.
- [ ] **1.2.** Read raw data from MPU-6050 (Gyro & Accel) via I2C.
- [ ] **1.3.** Read pressure and calculate relative altitude from BMP280 via I2C.
- [ ] **1.4.** Decode 6 PWM channels (1000µs – 2000µs) from Microzone MC6RE receiver.
- [ ] **1.5.** Spin-test 4 motors via ESCs to verify rotation directions (CW / CCW) *without props*.

### 📍 Phase 2: Core Flight Control Firmware
- [ ] **2.1.** Automated gyro/accel calibration on boot.
- [ ] **2.2.** Sensor fusion (Complementary Filter) with low-pass filtering to cut motor vibration.
- [ ] **2.3.** Safety logic: Arming/Disarming switch and Radio Failsafe engine.
- [ ] **2.4.** Quad-X motor mixer algorithm mapping Throttle/Pitch/Roll/Yaw to 4 motors.

### 📍 Phase 3: PID Stabilization (Stable Hover Flight)
- [ ] **3.1.** Implement Inner-Loop Angular Velocity (Rate) PID controller @ 500Hz.
- [ ] **3.2.** Implement Outer-Loop Attitude (Angle) PID controller for self-leveling.
- [ ] **3.3.** Bench testing on a single axis for Roll & Pitch PID damping.
- [ ] **3.4.** Yaw heading control tuning.
- [ ] **3.5.** **Milestone: First stable tethered/free hover flight.**

### 📍 Phase 4: Altitude Hold & Blackbox Logging
- [ ] **4.1.** Implement SPI datalogging to MicroSD card for post-flight PID analysis.
- [ ] **4.2.** Closed-loop Z-axis PID control with BMP280 for Altitude Hold mode.

### 📍 Phase 5: FPV Camera & Computer Vision
- [ ] **5.1.** Mount FPV camera payload and set up live video feed.
- [ ] **5.2.** Run object detection (OpenCV / TinyML) to locate target objects in frame.
- [ ] **5.3.** Calculate spatial error vector $(dx, dy)$ relative to target center.

### 📍 Phase 6: Autonomous Visual Servoing & Object Grasping
- [ ] **6.1.** Vision-guided autonomous approach toward detected object.
- [ ] **6.2.** Precision descent and alignment above target.
- [ ] **6.3.** Trigger robotic servo gripper to grasp and secure the target payload.
- [ ] **6.4.** Autonomous lift-off and transport.

---

## ⚠️ Safety Warning
- **ALWAYS REMOVE PROPELLERS** during USB connection, firmware flashing, and bench testing.
- Verify 5.0V regulated power before connecting ESP32 to prevent hardware damage.

---

## 📜 License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
