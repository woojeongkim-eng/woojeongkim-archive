# woojeongkim-archive — Kim Woojeong Portfolio Archive

Static single-file site (`index.html` + `support.js` + `uploads/`) built with a custom component framework (`class Component extends DCLogic`, `sc-if`/`sc-for` template tags). No build step — `index.html` is served as-is. Sibling project to the `woojeong.kim` ("Signature") repo — same underlying template, but this version gates the project/career sections behind a passcode lock screen.

## Hosting

- Live site: https://woojeongkim-archive.vercel.app/ (Vercel auto-deploys on every push to `main`)
- GitHub Pages is configured in the README but not actually used as the live URL — check before assuming it's live there.

## Known history / past fixes (don't redo or regress these)

1. Non-ASCII filenames broke on macOS Safari (404s). Some folders/files with Korean text, spaces, or accents were renamed to ASCII-safe names (e.g. `Pure Glacé` to `Pure-Glace`, `portre_5304 (배경 수정).jpg` to `portre_5304-baegyeong-sujeong.jpg`, romanized video filenames). Note: this repo's rename pass was less complete than woojeong.kim's — e.g. `The Atelier` folder still has the space/no-hyphen here, and `portre_18348_blur.JPG` keeps its uppercase extension (vs lowercase in the other repo). Always check the actual filename casing in `uploads/` for this repo — don't assume it matches the other repo.

2. Images were huge (~3.5GB). Compressed to ~370MB: resized to max 2000px, JPEG quality 80. Compress any newly added raw images the same way before committing.

3. Three gallery entries were silently dropped from the very first deploy (Tint Glow Jour / Pure Layer Cushion Glow / Pure Glacé — `photo-16/17/18`), even though their images were already in `uploads/`. Fixed by inserting just those three objects into the photo array (between `photo-15` Hand Cream Nouveau and `photo-19` Promotion) — not by overwriting the whole file. Same bug, independently, as woojeong.kim.

4. Passcode/lock screen hardening. The unlock inputs use `type="password" inputMode="numeric"` (not `type="tel"`), and `enterProject` / the home rolling timer are gated so they pause while a lock screen (`showProjectLock` / `showCareerLock`) is open. Fresh exports from the design tool regress both of these — watch for it.

## Critical gotcha: never blindly overwrite index.html

When the user hands over a fresh `index.html` export to "redeploy," do not just copy it over the existing file — it will regress everything above (non-ASCII filenames, passcode input type, lock-gating logic). Instead: diff the fresh export against the currently deployed `index.html` (or the "core files" commit) to see what's actually new. Port over only the genuinely new content, translating paths to match the already-renamed files actually sitting in `uploads/` (check exact case — see note above). Verify `uploads/` already has what's referenced before assuming anything needs re-uploading.

## Git push: no local credentials

This machine has no git credential helper / SSH key / `gh` CLI configured, so `git push` fails with "could not read Username." If a logged-in GitHub browser session is available, edit the file directly via GitHub's web editor (`/edit/main/<path>`) — for small targeted changes, use the editor's Find/Replace panel to splice in exact content rather than retyping the whole file, then "Commit directly to the main branch."

## Repo size note

`.git` history is ~3.5GB (original uncompressed image blobs from early batch commits) even though the working tree is small. Expected — no need to shrink history.
