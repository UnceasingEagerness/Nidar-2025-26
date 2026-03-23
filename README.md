#  Autonomous Disaster Management Drone System

## 1. Overview

This repository contains the complete software architecture, autonomous flight logic, and computer vision pipelines developed by **Team RAPTOR (ID: N250781)** for the **NIDAR Challenge 2025–26**.

Our solution is a **dual-drone autonomous system** designed for rapid **survey, reconnaissance, and precision payload delivery** in disaster-stricken environments.

---

## 2. Problem Statement

During severe flooding in a coastal region, residents may become stranded on rooftops without access to essential supplies.

### Mission Objectives

* Autonomously scan a **30-hectare disaster zone**
* Detect and geotag survivors using real-time vision
* Deploy a delivery drone to **precisely drop 200g survival kits**

---

## 3. System Architecture

```text
Scout Drone (Detection + Geotagging)
        ↓  (LoRa / Serial Communication)
Geotag Coordinates (lat, lon)
        ↓
Delivery Drone (Planning + Navigation + AI Alignment)
        ↓
Precision Payload Deployment
```

---
## 4. Project Structure

```bash
NIDAR/
├── README.md
├── .gitignore
│
├── delivery_drone/
│   ├── main.py
│   ├── behaviours/
│   ├── models/
│   │   └── yolov8s_new.hef
│   └── data/
│       └── received_geotags.json
│
├── scout_drone/
│   ├── scout.py
│   ├── geolocation.py
│   ├── models/
│   │   └── yolov8s_visdrone.pt
│   └── mission_files/
│       └── cricketground_full.kml
│
└── docs/
    ├── architecture.png
    └── system_design.md
```

---

## 5. Key Features

### 5.1 Scout Drone (Reconnaissance & Geotagging)

#### 5.1.1 Automated Flight Path Generation
- Generates a lawnmower grid pattern from KML boundaries  
- Ensures complete area coverage with minimal overlap  

---

#### 5.1.2 Real-Time Survivor Detection
- YOLOv8s (VisDrone dataset) with BoT-SORT tracking  
- Adaptive confidence scaling for distant targets  
- Robust detection with minimal false positives  

---

#### 5.1.3 Smart Geotagging
- Trigger-based detection (bottom-frame zone)  
- Ensures near-vertical alignment for accurate GPS tagging  
- Filters duplicate detections using spatial thresholds  

---

#### 5.1.4 Communication Pipeline
- Transmits geotags in real-time via LoRa serial communication  
- Lightweight and reliable for long-range communication  

---

### 5.2 Delivery Drone (Planning & Payload Delivery)

#### 5.2.1 TSP-Based Path Optimization
- Groups geotags into batches based on payload capacity  
- Uses Traveling Salesman Problem (TSP) solver  
- Minimizes total flight distance and energy usage  

---

#### 5.2.2 Behavior Tree Autonomy
- Built using py_trees  
- Handles:
  - Mission execution  
  - Safety checks  
  - Fail-safe actions (RTL, payload checks)  

---

#### 5.2.3 Precision AI Alignment
- Powered by Hailo-8 AI Accelerator  
- Real-time visual servoing using pixel error  
- Dynamically adjusts drone position for accurate targeting  

---

#### 5.2.4 Controlled Payload Deployment
- Servo-actuated payload release mechanism  
- Supports sequential and safe delivery  

---

#### 5.2.5 Parallel Execution System
- Dual-threaded architecture:
  - Control thread (flight logic)  
  - Communication thread (geotag reception)  
- Uses shared JSON blackboard for synchronization

---
## 6 System Components

---

### 6.1 Scout Drone (Quadcopter)

#### 6.1.1 Hardware

* Custom PLA + Carbon Fiber Frame
* 920KV BLDC Motors + 1045 Propellers
* Pixhawk 6C Flight Controller
* NVIDIA Jetson Orin Nano (AI compute)
* F9P GPS Module
* LoRa Communication Module
* USB / SIYI Camera

#### 6.1.2 Software Architecture

* Multi-threaded execution:

  * Flight path generation
  * Detection & tracking
  * Geotag transmission

---

### 6.2 Delivery Drone (Hexacopter)

#### 6.2.1 Hardware

* Custom Hexacopter Frame
* 22000mAh 4S LiPo Battery
* Pixhawk Cube Orange+
* Raspberry Pi 5 + Hailo AI Hat
* Here 3+ GNSS
* SiK Telemetry Module
* Raspberry Pi AI Camera

#### 6.2.2 Software Architecture

* Dual-threaded system:

  * **Control Thread** → Behavior Tree execution
  * **Communication Thread** → LoRa geotag reception

* Shared JSON-based **Blackboard System**

---

## 7 Tech Stack

### 7.1 Flight Control & Autonomy

* DroneKit Python
* MAVLink (pymavlink)
* py_trees (Behavior Trees)

### 7.2 Computer Vision

* OpenCV
* Ultralytics YOLOv8
* Hailo GStreamer Pipeline

### 7.3 Hardware & Communication

* NVIDIA JetPack
* Raspberry Pi OS
* LoRa (pyserial)

### 7.4 Design & Validation

* Autodesk Fusion 360 (CAD Modelling)
* ANSYS (Static Structural Analysis)

---

## 8 Installation & Setup

### 8.1 Clone Repository

```bash
git clone https://github.com/aeroiit/nidar-challenge-2025-26.git
cd nidar-challenge-2025-26
```

---

### 8.2 Scout Drone Setup (Jetson Orin Nano)

```bash
cd scout_drone
pip install -r requirements.txt
```

> Ensure YOLO weights are placed in:

```
scout_drone/models/
```

---

### 8.3 Delivery Drone Setup (Raspberry Pi 5)

```bash
cd delivery_drone
pip install -r requirements.txt
```

> Ensure:

* Hailo runtime installed
* GStreamer plugins configured
* `.hef` model placed in:

```
delivery_drone/models/
```

---

## 9 Usage

---

### 9.1 Run Scout Drone

```bash
cd scout_drone
python3 scout.py mission_files/PATH_TO_KML_FILE.kml
```

> Automatically:

* Generates path
* Detects survivors
* Sends geotags

---

### 9.2 Run Delivery Drone

```bash
cd delivery_drone
python3 main.py
```

> Automatically:

* Receives geotags
* Plans optimal route
* Aligns and drops payload

---

## 10 Key Modules

* **main.py (Delivery)**
  Behavior Tree execution and mission control

* **scout.py (Scout)**
  Flight path generation and navigation

* **geolocation.py (Scout)**
  Detection, tracking, and geotagging

* **drone_allign.py (Delivery)**
  Hailo-based visual alignment using pixel error

* **path_planner.py (Delivery)**
  TSP-based route optimization

* **geotag_read.py (Delivery)**
  LoRa communication + ACK protocol

* **deploy_action.py (Delivery)**
  MAVLink servo control for payload release

---

## 11 Safety & Reliability

* Return-to-Launch (RTL) on:

  * Low battery
  * Payload depletion

* Duplicate geotag filtering

* Stable detection verification

* Robust communication with ACK protocol

---

## 12 Future Improvements

* Multi-target prioritization using confidence scoring
* Swarm coordination between multiple scout drones
* Improved control using PID-based alignment
* Real-time mission monitoring dashboard

---

##  Team RAPTOR

Developed for the **NIDAR Disaster Management Challenge (2025–26)**

---

## 📜 License

This project is intended for academic and research purposes.
