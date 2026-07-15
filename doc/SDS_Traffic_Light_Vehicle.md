# SDS: Traffic Light & Front Vehicle Tracking

## 1. Description
This module is responsible for detecting the active traffic light state (Red/Yellow/Green) and tracking the front vehicle's movement if a traffic light is not in view.

## 2. Dependencies
*   **Object Detection:** YOLOv11n for detecting `traffic light` and `car`/`truck` classes.
*   **Color Classification:** YOLOv11n-cls model specifically trained to classify cropped traffic light boxes into `red`, `yellow`, or `green`.
*   **Centroid Tracker:** Custom script to track the coordinate centroids and bounding box area of the closest front vehicle.

## 3. Decision Logic & State Machine
The module runs a state machine to track environmental state changes:

*   **`STATE_IDLE`**: Initial standby state.
    *   *Transition:* If YOLOv11n detects a traffic light, crop it, run YOLOv11n-cls classification. If Red -> `STATE_RED_LIGHT`.
    *   *Transition:* If no traffic light is detected but a vehicle is directly in front, select the largest vehicle bounding box -> `STATE_NO_LIGHT_TRACK_VEHICLE`.
*   **`STATE_RED_LIGHT`**: Red light detected. Vehicle must remain stationary.
    *   *Transition:* If color classification shifts to Green -> `STATE_GREEN_LIGHT`.
    *   *Transition:* If color classification shifts to Yellow -> `STATE_YELLOW_LIGHT`.
*   **`STATE_YELLOW_LIGHT`**: Yellow light detected.
    *   *Action:* Trigger a one-shot alert: "Traffic light yellow, prepare to stop."
    *   *Transition:* Transition to `STATE_RED_LIGHT` (or `STATE_IDLE` if light goes out).
*   **`STATE_GREEN_LIGHT`**: Green light detected.
    *   *Action:* Trigger a one-shot edge-triggered voice alert: "Green light, you may proceed."
    *   *Transition:* Reset to `STATE_IDLE` after alert triggers.
*   **`STATE_NO_LIGHT_TRACK_VEHICLE`**: No traffic light in sight. The system locks onto the closest vehicle (largest bounding box in the driving lane ROI).
    *   *Tracking Metric:* Record the initial bounding box center `(cx, cy)` and area `A`.
    *   *Transition:* If the vehicle moves away (its centroid `cy` shifts upward toward the horizon AND area `A` decreases by >15%) -> `STATE_VEHICLE_MOVING`.
*   **`STATE_VEHICLE_MOVING`**: Front vehicle has started moving forward.
    *   *Action:* Trigger a one-shot voice alert: "Vehicle in front is moving."
    *   *Transition:* Reset to `STATE_IDLE` after alert triggers.

## 4. Inputs and Outputs
*   **Inputs:** Bounding box coordinates for traffic lights and vehicles from YOLOv11n, cropped traffic light image passed to YOLOv11n-cls.
*   **Outputs:** State signals and edge-triggered event flags sent to the Audio Alert Module and Dashboard.

