# Software Design Specification (SDS) for Luuk Mia Noi

## 1. Introduction
### 1.1 Purpose
The purpose of this Software Design Specification (SDS) is to describe the architectural and detailed design of the "Luuk Mia Noi" driver assistance system. It serves as a blueprint for developers to implement the system.

### 1.2 Scope
This document covers the structural design of the system, including the software modules, data flow, internal state management, and the technologies used (Python, OpenCV, YOLOv11n, YOLOv11n-cls). It details multiple core features: traffic light detection & state classification, front vehicle tracking, pedestrian crossing detection, traffic sign detection, and the web-based driver dashboard.

## 2. System Architecture
### 2.1 High-Level Architecture
The system follows a pipeline architecture, where the video feed is processed frame-by-frame in real-time.
- **Input Layer:** Captures real-time frames from the webcam.
- **Processing Layer:** Runs YOLOv11n inference to detect objects, and YOLOv11n-cls to classify traffic light states (Red, Yellow, Green).
- **Logic Layer:** Analyzes the detected objects, manages state machines, and handles specific feature logic (traffic light states, pedestrian crossing, traffic sign warnings) with safety-first priority.
- **Output Layer:** Triggers asynchronous audio alerts and streams frame data/states to the Web Dashboard UI via WebSockets.

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
- **Technology:** Ultralytics YOLOv11n API (for detection) and YOLOv11n-cls API (specifically for traffic light color classification).
- **Outputs:** List of detected objects with properties: `class_id`, `bounding_box` (x, y, w, h), and `confidence_score`.
- **Sub-task (State Extraction):** For each detected traffic light, crop its bounding box from the frame and pass the cropped image to the YOLOv11n-cls classification model to determine its active state (Red, Yellow, or Green). This ensures robust classification under varying illumination.

### 3.3 Decision Logic Module (Feature Separation)
- **Responsibility:** Maintain the state of the environment and trigger corresponding actions for each distinct feature.
- **Priority Logic:** Conflicting alert states must be resolved hierarchically. Safety-critical alerts (e.g., pedestrian crossing) take absolute precedence over operational alerts (e.g., vehicle moving/green light "Go").

The detailed design for each system feature has been separated into individual documents:
- **[Traffic Light & Front Vehicle Tracking](SDS_Traffic_Light_Vehicle.md)**
- **[Pedestrian Crossing Detection](SDS_Pedestrian_Crossing.md)**
- **[Traffic Sign Detection](SDS_Traffic_Sign.md)**

### 3.4 Audio Alert Module
- **Responsibility:** Play a sound or voice notification to alert the user.
- **Technology:** Multi-threaded or asynchronous playback using `pygame.mixer` or python's `threading` module to prevent voice alerts from blocking the main frame processing thread.
- **Inputs:** Trigger signal and priority flag from the Decision Logic Module.

## 4. Interface Design
### 4.1 Dashboard UI (Graphical Interface)
The system features a comprehensive Dashboard UI to provide clear, real-time visual feedback to the driver.
- For detailed specifications, see: **[Dashboard UI Design](SDS_Dashboard.md)**

### 4.2 Hardware Interfaces
- **Webcam:** Accessed via standard USB video class (UVC) drivers.
- **Speakers:** Accessed via standard OS audio APIs.

## 5. Error Handling & Constraints
- **Camera Disconnection:** The system should catch `VideoCapture` failures and attempt to reconnect or gracefully exit with an error message.
- **Model Missing:** Validate the presence of YOLOv11n weights (`yolo11n.pt`) and YOLOv11n-cls weights (`yolo11n-cls.pt`) before starting the capture loop.
- **Performance & Latency:** The main frame processing loop must execute within 100ms (10+ FPS) to guarantee responsiveness. Multi-threading is mandatory for heavy tasks like web streaming and audio playback to meet this constraint.
- **Lighting Constraints:** Color thresholding (HSV) is forbidden due to night-time/backlight susceptibility. The system must rely on YOLOv11n-cls classification for traffic light states.
