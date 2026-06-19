# AI Resume & Portfolio Builder

A full-stack web app that helps students build a resume, cover letter, and personal portfolio website — and uses **Grok (xAI)** to tailor the content to a specific job description.

Fill in your profile once (education, experience, projects, skills), then let AI rewrite your bullet points using the STAR method, draft a custom cover letter, or write a punchy portfolio bio — all previewed live and exportable as print-ready HTML/PDF or a standalone portfolio site.

---

## Features

- **Profile workspace** — tabbed editor for Personal Info, Education, Experience, Projects, and Skills
- **Grok AI tailoring hub**
  - Rewrites resume bullets (STAR method, action verbs, job-description keywords)
  - Generates a full cover letter from your profile + target job
  - Drafts a 3-sentence portfolio bio
  - Falls back to mocked `[MOCK]` output when no API key is configured, so the UI is fully demoable offline
- **Live preview** with three modes:
  - **Resume** — 3 templates (Modern Executive, Minimalist Sans, Classic Serif), print/PDF export
  - **Cover Letter** — 2 templates (Modern Minimal, Classic Serif), print/PDF export
  - **Portfolio Web** — 3 themes (Midnight Indigo, Sleek Emerald, Light Minimalist), desktop/mobile preview, exportable as a single downloadable `.html` file
- **Settings panel** — store your own Grok API key locally, export/import your full profile as JSON, reset profile
- **Auto-save** — profile changes are debounced and persisted to the backend automatically

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 19, Vite, lucide-react (icons) |
| Backend | Node.js, Express 4 |
| AI | xAI Grok API (`grok-2-1212`) |
| Storage | Flat-file JSON store (`backend/db.json`) — no database required |

---

## Project Structure

```
Trial/
├── backend/
│   ├── server.js          # Express API server + Grok integration
│   ├── db.js               # Simple JSON file read/write helpers
│   ├── db.json              # Persisted profile data
│   └── package.json
└── frontend/
    ├── src/
    │   ├── App.jsx                  # Root component, state, AI calls, auto-save
    │   ├── components/
    │   │   ├── ProfileForm.jsx       # Tabbed profile editor + AI tailoring hub
    │   │   ├── LivePreview.jsx       # Resume / cover letter / portfolio preview & export
    │   │   └── SettingsModal.jsx     # API key, backup import/export, reset
    │   ├── utils/
    │   │   └── templates.js          # Resume, cover letter, and portfolio HTML templates
    │   └── main.jsx
    ├── vite.config.js        # Dev proxy: /api -> http://localhost:5000
    └── package.json
```

---

## Prerequisites

- Node.js (v18+ recommended)
- An [xAI Grok API key](https://x.ai/) (optional — the app works in mock mode without one)

---

## Setup & Installation

### 1. Backend

```bash
cd backend
npm install
```

Create a `.env` file in `backend/` if you want server-side AI generation enabled for all users:

```env
PORT=5000
GROK_API_KEY=xai-your-key-here
```

Start the server:

```bash
npm start
```

The API will run at `http://localhost:5000`.

### 2. Frontend

```bash
cd frontend
npm install
npm run dev
```

The dev server runs at `http://localhost:5173` and proxies `/api` requests to the backend (configured in `vite.config.js`).

> Alternatively, you can paste your own Grok API key into the in-app **Settings** modal — it's stored in `localStorage` and sent per-request, so no `.env` is required to try the AI features.

### 3. Production Build

```bash
cd frontend
npm run build
```

The Express server already serves `frontend/dist` as static files (see `server.js`), so after building you can run just the backend (`npm start` in `backend/`) to serve the whole app from `http://localhost:5000`.

---

## API Reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/profile` | Fetch the saved profile |
| `POST` | `/api/profile` | Save/merge profile data |
| `POST` | `/api/ai/tailor` | Rewrite experience bullets for a target role/job description |
| `POST` | `/api/ai/cover-letter` | Generate a tailored cover letter |
| `POST` | `/api/ai/portfolio-bio` | Generate a short portfolio bio |

All `/api/ai/*` routes accept an optional `x-grok-api-key` header. If omitted, the server falls back to `GROK_API_KEY` from `.env`; if neither is present, a mocked response is returned.

---

## Known Issues

- **`App.jsx` has a JSX syntax error**: the `<LivePreview ... />` element (around line 309) is missing its closing `/>` before `</main>`, which will currently fail to compile. Add the closing tag to fix it:

  ```jsx
  <LivePreview
    profile={profile}
    bulletOverrides={bulletOverrides}
    coverLetterText={profile.coverLetter}
    portfolioTheme={portfolioTheme}
    setPortfolioTheme={setPortfolioTheme}
    portfolioBio={profile.portfolioBio}
  />
  ```

- `backend/db.json` ships with sample personal data already populated — clear it out (or use **Reset Current Profile** in Settings) before sharing/deploying.

---

## Author

Created by **Dharmik Vyas**
