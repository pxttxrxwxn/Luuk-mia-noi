# SDS: Traffic Light & Front Vehicle Tracking

## 1. Description
This module is responsible for detecting the active traffic light state (Red/Green) and tracking the front vehicle's movement if the traffic light is not visible.

## 2. Dependencies
- YOLOv11 for Object Detection.
- OpenCV for region of interest (ROI) extraction and color thresholding.

## 3. Decision Logic & State Machine
- `STATE_IDLE`: Initial state.
- `STATE_RED_LIGHT`: Red light detected; wait for green.
- `STATE_GREEN_LIGHT`: Green light detected; trigger alert, reset to IDLE.
- `STATE_NO_LIGHT_TRACK_VEHICLE`: Traffic light not found; select the largest/closest vehicle bounding box.
- `STATE_VEHICLE_MOVING`: Tracked vehicle's bounding box area decreases significantly (indicating forward movement); trigger alert, reset to IDLE.

## 4. Inputs and Outputs
- **Input:** Video frame containing bounding boxes for traffic lights and vehicles.
- **Output:** Trigger signal for "Go" to the Audio Alert Module.
