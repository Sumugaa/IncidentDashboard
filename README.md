# Incident Dashboard

A small web app that shows support incidents in one place. A FastAPI backend serves incident data, and a React (Vite) dashboard lets you view, filter, create and manage them.

## Features

- Incident table with title, severity, status, service and created time
- Filter by status and severity using dropdowns
- Severity colour coding: P1 red, P2 amber, P3 grey
- Acknowledge / Resolve buttons that call the API and refresh the view
- Create new incidents from a form (title, service, severity)
- Click an incident title to open a detail panel (fetched by id)
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

### 2.
