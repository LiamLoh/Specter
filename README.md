<div align="center">

# Specter

### The Sentient Mystery Shopper that sees, feels, and heals your conversion funnel

An autonomous AI agent that tests signup flows like a real human: it navigates by vision, role-plays user personas, measures friction, diagnoses root causes, and proposes code fixes.

![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?logo=tailwindcss&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-backend-009688?logo=fastapi&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![Playwright](https://img.shields.io/badge/Playwright-automation-2EAD33?logo=playwright&logoColor=white)
![Claude](https://img.shields.io/badge/Claude-Vision-D97757?logo=anthropic&logoColor=white)

</div>

---

## Overview

Broken or confusing signup flows silently cost businesses users and revenue, and traditional end-to-end tests only catch what someone thought to script. **Specter** replaces scripted tests with an AI "mystery shopper" that:

1. **Sees** the page through screenshots and the DOM, deciding each action autonomously with no hard-coded selectors.
2. **Feels** friction the way a real person would, by simulating personas, devices, and network conditions and scoring the experience.
3. **Heals** the funnel by diagnosing root causes, routing issues to the right team, and generating suggested code fixes as GitHub pull requests.

## Key Features

### Multimodal persona navigator
- Vision-based navigation powered by a multimodal LLM (Claude, with NVIDIA NIM and Gemini fallbacks)
- Five behavioural personas, from speed-runners to privacy skeptics to "chaos monkeys"
- Device emulation (iPhone, Android, desktop), network throttling (3G to Wi-Fi), and locale switching
- Accessibility checks for touch-target size, contrast, and text legibility

### Cognitive UX analysis and diagnosis
- **F-Score**: a 0–100 friction metric combining console errors, dwell time, and expectation mismatch
- **AI uncertainty heatmaps** and **Ghost Replay** GIFs that animate where users got stuck
- **P0–P3 severity** classification with automatic routing to Backend, Frontend, Design, or QA
- Root-cause analysis with recurring-pattern detection across runs

### Escalation and self-healing
- Slack escalation with PDF incident reports sent to the responsible team's channel
- **Specter Healer**: AI-generated fixes opened as GitHub pull requests
- **Neural Monologue**: live narration of the agent's reasoning with low-latency on-device TTS (Kokoro-ONNX)

### Dashboard

| Page | Purpose |
|---|---|
| **Lab** | Launch a test run and watch the agent live: device emulator, ghost cursor, neural monologue |
| **Vault** | Browse past runs and incidents with screenshots, heatmaps, and replays |
| **Command Center** | Funnel health overview: F-Score trends, revenue impact, root causes, and healing hub |

## Architecture

```
┌──────────────── Next.js dashboard ────────────────┐        ┌────────────── FastAPI backend ──────────────┐
│  Lab · Vault · Command Center · Specter Healer    │◄──WS──►│  api_server.py                               │
└───────────────────────────────────────────────────┘  REST  │   ├─ Planner brain   (main.py)               │
                                                             │   │    persona-driven vision navigation       │
                                                             │   ├─ Doctor brain    (diagnosis_doctor.py)    │
            Target website ◄── Playwright ──────────────────┤   │    per-step verification + diagnosis      │
                                                             │   ├─ Expectation engine (F-Score, heatmaps)  │
                                                             │   ├─ Root-cause intelligence                 │
                                                             │   └─ Escalation (Slack + PDF) · TTS          │
                                                             └──────────────────────────────────────────────┘
```

| Layer | Technology |
|---|---|
| Frontend | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS 4, shadcn/ui, Radix, Framer Motion |
| Backend | FastAPI, Uvicorn, WebSockets |
| Browser automation | Playwright with a custom crawler and action executor (`webqa_agent/`) |
| AI | Anthropic Claude (vision + reasoning), NVIDIA NIM Llama 3.2 Vision, Google Gemini |
| Analysis | NumPy, SciPy, scikit-image, OpenCV, sentence-transformers |
| Reporting | ReportLab (PDF), Slack SDK, Octokit (GitHub PRs), Kokoro-ONNX (TTS) |

## Getting Started

### Prerequisites

- Node.js 20+
- Python 3.10+
- An Anthropic API key (other providers are optional)

### 1. Clone and install

```bash
git clone https://github.com/LiamLoh/Specter.git
cd Specter

# Frontend
npm install

# Backend
pip install -r requirements.txt
pip install playwright
playwright install chromium
```

### 2. Configure environment variables

```bash
cp .env.example backend/.env       # backend keys (Claude, Slack, NVIDIA, Gemini)
cp .env.example .env.local         # frontend keys (GitHub, NVIDIA) for Specter Healer
```

Then fill in the values you need. See [Configuration](#configuration) below.

### 3. (Optional) Enable voice narration

Download `kokoro-v1.0.onnx` and `voices-v1.0.bin` from the [kokoro-onnx releases](https://github.com/thewh1teagle/kokoro-onnx/releases/tag/model-files) and place them in a `models/` folder at the project root. Without them, Specter runs normally with TTS disabled.

### 4. Run

```bash
npm run run
```

This starts the FastAPI backend on **http://localhost:8000** and the dashboard on **http://localhost:3000**. To run them separately, use `python run_dev.py` and `npm run dev` in two terminals.

> On Windows, the backend runs without auto-reload for Playwright compatibility. Restart it after backend code changes.

### Command-line usage

Run the agent directly against any signup page:

```bash
python main.py https://example.com/signup --persona boomer --device iphone13 --network 3g
```

| Option | Values | Default |
|---|---|---|
| `--persona` | `zoomer` (speedster), `boomer` (anxious first-timer), `skeptic` (privacy-focused), `chaos` (tries to break the form), `mobile` (small screen, fat fingers) | `zoomer` |
| `--device` | `iphone13`, `android`, `desktop` | `desktop` |
| `--network` | `wifi`, `4g`, `3g`, `slow` | `wifi` |
| `--locale` | Any locale code, e.g. `en-US`, `es-ES` | `en-US` |
| `--max-steps` | Maximum agent actions per run | `20` |

## Configuration

| Variable | Location | Required | Purpose |
|---|---|---|---|
| `CLAUDE_API_KEY` | `backend/.env` | Yes | Vision navigation and diagnosis |
| `NVIDIA_API_KEY` | both | No | NVIDIA NIM vision model and Healer fixes |
| `GEMINI_API_KEY` | `backend/.env` | No | Fallback model |
| `SLACK_BOT_TOKEN`, `SLACK_CHANNEL_ID` | `backend/.env` | No | Slack escalation |
| `SLACK_{BACKEND,FRONTEND,DESIGN,QA}_CHANNEL` | `backend/.env` | No | Per-team routing channels |
| `GITHUB_TOKEN`, `GITHUB_OWNER`, `GITHUB_REPO` | `.env.local` | No | Healer pull requests (mocked if missing) |

> Never commit `.env` or `.env.local`. Both are excluded by `.gitignore`.

## How the F-Score Works

```
F-Score = Console entropy    (0–25)   JS errors and warnings
        + Dwell time         (0–40)   waiting beyond 5 seconds
        + Semantic distance  (0–25)   gap between expected and actual outcome
        + 15 if any server (5xx) error occurred     (capped at 100)
```

| Severity | Meaning | Trigger |
|---|---|---|
| **P0** Critical | Signup blocked | F-Score > 85 or server (5xx) errors |
| **P1** Major | High drop-off risk | F-Score > 70 or high confusion |
| **P2** Minor | Degraded experience | F-Score 50–70 |
| **P3** Cosmetic | Minor UI issues | F-Score < 50 |

Heatmaps and Ghost Replays are generated for failing steps (F-Score > 50).

## API Overview

| Area | Endpoints |
|---|---|
| Health | `GET /api/health` |
| Test runs | `POST /api/test/start`, `GET /api/test/{id}`, `WS /ws/live/{id}` |
| Reports | `GET /api/reports/list`, `GET /api/reports/details/{id}` |
| Incidents | `GET /api/incidents`, `GET /api/incidents/{id}/root-cause`, `GET /api/root-cause/patterns` |
| Dashboard | `GET /api/dashboard/stats` |
| Diagnosis and alerts | `POST /api/diagnose`, `POST /api/alert` |
| Healing | `GET /api/healing/suggestions`, `POST /api/ai/fix-code` |
| Voice | `GET /api/tts/status`, `POST /api/tts` |

Interactive docs are available at **http://localhost:8000/docs** while the backend is running.

## Project Structure

```
Specter/
├── app/                    Next.js routes: landing, lab, vault, command, mock-target, API
├── components/             UI components (lab, vault, command, landing, shadcn/ui)
├── lib/                    Frontend utilities, TTS hook, Healer scenarios
├── backend/                Diagnosis, F-Score engine, root-cause, escalation, TTS, PDF reports
├── webqa_agent/            Playwright browser sessions, crawler, and action execution
├── config/                 Example run configurations
├── api_server.py           FastAPI application
├── main.py                 Autonomous agent (CLI entry point)
└── run_dev.py              Development server launcher
```

## Team

- [@hmhhmm](https://github.com/hmhhmm)
- [@johnong04](https://github.com/johnong04)
- Loh Yong Qiao ([@LiamLoh](https://github.com/LiamLoh))
- Felicia Sia Xin Rou
- Jin Wei
