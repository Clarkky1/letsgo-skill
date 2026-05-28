---
name: letsgo
description: This skill should be used when the user types "/letsgo", says "letsgo", "let's go build", "start a new project", or "kick off a new build". It runs the full 15-step project kickoff flow: personal vs client check (with client meeting prep if client), gather context + MCP setup, brand check + design system, UI standard (Hallmark/portfolio-design/motion-design-school/website-ui-v2/liquid-glass-design/landonorris-ui/pixel-art) + design-inspiration brief (Mobbin/Godly/Awwwards/Lapa/VibeUI/Aceternity/Origin UI/Magic UI/Motion Primitives/21st.dev/Fancy Components/hover.dev/buildui.com/UIverse/Emil Kowalski/Grainient/Mesher/CSS Pattern/SVG Backgrounds/Lummi/Fontjoy/Typewolf/Realtime Colors/oklch/Radix Colors/Lucide/Phosphor/Tabler/60fps.design/Codrops/transition.style), platform detection + animation stack + lenis + matter.js, 3-terminal setup (with iteration cap: 4 attempts max per sub-agent then escalate to supervisor; and model tiering: Sonnet for orchestration/known patterns, Opus for judgment calls/ambiguous decisions/second-failure escalation), background component (spline/haikei/vanta/grainient/mesher/css-pattern/svgbackgrounds/framer/endlesstools), typography + color + icon lock, engagement design, tech stack + database (supabase/prisma/drizzle) + auth (clerk/nextauth) + zod validation, folder structure, security standards, hand off to autonomous-engineer, design quality gate, app store prep (if mobile), n8n automation offer + next steps. Video generation: remotion (React) or hyperframes (HTML+GSAP, agent-first, Apache 2.0, no build step). Invoke hyperframes skill when building HTML-to-video pipelines.
version: 2.4.0
---

# /letsgo: Project Kickoff Flow

Run this exact 15-step sequence every time. Do not skip steps.

## Step 1: Personal or Client Project?

Ask: "Is this a personal project or a client project?"

Use `AskUserQuestion` with two options: **Personal** and **Client**.

### If Personal:
Proceed directly to Step 2.

### If Client:
Output the full **Client Meeting Prep** checklist below. Tell the user: "Here are the questions you need answered before the build starts. Use these in your client meeting."

---

**CLIENT MEETING PREP: Questions to Ask**

#### The Basics
1. What's the goal? (sell, get leads, get bookings, build community?)
2. Who is this for?
3. What does winning look like in 3 months?
4. Any competitors you admire or hate?
5. What makes you different?

#### What We're Building
6. What pages or screens do you need?
7. Do you have content ready (text, photos, videos) or do we create it?
8. Do you need to update the site yourself after launch?
9. Do you need people to log in?
10. Do you need to take payments?

#### Look and Feel
11. Do you have a logo, colors, or fonts already?
12. 3 sites you love: what do you like about them?
13. Anything you definitely don't want?

#### Tech and Tools
14. Do you have a domain already?
15. Any tools we need to connect? (booking, email, CRM, analytics?)
16. Who looks after the site after launch?

#### Timeline and Budget
17. When do you want to launch?
18. Any hard deadlines?
19. What's your budget?
20. Do you want ongoing support after launch?

#### Automation
21. What takes too long or keeps breaking in your process?
22. Any tools you wish talked to each other automatically?
23. What would you want to be notified about instantly? (new leads, orders, messages?)

#### Legal
24. Do you need a cookie banner or privacy policy?
25. Can you share access to your domain, hosting, and social accounts?

---

After the user confirms they've had the meeting and have answers, ask: "Do you have all the answers? Let's fill them in and then proceed to the build." Then continue to Step 2 with the context gathered from those answers.

## Step 2: Check Project & Gather Context

Before asking, always check if the current directory has existing files or an active codebase (e.g., check for package.json, pubspec.yaml, Package.swift, requirements.txt, or run a quick file search). 

- **If files/codebase exist:** Analyze the codebase to auto-detect the tech stack, framework, language, and dependencies. Output a friendly message like: "I've checked the project and detected [detected stack/framework/language]. Based on our current codebase, I think it's good to have these options: [suggest matching UI standard, animation stack, background tools, database, auth, and relevant agent skills]."
  - *Note:* Only check, reference, or suggest other specialized skills from the wider library of 400+ skills if they are specifically relevant to the detected codebase or project context (e.g., do not check mobile/SwiftUI skills for a web project, and only suggest `pixel-art` if retro graphics are detected or requested).
- **If new project:** Ask the user what they are building and who it is for. Use `AskUserQuestion`. Do not proceed until clear. Minimum needed:
  - Type of app or site (SaaS, portfolio, landing page, dashboard, iOS app, Flutter app, etc.)
  - Who the audience is
  - The primary goal or feeling the project must create

If the project goal is unclear or the user is still figuring out the concept, invoke `design-sprint` to run a focused discovery session before proceeding.

If the project needs Claude to connect to an external service (database, API, Slack, GitHub, file system) during development, ask if they want to set up an MCP server. If yes, invoke `mcp` skill. Also note: once the MCP server is wired, `mcp-reconnect` (`github.com/palios-taey/mcp-reconnect`) can auto-drive the `/mcp` → Reconnect menu after any server restart: worth installing for the dev loop.

If the project **requires building a new MCP server** (exposing an internal API, custom tool, or third-party integration to Claude), invoke `mcp-builder`: it covers TypeScript and Python server patterns, tool naming, output schemas, and eval creation.

## Step 2.5: Playback, Flow, and Summary Confirmation

Once the user provides their answers or project brief:
1. **Playback:** State clearly what you understand from the user's answers/inputs (e.g., goals, target audience, stack, features).
2. **Flow & Summary:** Outline the planned kickoff flow and a concise summary of the project.
3. **Change Specific option:** Ask the user if they want to proceed or change anything, using `AskUserQuestion` with options:
   - **Proceed to Step 3 (Brand Check)**
   - **Change specific details** (If selected, ask the user what specific details they want to change, update your understanding/summary, and present the confirmation options again).

## Step 3: Brand Check, Design System, and Assets

Ask: "Do you have existing brand guidelines (logo, colors, fonts, voice) and project assets (images, icons, vectors, illustrations, videos), or should I create them?"

- **Has brand/assets**: ask for brand colors, fonts, voice, and asset files. If they have files, ask them to copy them to the project directory or provide the paths. Lock these into the project's design tokens/directories.
- **No brand/assets**: invoke `brand-guidelines` skill to define: primary color, type pair, voice (2-3 adjectives), and logo direction. If design assets are needed later, use the `generate_image` tool or pull from `free-design-resources`.

After brand is locked, invoke `design-system` to establish the spacing scale, color tokens, type scale, and component naming conventions that will be used throughout the build.

## Step 4: Identify and State the UI Standard

Analyze the project files and context, and suggest the most appropriate UI standard, stating something like: "Hey, I think this project we have is good to have these options: we should use the **[recommended standard]** standard because..." Based on project context or auto-detection, pick one standard and state it explicitly:

- **Hallmark**: most expressive, best default for open-ended projects. Invoke `hallmark`.
- **portfolio-design**: personal brands, portfolios, agency homepages. Invoke `portfolio-design`.
- **motion-design-school**: course platforms, showcases, creative education. Invoke `motion-design-school`.
- **website-ui-v3 / Silencio**: agencies, studios, anti-brand bold sites. Invoke `website-ui-v2`.
- **liquid-glass-design**: glassmorphism, depth, blur layers, Apple-adjacent premium feel. Invoke `liquid-glass-design`.
- **landonorris-ui / The OFF+BRAND. Method**: not a fixed aesthetic. A process for hitting $20k-quality on any project: derive colors/type/motion from the subject, replace every browser default, finish all 5 layers (structure, visual, motion, interaction, polish). The landonorris.com dark + lime design is the reference example, not the output. Use when the project needs to feel premium, alive, and intentional. Source effects from: [Codrops](https://tympanus.net/codrops), [GSAP demos](https://gsap.com/demos), [Flowbase](https://www.flowbase.co). Invoke `landonorris-ui`.
- **pixel-art**: retro games, sprite art, 8-bit/16-bit retro graphics, limited palettes, dithering, subpixel animations, tilesets. Invoke `pixel-art`.
- **canvas-design**: posters, print art, static visual design, PDF/PNG deliverables. Design philosophy → museum-quality canvas output. Invoke `canvas-design`.

After the standard is chosen, invoke `design-principles` to reinforce the foundational rules (contrast, hierarchy, alignment, proximity, repetition) that apply regardless of style. Then invoke `getdesign` as a component and pattern reference alongside `21st-dev`.

Also invoke `design-inspiration` to pull at least 3 locked references before any UI code is written. Output the full Inspiration Brief (feeling, references, typography, color, icons, background, motion, components) before proceeding to Step 5. The skill covers every source in the toolkit: Mobbin, Godly, Awwwards, Lapa Ninja, Dark Mode Design, Pageflows, Screenlane, Land-book, VibeUI, Aceternity, Origin UI, Magic UI, Motion Primitives, 21st.dev, Fancy Components, hover.dev, buildui.com, UIverse, Emil Kowalski, Grainient, Mesher, CSS Pattern, SVG Backgrounds, Lummi, Fontjoy, Typewolf, Fonts in Use, Variable Fonts, Realtime Colors, oklch.com, Radix Colors, Happy Hues, Lucide, Phosphor, Tabler, Iconoir, SVG Repo, 60fps.design, Codrops, transition.style, cubic-bezier.com, Impeccable, Taste, oh-my-claude-sisyphus.

**CodePen** (`codepen.io`): use for sourcing micro-interactions, canvas effects, CSS animations, and GSAP demos that can be adapted into components. Search by technique (e.g. "gsap splittext", "scroll reveal", "liquid button") and port the relevant logic into the project stack.

Also reference `free-design-resources` for any icons, illustrations, vectors, or fonts needed: always pull from free sources first before suggesting paid assets.

## Step 5: Platform Detection and Animation Stack

Detect the platform and lock the animation + component approach:

| Platform | Animation Skills | Component Source |
|---|---|---|
| Web / Next.js / React | `framer-motion` + `motion-dev` + `gsap` (heavy scroll/timeline) + `css-animations` + `ui-animation` + `gstac` + `lenis` (smooth scroll) + `matter.js` (2D physics/rigid-body simulations) | `shadcn-ui`, `origin-ui`, `magic-ui`, `motion-primitives`, `skiper-ui`, `21st-dev`, `getdesign`, `reactbits`, `aceternity`, `hover.dev`, `buildui.com`, `fancy-components`, `uiverse` |

**GSAP setup for Next.js/React**: install: `npm install gsap @gsap/react`. Only import the plugins you actually use. Minimal landing page setup:
```ts
import { gsap } from "gsap";
import { useGSAP } from "@gsap/react";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { SplitText } from "gsap/SplitText";
gsap.registerPlugin(useGSAP, ScrollTrigger, SplitText);
```
Full plugin import reference is in project memory `reference_gsap.md`. Always use `useGSAP` hook (not `useEffect`): it handles cleanup automatically. Mark component `'use client'` in Next.js App Router.
| Responsive / Multi-screen | `mobile-responsiveness` + `responsive-web-design` + `mobile-first-design` + `frontend-design` | apply alongside web stack |
| iOS / macOS / SwiftUI | `apple-animations` + `ios-hig` + `apple-design` + `swiftui-patterns` | SF Symbols, SwiftUI native |
| Flutter | `flutter-animations` | Flutter Material / custom |
| Video generation | `remotion` + `hyperframes` (HTML-native, agent-first, Apache 2.0) | React components (Remotion) / HTML + GSAP (HyperFrames) |
| Document output | `docx`, `pdf`, `pptx`, `xlsx` (Anthropic document skills) | Word, PDF, PowerPoint, Excel files |
| Generative art | `algorithmic-art` (p5.js + seeded randomness) | Interactive p5.js HTML artifacts |
| Web artifacts | `web-artifacts-builder` (React + Tailwind + shadcn bundled to HTML) | Complex claude.ai artifacts |
| Animated GIFs | `slack-gif-creator` (PIL + GIFBuilder, Slack-optimized) | Slack emoji GIFs, message GIFs |
| AI application | `context-engineering` + (`claude-api` or `azure-ai`) |: |

For any web project targeting more than one screen size, invoke `mobile-responsiveness`, `responsive-web-design`, `mobile-first-design`, and `frontend-design` to lock breakpoints, fluid grids, and tablet/mobile layout rules before building.

Also invoke `animation-designer` to define the motion language: easing curves, durations, entrance/exit patterns, and stagger rules that will be consistent across the entire build.

State the platform and animation stack explicitly before moving on.

## Step 6: Output the 3-Terminal Setup

Output the three terminal prompts filled in with project name, goal, stack, UI standard, and animation approach:
- **Terminal 1: Orchestrator (Supervisor)**: plans architecture, delegates tasks, wakes on structured outcomes from workers. Does not poll or interrupt mid-loop
- **Terminal 2: Developer (Worker)**: builds components, runs local loop to completion, then delivers a structured outcome to the Orchestrator
- **Terminal 3: QA (Worker)**: reviews every component for regressions, runs local loop to completion, then delivers a structured outcome to the Orchestrator

### Supervisor-Worker Protocol

**No permission-asking.** Every session: Orchestrator, Developer, and QA: has full authorization for all actions within the approved scope. Do not ask "can I?", "should I?", or "is it okay to?" for anything already inside the plan. Execute. Surface blockers only when genuinely stuck on something outside the approved scope, never for routine task execution.

Workers do not interrupt mid-loop. When a worker finishes its local loop, it delivers a structured outcome:

```json
{
  "status": "done | blocked | needs_review",
  "artifacts": ["list of files changed or created"],
  "decisions": ["choices made and why"],
  "blockers": ["anything that needs supervisor resolution"],
  "next_steps": ["recommended actions for the supervisor to delegate next"]
}
```

The Orchestrator reads structured outcomes and decides what to delegate next. No pane-checking. No mid-loop status pings.

**Iteration cap.** Each worker gets a maximum of 4 attempts on the same task. If a worker reaches 4 attempts without resolving the blocker, it stops, sets `"status": "blocked"` in its outcome, describes what was tried and why it failed, and surfaces to the Orchestrator. The Orchestrator re-scopes, reassigns, or escalates: it does not send the same worker back into a fifth loop. Infinite retry loops are a bug, not persistence.

### Model Tiering

Use the right model tier for the right work. Default to mid-tier (Sonnet) and escalate to the big model (Opus) only when the task genuinely requires it.

| Task type | Model |
|---|---|
| Orchestration, delegation, task routing | Sonnet |
| Boilerplate generation, known patterns, file edits | Sonnet |
| Running established skills against clear inputs | Sonnet |
| Judgment calls with ambiguous or conflicting requirements | Opus |
| Architectural decisions with real trade-offs | Opus |
| Security review on sensitive or novel code | Opus |
| Debugging where the root cause is genuinely unclear | Opus |
| Any task where Sonnet has already failed twice | Opus |

The Orchestrator uses Sonnet for routing and delegation. Workers use Sonnet by default. Either escalates to Opus when they hit a judgment call, an ambiguous spec, or a second failed attempt. This keeps cost predictable without sacrificing quality on the decisions that matter.

Invoke `model-route` to apply this tiering automatically across the agent pipeline.

For fully unattended runs, launch each terminal with:

```bash
claude --dangerously-skip-permissions
```

Or pre-approve the tools workers use most in `~/.claude/settings.json`:

```json
{
  "allowedTools": ["Bash", "Read", "Write", "Edit", "Glob", "Grep"]
}
```

This eliminates the approval prompts for routine file and shell operations without opening the full skip-permissions flag.

### Session Resilience (for unattended runs)

When workers run overnight, in loops, or across long autonomous tasks, two tools keep them alive:

**1. API Watchdog**: detects when a transient API error (529 overloaded, 429, 500, ECONNRESET) stalls a session at the prompt and auto-injects `Continue` with exponential backoff. Distinguishes transient errors from real usage limits (leaves those alone):

```bash
# Install (single file, no dependencies)
curl -O https://raw.githubusercontent.com/palios-taey/claude-code-api-watchdog/main/watchdog.py

# Dry-run first: logs every keystroke it WOULD send without sending any
python3 watchdog.py --sessions orchestrator,developer,qa --dry-run

# Live: escalate-only by default (never auto-restarts unless you pass --resume-cmd)
python3 watchdog.py --sessions orchestrator,developer,qa
```

Point `--sessions` at the exact tmux session names used in the 3-terminal setup above. Repo: `github.com/palios-taey/claude-code-api-watchdog`

**2. MCP Reconnect**: drives the `/mcp` → Reconnect menu sequence automatically after an MCP server restarts or drops. Eliminates the manual context-switch during the edit-restart-reconnect loop:

```bash
# Install
git clone https://github.com/palios-taey/mcp-reconnect && cd mcp-reconnect && sudo make install

# Reconnect all Claude Code sessions
mcp-reconnect

# Target specific sessions
mcp-reconnect orchestrator developer qa

# When calling FROM WITHIN a Claude Code session (e.g. a deploy script), always detach with a delay
nohup mcp-reconnect --delay 10 &>/dev/null & disown
```

Use `--path LABEL` to navigate submenus (e.g. `mcp-reconnect --path taeys-hands --path Reconnect`). Repo: `github.com/palios-taey/mcp-reconnect`

---

## Step 7: Ask About Background Component

Always ask, never skip. Use `AskUserQuestion`. Options: Spline, Vanta.js, Haikei, Framer marketplace, Grainient, Mesher, CSS Pattern, SVG Backgrounds, CSS-only (mesh gradient, animated gradient, noise texture), endlesstools.io, none.

Invoke the matching skill based on the user's choice:
- **Spline**: invoke `spline`: 3D scene setup, lazy loading, mobile fallback pattern
- **Vanta.js**: if mobile target, automatically apply `vanta-mobile-fix`
- **Haikei**: invoke `haikei`: SVG export, section divider patterns
- **Framer marketplace**: invoke `framer-marketplace`: free component browsing and code extraction
- **Grainient**: use `grainient.supply`: grain + gradient generator, export as WebP/SVG
- **Mesher**: use `mesher.io`: mesh gradient with precise color placement, exports SVG or CSS
- **CSS Pattern**: use `css-pattern.com`: pure CSS repeating patterns, zero HTTP cost
- **SVG Backgrounds**: use `svgbackgrounds.com`: scalable SVG backgrounds, free
- **endlesstools.io**: invoke `endlesstools`
- **CSS-only**: use `css-animations` for mesh gradient or animated gradient background

## Step 7.5: Lock Typography, Color, and Icons

After the Inspiration Brief is output and the background is chosen, lock these three before any UI code is written:

### Typography
- Use **Fontjoy** (`fontjoy.com`) to generate the heading + body pairing
- Validate each font in context on **Typewolf** (`typewolf.com`) before committing
- For variable fonts or editorial direction, check **Variable Fonts** (`v-fonts.com`)
- Apply `impeccable-style` tracking, line-height, and measure rules to the chosen pair

### Color
- Build the palette live on **Realtime Colors** (`realtimecolors.com`): validate in a real UI, not swatches
- Pick individual values using **oklch.com** (matches the project's CSS token standard)
- Use **Radix Colors** (`radix-ui.com/colors`) for semantic token scales with light + dark mode
- For quick full-palette direction, reference **Happy Hues** (`happyhues.co`)

### Icons
Choose one icon set and commit. Do not mix sets:
- **Lucide** (`lucide.dev`): default for shadcn projects. `npm install lucide-react`
- **Phosphor** (`phosphoricons.com`): when weight is a design variable (thin luxury → bold energy)
- **Tabler** (`tabler-icons.io`): when comprehensive coverage is needed (5000+)
- **Iconoir** (`iconoir.com`): when a slightly more geometric, distinctive style fits
- **SVG Repo** (`svgrepo.com`): for brand logos, one-off illustrations, anything outside a standard set

State the locked typography pair, color palette, and icon set explicitly before moving on.

### Quick Theme Option
If the project output is a **slide deck, document, or HTML artifact** (not a full web app), offer `theme-factory` as a shortcut: 10 pre-set color/font themes the user can browse and apply in one step instead of building tokens from scratch. Show the theme showcase, let the user pick, then apply. Can also generate a custom theme from a mood or brief.

### Push to Figma
After tokens are locked, invoke `figma-design-system` to automatically create a new Figma file for the project. The skill will:
- Create a file named `[Project Name]: Design System`
- Populate color styles, text styles, spacing tokens, border radius scale
- Build a Brand Foundation page with swatches and type specimen
- Create named component starter frames
- Return the direct Figma link

This runs via the `figma-developer-mcp` (already configured in `~/.claude/settings.json`). If the MCP is unavailable, output the tokens as a structured list for manual Tokens Studio import instead.

## Step 8: Engagement Design (for apps with user flows)

If the project has user flows, onboarding, or retention goals, apply:
- `hooked-ux`: identify the internal trigger and variable reward
- `psychology-principles`: apply Fitts, Hick, social proof, peak-end rule to key screens
- `ui-ux-pro-max`: layer advanced UX patterns on top: micro-interactions, empty states, error states, loading patterns, progressive disclosure

State the engagement design decisions before building.

## Step 9: Tech Stack and Database Selection

If an existing project was detected in Step 2, inspect the codebase and suggest the database, ORM, and auth options that best integrate with the current codebase (e.g., "Hey, since this is a Next.js project, I think it's good to have Supabase for our database and Clerk for auth"). Present these as active suggestions to the user.

If starting a new project, use `AskUserQuestion` for BOTH questions. Always present options: never assume or skip. The user must actively choose.

### Tech Stack
Ask which framework/language to use. Present relevant options based on platform detected in Step 4:

**Web options:** Next.js (App Router), Remix, Astro, plain React + Vite, Vue/Nuxt, SvelteKit
**Mobile options:** SwiftUI, UIKit, Flutter, React Native, Expo
**Backend options (if needed):** Next.js API routes, Express, Fastify, NestJS, Django, FastAPI, Go
*Note:* If building API endpoints or backend routers, always invoke the `api-design` and `rest-api-design` skills to ensure clean resource modeling, REST standards, versioning, and OpenAPI/Swagger documentation.
**AI app:** Add Claude API or Azure AI to whichever framework is chosen

### Database
Ask which database to use. Present these options with their use cases:

| Database | Best for |
|---|---|
| **PostgreSQL** (via Supabase or Railway) | Default choice: relational data, full SQL, scales well |
| **MySQL** | Existing MySQL infra, WordPress-adjacent, simpler queries |
| **SQLite** | Local-first apps, small scale, no server needed |
| **MongoDB** | Flexible schema, document data, rapid iteration |
| **Supabase** | Postgres + auth + realtime + storage in one, great DX |
| **PlanetScale** | MySQL-compatible, serverless, branching workflow |
| **Turso (libSQL)** | Edge/SQLite, low latency, great for global apps |
| **Redis** | Cache, sessions, queues, pub/sub |
| **Firestore** | Firebase ecosystem, realtime sync, mobile-first |
| **Neon** | Serverless Postgres, great with Vercel |
| **None** | No database needed (static site, API-only consumer) |

Once the user chooses:
- Lock the ORM and invoke the matching skill:
  - JS/TS default: invoke `prisma`
  - Edge/serverless/lightweight: invoke `drizzle`
  - Supabase chosen: invoke `supabase` (covers DB + auth + storage in one)
  - SQLAlchemy (Python), GORM (Go): no skill, use docs
- If PostgreSQL or Supabase is selected, invoke the `supabase-postgres-best-practices` skill to guarantee optimal database query performance, indexing, schema design, Row-Level Security (RLS) configuration, and connection scalability.
- Note connection pooling if serverless: PgBouncer or Supabase's built-in pooler

### Auth Selection

Ask which auth library to use and invoke the matching skill:
- **Clerk**: invoke `clerk`: best for Next.js, pre-built UI, orgs, MFA, passkeys
- **NextAuth**: invoke `nextauth`: open-source, free, OAuth + credentials, full control
- **Supabase Auth**: already covered if `supabase` is the database choice
- **None**: public-only app, no auth needed

### Caching Selection

If a database (e.g. Postgres, MySQL, MongoDB) was chosen, ask: "Do you want to add Redis Cache to our stack so the database/server doesn't get overloaded under traffic?"
- **Yes**: Add Redis to the stack and invoke the `redis-patterns` skill. If high concurrency or millions of users are expected, also invoke the `redis-connections` (for connection pooling, pipelining, and client-side caching) and `redis-clustering` (for slot tagging and read replicas) skills.
- **No**: Proceed without cache.

### Pipeline Selection

Ask: "Do you want to configure an automated CI/CD pipeline (GitHub Actions) for testing, security gates, and deployments?"
- **Yes**: Add GitHub Actions to the stack and invoke the `github-actions-docs` and `deployment-pipeline-design` skills to implement multi-stage build, test, scan, and deploy pipelines.
- **No**: Proceed with manual deployments.

### Observability Selection

Ask: "Do you want to implement structured logging and observability (Stripe-style canonical log lines)?"
- **Yes**: Add structured logging to the stack and invoke the `logging-best-practices` skill to implement context-rich wide events per request with middleware.
- **No**: Proceed with standard console logging.

### Confirm Full Stack

Output the complete locked stack before proceeding:

```
Framework:       [chosen framework]
Language:        [TypeScript / Swift / Dart / Python / etc.]
Base UI:         shadcn-ui (web) | native (Apple) | Material (Flutter)
Primary UI:      skiper-ui (web) | platform native
Supplementary:   21st-dev, getdesign, reactbits, free-design-resources
Background:      [chosen in Step 7]
UI Standard:     [chosen in Step 4]
Animation:       [chosen in Step 5] + lenis (smooth scroll, web only)
Brand:           [colors, fonts locked in Step 3]
Database:        [chosen database]
ORM:             [prisma / drizzle / SQLAlchemy / etc.]
Auth:            [clerk / nextauth / supabase-auth / none]
Caching:         [redis (via redis-patterns, redis-connections, redis-clustering) / none]
API Design:      [api-design, rest-api-design / none]
DB Optimization: [supabase-postgres-best-practices / none]
Pipeline:        [github-actions (via github-actions-docs, deployment-pipeline-design) / none]
Observability:   [logging-best-practices / none]
Validation:      zod (all API routes + env vars)
Hosting:         [Netlify / Vercel / Railway / Fly.io / App Store / etc.: ask if not obvious]
MCP:             [list any MCP servers being connected, or none]
```

## Step 10: Output Folder Structure

Read `references/project-structure.md` and output the full folder structure. Rules always apply:
- Clean architecture: frontend, backend, api, shared: always separate
- `constants/` in every top-level dir: no exceptions
- Env vars never accessed directly: always through `config/env.ts`
- Shared types in `shared/types/`

## Step 11: Enforce Security Standards

Run `/security-scan` (ECC: 1,282 security tests across CLAUDE.md, MCP configs, hooks, and skills). Then confirm before handoff:
- `.env` created, `.env.example` committed, `.env` in `.gitignore`
- `config/env.ts` with `zod` validation: invoke `zod` skill: no raw `process.env` anywhere
- Auth library confirmed (`clerk`, `nextauth`, or `supabase` auth): never custom auth
- `zod` validators in `api/validators/` for all input boundaries
- Security headers in `next.config.ts`
- ORM confirmed (Prisma or Drizzle): no raw SQL string concatenation
- `npm audit` runs as part of scaffold
- Rate limiting on all public API routes: invoke `api-rate-limiting` skill to implement token bucket, fixed window, or sliding window rate limit algorithms (using Redis if distributed).

## Step 12: Hand Off to autonomous-engineer

Invoke `autonomous-engineer`. Pass: goal, audience, platform, stack, UI standard, animation approach, background, brand tokens, engagement design decisions, folder structure, and security requirements.

```
architect_planner (supervisor)
  ↓ delegates with locked plan
developer_engineer (worker) → structured outcome
  ↓
qa_evaluator (worker) → structured outcome
  ↓
test_engineer (worker) → structured outcome
  ↓
tdd_auto_fixer (worker) → structured outcome
  ↓
architect_planner reviews all outcomes, decides next delegation
```

Workers run their local loops to completion before surfacing. Each delivers a structured outcome (status, artifacts, decisions, blockers, next steps) to `architect_planner`. The supervisor wakes on outcomes, not on check-in intervals.

## Step 12.5: Run Graphify (optional, requires API key)

If `ANTHROPIC_API_KEY` is set in the environment, run graphify to map the codebase and auto-open the knowledge graph:

```bash
gfy
```

(`gfy` is the alias for `graphify . && open graphify-out/graph.html`)

If no API key is set, skip this step silently. When available, it generates:
- `graphify-out/graph.html`: interactive knowledge graph (auto-opens)
- `graphify-out/GRAPH_REPORT.md`: key insights and suggested questions
- `graphify-out/graph.json`: queryable graph data

## Step 13: Design Quality Gate

Before marking the build complete, run a final pass using:
- `refactoring-ui`: hierarchy, spacing, color: fix violations
- `ux-heuristics`: check all 10 heuristics, flag severity 3+ issues
- `taste`: does it look like only this product, or like every other app?
- `impeccable-style`: typography tracking, widow fix, spacing scale, hover/focus/active states
- `webapp-testing`: Playwright-based functional testing of the live app: invoke when the project has interactive UI flows that need verification beyond visual QA

Do not call the build done until this gate passes.

## Step 14: App Store Prep (mobile apps only)

If the project targets iOS, Android, or both, run `appstore-guidelines` checklist:
- App icon at correct sizes (1024x1024 for iOS, 512x512 for Android)
- Screenshots at required device sizes
- Privacy labels accurate
- Demo account + setup steps written for App Review Notes
- In-App Purchase via Apple IAP if selling digital content

## Step 15: End of Build

Two things always happen at the end:

**n8n offer**: If any part of the project involved repeating triggers, API calls, or multi-service workflows, use `AskUserQuestion` to ask: "Want to automate any part of this with n8n?" with options:
- **Yes, automate it**: invoke `n8n` skill: ask follow-up questions (what to trigger, what to connect, what the output should be) using `AskUserQuestion`, then output a ready-to-paste n8n AI prompt and setup instructions
- **Not right now**: skip and move to next steps
- **Tell me what could be automated**: list 3-5 specific automation opportunities based on what was just built, then ask again

**Test before push**: Open all changed/new files in the browser using `open` and tell the user: "Test it locally: let me know when everything looks good and I'll push." Do NOT run `git commit` or `git push` until the user explicitly confirms (e.g. "good", "looks good", "push it").

**What's next**: Present a list of 4-5 contextual next steps based on what was just built. Include relevant offers from:
- `doc-coauthoring`: if the project needs a PRD, spec, proposal, or decision doc written with the 3-stage (context → refinement → reader testing) workflow
- `internal-comms`: if the project involved a team workflow and needs 3P updates, a launch newsletter, FAQ, or incident write-up
- `skill-creator`: if what was just built follows a repeatable pattern the user will want to invoke again, offer to turn it into a reusable skill with evals

---

## Additional Resources

- **`references/ui-standards.md`**: design standards, component stack, background tools, 3-terminal template
- **`references/project-structure.md`**: clean architecture folder layout, constants rules, env var handling
- **`references/security.md`**: full OWASP checklist, auth rules, input validation, env var handling, security headers
