
#  Luuk Mia Noi (ลูกเมียน้อย)

  

**Luuk Mia Noi** is a real-time Advanced Driver Assistance System (ADAS) designed to enhance road safety by leveraging deep learning and computer vision. Powered by **YOLOv11** and **OpenCV**, the system monitors the environment for traffic lights, vehicles, pedestrians, and traffic signs, and provides instant audio-visual warnings to the driver.

  

---

  

##  Team Members (สมาชิกในกลุ่ม)

  


| **Student ID** | **Name**               |
|-----------------|------------------------|
|**67026506**| อภิวิชญ์ ทองย้อย |
|**67022962**| อดิเทพ แก้วเอ      |
|**67026427**| ภัทรวินทร์ รุ่งพนารัตน์ |



---

  

##  System Architecture & Data Flow

  

The system employs a real-time frame processing pipeline:

1.  **Input Layer**: Captures real-time video frames from the webcam using OpenCV.

2.  **Detection Layer**: Runs inference using YOLOv11 to detect objects (vehicles, traffic lights, pedestrians, traffic signs).

3.  **Decision Logic Layer**: Separates logic into modules to determine system states and safety rules.

4.  **Output Layer**: Updates the Graphical Dashboard UI and triggers corresponding text-to-speech or audio alerts.

  

```text

[Webcam] ──(Raw Frames)──> [OpenCV Capture]

│

▼

(Pre-processed Frames)

│

▼

[YOLOv11 Model] <──(Bounding Boxes, Classes, Confidences)

│

▼

[Decision Logic Module] ──(Trigger Signal)──> [Audio Output Module]

│

└─────────────────────────────────> [Dashboard UI]

```

  

---

  

## Core Features & Modules

  

### 1.  Traffic Light & Front Vehicle Tracking

*  **Description**: Monitors the active state of traffic lights (Red/Green) and tracks the front vehicle's movement if no traffic light is detected.

*  **Logic**: Transitions through a state machine (`STATE_RED_LIGHT`, `STATE_GREEN_LIGHT`, `STATE_NO_LIGHT_TRACK_VEHICLE`) to alert the driver when it is safe to proceed.


  

### 2.  Pedestrian Crossing Detection

*  **Description**: Evaluates road safety by detecting pedestrians crossing in the vehicle's path.

*  **Logic**: Overrides the standard green light "Go" warning and signals a "Pedestrian Crossing" warning if a person's bounding box intersects the central ROI.


  

### 3.  Traffic Sign Detection

*  **Description**: Scans the road ahead for warning and regulatory signs, such as the "Traffic Light Ahead" sign.

*  **Logic**: Triggers preparation alerts like "Traffic light ahead, prepare to reduce speed" when the bounding box area increases past a specified threshold.


  

### 4. Dashboard UI

*  **Description**: Provides real-time visual feedback to the driver, displaying the camera feed alongside current safety states.

  

---

  

##  Tech Stack

  

*  **Programming Language**: Python 3.x

*  **Computer Vision**: OpenCV (`opencv-python`)

*  **Deep Learning**: Ultralytics YOLOv11 (`ultralytics`)

*  **Audio Alerts**: Pygame (`pygame.mixer`), `playsound`, or Text-to-Speech (`pyttsx3`)

*  **Dashboard Frontend**: React 18+ (Vite) with Tailwind CSS 3+ (Glassmorphism UI)
*  **Dashboard Backend**: Flask / FastAPI (WebSocket server)