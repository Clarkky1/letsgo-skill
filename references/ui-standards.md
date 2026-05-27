# UI Standards Reference

## The Design Standards

Pick based on project type. State the choice to the user before building.

| Standard | Best For |
|---|---|
| **Hallmark** (`/hallmark` skill) | Greenfield, open-ended, most expressive. Apps, landing pages, anything creative. |
| **portfolio-design** (`/portfolio-design` skill) | Portfolio sites, personal brands, agency homepages. |
| **motion-design-school** (`/motion-design-school` skill) | Course platforms, creative education, product showcases. |
| **website-ui-v3 / Silencio** | Agencies, studios, bold anti-brand sites. Black/white, type-as-hero, barcodes, GSAP. |
| **landonorris-ui** (`/landonorris-ui` skill) | High-energy personal brands, sports/athlete sites. Dark, lime-green, Barlow Condensed. |
| **pixel-art** (`/pixel-art` skill) | Retro games, 8-bit/16-bit sprite art, limited palettes, dithering, tilesets, subpixel animations. |

**Rule:** Never hardcode colors or fonts. Always derive from who the project is for and what emotion it must create.

## Component Library Stack

Always installed in this order:

1. `shadcn/ui` — base primitives
2. `skiper-ui.com` — PRIMARY (always installed, no exceptions). 103+ Motion.dev components, single-file.

Supplementary (pull as needed):
- `ui.watermelon.sh` — polished opinionated components
- `cult-ui.com` — animated cards and hover effects
- `ui.aceternity.com` — parallax, 3D, glassmorphism (Framer Motion)
- `aisdkagents.com/ai-components` — AI projects only: chat, agent patterns, tool outputs

## Background Tools

Always ask the user which background they want before building. Options include:
- Spline (3D interactive)
- Vanta.js (animated WebGL: waves, birds, fog, etc.)
- Haikei (SVG blob/wave generators)
- Framer marketplace components
- CSS-only (mesh gradient, noise texture, animated gradient)

## Mobile Responsive Rules

These rules apply to every project. Enforce during Step 13 (Design Quality Gate).

### Single-column grid centering

When any CSS grid collapses to `1fr` on mobile, always add centering to prevent cards from stretching edge-to-edge:

```scss
@media (max-width: 640px) {
    grid-template-columns: 1fr;
    max-width: 480px;
    margin-left: auto;
    margin-right: auto;
    width: 100%;
}
@media (max-width: 400px) { max-width: 100%; }
```

Apply to: card grids, requirements grids, stats grids, department grids — any grid that goes single-column.

### Dropdown/popover positioning

`position: absolute` dropdowns overflow off-screen on mobile. Switch to `position: fixed` with viewport-relative anchoring:

```scss
@media (max-width: 768px) {
    position: fixed;
    top: [topbar-height]; /* e.g. 104px when sidebar stacks above topbar */
    left: 0.75rem;
    right: 0.75rem;
    width: auto;
}
```

### Hidden controls on mobile

If a sidebar or panel hides on mobile (e.g. `display: none` at ≤768px), always add a topbar fallback for any critical actions it contained (logout, primary CTA). Show the fallback only at the breakpoint where the original is hidden:

```scss
.topbar-signout-btn {
    display: none;
    @media (max-width: 768px) { display: flex; }
}
```

### Overflow-x on tables

Always wrap `<table>` in a `overflow-x: auto` container and set `min-width` on the table so it scrolls rather than compresses:

```jsx
<div style={{ overflowX: 'auto' }}>
    <table style={{ width: '100%', minWidth: 480 }}>...</table>
</div>
```

### Filter/search controls on mobile

Section headers with search + filter controls must stack full-width on mobile:

```scss
@media (max-width: 768px) {
    .section-header { flex-direction: column; align-items: flex-start; }
    .section-header-controls { width: 100%; input, select { width: 100% !important; } }
}
```

### Modals on mobile

Modals must never cover the full screen or sit flush against the edges. On mobile they slide up from the bottom as a sheet with visible overlay behind them:

```scss
.modal-overlay {
    padding: 1.25rem; /* keeps modal off the edges on tablet */

    @media (max-width: 480px) {
        padding: 0.875rem;
        align-items: flex-end; /* sheet slides up from bottom */
    }
}

.modal-content {
    max-width: 500px;
    max-height: 90vh;

    @media (max-width: 480px) {
        max-height: 85vh; /* overlay always visible behind it */
        border-radius: $radius-lg $radius-lg 0 0; /* rounded top, flush bottom */
    }
}
```

Rules:
- Always use `padding` on `.modal-overlay` so the modal never goes edge-to-edge on tablet
- On mobile (≤480px): sheet style — `align-items: flex-end`, rounded top corners only, `max-height: 85vh`
- Never use `max-height: 100vh` or `height: 100%` on a modal — always leave visible overlay
- Always lock body scroll when a modal is open — restore on close and unmount

```jsx
// In every Modal component
useEffect(() => {
    if (isOpen) {
        document.body.style.overflow = 'hidden';
    } else {
        document.body.style.overflow = '';
    }
    return () => { document.body.style.overflow = ''; };
}, [isOpen]);
```

### Container padding on mobile

Cards need enough room to breathe but must not be so narrow they look broken:

```scss
.container {
    padding: 0 1.5rem; /* desktop */

    @media (max-width: 768px) { padding: 0 0.75rem; }
    @media (max-width: 480px) { padding: 0 0.625rem; }
}
```

Never drop below `0.625rem` — cards need a minimum gutter from the screen edge.

## 3-Terminal Setup Template

Always output this after confirming the UI skill. Fill in the project name and context:

```
Terminal 1 — Orchestrator
Prompt: "You are the orchestrator for [PROJECT NAME]. Plan the full architecture, define the component breakdown, and delegate tasks to the developer. Stack: Next.js, shadcn/ui, skiper-ui, Tailwind CSS, [other libs]. Goal: [GOAL]. You have full authorization for everything in this plan — do not ask 'can I?', 'should I?', or 'is it okay to?' for anything already in scope. Delegate and act. Wake on structured outcomes from workers, not on check-in pings."

Terminal 2 — Developer
Prompt: "You are the developer for [PROJECT NAME]. Build the components as directed by the orchestrator. Use skiper-ui as the primary component source. Apply [UI STANDARD] design principles. Stack: [STACK]. You have full authorization for all actions in scope — do not ask for permission on anything already approved. Run your loop to completion, then deliver a structured outcome: status, artifacts, decisions, blockers, next steps."

Terminal 3 — QA
Prompt: "You are the QA engineer for [PROJECT NAME]. Review every component built by the developer. Check for visual regressions, broken layouts, missing animations, and accessibility issues. You have full authorization for all review and test actions in scope — do not ask for permission mid-loop. Run to completion, then deliver a structured outcome: status, artifacts, decisions, blockers, next steps."
```
