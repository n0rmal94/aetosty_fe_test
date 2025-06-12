# 🖼️ AETOSKY Test Frontend (Vue 3 + Mapbox)

This is a simple Vue 3 frontend that displays mock "intelligence events" on an interactive Mapbox map. It supports filtering by severity and keyword.

---

## 📁 Project Structure (simplified)

```
.
├── public/
├── src/
│   ├── assets/
│   ├── components/
│   └── views/
├── .env.example
├── Dockerfile
├── package.json
├── vite.config.js
└── README.md
```

---

## 🚀 How to Run

### ✅ Option 1: Run with npm (Recommended for Development)

```bash
# 1. Clone the repository & install dependencies
cd aetosky-frontend
npm install

# 2. Setup environment variables
cp .env.example .env
# ➜ Edit the .env file to set VUE_APP_MAPBOX_TOKEN and VUE_APP_API_URL

# 3. Run development server
npm run serve
# ➜ Visit: http://localhost:8080
```

---

### 🐳 Option 2: Run with Docker

```bash
# 1. Build Docker image
docker build -t aetosky-frontend .

# 2. Run Docker container with environment file
docker run -p 8080:80 --env-file .env aetosky-frontend
# ➜ Visit: http://localhost:8080
```

---

## 🛠️ Notes

- Make sure your `.env` file includes a valid [Mapbox Access Token](https://account.mapbox.com/) under `VUE_APP_MAPBOX_TOKEN`.
- The frontend expects a running backend API at `VUE_APP_API_URL`, such as `http://localhost:8000/api`.

---

## 📌 Requirements

- Node.js 16+
- Docker (optional, for containerized deployment)
