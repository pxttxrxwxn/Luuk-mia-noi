# Software Design Specification (SDS) for Luuk Mia Noi

## 1. Introduction
### 1.1 Purpose
The purpose of this Software Design Specification (SDS) is to describe the architectural and detailed design of the "Luuk Mia Noi" driver assistance system. It serves as a blueprint for developers to implement the system.

### 1.2 Scope
This document covers the structural design of the system, including the software modules, data flow, internal state management, and the technologies used (Python, OpenCV, YOLOv11). It details multiple core features: traffic light detection, front vehicle tracking, pedestrian crossing detection, traffic sign detection, and the driver dashboard.

## 2. System Architecture
### 2.1 High-Level Architecture
The system follows a pipeline architecture, where the video feed is processed frame-by-frame in real-time.
- **Input Layer:** Captures real-time frames from the webcam.
- **Processing Layer:** Runs YOLOv11 inference to detect objects (traffic lights, vehicles, pedestrians, and traffic signs).
- **Logic Layer:** Analyzes the detected objects and handles specific feature logic (traffic light states, pedestrian crossing, traffic sign warnings).
- **Output Layer:** Triggers audio alerts and updates the Graphical Dashboard.

### 2.2 Data Flow
```text
[Webcam] --> (Raw Frames) --> [OpenCV Capture]
                                   |
                                   v
                             (Pre-processed Frames)
                                   |
                                   v
[YOLOv11 Model] <-- (Bounding Boxes, Classes, Confidences)
                                   |
                                   v
[Decision Logic Module] --> (Trigger Signal) --> [Audio Output Module]
           |
           +--------------> [Dashboard UI]
```

## 3. Module Design
### 3.1 Camera Input Module
- **Responsibility:** Interface with the physical webcam to continuously capture video frames.
- **Technology:** OpenCV (`cv2.VideoCapture`)
- **Outputs:** Numpy array representing an RGB/BGR image frame.

### 3.2 Detection Module
- **Responsibility:** Perform object detection on the provided frame to identify traffic lights, vehicles, pedestrians, and traffic signs.
- **Technology:** Ultralytics YOLOv11 API.
- **Outputs:** List of detected objects with properties: `class_id`, `bounding_box` (x, y, w, h), and `confidence_score`.
- **Sub-task (State Extraction):** Extract the region of interest (ROI) for the traffic light and determine its active color (Red or Green) using HSV color thresholding or a fine-tuned model classification.

### 3.3 Decision Logic Module (Feature Separation)
- **Responsibility:** Maintain the state of the environment and trigger corresponding actions for each distinct feature.

The detailed design for each system feature has been separated into individual documents:
- **[Traffic Light & Front Vehicle Tracking](SDS_Traffic_Light_Vehicle.md)**
- **[Pedestrian Crossing Detection](SDS_Pedestrian_Crossing.md)**
- **[Traffic Sign Detection](SDS_Traffic_Sign.md)**

### 3.4 Audio Alert Module
- **Responsibility:** Play a sound or voice notification to alert the user.
- **Technology:** Libraries such as `pygame.mixer`, `playsound`, or `pyttsx3` (for Text-to-Speech).
- **Inputs:** Trigger signal from the Decision Logic Module.

## 4. Interface Design
### 4.1 Dashboard UI (Graphical Interface)
The system features a comprehensive Dashboard UI to provide clear, real-time visual feedback to the driver.
- For detailed specifications, see: **[Dashboard UI Design](SDS_Dashboard.md)**

### 4.2 Hardware Interfaces
- **Webcam:** Accessed via standard USB video class (UVC) drivers.
- **Speakers:** Accessed via standard OS audio APIs.

## 5. Error Handling & Constraints
- **Camera Disconnection:** The system should catch `VideoCapture` failures and attempt to reconnect or gracefully exit with an error message.
- **Model Missing:** Validate the presence of YOLOv11 weights (e.g., `yolo11n.pt`) before starting the capture loop.
- **Performance:** The frame processing time must not exceed 100ms (to maintain a minimum of 10 FPS) for the system to feel responsive.
