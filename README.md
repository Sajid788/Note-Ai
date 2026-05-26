# AI Smart Notes Manager

A full-stack notes application with JWT authentication, CRUD operations, search/filter, and AI-powered title generation and summarization using Groq.

Built as a clean, interview-friendly project: simple Redux (no thunks), plain `fetch` API calls, and a premium dark dashboard UI.

---

## Features

### Authentication
- User registration and login
- JWT stored in localStorage (7-day expiry)
- Password validation: minimum 8 characters + special character
- Toast notifications for success and error states

### Notes
- Create, read, update, and delete notes
- Dedicated **Create Note** page (`/dashboard/create`)
- Edit notes from the dashboard (modal)
- Search by title or content
- Filter by category (General, Work, Personal, Ideas, Study)
- Stats cards: total notes, AI titles, AI summaries

### AI (Groq)
- **Generate Title** — from note content (works before saving)
- **Summarize** — from content while creating a note; no need to save first
- Summaries persist when the note is created or updated

### UI
- Dark premium dashboard with glass-style cards
- Responsive layout (sidebar on desktop, compact header on mobile)
- Sonner toasts for feedback

---

## Tech Stack

| Layer | Technologies |
|--------|----------------|
| **Frontend** | Next.js 16, TypeScript, Tailwind CSS 4, Redux Toolkit, Sonner |
| **Backend** | Node.js, Express 5, MongoDB, Mongoose |
| **Auth** | bcryptjs, JSON Web Token |
| **AI** | Groq API (`llama-3.3-70b-versatile`) |

---

## Project Structure

```
Ai-notes/
├── client/                 # Next.js frontend
│   ├── src/
│   │   ├── app/            # Pages (login, register, dashboard, create)
│   │   ├── components/     # UI, layout, notes
│   │   ├── redux/          # Store, slices, API (fetch)
│   │   └── lib/            # Types, validation, toast helpers
│   └── .env.local          # NEXT_PUBLIC_API_URL
│
└── server/                 # Express API
    ├── config/             # MongoDB connection
    ├── controllers/        # auth, notes, AI
    ├── middleware/         # JWT protect
    ├── models/             # User, Note
    ├── routes/             # API routes
    └── .env                # Secrets (not committed)
```

---

## Prerequisites

- **Node.js** 18+ (20+ recommended)
- **MongoDB** (local or [MongoDB Atlas](https://www.mongodb.com/cloud/atlas))
- **Groq API key** — [console.groq.com](https://console.groq.com)

---

## Environment Variables

### Server (`server/.env`)

Create `server/.env`:

```env
PORT=8080
MONGO_URI=mongodb://127.0.0.1:27017/ai-notes
JWT_SECRET=your_super_secret_jwt_key_here
GROQ_API_KEY=your_groq_api_key_here
```

| Variable | Description |
|----------|-------------|
| `PORT` | API server port (default `8080`) |
| `MONGO_URI` | MongoDB connection string |
| `JWT_SECRET` | Secret for signing JWT tokens |
| `GROQ_API_KEY` | Groq API key for AI features |

### Client (`client/.env.local`)

Create `client/.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:8080
```

Must match the backend `PORT`.

---

## Installation & Run

### 1. Clone and install dependencies

```bash
# Backend
cd server
npm install

# Frontend
cd ../client
npm install
```

### 2. Configure environment

- Add `server/.env` (see above)
- Add `client/.env.local` with your API URL

### 3. Start MongoDB

Ensure MongoDB is running locally, or use a cloud `MONGO_URI`.

### 4. Run the backend

```bash
cd server
npm run dev
```

API runs at `http://localhost:8080` (or your `PORT`).

### 5. Run the frontend

```bash
cd client
npm run dev
```

Open **http://localhost:3000**

---

## API Reference

Base URL: `http://localhost:8080`

### Auth (public)

| Method | Endpoint | Body |
|--------|----------|------|
| `POST` | `/api/auth/register` | `{ name, email, password }` |
| `POST` | `/api/auth/login` | `{ email, password }` |

### Notes (Bearer token required)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/notes` | List notes (`?search=&category=`) |
| `GET` | `/api/notes/:id` | Get one note |
| `POST` | `/api/notes` | Create note |
| `PUT` | `/api/notes/:id` | Update note |
| `DELETE` | `/api/notes/:id` | Delete note |

### AI (Bearer token required)

| Method | Endpoint | Body |
|--------|----------|------|
| `POST` | `/api/ai/title` | `{ content, noteId? }` |
| `POST` | `/api/ai/summary` | `{ content, noteId? }` |
| `POST` | `/api/ai/summarize/:id` | Summarize saved note by ID |

**Authorization header:** `Bearer <token>`

---

## Frontend Routes

| Route | Description |
|-------|-------------|
| `/` | Redirects to dashboard or login |
| `/login` | Sign in |
| `/register` | Create account |
| `/dashboard` | Notes grid, search, stats |
| `/dashboard/create` | Create note (with AI before save) |

---

## Redux Overview

Simple Redux setup without `createAsyncThunk`:

- **Slices** — `authSlice`, `notesSlice` (sync actions only)
- **API** — `authApi.ts`, `notesApi.ts` (plain `fetch`)
- **Usage** — call API in components, then `dispatch(setNotes(...))` etc.

Example:

```ts
const data = await getNotes(token, search, category);
dispatch(setNotes(data));
```

---

## Production Build

```bash
# Frontend
cd client
npm run build
npm start

# Backend
cd server
npm start
```

Set production `NEXT_PUBLIC_API_URL` to your deployed API URL.

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Network / fetch errors | Check `NEXT_PUBLIC_API_URL` matches backend `PORT` |
| AI features fail | Verify `GROQ_API_KEY` in `server/.env` and restart server |
| MongoDB connection error | Check `MONGO_URI` and that MongoDB is running |
| 401 on notes | Log in again; token may be expired or invalid |

---

## License

ISC
