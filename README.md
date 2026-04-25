# 🚀 Smart CRM

A full-stack, production-grade CRM application with a modern SaaS-style UI.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18 + Vite + Tailwind CSS |
| State | Zustand |
| Backend | Node.js + Express |
| Database | PostgreSQL + Prisma ORM |
| Auth | JWT (RS256) |
| Charts | Recharts |
| Drag & Drop | @dnd-kit |

---

## Features

- **Authentication** — JWT login/register, role-based access (Admin / Salesperson)
- **Lead Management** — Full CRUD with search, filter, pagination
- **Kanban Pipeline** — Drag-and-drop leads across stages (New → Contacted → Qualified → Closed)
- **Lead Scoring** — Automatic score (0–100) based on deal value, interaction frequency, and recency. Categorized as 🔥 Hot / ⚠️ Warm / ❄️ Cold
- **Tasks & Follow-ups** — Create tasks linked to leads, due dates, priority, auto-overdue detection
- **Notes & Activity Log** — Per-lead notes and full activity history (calls, meetings, emails, status changes)
- **Analytics Dashboard** — Pipeline charts, temperature distribution, trend lines, team performance table
- **Export** — Download leads as CSV
- **Dark Mode** — Full dark/light theme toggle

---

## Project Structure

```
smart-crm/
├── backend/
│   ├── prisma/
│   │   ├── schema.prisma       # DB schema (Users, Leads, Tasks, Activities, Notes)
│   │   └── seed.js             # Demo data seeder
│   └── src/
│       ├── app.js              # Express app setup
│       ├── server.js           # Entry point
│       ├── config/prisma.js    # Prisma singleton
│       ├── middleware/
│       │   ├── auth.js         # JWT authenticate + authorize
│       │   ├── errorHandler.js # Global error handler
│       │   └── validate.js     # express-validator middleware
│       └── modules/
│           ├── users/          # Auth + user management
│           ├── leads/          # Lead CRUD + scoring engine
│           ├── tasks/          # Task management
│           ├── analytics/      # Dashboard + performance metrics
│           └── export/         # CSV/JSON export
└── frontend/
    └── src/
        ├── components/
        │   ├── layout/         # Sidebar, Header, Layout
        │   ├── leads/          # LeadCard, LeadForm
        │   ├── tasks/          # TaskForm
        │   └── ui/             # Badge, Modal, StatCard, etc.
        ├── pages/
        │   ├── auth/           # Login, Register
        │   ├── leads/          # LeadsPage, LeadDetailPage
        │   ├── DashboardPage
        │   ├── PipelinePage    # Kanban board
        │   ├── TasksPage
        │   ├── AnalyticsPage
        │   ├── UsersPage
        │   └── SettingsPage
        ├── store/              # Zustand stores (auth, leads, tasks, theme)
        └── lib/api.js          # Axios instance with JWT interceptor
```

---

## Getting Started

### Prerequisites
- Node.js 18+
- PostgreSQL 14+

### 1. Backend Setup

```bash
cd backend
cp .env.example .env
# Edit .env with your DATABASE_URL and JWT_SECRET

npm install
npx prisma migrate dev --name init
npm run prisma:seed
npm run dev
```

### 2. Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

### 3. Open the app

Visit **http://localhost:5173**

**Demo accounts:**
- Admin: `admin@smartcrm.com` / `admin123`
- Sales: `sales@smartcrm.com` / `sales123`

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/login` | Login |
| POST | `/api/auth/register` | Register |
| GET | `/api/auth/me` | Current user |
| GET | `/api/leads` | List leads (paginated, filterable) |
| GET | `/api/leads/pipeline` | Kanban grouped leads |
| POST | `/api/leads` | Create lead |
| PUT | `/api/leads/:id` | Update lead |
| PATCH | `/api/leads/:id/status` | Quick status update |
| DELETE | `/api/leads/:id` | Delete lead (Admin) |
| POST | `/api/leads/:id/notes` | Add note |
| POST | `/api/leads/:id/activities` | Log activity |
| GET | `/api/tasks` | List tasks |
| POST | `/api/tasks` | Create task |
| PUT | `/api/tasks/:id` | Update task |
| GET | `/api/analytics/dashboard` | Dashboard stats |
| GET | `/api/analytics/performance` | Team performance (Admin) |
| GET | `/api/analytics/trends` | Lead trends |
| GET | `/api/export/leads/csv` | Export CSV |

---

## Lead Scoring Algorithm

```
Score (0–100) = Deal Value Score (0–40) + Interaction Score (0–30) + Recency Score (0–30)

Deal Value:    min(dealValue / $100k * 40, 40)
Interactions:  min(count / 10 * 30, 30)
Recency:       30 (today) → 25 (week) → 15 (2 weeks) → 8 (month) → 0 (>30 days)

Temperature:
  🔥 Hot  = score ≥ 70
  ⚠️ Warm = score ≥ 40
  ❄️ Cold = score < 40
```

Score is recalculated automatically on every lead update, note, activity, or status change.
