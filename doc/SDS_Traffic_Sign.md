# SDS: Traffic Sign Detection

## 1. Description
This module scans the road ahead for specific traffic signs, specifically "Traffic Light Ahead" signs, to warn the driver to prepare to slow down.

## 2. Dependencies
- YOLOv11 trained/configured to detect traffic sign classes.

## 3. Decision Logic
- **Condition:** A "Traffic Light Ahead" sign is detected.
- **Proximity check:** The bounding box size is increasing and surpasses a predefined area threshold (indicating proximity to the sign).
- **Action:** Trigger a voice alert: "Traffic light ahead, prepare to reduce speed."

## 4. Inputs and Outputs
- **Input:** YOLOv11 bounding boxes for traffic signs.
- **Output:** Trigger signal to Audio Alert Module (Warning: Reduce Speed).
