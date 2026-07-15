# SDS: Dashboard UI

## 1. Description
The Dashboard UI provides real-time visual feedback to the driver, displaying the camera feed alongside system states and alerts. Built as a modern single-page application using React and Tailwind CSS for a responsive, performant glassmorphism interface.

## 2. Technology Stack
*   **Backend Server:** Python with Flask / FastAPI (WebSocket server for real-time data streaming).
*   **Frontend Framework:** React 18+ with Vite as the build tool and dev server.
*   **Styling:** Tailwind CSS 3+ with custom glassmorphism theme configuration.
*   **State Management:** React Context API for global real-time system state.
*   **Communication Protocol:** WebSocket connection via a custom `useWebSocket` hook for real-time frame streaming and JSON state synchronization with sub-100ms latency.

## 3. React Component Architecture

### 3.1 Component Tree
```
App
├── Dashboard
│   ├── LiveFeed
│   │   ├── CameraStream
│   │   └── BoundingBoxOverlay
│   ├── StatusPanel
│   │   ├── TrafficLightIndicator
│   │   ├── PedestrianAlert
│   │   ├── VehicleTracker
│   │   └── TrafficSignAlert
│   └── AlertHistory
│       └── AlertItem
└── ConnectionStatus
```

### 3.2 File Structure
```
dashboard-ui/
├── public/
│   └── favicon.ico
├── src/
│   ├── components/
│   │   ├── Dashboard.jsx
│   │   ├── LiveFeed.jsx
│   │   │   ├── CameraStream.jsx
│   │   │   └── BoundingBoxOverlay.jsx
│   │   ├── StatusPanel.jsx
│   │   │   ├── TrafficLightIndicator.jsx
│   │   │   ├── PedestrianAlert.jsx
│   │   │   ├── VehicleTracker.jsx
│   │   │   └── TrafficSignAlert.jsx
│   │   ├── AlertHistory.jsx
│   │   │   └── AlertItem.jsx
│   │   └── ConnectionStatus.jsx
│   ├── context/
│   │   └── SystemStateContext.jsx
│   ├── hooks/
│   │   └── useWebSocket.js
│   ├── utils/
│   │   └── formatters.js
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── tailwind.config.js
├── postcss.config.js
├── vite.config.js
├── package.json
└── index.html
```

### 3.3 State Management
Global system state is managed via React Context (`SystemStateContext`):

```javascript
// Initial state shape
const initialState = {
  connectionStatus: "disconnected",  // "connected" | "disconnected" | "reconnecting"
  trafficLight: {
    state: "UNKNOWN",                // "RED" | "YELLOW" | "GREEN" | "UNKNOWN"
    confidence: 0
  },
  pedestrian: {
    detected: false,
    inPath: false,
    overlapPercentage: 0
  },
  vehicle: {
    detected: false,
    state: "IDLE",                   // "IDLE" | "TRACKING" | "MOVING"
    boundingBox: null
  },
  trafficSign: {
    detected: false,
    type: null,
    proximityAlert: false
  },
  alerts: []                         // Array of { id, timestamp, message, priority }
};
```

### 3.4 Custom Hook: `useWebSocket`
A reusable React hook that manages the WebSocket connection lifecycle:

```javascript
// Usage in components
const { isConnected, latestFrame, systemState } = useWebSocket(WS_URL);
```

**Responsibilities:**
- Establishes and maintains WebSocket connection to the Flask/FastAPI backend.
- Handles automatic reconnection with exponential backoff on disconnect.
- Parses incoming base64-encoded JPEG frames and updates the `<canvas>` element.
- Parses incoming JSON state payloads and dispatches updates to `SystemStateContext`.
- Provides connection status indicator (`connected`, `disconnected`, `reconnecting`).

### 3.5 Tailwind CSS Glassmorphism Theme
The glassmorphism design is achieved via a custom Tailwind configuration:

```javascript
// tailwind.config.js
export default {
  content: ["./index.html", "./src/**/*.{js,jsx}"],
  theme: {
    extend: {
      backdropBlur: {
        xs: "2px",
      },
      boxShadow: {
        glass: "0 8px 32px 0 rgba(31, 38, 135, 0.37)",
      },
      animation: {
        "pulse-green": "pulseGreen 2s ease-in-out infinite",
        "flash-red": "flashRed 1s ease-in-out infinite",
      },
      keyframes: {
        pulseGreen: {
          "0%, 100%": { boxShadow: "0 0 5px #22c55e, 0 0 20px #22c55e" },
          "50%": { boxShadow: "0 0 20px #22c55e, 0 0 40px #22c55e" },
        },
        flashRed: {
          "0%, 100%": { opacity: "1" },
          "50%": { opacity: "0.5" },
        },
      },
    },
  },
  plugins: [],
};
```

**Key utility classes for glass panels:**
- `bg-white/10 backdrop-blur-md border border-white/20 rounded-2xl shadow-glass` — Base glass panel
- `animate-pulse-green` — Green "GO" state glow animation
- `animate-flash-red` — Flashing red "WAIT / PEDESTRIAN" alert animation

## 4. Interface Elements & Layout
The dashboard employs a dual-panel responsive grid system built with Tailwind CSS:

*   **Live Camera Feed (Left Panel - 70% Width):** A React `<canvas>` element rendered by the `CameraStream` component, displaying real-time camera frames. A transparent `BoundingBoxOverlay` layer renders YOLOv11n bounding boxes, labels, and ROI paths using absolute positioning.
*   **System Status Panel (Right Panel - 30% Width):** A vertical stack of glassmorphism cards, each representing a sub-system state:
    - `TrafficLightIndicator` — Displays current light state with color-coded glow (green pulse for "GO", flashing red for "WAIT").
    - `PedestrianAlert` — Activates with high-priority animation when a pedestrian is detected in-path.
    - `VehicleTracker` — Shows front vehicle tracking status and movement state.
    - `TrafficSignAlert` — Displays proximity warning when a traffic sign is detected nearby.
*   **System Alert History (Bottom Section):** A scrollable container rendered by `AlertHistory`, displaying timestamped log entries of all triggered events and active warnings via `AlertItem` components.

## 5. Inputs and Outputs
*   **Inputs:**
    - Raw NumPy frame arrays from the backend, encoded as base64 JPEG strings via WebSocket.
    - JSON payloads containing detected object bounding boxes and system state events from the Decision Logic module.
*   **Outputs:**
    - Real-time `<canvas>` rendering of camera frames with overlaid bounding boxes.
    - Color-coded status panels updating reactively via React Context state changes.
    - Scrollable alert history log with timestamped entries.

## 6. Deployment & Development
*   **Dev Server:** `npm run dev` starts the Vite development server with hot module replacement (HMR).
*   **Production Build:** `npm run build` outputs optimized static assets to `dist/`.
*   **Backend Integration:** The Flask/FastAPI backend serves the built React app from `dist/` and exposes the WebSocket endpoint at `/ws`.
