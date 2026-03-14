# AgentCore OS Site

Official landing site for AgentCore OS.

## What this repo contains

This repository currently serves two roles at the same time:

1. **Public landing site** for AgentCore OS
2. **Public documentation entry** for the China-facing ECS website

It is no longer just a single homepage. The public site now includes:

- the main landing page: `index.html`
- a bilingual docs section under `docs/`
- real public download links for the current release
- ECS-published static pages used as the live website

## Stack

- Static HTML / CSS / JS
- No frontend build step required
- ECS static-site deployment via nginx

## Site structure

### Homepage

- `index.html`
  - landing page
  - product positioning
  - download section
  - developer / architecture / use-case sections
  - CN / EN switching

### Docs section

The docs directory is now a **true bilingual static docs section**, not just a group of Chinese placeholder pages.

- `docs/index.html` — docs hub
- `docs/getting-started.html` — getting started
- `docs/usage-notes.html` — usage notes and reading paths
- `docs/install-deploy.html` — install and deploy notes for the public site
- `docs/workflows.html` — workflow examples
- `docs/faq.html` — FAQ

## Bilingual behavior

Both the homepage and the docs pages are expected to support Chinese and English consistently.

### Docs language behavior

The docs pages use a shared pattern:

- CN / EN language switch in the top area
- a `setLanguage(...)` function per page
- browser persistence via:

```js
localStorage.setItem('agentcore-docs-lang', lang)
```

and:

```js
localStorage.getItem('agentcore-docs-lang') || 'zh'
```

### Maintenance rule

When adding or editing docs pages, do **not** ship Chinese-only content and assume the English layer will be filled later.

For every new page or section, keep these in sync:

- visible Chinese copy
- visible English copy
- navigation labels
- pager labels
- button labels
- `setLanguage(...)` mapping
- any `id` values used for language replacement

If one of those is missed, the page will fall back into the old problem state:

- partial English
- untranslated Chinese blocks in EN mode
- mismatched navigation
- “homepage is bilingual, docs are not” inconsistency

## Local preview

You can preview the site in either of these ways:

### Option A

Open `index.html` or `docs/*.html` directly in the browser.

### Option B

Run a simple static file server:

```bash
cd agentcore-os-site-ecs-cn
python3 -m http.server 8000
```

Then open:

- <http://localhost:8000>

## Change summary

- `docs/SITE_CHANGELOG.md` — summary of the 2026-03-14 homepage i18n repair, bilingual docs rebuild, deployment pitfall, and maintenance-documentation work.

## Live site

Current ECS site:

- <http://59.110.93.188/>

Current docs entry:

- <http://59.110.93.188/docs/index.html>

## Current public downloads

- macOS (Apple Silicon / aarch64 DMG): <http://59.110.93.188/downloads/AgentCore.OS_0.2.0-beta.2_aarch64.dmg>
- Windows (x64 EXE installer): <http://59.110.93.188/downloads/AgentCore.OS_0.2.0-beta.2_x64_setup.exe>
- Windows compatibility alias (legacy filename kept alive): <http://59.110.93.188/downloads/AgentCore%20OS_0.2.0-beta.2_x64-setup.exe>

## Assets

- `assets/logo.png` — brand logo copied from desktop source provided by the user.

## ECS deployment notes

This repository is currently deployed as a static site on ECS and served by nginx.

Typical update flow:

1. edit local HTML files
2. verify locally when needed
3. sync files to ECS
4. reload and verify the real site in a browser
5. commit and push the repo so source and live site stay aligned

## Important deployment pitfall

When replacing the `docs/` directory on ECS with `scp` + `mv`, file ownership and permissions may become too restrictive.

A real failure already happened during deployment:

- directory became `700`
- files became `600`
- nginx returned `403 Forbidden` for `/docs/`

If docs pages suddenly become inaccessible after deployment, check permissions first.

Recommended static-site permissions:

- directories: `755`
- files: `644`

## Current maintenance expectations

For this repo, “done” means all three stay aligned:

- local source
- GitHub repository
- ECS live site

Do not leave the live ECS page ahead of the repo for long, and do not treat a browser-side patch as complete until it is committed back to source.
