# MySpendMate backend

The Express API connects to **MySQL Server**. MySQL Workbench is a client for managing MySQL Server; it is not the database server itself.

## Database setup

You do not need to create the database or tables in Workbench. On startup, the backend creates the configured database and its tables automatically. The configured MySQL account must have permission to create databases. For local development, you can use your MySQL `root` account.

## Configure and run (Windows PowerShell)

Install/start MySQL Server, then from the project folder run:

```powershell
cd .\backend
npm install
Copy-Item .env.example .env
notepad .env
```

In `.env`, set `DB_HOST`, `DB_PORT`, `DB_USER`, and `DB_PASSWORD` to match your local MySQL Server account. `DB_NAME` defaults to `myspendmate`; the backend creates it when needed. Keep `.env` private; it is excluded from source control.

Then start the API:

```powershell
npm start
```

Open [http://localhost:5000](http://localhost:5000), not `index.html` directly. Check the connection at [http://localhost:5000/api/health](http://localhost:5000/api/health).

## Deploy on Render

Render does not provide a managed MySQL database by default. Create a MySQL database with an external provider and allow Render network access.

Deploy this repository using `render.yaml` or create a Render Web Service with:

- **Root Directory:** `backend`
- **Build Command:** `npm ci`
- **Start Command:** `npm start`
- **Health Check Path:** `/api/health`

Set `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`, and `DB_SSL=true` in the Render dashboard.

If the provider requires a CA certificate, set `DB_SSL_CA` to its path. The included `ca.pem` is used automatically when `DB_SSL=true` and `DB_SSL_CA` is blank.

Do not add real database passwords to `backend/.env` or commit them. Keep production secrets in Render environment settings.

## API used by the app

- `GET /api/health`
- `POST /api/auth/signup`
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `GET /api/auth/me`
- `GET` and `PUT /api/profile` (including the account's budget reset day, 1–31)
- `GET` and `PUT /api/store` (requires login)

Passwords are stored as salted scrypt hashes. Login uses an HTTP-only session cookie, and profile and finance records are scoped to the signed-in user. Sessions are held in memory, so restarting the backend signs users out.

The old SQLite database, if present, is not deleted or imported automatically. Existing browser data is synced by the app after the user signs in.
