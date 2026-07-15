# SDS: Pedestrian Crossing Detection

## 1. Description
This module ensures pedestrian safety by suppressing the acceleration alert if a pedestrian is crossing the road while the light changes to green or is currently green.

## 2. Dependencies
- YOLOv11 for Pedestrian (person class) detection.
- ROI (Region of Interest) mapping for the road path.

## 3. Decision Logic
- **Condition:** When the traffic light turns from Red to Green (or while Green).
- **Trigger:** If a pedestrian bounding box intersects the central ROI (the vehicle's forward path).
- **Action:** The system suppresses the normal "Go" alert and triggers a "Pedestrian Crossing" warning advising the driver not to accelerate.

## 4. Inputs and Outputs
- **Input:** YOLOv11 bounding boxes for 'person' and current traffic light state.
- **Output:** Override trigger to Audio Alert Module (Warning: Pedestrian Crossing).
