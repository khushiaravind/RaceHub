# RaceHub — AI Pit Wall & F1 Telemetry Terminal

**RaceHub** is a live, AI-powered Formula 1 race engineering workstation. It simulates a real pit wall — streaming telemetry, tire strategy, race predictions, and an AI race engineer you can talk to mid-session — all in a single glass-cockpit dashboard.

Built for the **Google AI Studio 5-Day Intensive (Kaggle)** capstone project.

**Live demo:** [racehub-839328821518.europe-west3.run.app](https://racehub-839328821518.europe-west3.run.app)


---

## What it does

RaceHub puts you on the pit wall with a full simulated race session:

- **AI Race Engineer** — Chat live with an AI engineer powered by **Gemini**, grounded in real-time telemetry (speed, RPM, gear, DRS, tire compound/age, fuel load, sector splits) *and* a running log of everything that's happened in the session so far (pit calls, safety cars, strategy shifts). Ask it something mid-race and it answers with the actual context, not just the current snapshot. Responses are styled like authentic F1 radio comms — short, analytical, in-character.
- **Track-aware telemetry simulation** — Speed, braking zones, gear, and DRS availability aren't random noise; they're generated per corner based on the selected circuit's real characteristics (Monaco's tight, low-speed corners vs. Monza's flat-out chicanes vs. Spa's Eau Rouge/Bus Stop contrast, etc).
- **Live Telemetry Dashboard** — Streaming speed, throttle, brake, RPM, and lap-delta traces rendered with Recharts, tied to the car's position on the current lap.
- **Tire Strategy Center** — Tracks compound, tire age, wear, and pit window, and runs strategy simulations.
- **Race Prediction Engine** — Live win / podium / top-5 probability model.
- **Interactive Track Map** & **Driver Center** — Pick your driver, team, and circuit, with head-to-head comparison mode; car position on the map moves in sync with the telemetry.
- **Pit Wall Actions** — Trigger Safety Car and Red Flag conditions and watch telemetry react in real time (reduced speeds, neutralized DRS, etc), each logged as a session event the AI engineer can reference later.
- **Insights Feed** — A scrolling log of strategy calls and system events, like a live team radio/timing feed.
- **Graceful degradation** — If no Gemini API key is configured, the app falls back to a local "Pit Wall Simulation" mode instead of breaking, so the UI is always demoable.

## Tech stack

- **Frontend:** React 19 + TypeScript, Vite, Tailwind CSS v4, Recharts, Framer Motion (`motion`), Lucide icons
- **Backend:** Express server (`server.ts`) proxying requests to the **Gemini API** (`@google/genai`) via a `/api/engineer/chat` endpoint
- **AI:** Gemini, prompted with a custom system instruction and live telemetry context to role-play an F1 race engineer
- **Deployment:** Google Cloud Run
- **Built with:** Google AI Studio

## Running locally

**Prerequisites:** Node.js

```bash
npm install
```

Set your `GEMINI_API_KEY` in `.env.local`:

```
GEMINI_API_KEY=your_key_here
```

Then run:

```bash
npm run dev
```

## Project structure

```
src/
├── App.tsx                     # Session state, per-track telemetry physics, session event log, layout
├── components/
│   ├── AIEngineerPanel.tsx     # Gemini-powered chat interface
│   ├── TelemetryDashboard.tsx  # Live speed/throttle/RPM charts
│   ├── StrategyCenter.tsx      # Tire strategy + pit window logic
│   ├── RacePredictionEngine.tsx# Win/podium probability model
│   ├── TrackMap.tsx            # Circuit selector + map
│   ├── DriverCenter.tsx        # Driver/team selection + comparison
│   └── InsightsFeed.tsx        # Live strategy/system log
└── types.ts                    # Drivers, teams, tracks, telemetry types
server.ts                       # Express + Gemini API proxy
```

## Notes

The chat endpoint doesn't hard-fail if `GEMINI_API_KEY` is missing — it returns a scripted "local simulation" response instead, so the UI never breaks in a demo even without a key configured.

The AI engineer's context isn't just the current telemetry snapshot — every strategy call, safety car, and red flag gets pushed to a `sessionHistoryEvents` array that's sent along with each chat request, so the model can reason about what already happened this session instead of only the instant it's asked about.

---

Built solo as a capstone for the Google/Kaggle 5-Day AI Intensive — an exercise in building a context-grounded AI app with Gemini and AI Studio.
