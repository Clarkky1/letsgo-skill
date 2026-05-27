# Project Structure Standard

Every project scaffolded via /letsgo must follow clean architecture with clear separation of concerns.

## Standard Folder Structure

```
project-name/
├── frontend/                   # All UI/client-side code
│   ├── app/                    # Next.js app router pages
│   ├── components/
│   │   ├── ui/                 # shadcn/skiper-ui base components
│   │   ├── layout/             # Header, footer, sidebar, nav
│   │   ├── features/           # Feature-specific components
│   │   └── shared/             # Reusable across features
│   ├── hooks/                  # Custom React hooks
│   ├── lib/                    # Frontend utilities, helpers
│   ├── styles/                 # Global CSS, Tailwind config
│   └── constants/              # Frontend constants (routes, labels, config values)
│       └── index.ts
│
├── backend/                    # Server-side logic
│   ├── services/               # Business logic layer
│   ├── repositories/           # Data access layer
│   ├── models/                 # Data models / schemas
│   ├── middleware/             # Auth, logging, error handling
│   ├── utils/                  # Backend utilities
│   └── constants/              # Backend constants (enums, config keys, defaults)
│       └── index.ts
│
├── api/                        # API layer
│   ├── routes/                 # Route definitions
│   ├── controllers/            # Request/response handlers
│   ├── validators/             # Input validation schemas (Zod)
│   └── types/                  # Shared API types and interfaces
│
├── shared/                     # Shared between frontend and backend
│   ├── constants/              # App-wide constants (used on both sides)
│   │   └── index.ts
│   ├── types/                  # Shared TypeScript types
│   └── utils/                  # Shared utility functions
│
├── config/                     # Environment and app configuration
│   ├── env.ts                  # Validated env vars (use zod or t3-env)
│   └── site.ts                 # Site metadata, app name, URLs
│
├── public/                     # Static assets
├── prisma/ or db/              # Database schema and migrations (if applicable)
├── tests/                      # All tests
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── .env                        # Local env vars (never commit)
├── .env.example                # Template for env vars (always commit)
└── .gitignore                  # Always include .env
```

## Constants Rule

**Always create a `constants/` directory.** Never scatter magic strings, numbers, route paths, or config values inline in components or functions.

### What goes in constants:

```ts
// shared/constants/index.ts

export const APP_NAME = "Project Name"
export const APP_URL = process.env.NEXT_PUBLIC_APP_URL

// Routes
export const ROUTES = {
  home: "/",
  dashboard: "/dashboard",
  login: "/login",
  signup: "/signup",
} as const

// API endpoints
export const API = {
  auth: "/api/auth",
  users: "/api/users",
} as const

// UI
export const BREAKPOINTS = {
  sm: 640,
  md: 768,
  lg: 1024,
  xl: 1280,
} as const

// Status / enums
export const STATUS = {
  active: "active",
  inactive: "inactive",
  pending: "pending",
} as const
```

### Constants placement rule:

| Constant type | Where |
|---|---|
| Routes, labels, UI copy | `frontend/constants/` |
| DB keys, service config, defaults | `backend/constants/` |
| App name, shared enums, API paths | `shared/constants/` |
| Env var access | `config/env.ts` only — never access `process.env` directly in components |

## Clean Architecture Rules

1. **Frontend never imports from backend directly.** Communication goes through the API layer only.
2. **Services handle business logic.** Controllers only handle request/response shaping.
3. **Repositories handle all data access.** Services never query the DB directly.
4. **Constants are never inlined.** All magic values live in the appropriate constants file.
5. **Types are shared via `shared/types/`.** Never duplicate type definitions across frontend and backend.
6. **Env vars are accessed only through `config/env.ts`.** Never call `process.env.X` directly in components or services.
