# Overtime LEDGER (BTC)

Developed by **Muddasir Ali**.

Mobile-first overtime for teams of about 30–300 people. Supervisors sign in. Workers never create an account — they open a private link, mark attendance, and log extra hours. Supervisors approve with a signature. Admins export one Excel file for payroll.

## How it works

1. A supervisor (or the first Super Admin) signs up.
2. They add workers by name — no email, no password.
3. Each worker gets a personal link (`/w/…`) to share on WhatsApp.
4. The worker only sees their own hazri and overtime. They cannot open the rest of the company.
5. The supervisor signs pending overtime on their phone.

## Stack

- **TanStack Start + React 19** — one TypeScript app for UI and server functions
- **Postgres** (Neon in production, embedded PGLite in preview)
- **Better Auth** — Google, X, and email/password for supervisors/admins only
- **Tailwind v4** — large tap targets, ledger-paper visual system

## Roles

| Role | Access |
|---|---|
| Super Admin | Everything, including admins, settings, audit, Excel |
| Admin | People, records, reports, Excel — cannot manage Super Admins |
| Supervisor | Assigned workers only, add workers, share links, approve/reject with signature |
| Worker | Personal link only — attendance + own overtime. No signup. |

Permissions are enforced in every server function, not only in the UI. Worker-link functions resolve the token server-side and never return other workers.

## Local development

1. Node 22
2. `npm install`
3. Auth is on when `VITE_AUTH_ENABLED` is not `"false"`
4. `npm run dev` — app listens on port 8080
5. First sign-in becomes Super Admin and loads the Northline sample company

No `.env` file is required in this environment. Production injects `DATABASE_URL` and auth credentials.

## Database

Schema lives in `migrations/`:

- `0001_auth.sql` — Better Auth identity tables
- `0002_overtime.sql` — departments, profiles, overtime, audit, settings
- `0003_worker_links.sql` — personal worker links and attendance

Migrations apply on preview startup and during `npm run build` (production).

## Production

Deploy the web app with HTTPS on your domain. Point DNS at the host, enable TLS, and keep `BETTER_AUTH_URL` equal to the public URL.

Treat worker links like passwords: anyone with the URL can mark that worker's attendance. Rotate a link from the worker's profile if it leaks.

### Backups

Neon (or your Postgres host) should have daily automated backups. For a self-hosted database:

```
pg_dump "$DATABASE_URL" -Fc -f overtime-$(date +%F).dump
```

Restore with `pg_restore`. Do not delete overtime or user rows — deactivate instead.

## Demo

Password for sample **supervisor/admin** users: `Ledger2026!`

- Admin: `maria.chen@northline.demo`
- Supervisor: `james.okonkwo@northline.demo`

Demo workers have **no login**. After the sample company loads, open `/w/demo-luis` (also `demo-kenji`, `demo-amina`, `demo-elena`, `demo-omar`, `demo-grace`).
