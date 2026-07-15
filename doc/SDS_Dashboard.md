# SDS: Dashboard UI

## 1. Description
The Dashboard UI provides real-time visual feedback to the driver, displaying the camera feed alongside system states and alerts.

## 2. Technology Stack
*   **Backend Server:** Python with Flask / FastAPI.
*   **Frontend Client:** HTML5, CSS3 (Modern Glassmorphism Design), Vanilla JavaScript.
*   **Communication Protocol:** WebSockets (`Flask-SocketIO` or FastAPI's `websockets`) for real-time frame streaming and JSON state synchronization with sub-100ms latency.

## 3. Interface Elements & Layout
The dashboard employs a dual-panel responsive grid system:
*   **Live Camera Feed (Left Panel - 70% Width):** HTML `<canvas>` or `<img>` element rendering real-time camera frames overlaid with YOLOv11n bounding boxes, labels, and ROI paths.
*   **System Status Panel (Right Panel - 30% Width):** Displays the current system states prominently with color-coded alerts using modern animations (e.g., green glowing pulse for "GO", flashing red container for "WAIT / PEDESTRIAN CROSSING").
*   **System Alert History (Bottom Section):** A scrollable text log showing timestamps, triggered event classes, and active warnings.

## 4. Inputs and Outputs
*   **Inputs:** Raw NumPy frame arrays, list of detected object bounding boxes, and system state events from the Decision Logic module.
*   **Outputs:** WebSocket streams sending base64-encoded JPEG frames and JSON payloads containing state changes (`{state: "GO", pedestrian_detected: true}`).
