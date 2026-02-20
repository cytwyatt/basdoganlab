# Website Maintenance Instructions

This document is for lab members who maintain the Basdogan Lab website.

## 0. Run Locally

### Prerequisites
1. Node.js 20+ installed.
2. Run from repository root: `basdoganlab/`.

### Install dependencies
1. Run:
   ```bash
   npm ci
   ```

### Development mode (hot reload)
1. Start dev server:
   ```bash
   npm run dev -- --host 127.0.0.1 --port 4321
   ```
2. Open:
   - `http://127.0.0.1:4321/basdoganlab`
3. Stop server with `Ctrl + C`.

### Production preview (same output as deploy)
1. Build:
   ```bash
   npm run build
   ```
2. Preview:
   ```bash
   npm run preview -- --host 127.0.0.1 --port 4321
   ```
3. Open:
   - `http://127.0.0.1:4321/basdoganlab`
4. Stop server with `Ctrl + C`.

### Quick local verification checklist
1. Home: `/basdoganlab`
2. Research: `/basdoganlab/research`
3. News: `/basdoganlab/news`
4. Publications: `/basdoganlab/publications`
5. Team: `/basdoganlab/team`
6. Contact: `/basdoganlab/contact`

## 1. Page-by-Page Update Guide

Use this section to update each public page explicitly: Home, Research, News, Publications, Team, and Contact.

### Home page (`/`)
File: `src/pages/index.astro`
1. Update hero title, subtitle, and call-to-action text in the top section.
2. Update the three summary cards (Research, Publications, Join Us) as needed.
3. Keep links using `getPermalink(...)` for project-site compatibility.
4. Run `npm run build` and confirm `/` renders as expected.

### Research page (`/research`)
Main listing file: `src/pages/research.astro`
Research data source: `src/data/research.ts`
Detail page template: `src/pages/research/[slug].astro`
1. Edit research content in `src/data/research.ts` only.
2. For each area, maintain:
   - `slug`
   - `title`
   - `summary`
   - `overview`
   - `focus`
   - `methods`
   - `recentDirections`
3. The listing page and detail pages update automatically from this data.
4. Run `npm run build` and verify:
   - `/research`
   - `/research/<slug>` for each research area.

### News page (`/news`)
Content folder: `src/content/news/`
Rendered page file: `src/pages/news.astro`
1. Add one Markdown file per news item: `YYYY-MM-DD-title.md`.
2. Use required frontmatter:
   ```md
   ---
   title: "Your title"
   date: 2026-02-20
   summary: "One sentence summary."
   tags: ["tag1", "tag2"]
   draft: false
   ---
   ```
3. Add optional body text below frontmatter.
4. Keep `draft: false` for published items.
5. Run `npm run build` and verify newest items appear first on `/news`.

### Publications page (`/publications`)
Data file: `src/data/publications.json`
Rendered page file: `src/pages/publications.astro`
1. Add or edit publication objects in `src/data/publications.json`.
2. Required fields:
   - `title`
   - `authors`
   - `venue`
   - `year`
3. Optional fields:
   - `doi`
   - `url`
   - `pdf`
   - `highlight`
4. Keep JSON valid (double quotes, commas, no trailing commas).
5. Run `npm run build` and verify order and links on `/publications`.

### Team page (`/team`)
Data file: `src/data/team.json`
Rendered page file: `src/pages/team.astro`
1. Add or edit team entries in `src/data/team.json`.
2. Required fields:
   - `name`
   - `role`
   - `photo`
   - `order`
3. Optional fields:
   - `email`
   - `website`
   - `researchInterests`
4. Put images in `public/images/` and reference as `/images/...`.
5. Use `order` to control display sequence.
6. Run `npm run build` and verify `/team`.

### Contact page (`/contact`)
File: `src/pages/contact.astro`
1. Update PI name, email, office location, and application instructions.
2. Keep contact email and role text concise and current.
3. Run `npm run build` and verify `/contact`.

### Standard update sequence (for any page)
1. Create a branch: `git checkout -b feature/<short-name>`.
2. Make edits in the correct source file(s).
3. Run checks:
   - `npm ci` (if dependencies changed)
   - `npm run build`
4. Preview locally with `npm run dev`.
5. Open PR to `main`, get review, merge.

## 2. Release Process

### Branch and PR workflow
1. Create feature branch: `git checkout -b feature/<short-name>`.
2. Commit changes with clear message.
3. Open pull request to `main`.
4. Require at least one reviewer before merge.

### Required checks before merge
1. `npm ci`
2. `npm run build`
3. Verify affected page locally with `npm run dev`.
4. Confirm JSON/Markdown schema compliance.

### Deploy verification checklist
1. Confirm GitHub Actions `deploy.yml` completed successfully.
2. Open public URL and verify:
   - Home loads
   - navigation links to all 6 pages
   - updated content visible
3. Check mobile viewport for layout regressions.
4. Confirm no console/network errors in browser dev tools.

### Rollback procedure
1. Identify last known-good commit from `main` history.
2. Revert bad commit(s):
   ```bash
   git checkout main
   git pull
   git revert <bad-commit-sha>
   git push
   ```
3. Wait for auto-deploy and verify site restored.

## 3. Operations Schedule

### Monthly
- Run dependency updates (`npm outdated`, then selective updates).
- Rebuild and verify deploy.

### Quarterly
- Link check on all pages.
- Remove stale announcements and outdated opportunities.

### Each semester
- Validate team roster and roles.
- Archive alumni or update positions.

### Yearly
- Clean publication metadata consistency:
  - author order
  - venue naming
  - DOI/link validity

## 4. Domain and DNS Operations

### CNAME ownership
- Source of truth: root `CNAME` file.
- Must match custom domain configured in GitHub Pages.

### DNS expectations
- Apex domain: A/ALIAS records for GitHub Pages.
- `www`: CNAME to `<org-or-user>.github.io`.
- DNS changes may take minutes to 48 hours.

### HTTPS verification
1. In GitHub Pages settings, wait until certificate is issued.
2. Enable "Enforce HTTPS".
3. Verify browser shows secure lock icon.
4. Verify no mixed-content warnings in console.

## 5. Troubleshooting

### Build fails
1. Run `npm ci` again to reset dependencies.
2. Check JSON syntax in `src/data/*.json`.
3. Check frontmatter fields in `src/content/news/*.md`.
4. Re-run `npm run build` and read first error.

### Missing image/content
1. Confirm file exists under `public/images/`.
2. Confirm path starts with `/images/...` in JSON/pages.
3. Confirm content file names are unique and valid.

### GitHub Pages propagation delay
- New deploys may take a few minutes.
- Domain and certificate changes can take significantly longer.
- Check Actions logs and Pages settings before re-deploying.

## 6. Ownership Matrix

- PI content delegate:
  - approves scientific wording, publication visibility, and people updates.
- Technical maintainer:
  - owns CI/CD workflow, deployment health, and DNS/domain settings.
- Backup reviewer:
  - can approve urgent hotfix PRs and execute rollback if primary maintainer is unavailable.
