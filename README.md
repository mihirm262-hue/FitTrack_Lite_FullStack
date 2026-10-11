# FitTrack Lite — Full Stack

- **frontend/** — React + Vite → deploys to **Vercel**
- **backend/** — Java 21+ · Spring Boot 4.1.1 · Spring Security · JPA · MySQL → deploys to **Render** (Vercel cannot run Java)

---

## 1. Run locally in VS Code

### Install once
| Tool | Version | Check |
|---|---|---|
| JDK | 21 or newer (25 is fine) | `java -version` |
| Maven | 3.9+ | `mvn -v` |
| Node.js | 18+ (20/22 recommended) | `node -v` |
| MySQL Server | 8.x | MySQL Workbench / `mysql -u root -p` |

Open the **FitTrack_Lite_FullStack** folder in VS Code and accept the recommended extensions (Java Pack + Spring Boot tools).

### Terminal 1 — backend
Windows PowerShell:
```powershell
cd backend
$env:DB_USERNAME="root"
$env:DB_PASSWORD="your_mysql_password"
mvn spring-boot:run
```
macOS / Linux / Git Bash:
```bash
cd backend
DB_USERNAME=root DB_PASSWORD=your_mysql_password mvn spring-boot:run
```
The database `fittrack_lite` and all tables are created automatically.
Check: open http://localhost:8080 → `{"status":"UP",...}`

### Terminal 2 — frontend
```bash
cd frontend
npm install
npm run dev
```
Open http://localhost:5173 → Sign up → use the app.
(Vite forwards every `/api/...` call to `localhost:8080`, so no extra config is needed.)

---

## 2. Put it live

### Step A — Push to GitHub
```bash
git init && git add . && git commit -m "FitTrack Lite"
git branch -M main
git remote add origin https://github.com/<you>/fittrack-lite.git
git push -u origin main
```

### Step B — Cloud MySQL database
Create a free MySQL database (e.g. **Aiven for MySQL**, **Railway MySQL**, or **TiDB Cloud Serverless**). Note host, port, database name, user, password.

### Step C — Backend on Render
1. render.com → **New → Web Service** → pick your repo.
2. **Root Directory:** `backend` · **Runtime/Language:** `Docker` (uses `backend/Dockerfile`).
3. Environment variables:
   | Key | Value |
   |---|---|
   | `DB_URL` | `jdbc:mysql://HOST:PORT/DBNAME?sslMode=REQUIRED` |
   | `DB_USERNAME` | your db user |
   | `DB_PASSWORD` | your db password |
   | `FRONTEND_URL` | `https://your-app.vercel.app` (fill after Step D) |
4. Deploy → open `https://<name>.onrender.com` → should show `"status":"UP"`.

### Step D — Frontend on Vercel
1. Edit **frontend/vercel.json** → replace `YOUR-BACKEND-URL.onrender.com` with your Render URL. Commit & push.
2. vercel.com → **Add New → Project** → import the repo.
3. **Root Directory:** `frontend` · Framework: **Vite** (auto) · Build `npm run build` · Output `dist`.
4. Deploy. Done 🎉

Vercel forwards `/api/*` to Render, so the browser sees one site and the login cookie works on every browser (including Safari / iPhone).

> Render's free tier sleeps after ~15 min idle; the first request afterwards takes ~30–50 s.

---

## API
| Method | Path |
|---|---|
| POST | `/api/auth/register`, `/api/auth/login`, `/api/auth/logout` |
| GET | `/api/auth/me`, `/api/dashboard`, `/api/workouts/plans`, `/api/workouts/history`, `/api/progress`, `/api/records` |
| POST | `/api/workouts`, `/api/progress/measurements`, `/api/records` |
