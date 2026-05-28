# Security Standards

Every project scaffolded via /letsgo must follow these security rules from day one. Not after launch. Not as a follow-up. At scaffold time.

## Environment Variables

- Never hardcode secrets, API keys, tokens, or credentials anywhere in code
- All secrets go in `.env`: never committed to git
- `.env.example` is always committed with placeholder values
- `.env` is always in `.gitignore`: add it before the first commit
- All env vars are validated and typed through `config/env.ts` using `zod` or `t3-env`
- Never access `process.env.X` directly in components, services, or routes

```ts
// config/env.ts: always use this pattern
import { z } from "zod"

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NEXTAUTH_SECRET: z.string().min(32),
  NEXT_PUBLIC_APP_URL: z.string().url(),
})

export const env = envSchema.parse(process.env)
```

## Authentication and Authorization

- Never roll custom auth. Use proven libraries: NextAuth.js, Clerk, Auth.js, Supabase Auth
- Always protect routes at the middleware level, not just in the UI
- Never trust client-side role checks alone: always verify on the server
- Session tokens must be httpOnly cookies, never localStorage
- Implement CSRF protection on all state-mutating endpoints

## Input Validation

- Validate all user input at the API boundary using Zod
- Never trust data coming from the client: validate on the server regardless of client-side validation
- Sanitize all inputs before passing to a database query
- Use parameterized queries or an ORM (Prisma, Drizzle): never raw string SQL concatenation

```ts
// api/validators/user.ts
import { z } from "zod"

export const createUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email(),
  password: z.string().min(8),
})
```

## API Security

- All API routes that mutate data require authentication checks
- Rate limit all public-facing API endpoints (use `upstash/ratelimit` or similar)
- Never expose internal error messages or stack traces to the client
- Return generic error messages to the client, log details server-side only
- Use HTTP-only cookies for auth tokens, not Authorization headers with localStorage tokens

## Database

- Use an ORM (Prisma, Drizzle): never raw SQL string concatenation
- Principle of least privilege: DB user has only the permissions it needs
- Never store plain-text passwords: use bcrypt or argon2
- Sensitive fields (passwords, tokens) are never returned in API responses

## Dependencies

- Audit dependencies at scaffold time: `npm audit`
- Pin dependency versions in `package.json`: avoid `*` or `latest`
- Never install packages with known critical vulnerabilities
- Keep `package-lock.json` or `yarn.lock` committed

## Headers and CORS

- Set security headers via Next.js config or middleware:
  - `X-Frame-Options: DENY`
  - `X-Content-Type-Options: nosniff`
  - `Referrer-Policy: strict-origin-when-cross-origin`
  - `Content-Security-Policy` (at minimum for production)
- CORS: whitelist only known origins, never use `*` in production

```ts
// next.config.ts
const securityHeaders = [
  { key: "X-Frame-Options", value: "DENY" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
]
```

## File Uploads (if applicable)

- Validate file type on the server, not just the client
- Limit file size on the server
- Never serve uploaded files from the same origin as the app
- Scan uploads for malware if handling sensitive content

## OWASP Top 10 Checklist

Apply at scaffold and before every major release:

| Threat | Defense |
|---|---|
| Injection (SQL, NoSQL, command) | ORM + parameterized queries, never string concat |
| Broken auth | NextAuth/Clerk, httpOnly cookies, server-side checks |
| Sensitive data exposure | Env vars, never log secrets, HTTPS only |
| XXE | Avoid XML parsers, use JSON APIs |
| Broken access control | Server-side auth on every protected route |
| Security misconfiguration | Security headers, no debug mode in prod |
| XSS | Sanitize inputs, avoid dangerouslySetInnerHTML |
| Insecure deserialization | Validate and type all incoming data with Zod |
| Known vulnerable components | `npm audit` at scaffold, pin versions |
| Insufficient logging | Log auth events, errors server-side (never to client) |
