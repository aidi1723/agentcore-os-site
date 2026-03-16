# Site Changelog

## 2026-03-16 — Homepage demo section, local-first review flow, and ECS deployment record

This round added a homepage demo block for the ECS site, formalized a safer local-first review workflow, and recorded the deployment / rollback details so future maintenance can query what happened quickly.

### Scope of this round

The work covered four connected parts:

1. **Homepage demo placement**
2. **Local preview and copy refinement**
3. **ECS deployment and rollback discipline**
4. **Repository traceability cleanup**

---

## 1. Homepage demo placement

A product demo video (`demo/demo.mp4`) was added to the homepage.

### Placement decision

The chosen position was:

- directly below the Hero section
- before the trust bar

This keeps the public flow coherent:

- product positioning first
- real workflow demo second
- trust / capability cues after that

### What was added

- new homepage demo block in `index.html`
- local video asset at `demo/demo.mp4`
- bilingual headline/copy around the demo area

### Related commit

- `68df7f5` — `feat: add homepage demo section and video`

---

## 2. Local preview and copy refinement

This round also established a clearer execution pattern for ECS site work.

### Workflow used

1. edit locally in `agentcore-os-site-ecs-cn`
2. preview locally first
3. get user confirmation
4. deploy to ECS separately
5. verify on ECS itself and via public fetch

### Local preview entry

The demo version was reviewed locally through:

- `http://127.0.0.1:8016`

### Copy refinement

After the first deployment, the homepage demo CTA wording was refined from a more literal bilingual-style button copy into a more natural reading flow:

- `查看使用说明 · Docs`
- `继续看产品介绍`

That wording update was then synced to ECS as a follow-up deployment.

---

## 3. ECS deployment and rollback discipline

A useful deployment lesson came out of this round.

### Key environment fact

For the live ECS website, nginx serves:

- `/usr/share/nginx/html`

It does **not** serve:

- `/root/agentcore-os-site`

This means editing files under `/root/agentcore-os-site` does not change the live public site.

### Rollback record

Before the final local-first flow was re-established, a direct demo insertion on ECS was rolled back safely.

The live homepage was restored from:

- `/usr/share/nginx/html/index.html.bak-20260316-084402-predemo`

The previously uploaded demo directory was preserved rather than deleted:

- `/usr/share/nginx/html/demo.disabled-20260316-084613`

### Final deployed files

After local confirmation, the confirmed ECS homepage version was deployed to:

- `/usr/share/nginx/html/index.html`
- `/usr/share/nginx/html/demo/demo.mp4`

### Verification pattern that worked

Server-local verification:

- `curl http://127.0.0.1/`
- `curl -I http://127.0.0.1/demo/demo.mp4`

Public-side confirmation:

- fetch/check `http://59.110.93.188/`

---

## 4. Repository traceability cleanup

To make future lookup easier, this round also tightened repository hygiene.

### Repo hygiene updates

- committed deployable homepage/video files into git
- kept local rollback backup files out of version control
- added local backup ignore rule:
  - `index.html.bak-local-demo-*`

### Current repo state after this round

The repo now contains both:

- the homepage demo/video addition
- the later CTA wording refinement

This makes the repo easier to use as the source of truth for future ECS updates.

---

## Recommended next follow-ups

If future work continues, the most reasonable next steps are:

1. add a lightweight screenshot-based review habit before ECS uploads for homepage changes
2. continue tightening English copy so CN / EN button tone stays equally natural
3. keep recording ECS deployment rounds in this changelog whenever live-site behavior diverges from local assumptions

---


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
