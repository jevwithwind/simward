# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project

One-page static landing site for **SimWard**, an individual student concept for an AI prototyping course (autumn 2026): a synthetic-data workflow for hospital analytics, with a Chatbase chat assistant embedded on the page.

- Repo: `jevwithwind/simward`
- Served by GitHub Pages from `main`, `/ (root)` at https://jevwithwind.github.io/simward/

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole site. Inline CSS and inline JS (the dot-chart illustration). |
| `favicon.svg` | Site icon. Same mark as the inline logo in the header. |
| `README.md` | Short repo description. |
| `.nojekyll` | Empty. Tells Pages to serve files as-is without Jekyll. |
| `CLAUDE.md` | This file. |

## Ground rules

- **Plain static site.** No frameworks, build step, npm dependencies, analytics or trackers.
- **External requests:** only Google Fonts (`fonts.googleapis.com`, `fonts.gstatic.com`) and the Chatbase widget script the owner supplies. Nothing else.
- **Content:** never name a real hospital or organisation as a client or partner. Never mention a team (this is an individual project). Keep page copy exactly as the owner wrote it; don't reword.
- **Chatbase:** never invent an embed script, agent ID or Help page URL. Use only what the owner pastes.
- **GitHub settings:** don't try to change Pages, visibility or other repo settings. The owner does that.
- **Deviations:** only change owner-supplied file contents to fix a real bug, and report what changed and why.

## Git workflow

- Work on a feature branch, commit, push, open a PR into `main`.
- If a PR can't be opened, push the branch and report its name.
- If the repo has no commits at all, commit straight to `main`.

## Verification (run before every push)

1. **JS syntax:** extract the page's own inline `<script>` (the one without `src`) and run `node --check` on it.
   ```sh
   python3 - <<'PY' > /tmp/inline.js
   import re; html = open('index.html').read()
   print('\n'.join(m for m in re.findall(r'<script>(.*?)</script>', html, re.S)))
   PY
   node --check /tmp/inline.js
   ```
2. **Serve locally:** `python3 -m http.server 8000`, then confirm `index.html` and `favicon.svg` both return 200.
3. **Browser check (if Playwright + Chromium is already available; don't spend long installing):** screenshots at 390×844 and 1280×800. Confirm the dot chart renders, nothing overflows horizontally, and both "Open the assistant" buttons (`.chat-link`) reach the "Ask the assistant" section (`#ask`) (in Phase 1) or point at the Help page (after Phase 2).
4. **External URLs:** grep for `https?://` and confirm only the allowed hosts appear (the SVG namespace `http://www.w3.org/2000/svg` is not a request).

## Phases

### Phase 1: build, verify, ship (done)

Create the four site files exactly as supplied, verify, commit as `Add SimWard landing page`, push, open the PR. Then stop and give the owner this checklist:

- Merge the PR into `main`.
- Settings → Pages → Source: Deploy from a branch → `main`, `/ (root)` → Save.
- After 1–2 minutes, check https://jevwithwind.github.io/simward/ loads in an incognito window.
- Come back with the Chatbase Help page URL and chat widget script.

### Phase 2: connect Chatbase (only when the owner sends the details)

The owner sends a Help page URL and a Chatbase chat widget script. Then:

1. Replace the line `<!-- CHATBASE_WIDGET_SCRIPT -->` in `index.html` with the script, pasted exactly as given, with a comment line `<!-- Chatbase chat widget -->` directly above it.
2. On both elements with class `chat-link`, set `href` to the Help page URL and add `target="_blank" rel="noopener"`.
3. Remove the `hidden` attribute from `#widget-hint`.
4. Verify:
   - the URL starts with `https://`;
   - the script references `chatbase.co`;
   - the page's own inline script still passes `node --check`;
   - nothing else in the file changed. Show `git diff --stat` and the full diff.
5. Commit as `Connect Chatbase assistant`, push, open a PR. Tell the owner to merge it and hard-refresh the live site after 1–2 minutes.

**If only the Help page URL arrives** (widget didn't work out): do steps 2, 4 and 5 only, and leave `#widget-hint` hidden.
