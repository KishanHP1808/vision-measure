# Vantage — Real-World AI Measurement Platform
> *"See it. Scale it. Trust the number."*

[![FastAPI](https://img.shields.io/badge/Backend-FastAPI_0.110+-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/Frontend-React_19_+_TypeScript-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![Tailwind CSS](https://img.shields.io/badge/Styling-Tailwind_CSS_v3-38B2AC?logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Computer Vision](https://img.shields.io/badge/CV_Pipeline-MediaPipe_+_OpenCV.js-FF6F00?logo=google&logoColor=white)](https://developers.google.com/mediapipe)
[![Uncertainty Framework](https://img.shields.io/badge/Metrology-GUM_Uncertainty_Propagation-blue)](#uncertainty-quantification-engine)

---

## 1. Executive Summary & Problem Statement

Most browser-based camera measurement prototypes fail in production for three reasons:
1. **Arbitrary "Confidence" Scores:** Many tools display numbers like "95% accuracy" without any physical or statistical grounding.
2. **Silent Scale Drift:** If a user moves their camera closer, tilts the phone, or nudges the reference object, typical systems silently output invalid dimensions without warning.
3. **Black-Box Opacity:** Users cannot inspect which body landmarks were directly detected vs. interpolated due to occlusions.

**Vantage** solves these shortcomings by coupling client-side MediaPipe landmark detection and adaptive edge segmentation with a **metrology-grade reference calibration engine**, **real-time calibration drift detection**, and a **transparent uncertainty quantification framework**.

```
Camera / Photo
      │
      ▼
Client-Side Vision Engine (MediaPipe Pose / Canvas Contour Segmentation)
      │
      ▼
Reference-Object Calibration (A4 Paper / ID-1 Bank Card / Custom Dimension)
      │
      ▼
Pixel-to-Real-World Conversion (scale = real_cm / pixel_distance)
      │
      ▼
Drift Detector (Real-Time Relative Scale Variance Surveillance)
      │
      ▼
Uncertainty Propagation Engine: u_total = √(u_pixel² + u_calib² + u_pose² + u_skew²)
      │
      ▼
Visual Overlay HUD ───► Explainability & Math Breakdown Drawers
      │
      ▼
Persistence & Executive PDF Report Generation (FastAPI + SQLite + ReportLab)
```

---

## 2. Differentiation Layer (Why Vantage Stands Out)

### 1. Uncertainty Bands, Not Fake Confidence Scores
Instead of an arbitrary confidence score, Vantage calculates a physical uncertainty range using the Guide to the Expression of Uncertainty in Measurement (GUM) framework:
$$\text{Measurement} = \text{Value} \pm u_{\text{total}}\text{ cm}$$
$$u_{\text{total}} = \sqrt{u_{\text{pixel}}^2 + u_{\text{calib}}^2 + u_{\text{pose}}^2 + u_{\text{skew}}^2}$$
- **$u_{\text{pixel}}$ (Sub-pixel Discretization):** Accounts for sensor pixel pitch and discrete edge quantization error.
- **$u_{\text{calib}}$ (Calibration Variance):** Propagates manufacturing standard tolerances ($\pm 0.3\text{ mm}$ for ID-1 cards, $\pm 0.8\text{ mm}$ for A4 sheets).
- **$u_{\text{pose}}$ (Landmark Attenuation):** Applies penalties scaled to landmark visibility confidence and anatomical occlusion extrapolations.
- **$u_{\text{skew}}$ (Perspective Distortion):** Foreshortening error from camera inclination angle.

Every measurement features an interactive **"How was this calculated?"** drawer showing the exact mathematical breakdown of each error vector.

### 2. Calibration Drift Detector
When measuring physical objects or people, changes in camera distance or reference object orientation invalidate the pixel scale. Vantage tracks the reference anchor's pixel dimensions over rolling time windows:
$$\Delta s = \frac{|s_{\text{current}} - s_{\text{baseline}}|}{s_{\text{baseline}}}$$
- If $\Delta s > 4\%$, a minor drift warning is issued.
- If $\Delta s > 8\%$, a critical alert halts stale measurements and prompts instant recalibration.

### 3. Transparent Explainability Overlay
Users can toggle an explainability HUD that renders:
- Individual confidence percentages for all 33 MediaPipe pose landmarks.
- Visual tagging of interpolated/occluded anatomical nodes vs. directly observed nodes.
- Kinematic skeleton connection chains and diagnostic health notes.

### 4. Session Comparison Mode
Compare two measurement sessions side-by-side:
- Computes dimensional deltas ($\Delta\text{cm}$ and $\Delta\%$).
- Identifies which session produced tighter uncertainty bounds.
- Evaluates mutual statistical significance ($|\Delta| > \sqrt{u_1^2 + u_2^2}$).
- **Real-world use cases:** Physical therapy rehabilitation tracking (joint range of motion & limb symmetry), bespoke tailoring, packaging/shipping dimensioning, and athletic posture coaching.

---

## 3. Core Capabilities & Modes

| Mode | Features |
| :--- | :--- |
| **Hand Gesture Mode (Primary)** | **Direct Hand Gesture Measurement Engine**: Real-time 21-node hand skeleton tracking supporting **Pinch Caliper** (Thumb-to-Index span gauge), **Two-Hand Air Tape Measure** (Left-to-Right Index span), and **Gesture Air-Tap** (Pinch in air to drop start/end anchor points on any object). Includes gesture controls (Peace sign ✌️ to save, Fist ✊ to reset). |
| **Human Pose Mode** | MediaPipe 33-point pose landmark extraction for Height, Shoulder Width, Arm Span, Arm Lengths, Inseams, Torso Length, plus custom point-to-point anatomical metric selector. |
| **Object Scale Mode** | Segmentation of target objects beside reference markers (A4/Credit Card), computing Width, Height, Perimeter, Surface Area ($\text{cm}^2$), and pinhole camera distance. |
| **Manual 2-Point Mode** | Click-to-calibrate reference line + click-to-measure target distance with live uncertainty readout. |
| **Photo Upload Mode** | Upload JPG, PNG, or WebP images, auto-detect biometric landmarks, and drag individual landmark handles for manual micro-adjustment. |
| **Session Comparison** | Compare before/after sessions with delta metrics and tighter uncertainty indicators. |
| **History & Reports** | Persistent session history with thumbnail preview, JSON metadata, and one-click Executive PDF report generation via ReportLab. |
| **Demo Mode** | Bundled offline scenarios (Human standing, shipping carton, precision ruler test) allowing full end-to-end demonstrations without a webcam. |
| **Dev / Debug HUD** | Live FPS counter, inference latency in ms, active vision model, video resolution, and per-landmark confidence log. |

---

## 4. System Architecture & Tech Stack

### Frontend
- **Framework:** React 19 + TypeScript + Vite
- **Styling:** Tailwind CSS (Vantage Dark Glassmorphism palette with Teal `#0d9488` and Amber `#f59e0b` accents)
- **Icons & Visuals:** Lucide React, Canvas Confetti
- **Computer Vision:** MediaPipe Tasks Vision, Canvas 2D / OpenCV-style gradient edge segmentation

### Backend
- **Framework:** Python 3.14 + FastAPI
- **Database:** SQLite (PostgreSQL compatible schema via SQLAlchemy 2.0)
- **PDF Generation Engine:** ReportLab 4.x (Executive two-column PDF reports with embedded captures, metric tables, and uncertainty disclaimers)
- **Validation:** Pydantic V2

### Directory Layout
```
vision measure/
├── backend/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py            # FastAPI endpoints & CORS
│   │   ├── database.py        # SQLAlchemy engine & session
│   │   ├── models.py          # MeasurementSession & Item models
│   │   ├── schemas.py         # Pydantic v2 schemas
│   │   └── pdf_generator.py   # ReportLab PDF report generation
│   ├── tests/
│   │   └── test_main.py       # Pytest test suite
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/        # UI Views & Interactive HUDs
│   │   ├── data/              # Demo datasets & vector scenes
│   │   ├── services/          # REST API & LocalStorage client
│   │   ├── types/             # TypeScript type definitions
│   │   ├── utils/             # Units conversion (cm, m, in, ft)
│   │   ├── vision/            # Calibration, Uncertainty & Drift Engines
│   │   ├── App.tsx            # Main application root
│   │   └── index.css          # Glassmorphism & custom Tailwind
│   ├── package.json
│   └── vite.config.ts
└── README.md
```

---

## 5. Getting Started & Installation

### Prerequisites
- Python 3.10+
- Node.js 18+ & npm

### 1. Start Backend Service
```bash
cd backend
python -m venv venv

# Windows:
.\venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

pip install -r requirements.txt

# Run Unit Tests:
pytest

# Start FastAPI server on http://localhost:8000
uvicorn app.main:app --reload --port 8000
```

### 2. Start Frontend Web Application
```bash
cd frontend
npm install

# Start Vite dev server on http://localhost:5173
npm run dev
```

Visit **`http://localhost:5173`** in your browser.

---

## 6. API Documentation

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/health` | Health check & engine status |
| `POST` | `/api/measurements` | Save a new measurement session with items & uncertainty breakdown |
| `GET` | `/api/measurements` | Retrieve list of saved sessions (paginated) |
| `GET` | `/api/measurements/{id}` | Get detailed measurement session by ID |
| `DELETE` | `/api/measurements/{id}` | Delete a measurement session |
| `GET` | `/api/measurements/{id}/pdf` | Generate and download executive PDF report |
| `POST` | `/api/measurements/preview-pdf` | Generate instant PDF preview from in-memory session |
| `POST` | `/api/compare` | Evaluate deltas, tighter uncertainty bounds, and significance between 2 sessions |

---

## 7. Metrology & Calibration Methodology

1. **Standard Reference Target Options:**
   - **A4 Paper Sheet:** $21.0 \times 29.7\text{ cm}$ (ISO 216 standard, aspect ratio $1.414$)
   - **Standard ID-1 Card:** $8.56 \times 5.398\text{ cm}$ (ISO/IEC 7810 ID-1 standard)
   - **Custom Reference Object:** User-specified dimension in cm.
2. **Scale Factor Calculation:**
   $$\text{Scale } (\text{cm/px}) = \frac{\text{Known Real Dimension (cm)}}{\text{Detected Pixel Distance (px)}}$$
3. **Calibration Quality Score ($Q$):**
   $$Q = f(\text{Spatial Resolution, Aspect Ratio Deviation, Edge Gradient})$$
   - $Q \ge 0.85 \implies \text{Excellent}$
   - $0.65 \le Q < 0.85 \implies \text{Good}$
   - $0.45 \le Q < 0.65 \implies \text{Fair}$
   - $Q < 0.45 \implies \text{Poor}$

---

## 8. Privacy & Data Protection

- **100% Client-Side Vision:** All MediaPipe neural network inferences and OpenCV edge analysis run directly in your local browser via WebAssembly (WASM) and WebGL.
- **Zero Video Streaming:** Live camera feeds never leave your device.
- **Opt-In Storage:** Frame captures are stored locally in SQLite only when explicitly saved by the user for reporting or session comparison.

---

## 9. Known Limitations & Honest Review

- **Planar Constraint:** Measurement subjects and reference objects should ideally be in the same focal plane. Depth skew is approximated, but non-planar distances in pure RGB video have higher uncertainty.
- **Lens Distortion:** Wide-angle or fisheye webcams without intrinsic camera matrix calibration will experience radial edge distortion.
- **Lighting Sensitivity:** Low-contrast scenes or harsh backlighting may degrade sub-pixel edge detection.

---

## 10. Future Enhancements & Roadmap

1. **AR / WebXR Depth Estimation:** Leverage WebXR Depth Sensing API for mobile devices with ToF/LiDAR sensors.
2. **Multi-View 3D Point Cloud Reconstruction:** Synthesize multiple calibrated camera snapshots into a 3D dense mesh.
3. **Room & Furniture CAD Dimensioning:** Automated bounding plane extraction for interior design.
4. **Cloud Sync & Multi-Device Workspace:** Optional encrypted cloud synchronization for enterprise logistics workflows.

---

## License
MIT License. Built with precision for the **Vantage AI Measurement Platform**.
