# Aetosky Frontend Setup Instructions (npm & Docker)

# 1. Clone & enter project
git clone https://your-repo-url.git aetosky-frontend
cd aetosky-frontend

# 2. Install dependencies
npm install

# 3. Copy and edit .env file
cp .env.example .env

# 4. Run development server
npm run serve

# -------- Optional: Docker-based workflow --------

# 5. Build Docker image
docker build -t aetosky-frontend .

# 6. Run Docker container with .env file
docker run -p 8080:80 --env-file .env aetosky-frontend

# -------- Optional: Production build with npm --------

# 7. Build production-ready files
npm run build

# Files will be output to the 'dist/' directory
