# No-Due Academic Management System — Localhost & Docker Setup

Production-ready Role-Based Academic Management Software with Fastify backend and Next.js frontend.

---

## 🚀 Quick Start with Docker Compose

### 1. Prerequisite: Docker Permissions
If your current user does not have permission to access the Docker daemon, add your user to the `docker` group once:
```bash
sudo usermod -aG docker $USER
newgrp docker
```

### 2. Start all services
From the `Nodue` directory:
```bash
docker compose up --build -d
```
This spins up:
- **PostgreSQL 16**: Port `5432` (`nodue-postgres`)
- **Redis 7**: Port `6379` (`nodue-redis`)
- **Fastify Backend**: Port `5000` (`nodue-backend`)
- **Next.js Frontend**: Port `3000` (`nodue-frontend`)

### 3. Access the Applications
- **Frontend Web Portal**: [http://localhost:3000](http://localhost:3000)
- **Backend API Base**: [http://localhost:5000/api/v1](http://localhost:5000/api/v1)
- **Backend Health Check**: [http://localhost:5000/health](http://localhost:5000/health)

### 4. Default Seed Credentials
The system comes with the following pre-configured administrative accounts:
- **Super Administrator**:
  - Email: `admin@institution.edu`
  - Password: `Admin@12345`
  - Portal: [http://localhost:3000/admin](http://localhost:3000/admin)
- **Cashier Account**:
  - Email: `cashier@institution.edu`
  - Password: `Cashier@12345`
  - Portal: [http://localhost:3000/cashier](http://localhost:3000/cashier)

### 5. Stop services
```bash
docker compose down
```
To also remove volumes (reset database):
```bash
docker compose down -v
```

---

## 🛠️ Running Locally Without Docker (Direct Node.js)

If you prefer to run services natively on your host machine:

### 1. Start PostgreSQL and Redis locally
Ensure PostgreSQL is running with database `nodue_core` and Redis is running on port `6379`.

### 2. Backend Setup
```bash
cd nodue-backend-main
npm install
npx prisma generate
npx prisma migrate dev
npm run prisma:seed     # Seeds official departments and admin
npm run dev             # Starts Fastify on http://localhost:5000
```

### 3. Frontend Setup
```bash
cd nodue-frontend-main
npm install
npm run dev             # Starts Next.js on http://localhost:3000
```

---

## 🔄 GitHub Actions CI/CD Pipelines

Automated pipelines are pre-configured:
- **Monorepo / Full-Stack**: `.github/workflows/ci-cd.yml` (lints, builds both frontend and backend, tests Docker compose)
- **Backend**: `nodue-backend-main/.github/workflows/ci.yml` & `deploy.yml` (Prisma migrations, TypeScript checks, GHCR image publishing)
- **Frontend**: `nodue-frontend-main/.github/workflows/ci.yml` & `deploy.yml` (Next.js production build, GHCR image publishing)
