# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static HTML prototypes for the **DT-Trak Medical Coder Training Portal**, intended to be embedded in **Microsoft Power Pages**. Each file is a self-contained page with inline CSS and vanilla JS — no build step, no bundler, no package manager.

## Local development

```bash
python3 -m http.server 8080
# then open http://localhost:8080/
```

There are no tests, linters, or build commands.

## Page inventory

| File | Purpose |
|---|---|
| `index.html` | Employee dashboard (home) + Manager View toggle |
| `02-modules.html` | All 25 training modules listing |
| `03-module-player.html` | Single module player (template for all modules) |
| `04-resources.html` | Resource library |
| `05-checklist.html` | New-employee onboarding checklist |
| `project-dashboard-live.html` | Live project dashboard (requires Azure AD + SharePoint) |

## Architecture patterns

**Self-contained pages** — every page duplicates the nav, CSS custom properties (`:root` design tokens), Bootstrap 5.3 + Bootstrap Icons CDN links, and Google Fonts (Inter). There is no shared stylesheet or component file; changes to the nav or color system must be applied to each file.

**Design tokens** — all colors are defined as CSS custom properties on `:root`: `--dt-navy`, `--dt-blue`, `--dt-blue-lt`, `--dt-green`, `--dt-green-lt`, `--dt-orange`, `--dt-gold`, `--dt-bg`, `--dt-white`, `--dt-gray`, `--dt-border`, `--dt-shadow`.

**State persistence** — `05-checklist.html` stores checkbox state in `localStorage` keyed by employee name (`dt_checklist_portal_v1_<name>`). The dashboard's checklist widget reads the same keys to render the sidebar summary. When the portal goes live, these `localStorage` calls should be replaced with Dataverse/SharePoint API calls.

**Manager View** (index.html) — toggled client-side with `toggleManagerView()`. Employee data is hardcoded in a `EMPLOYEES` array and rendered via `renderMgrTable()`. The manager dark-theme view and employee light-theme view are in the same HTML, shown/hidden with CSS classes.

**Module player** (`03-module-player.html`) — one template page intended to serve all 25 modules. In the live Power Pages portal, it becomes a dynamic page at `/module/{id}` backed by a Dataverse `Training_Module` table.

**Power Pages integration** — every page has a comment block at the top explaining which Liquid template variables (`{{ user.fullname }}`, `{{ record.dt_stream_url }}`, etc.) to substitute and which Dataverse tables to connect. Treat these comments as the handoff spec for the Power Pages developer.

**`project-dashboard-live.html`** is distinct from the training portal — it uses MSAL.js for Azure AD auth and Chart.js for visualizations, and requires an Azure AD App Registration and SharePoint list to function.
