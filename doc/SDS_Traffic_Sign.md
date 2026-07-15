# SDS: Traffic Sign Detection

## 1. Description
This module scans the road ahead for warning traffic signs—specifically the "Traffic Light Ahead" sign—and alerts the driver to prepare to decelerate.

## 2. Dependencies
*   **Object Detection:** YOLOv11n model fine-tuned on a custom Traffic Sign Dataset (since "Traffic Light Ahead" is not in standard COCO classes).
*   **Temporal Tracker:** A simple queue tracking the bounding box dimensions of detected signs over consecutive frames to calculate scale progression.

## 3. Decision Logic & Proximity Calculation
To prevent false warnings from signs that are too far away or outside the path, the system employs scale-tracking logic:

*   **Proximity Metric (Bounding Box Area):**
    *   *Calculation:* The bounding box area is calculated as `Area = width * height` in pixels.
    *   *Tracking:* The system checks if `Area` increases consecutively over 5 frames (indicating the vehicle is approaching the sign).
    *   *Trigger Threshold:* The warning is only triggered if the sign's bounding box area exceeds **4,000 pixels** (e.g., approximately 64x64 pixels on a standard 640x480 resolution frame), corresponding to a close distance of ~15-20 meters.
*   **Action:** Trigger a one-shot voice alert: "Traffic light ahead, prepare to reduce speed."
*   **Reset Condition:** Once triggered, the alert for that specific sign instance is locked (cooldown period of 15 seconds) to avoid spamming the driver.

## 4. Inputs and Outputs
*   **Inputs:** Bounding box coordinates and class confidences for `Traffic Light Ahead` class from the fine-tuned YOLOv11n model.
*   **Outputs:** Trigger warning events dispatched to the Audio Alert Module and Web Dashboard.

