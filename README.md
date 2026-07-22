# Deploy: Kim Woojeong Portfolio (Portfolio.dc.html)

Static site, ready to push to a GitHub repo and serve via GitHub Pages.

## What's here
- `index.html` — the whole site (single-file component, renders everything)
- `support.js` — runtime the site depends on (must stay next to index.html)
- `uploads/` — all images/videos referenced by the site

## Task for Claude Code
1. Open a terminal in this folder.
2. If deploying to a NEW repo: create it on GitHub first, then `git init`, `git remote add origin <repo-url>`.
   If deploying to an EXISTING repo (e.g. same one as the Signature site, in a subfolder or separate repo — ask the user which), clone it and copy these files in.
3. Commit and push:
   ```
   git add .
   git commit -m "Deploy portfolio site"
   git push origin main
   ```
   If push fails/times out partway (common with large image folders), just retry `git push origin main` — git resumes. If it keeps failing, commit `uploads/` in smaller batches instead of all at once.
4. Enable GitHub Pages: repo Settings → Pages → Source: Deploy from a branch → Branch: `main`, folder: `/(root)`.
5. Site goes live at `https://<username>.github.io/<repo-name>/`.

## Notes
- Do not rename `uploads/` or move `support.js` — `index.html` references both by relative path.
- No build step needed — fully static, ready-to-serve site.
