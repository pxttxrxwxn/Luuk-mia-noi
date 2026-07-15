# SDS: Pedestrian Crossing Detection

## 1. Description
This module ensures pedestrian safety by suppressing the green-light "Go" alert if a pedestrian is crossing the road in the vehicle's forward path.

## 2. Dependencies
*   **Object Detection:** YOLOv11n model configured to detect the `person` class.
*   **Vehicle Lane ROI:** A predefined region representing the forward driving path, defined as:
    *   `X-axis range:` 25% to 75% of the frame width.
    *   `Y-axis range:` 40% to 100% of the frame height (representing the immediate foreground and mid-ground road lane).

## 3. Decision Logic & Alert Priorities
The module overrides normal traffic logic to ensure safety first:

*   **Intersection Metric:** The system calculates the bounding box overlap of any detected `person` with the Vehicle Lane ROI.
    *   *Calculation:* Overlap Area / Pedestrian Bounding Box Area.
    *   *Trigger Threshold:* If the overlap is greater than 10%, a pedestrian is flagged as "in-path".
*   **Safety Overrides (Alert Priorities):**
    *   *Condition:* Even if the traffic light transitions to Green (`STATE_GREEN_LIGHT`) or the vehicle in front moves (`STATE_VEHICLE_MOVING`), if a pedestrian is flagged as "in-path", the "Go" voice alert is suppressed.
    *   *Action:* Trigger a high-priority interrupt warning: "Warning: Pedestrian crossing, do not accelerate."
    *   *Duration:* The warning remains active, and the "Go" alert is blocked, until the pedestrian's overlap with the ROI drops to 0%.

## 4. Inputs and Outputs
*   **Inputs:** Bounding box coordinates for `person` classes from YOLOv11n, and current state signals from the Traffic Light/Vehicle module.
*   **Outputs:** Override signal to Audio Alert Module (suppressing "Go", forcing "Pedestrian Warning") and status update to Web Dashboard.
