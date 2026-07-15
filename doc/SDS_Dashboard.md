# SDS: Dashboard UI

## 1. Description
The Dashboard UI provides real-time visual feedback to the driver, displaying the camera feed alongside system states and alerts.

## 2. Technology Stack
- Web-based dashboard (HTML/CSS/JS with Flask/FastAPI) OR Desktop GUI (PyQt/Tkinter).

## 3. Interface Elements
- **Live Camera Feed:** Shows real-time video overlaid with object bounding boxes.
- **System State Panel:** Displays the current active status prominently (e.g., "Wait", "Go", "Pedestrian Crossing", "Reduce Speed").
- **Alert Notifications:** Visual pop-ups that correspond with the generated voice alerts.

## 4. Inputs and Outputs
- **Input:** Raw camera frames, bounding box data from YOLOv11, state signals from the Logic Module.
- **Output:** Visual rendering on the screen for the driver.
