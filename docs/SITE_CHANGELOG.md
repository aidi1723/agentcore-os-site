# Site Changelog

## 2026-03-14 — Homepage i18n repair, bilingual docs rebuild, and maintenance documentation

This round turned the public ECS website from a partially localized landing page into a more consistent public site with a bilingual docs section.

### Scope of this round

The work covered three layers:

1. **Homepage English repair**
2. **Docs section bilingual rebuild**
3. **Repository maintenance documentation**

---

## 1. Homepage English repair

The homepage originally had a real bilingual-switch gap.

### Problems that existed

In English mode, some parts of the homepage still stayed in Chinese, especially around:

- the download section
- the documentation section
- the top-right `国内仓库` button
- the native `details/summary` fold blocks

This created a broken experience where the page looked only partially translated.

### What was fixed

- Added the missing homepage translation wiring for:
  - `Downloads and China Access Entry Points`
  - `Documentation`
- Fixed the top-right navigation button so `国内仓库` becomes `China Mirror` in English mode
- Replaced the native `details/summary` fold blocks with custom expandable UI so the English page no longer depends on browser-native Chinese prompts such as `展开`
- Standardized fold labels in English mode to controllable text like:
  - `Expand`
  - `Collapse`

### Related commits

- `1ebf352` — `Fix English translations for download and docs sections`
- `5bfb3e2` — `Polish English navigation and fold toggles`

---

## 2. Docs section bilingual rebuild

The docs section used to be the biggest consistency gap.

### Problems that existed

The homepage had already moved toward bilingual behavior, but the docs pages were still mostly static Chinese pages with limited or incomplete English behavior.

That caused a split experience:

- homepage looked bilingual
- docs pages were still mostly Chinese
- navigation and reading flow were not aligned

### What changed

The docs section was rebuilt into a real bilingual static docs layer.

Updated pages:

- `docs/index.html`
- `docs/getting-started.html`
- `docs/usage-notes.html`
- `docs/install-deploy.html`
- `docs/workflows.html`
- `docs/faq.html`

### Shared docs behavior

The rebuilt docs pages now follow a shared pattern:

- unified top navigation
- CN / EN switch on every page
- per-page `setLanguage(...)` wiring
- language persistence via:

```js
localStorage.setItem('agentcore-docs-lang', lang)
localStorage.getItem('agentcore-docs-lang') || 'zh'
```

### Result

The docs section is no longer just a collection of Chinese placeholders.

It is now a true bilingual docs layer that matches the homepage better and gives the public site a clearer documentation structure.

### Related commit

- `3a917f0` — `Build bilingual docs section`

---

## 3. ECS deployment pitfall found during this round

A real deployment problem appeared while syncing the rebuilt docs directory to ECS.

### Failure mode

After replacing `/usr/share/nginx/html/docs` via `scp` + `mv`, permissions became too restrictive:

- directory permissions became `700`
- file permissions became `600`

That caused nginx to return:

- `403 Forbidden`

### Resolution

Permissions were corrected back to the expected static-site shape:

- directories: `755`
- files: `644`

### Maintenance takeaway

When replacing static directories on ECS, check permissions immediately if the site suddenly becomes inaccessible.

---

## 4. Repository maintenance documentation

The repository README was expanded so the repo can be understood and maintained more easily.

### README additions

The updated README now documents:

- that this repo is no longer just a landing page repo
- the role of `index.html` and `docs/`
- bilingual docs maintenance expectations
- language persistence behavior
- ECS deployment flow
- the `403 Forbidden` permission pitfall
- the rule that local source, GitHub, and ECS live site should stay aligned

### Related commit

- `e4cd97e` — `Document bilingual docs site maintenance`

---

## Current state after this round

After this work, the following are back in sync:

- local source
- GitHub repository
- ECS live site
- repository maintenance README

The site is still a static site, but it now has a much clearer public structure:

- bilingual homepage
- bilingual docs hub
- public download entry points
- documented maintenance expectations

---

## Recommended next follow-ups

If future work continues, the most reasonable next steps are:

1. run another full English-state audit across the live site to catch any remaining Chinese fragments
2. continue strengthening public-facing docs and release summaries as the public scope evolves
3. keep future homepage and docs changes bilingual from the start, instead of patching English later
