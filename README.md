# Incident Dashboard

A small web app that shows support incidents in one place. A FastAPI backend serves incident data, and a React (Vite) dashboard lets you view, filter and manage them.

## Features

- Incident table with title, severity, status, service and created time
- Filter by status and severity using dropdowns
- Severity colour coding: P1 red, P2 amber, P3 grey
- Acknowledge / Resolve buttons that call the API and refresh the view
- Summary strip powered by `/stats` (open incidents per severity, total resolved)
- 14 sample incidents seeded on startup

## Tech stack

- **Backend:** Python, FastAPI, Uvicorn, in-memory storage (a Python dict)
- **Frontend:** React, Vite, plain CSS

## Project structure

```
incident-dashboard/
├── backend/
│   └── main.py
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── App.css
│   │   └── main.jsx
│   └── package.json
└── README.md
```

## Prerequisites

- Python 3.10 or newer
- Node.js 18 or newer

## Running the app

The app is two servers, so you need two terminals running at the same time.

### 1. Backend (port 8000)

```powershell
cd backend
python -m venv venv
venv\Scripts\Activate.ps1          # macOS/Linux: source venv/bin/activate
pip install fastapi "uvicorn[standard]"
uvicorn main:app --reload
```

The API runs at http://localhost:8000. Interactive API docs are at http://localhost:8000/docs.

### 2. Frontend (port 5173)

In a second terminal:

```powershell
cd frontend
npm install
npm run dev
```

Open http://localhost:5173 in your browser.

> The frontend must run on port 5173, because the backend only allows requests from that origin (CORS). If another process is using 5173, Vite will pick a different port and the dashboard won't be able to load data. Stop the other process first.

## API endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/incidents` | List incidents. Optional filters: `?status=OPEN`, `?severity=P1` |
| GET | `/incidents/{id}` | Get one incident (404 if not found) |
| POST | `/incidents` | Create an incident (severity and status are validated) |
| PATCH | `/incidents/{id}/status` | Update an incident's status |
| GET | `/stats` | Open incidents per severity and total resolved |

Allowed values:

- **severity:** `P1`, `P2`, `P3`
- **status:** `OPEN`, `ACKNOWLEDGED`, `RESOLVED`

Invalid values return a `422` validation error.

### Example incident

```json
{
  "id": 1,
  "title": "Checkout API high response time",
  "severity": "P1",
  "status": "OPEN",
  "service": "checkout-api",
  "created_at": "2026-09-28T10:00:00Z"
}
```

### Example requests

```bash
# List open P1 incidents
curl "http://localhost:8000/incidents?status=OPEN&severity=P1"

# Acknowledge incident 1
curl -X PATCH http://localhost:8000/incidents/1/status \
  -H "Content-Type: application/json" \
  -d '{"status": "ACKNOWLEDGED"}'

# Stats
curl http://localhost:8000/stats
```

## Design notes

- **In-memory storage:** data lives in a Python dict and is reset to the 14 seeded incidents whenever the backend restarts. This keeps setup simple. Swapping in SQLite would only change the backend, not the frontend.
- **Filtering happens on the server:** the dropdowns send `status` and `severity` as query parameters to `GET /incidents`.
- **Refresh after changes:** after Acknowledge or Resolve succeeds, the frontend re-fetches both incidents and stats so the table and summary strip stay in sync.
- **Validation:** `Severity` and `Status` are enums, so FastAPI rejects invalid values automatically.
