# Flow Music Automation - Webhook API Integration Guide

> Audience: developers, technical integrators, and AI agents.
> Goal: everything needed to drive music generation over the local Webhook REST
> API - endpoints, auth, request/response schemas, parameters, error handling,
> and the async (submit → poll → download) workflow.

This document describes the API exposed by the **Webhook API** tab of the Flow
Music Automation desktop app. It lets external tools (n8n, Make.com, custom
scripts, AI agents, Story Machine) generate music over a simple REST API.

---

## 1. Overview & key concepts

- **Bind address (default loopback).** The API runs on `http://<host>:<port>`.
  By default `host` is `127.0.0.1` (this machine only). In the Webhook tab's
  **IP** field you can enter `0.0.0.0` (all interfaces) or this machine's LAN IP
  so other machines on your network can reach it. For LAN access, also allow the
  port through the OS firewall. For internet access you need your own tunnel /
  reverse proxy.
- **Default port:** `8799` (configurable in the Webhook tab).
- **You must start the server.** Open the app → **Webhook API** tab → **Server
  Control** → **Start Server**. IP / Port are locked while it runs. Tick **Auto-start
  server when app launches** to have it start by itself every time the app opens.
  The **API Key** block shows the key (**Generate** makes a new one at once - the old
  key stops working - and **Copy** copies it).
- **Result storage.** Webhook results are saved in their own folder (the
  **Result storage** block): `<save folder>/music`, default
  `Music/Flow Music Automation/webhook/music`. The block shows disk usage, can
  auto-delete files older than N days (checked when the app opens and every 6 hours)
  and has a **Clean up** button. Clean-up only deletes inside the `music` subfolder
  and is refused while a webhook task is running; links of deleted files return 404.
- **A valid Flow Music Automation license is required** for generate requests to
  run. `GET /api/health` responds regardless; `POST /api/music/generate` returns
  `403 LICENSE_REQUIRED` when you are signed out or expired.
- **Generation runs on the app's logged-in Flow Music accounts.** The webhook
  uses the same account pool and the same thread settings ("Total threads" /
  "Threads per account") as the Create tab. If no enabled account is available,
  the task fails with `NO_ACCOUNT`.
- **Asynchronous by design.** Generation takes time, so the API is
  **submit → poll → download**:
  1. `POST /api/music/generate` → get a `task_id` immediately (HTTP `202`).
  2. `GET /api/status/{task_id}` repeatedly until `status` is `completed` or `failed`.
  3. On `completed`, download the files from the returned `results` URLs.
- **Each request = one prompt = one task** that produces **2 takes** (Flow Music
  returns 2 songs per prompt), so `results` usually has 2 audio URLs.
- **Webhook tasks only.** The API key sees and stops only tasks created through the
  webhook; songs queued from the app's Create tab are never visible here. A webhook task
  runs right away even if the user paused the app's own queue (only that task runs).
- **App restarts.** If the app is closed while a webhook task is still waiting, that task
  is canceled when the app opens again (`status: "failed"`, `error: "CANCELED"`): submit
  it again.

---

## 2. Authentication

All endpoints **except** `GET /api/health` and `GET /api/files/{filename}` require
the API key in a header:

```
X-API-Key: <your-api-key>
```

A missing/invalid key returns `401`. The key is shown in the Webhook tab; press
**Generate** to rotate it (the old one stops working immediately).

---

## 3. Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `GET`  | `/api/health` | ❌ | Server health / uptime / queue counts |
| `POST` | `/api/music/generate` | ✅ | Queue a music generation task |
| `GET`  | `/api/status/{task_id}` | ✅ | Poll task status; returns result or error |
| `GET`  | `/api/result/{task_id}` | ✅ | Get result (only once `completed`) |
| `GET`  | `/api/tasks` | ✅ | List the 50 most recent webhook tasks |
| `GET`  | `/api/files/{filename}` | ❌ | Download a generated audio/cover file |
| `POST` | `/api/stop` | ✅ | Stop every pending/running task - or one: `/api/stop/{task_id}` or body `{"task_id":"…"}` |

Trailing slashes are tolerated (e.g. `/api/health/`). CORS is open (`*`) so
browser-based tools work.

---

## 4. The async workflow

### Step 1 - Submit

```
POST /api/music/generate
X-API-Key: <key>
Content-Type: application/json
```

```json
{
  "prompt": "calm ambient piano, instrumental, slow tempo",
  "model": "Lyria 3.5",
  "ghostwriter": "standard",
  "producer": "standard",
  "task": "n8n batch"
}
```

Only `prompt` is required. `model` / `ghostwriter` / `producer` fall back to the
app's current defaults when omitted. `task` is an optional label (shown in the
app's queue). Response:

```json
{
  "task_id": "g1760000000001",
  "status": "pending",
  "message": "Music task queued",
  "poll_url": "/api/status/g1760000000001"
}
```

### Step 2 - Poll status

`GET /api/status/{task_id}` with the `X-API-Key` header. `status` is one of
`pending`, `running`, `completed`, `failed` (or `expired` if the task aged out of
history).

Running:

```json
{ "task_id": "g176…", "type": "music", "status": "running", "progress": 58, "prompt": "…" }
```

Completed:

```json
{
  "task_id": "g176…",
  "type": "music",
  "status": "completed",
  "results": [
    "http://127.0.0.1:8799/api/files/Calm%20Piano_1a2b3c.m4a",
    "http://127.0.0.1:8799/api/files/Calm%20Piano_4d5e6f.m4a"
  ],
  "titles": ["Calm Piano", "Calm Piano"],
  "completed_at": 1760000000123
}
```

Failed:

```json
{ "task_id": "g176…", "type": "music", "status": "failed", "error": "…reason…" }
```

### Step 3 - Download results

`results` is an array of URLs like `http://<host>:<port>/api/files/<urlencoded-name>`.
Download them directly (no API key needed for `/api/files/`). The file format
(M4A / WAV / MP3) follows the app's Settings → download format.

---

## 5. Request body schema - `POST /api/music/generate`

| Field | Type | Required | Default | Notes |
|-------|------|----------|---------|-------|
| `prompt` | string | ✅ | - | Up to 5000 chars |
| `model` | string | ❌ | app default | e.g. `"Lyria 3.5"`, `"Lyria 3 Pro"` |
| `ghostwriter` | string | ❌ | `"standard"` | `"standard"` or `"pro"` |
| `producer` | string | ❌ | `"standard"` | `"standard"` or `"tiny"` (fast) |
| `timeoutSec` | number | ❌ | app default | Per-phase timeout in seconds |
| `task` | string | ❌ | `"Webhook"` | Label shown in the app queue |

Valid values for `model` are whatever the Create tab offers for your account.

---

## 6. Error handling

| HTTP | Meaning |
|------|---------|
| `400` | Bad request (missing/invalid `prompt`, non-JSON body) |
| `401` | Missing/invalid API key |
| `403` | `LICENSE_REQUIRED` - sign in or renew |
| `400` | `NO_ACCOUNT` - no enabled Flow Music account |
| `404` | Task or file not found |
| `413` | Body too large (> 1 MB) |

A task that fails during generation returns `status: "failed"` with an `error`
string from `GET /api/status/{task_id}`.

---

## 7. Quick example (bash)

```bash
BASE=http://127.0.0.1:8799
KEY=YOUR_API_KEY

# submit
TID=$(curl -s -X POST $BASE/api/music/generate \
  -H "X-API-Key: $KEY" -H "Content-Type: application/json" \
  -d '{"prompt":"upbeat lofi hip hop, 80 bpm, jazzy chords"}' | jq -r .task_id)

# poll until done
while :; do
  S=$(curl -s $BASE/api/status/$TID -H "X-API-Key: $KEY")
  echo "$S" | jq -r .status | grep -qE 'completed|failed' && { echo "$S" | jq .; break; }
  sleep 5
done
```
