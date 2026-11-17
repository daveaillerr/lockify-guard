<div align="center">

<h1>
  <img src="assets/icon-128.png" width="32" height="32" align="absmiddle" alt="Lockify Guard Icon" />
  &nbsp;Lockify Guard&nbsp;
  <img src="assets/icon-128.png" width="32" height="32" align="absmiddle" alt="Lockify Guard Icon" />
</h1>

![alt text](image.png)

**Extension-first deep work and certification tracking platform**

A privacy-first browser extension and companion web cockpit that captures objective focus telemetry automatically and visualizes study consistency without manual logging.

[![Repository](https://img.shields.io/badge/Repository-Lockify%20Guard-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/daveaillerr/lockify-guard)
[![Chrome](https://img.shields.io/badge/Chrome-MV3-0666FF?style=for-the-badge&logo=googlechrome&logoColor=white)](https://developer.chrome.com/docs/extensions/mv3/)
[![Privacy](https://img.shields.io/badge/Privacy-Zero%20PII-00D4FF?style=for-the-badge&logo=shield&logoColor=white)](https://github.com/daveaillerr/lockify-guard/blob/main/docs/privacy/decision-log.md)
[![Backend](https://img.shields.io/badge/Backend-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Frontend](https://img.shields.io/badge/Frontend-Astro-FF5D01?style=for-the-badge&logo=astrodotcom&logoColor=white)](https://astro.build/)

</div>

---

> Lockify Guard is a browser-extension-first deep work and certification tracking platform that collects objective productivity telemetry automatically and presents deterministic focus analytics through a companion web cockpit.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Overview

Lockify Guard replaces manual study logging with automatically collected focus telemetry, deterministic scoring, and web-based analytics. The browser extension acts as the primary telemetry collector and enforcement engine, the FastAPI backend computes deterministic scores and trend velocity at the production backend (`https://lockify-guard.vercel.app`), and the Astro web cockpit renders the resulting analytics.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Core Features

Lockify Guard is deliberately scoped to six core feature areas.

| Feature | Description |
|---------|-------------|
| Focus Session Management | Explicit start, pause, resume, and stop controls with interruption-resilient state persistence. |
| Pomodoro and Scheduled Breaks | Configurable focus and break intervals with automated phase transitions. |
| Distraction Blocking | Native declarative network interception with a calm distraction redirect screen. |
| Automatic Productivity Tracking | Context switch, blur, fullscreen exit, and idle drift sensing with deterministic focus scoring. |
| Focus Analytics | Net focus minutes, flow stability index, streak health, and velocity trajectory visualization. |
| Certification Tracking | Automatic telemetry association with certification roadmaps and user-confirmed milestones. |

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> How It Works

1. The Chrome MV3 extension service worker and content script collect objective telemetry: tab switches, window blurs, fullscreen exits, idle drift, pauses, and blocked-site hits.
2. The FastAPI backend computes deterministic focus scores and trend velocity.
3. The web cockpit renders analytics from local-first storage, with a client-side scoring fallback when the backend is unavailable.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Tech Stack

| Layer | Technology |
|-------|------------|
| Browser Extension | Chrome MV3 (service worker + content script + declarativeNetRequest) |
| Web Frontend | Astro + TypeScript + Tailwind |
| Backend | FastAPI + Uvicorn + Pydantic |
| Deployment | Vercel (backend serverless function, web deployment) |

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Getting Started

### Installation & Setup

1. **Download Extension ZIP** (`v2.0.0 · Manifest V3`)
   - Download the official **Lockify Focus Guard** extension archive and extract it anywhere on your computer: [**Download Extension (ZIP)**](#) *(replace `#` with your release/ZIP link)*
2. **Open Extensions Page**
   - Open your browser and navigate to `chrome://extensions`
3. **Enable Developer Mode**
   - Toggle the switch labeled **Developer mode** in the top-right corner of the extensions manager.
4. **Load Unpacked Folder**
   - Click **Load unpacked** and select the unzipped extension folder on your device. Pin **Lockify** to your browser toolbar to start focusing immediately.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Privacy

Lockify Guard implements privacy by design with a zero Personally Identifiable Information posture, pseudonymous device identity, and local-first data persistence.

- The extension reads only hostnames to detect course sites; it never reads or stores full URLs, page titles, content, or queries.
- Telemetry is stored locally (browser storage / IndexedDB) and transmitted pseudonymous outbox records only.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Troubleshooting

### `Uncaught Error: Extension context invalidated`

Content scripts can outlive an extension reload or update without the host tab being refreshed. All `chrome.runtime.sendMessage` call sites are wrapped in `try/catch` with promise `.catch()` handlers, so orphaned contexts now fail silently instead of throwing uncaught errors.

If the error still appears, reload the extension at `chrome://extensions` and reopen the affected course tab.

### Course Site Detection Popup Not Appearing

If the "Ready to lock in?" popup does not show up on course pages (e.g. Coursera or Udemy), ensure that course platform domains are routed to the course site experience (`initCourse()`) rather than the web cockpit bridge (`initBridge()`). The web bridge role should be reserved strictly for the cockpit application.

### Backend Root Returns `{"detail": "Not Found"}`

FastAPI returns a 404 response on any path that lacks a registered route handler. Defining an explicit root endpoint (`@app.get("/")`) ensures that direct visits to the API root show the service name, online status, and pointers to documentation (`/docs`) and health checks (`/health`).

### Session Telemetry Not Syncing to Web Cockpit After Deployment

If completed study session records do not appear in the web dashboard:
1. Verify that your deployed web domain is included in `content_scripts.matches` in `manifest.json`.
2. Confirm the origin is allowlisted in the extension background worker and content bridge origin checks.
3. Ensure the web application origin is permitted in backend CORS configuration (`ALLOWED_ORIGINS`).
4. Ensure the web client's backend base URL defaults to the deployed HTTPS backend rather than a local loopback IP to avoid mixed-content blocking.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> Learnings

- **Strict Content Script Role Separation:** In content scripts serving dual purposes (acting as a communication bridge on your app vs. displaying study overlays on external learning platforms), keep origin checks strictly partitioned so learning platforms are never misidentified as the dashboard application.
- **Cross-Component Origin Consistency:** Browser extensions enforcing origin boundaries require exact domain matching across `manifest.json`, background service worker sender validations, and backend CORS policies.
- **Defensive Messaging Guards:** Wrap all `chrome.runtime` messaging in `try/catch` with promise `.catch()` handlers to smoothly handle extension updates and lifecycle transitions.
- **Production Fallback Defaults:** Client-side network helpers should default to production HTTPS endpoints to maintain zero-config functionality when environment variables are omitted during deployment.
- **Timer Integrity Under Service Worker Suspension:** MV3 background workers suspend when idle. Compute remaining countdown times strictly from absolute timestamps (`phaseEndsAt - Date.now()`) rather than interval-based loops.

## <img src="assets/icon-128.png" width="24" height="24" align="absmiddle" alt="" /> License

This project does not currently include an open-source license file. All rights are reserved by the repository owner unless a license is added in the future.