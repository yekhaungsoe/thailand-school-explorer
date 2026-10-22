# School Directory and Location Mapping

A web programming project built with **Vue.js**, **FastAPI**, and **Leaflet**. It displays school information and shows each school's location on an interactive map using OpenStreetMap.

## Features

- Browse school information and details.
- Retrieve school latitude and longitude through a FastAPI endpoint.
- Display school locations on an interactive map.
- Use Leaflet with OpenStreetMap tiles.
- Handle schools with missing or invalid location coordinates.
- Return appropriate API errors when a school is not found or its location is unavailable.

## Technologies Used

- **Frontend:** Vue.js, Vite, Leaflet
- **Backend:** Python, FastAPI
- **Database:** SQLite
- **Map tiles:** OpenStreetMap

## Project Structure

```text
thailand-school-explorer/
├── backend/
│   ├── main.py
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   └── SchoolLocationMap.vue
│   │   ├── views/
│   │   │   └── SchoolDetailView.vue
│   │   └── config.js
│   ├── package.json
│   └── package-lock.json
├── school_directory.db
└── README.md
```

*Note: The exact files and folders may vary slightly depending on the project version.*

## Requirements

Install the following before running the project:

- Python
- Node.js and npm

The `node_modules` and Python virtual environment folders are not included. They will be created during setup.

## How to Run

### 1. Start the Backend

Open a terminal in the `backend` directory.

Create and activate a Python virtual environment on Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

Start the FastAPI server:

```bash
uvicorn main:app --reload
```

Backend API: http://127.0.0.1:8000

API documentation: http://127.0.0.1:8000/docs

### 2. Start the Frontend

Open a second terminal in the `frontend` directory.

Install the dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

Open the local URL displayed in the terminal, usually http://localhost:5173.

Keep both servers running while using the website.

## Location API

### Get a School's Location

```http
GET /schools/{school_id}/location
```

A successful response returns the school's coordinates:

```json
{
  "latitude": 13.7563,
  "longitude": 100.5018
}
```

The coordinates above are only an example.

### Error Handling

- **404 — School not found:** The requested school ID does not exist.
- **404 — School location unavailable:** The school exists, but its coordinates are missing or invalid.

## Credits

- [Vue.js](https://vuejs.org/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [Leaflet](https://leafletjs.com/)
- [OpenStreetMap](https://www.openstreetmap.org/)

