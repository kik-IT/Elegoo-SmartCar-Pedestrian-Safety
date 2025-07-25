# Elegoo Smart Car - Vision-Based Pedestrian Safety System

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)

### Project Demo
[ElegooSmartCar Pedstrian Safety Demo](https://youtu.be/levevryceGo)

## Table of Contents
1. [Project Overview](#project-overview)
2. [System Specifications](#system-specifications)
3. [Hardware Requirements](#hardware-requirements)
4. [Software Architecture](#software-architecture)
5. [Installation Guide](#installation-guide)
6. [Operation Manual](#operation-manual)
7. [Technical Implementations](#technical-implementations)
8. [Development Process](#development-process)
9. [System Limitations](#system-limitations)

## Project Overview

The primary function is to detect pedestrians in the car's path using an onboard camera and automatically initiate stopping procedures when pedestrians are identified.

Key features:
- Real-time image processing at 3-5 FPS
- Custom-trained convolutional neural network
- WiFi-based control interface
- Configurable confidence threshold (default: 0.75)
- Connection management

## System Specifications

| Component               | Specification                          |
|-------------------------|----------------------------------------|
| Processing Unit         | External host computer (Python 3.8+)   |
| Detection Latency       | 250-350ms end-to-end                   |
| Camera Resolution       | 1600×1200 (MJPEG stream at 5 FPS)      |
| Model Input Resolution  | 224×224 pixels                         |
| Control Protocol        | Port 100                               |
| Heartbeat Interval      | 800ms                                  |
| Max Reconnection Attempts | 5                                    |

## Hardware Requirements

### Essential Components
1. Elegoo Smart Car v4.0 with:
   - Assembled chassis and motor assembly
   - ESP32-S3-Eye camera module
   - Fully(or mostly) charged 18650 battery pack

2. Host Computer:
   - Minimum: Intel Core i5 or equivalent
   - Operating System: Windows 10/11 or Linux
   - WiFi connectivity

### Connection Diagram
```plaintext
[Laptop] ←WiFi→ [ESP32-S3-Eye]
                   │
                   ├─ Camera Stream (MJPEG)
                   └─ Motor Control (TCP Commands)
```

## Software Architecture

### Core Components

1. **Main Control System (main_control.py)**
   - Image acquisition and preprocessing
   - Model inference engine
   - Decision-making logic
   - Command transmission handler

Key implementation details:
```python
# Safety stop condition implementation
if ("person" in detected_class and 
    prediction_confidence > CONFIDENCE_THRESHOLD):
    send_emergency_stop()
```
2. **Debugging Interface (debug_interface.py)**
   - Frame capture diagnostics
   - Model performance metrics
   - Command echo testing
   - Verification and debugging messages
   
3. **Motor Test Utility (motor_test.py)**
   - Basic movement validation
   - Connection testing

## Installation Guide

### Prerequisites
   - Python 3.8+
   - pip package manager
   - Git version control

### Set-up Instructions
1. Clone the repository:
```bash
  git clone https://github.com/keiraIT/Elegoo-SmartCar-Pedestrian-Safety.git
  cd Elegoo-SmartCar-Pedestrian-Safety
   ```
2. Install python dependencies:
``` bash
pip install -r requirements.txt
```
3. Configure model files:
``` bash
cp /path/to/your/model.h5 models/keras_model.h5
cp /path/to/your/labels.txt models/labels.txt
``` 

## Operation Manual

### Starting the System
1. Power on the Elegoo Smart Car
2. Connect host computer to car's WiFi network (typically 192.168.4.1)
3. Execute the main control script:
``` bash
  python src/main_control.py
``` 

### Expceted Output
```
   System Initialization Complete
   Connected to vehicle at 192.168.4.1:100
   Camera stream established
   [Detection] Class: person, Confidence: 0.89 - Executing safety stop
   [Detection] Class: allowed_object, Confidence: 0.82 - Resuming movement
```

## Technical Implementations

### Model Architecture
- Type: Custom CNN based on MobileNetV2
- Input: 224x224 RGB images (normalized to [-1, 1])
- Output: classification probabilities
- Classes: Defined in labels.txt

### **Communication Protocol**
| Command Format          | Description                  |
|-------------------------|------------------------------|
| ``{"N":100}``           | Emergency stop               |
| ``{"N":3,"D1":X,"D2":Y}`` | Moves (X= direction) at (Y = speed)    |
| ``{Heartbeat}``         | Keeps connection alive       | 

## Development Process

1. **Part 1:** Base
   - Establish basic WiFi control
   - Implement camera streaming
   - Developed motor test utility

2. **Part 2:** Model Integration
   - Custom model training and conversion
   - Implemented custom layer compatibility
   - Developed image preprocessing pipeline
3. **Part 3:** Safety Implementation
   - Added confidence thresholding
   - Implemented emergency stop protocol
   - Implemented heartbeat system
4. **Part 4:** Optimization
   - Tuned system timings
   - Improved error handling
   - Added reconnection logic

## System Limitations

1. **Functional Constraints**
   - Requires external host computer
   - Limited to predefined detection classes
   - Optimal performance requires good lighting
2. **Performance Boundaries**
   - Maximum reliable detection range: 3-4 meters
   - Processing latency: 250-350ms
   - WiFi connection stability affects performance
3. **Environmental Factors**
   - Performance degrades in low light
   - Complex backgrounds may reduce accuracy
   - Fast-moving objects may not be detected reliably


## References and Attributions
### Official Documentation Used
- **Elegoo Smart Car Protocol**:  
  All motor control commands (`N:3`, `D1:1`, etc.) follow the [Elegoo Smart Robot Car V4.0 Communication Protocol](Communication%20protocol%20for%20Smart%20Robot%20Car.pdf).  
     - EX: `{"N":3,"D1":3,"D2":200}` for move car, forward(3) at 200(PWM = 0-255) speed.
### Course Materials Used
- **Network Implementations**:  
  The socket initialization and heartbeat logic were both adapted from unpublished course materials (   ).  
- **Camera Integration**:  
  The TensorFlow Lite deployment for the model was also adapted from unpublished material provided via my course (   ).
### AI Collaboration
- **Debugging and Optimization**:  
  Used AI to resolve version compatibility issues (i.e., custom `DepthwiseConv2D` layer), and for isolating errors.
