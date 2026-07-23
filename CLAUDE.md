# woojeongkim-archive — Kim Woojeong Portfolio Archive

Static single-file site (`index.html` + `support.js` + `uploads/`) built with a custom component framework (`class Component extends DCLogic`, `sc-if`/`sc-for` template tags). No build step — `index.html` is served as-is. Sibling project to the `woojeong.kim` ("Signature") repo — same underlying template, but this version gates the project/career sections behind a passcode lock screen.

## Hosting

- Live site: https://woojeongkim-archive.vercel.app/ (Vercel auto-deploys on every push to `main`)
- GitHub Pages is configured in the README but not actually used as the live URL — check before assuming it's live there.
- Sibling repo: `woojeongkim-eng/woojeong.kim` ("Signature"), live at https://woojeongkim-portfolio.vercel.app/ (not `woojeong-kim.vercel.app` — that old URL 404s now). The user (woojeongkim-eng) typically wants changes applied to **both** repos — always ask/confirm scope ("archive만 / portfolio만 / 둘 다") before assuming one vs both.

## Known history / past fixes (don't redo or regress these)

1. Non-ASCII filenames broke on macOS Safari (404s). Some folders/files with Korean text, spaces, or accents were renamed to ASCII-safe names (e.g. `Pure Glacé` to `Pure-Glace`, `portre_5304 (배경 수정).jpg` to `portre_5304-baegyeong-sujeong.jpg`, romanized video filenames). Note: this repo's rename pass was less complete than woojeong.kim's — e.g. `The Atelier` folder still has the space/no-hyphen here, and `portre_18348_blur.JPG` keeps its uppercase extension (vs lowercase in the other repo). Always check the actual filename casing in `uploads/` for this repo — don't assume it matches the other repo.

2. Images were huge (~3.5GB). Compressed to ~370MB: resized to max 2000px, JPEG quality 80. Compress any newly added raw images the same way before committing.

3. Three gallery entries were silently dropped from the very first deploy (Tint Glow Jour / Pure Layer Cushion Glow / Pure Glacé — `photo-16/17/18`), even though their images were already in `uploads/`. Fixed by inserting just those three objects into the photo array (between `photo-15` Hand Cream Nouveau and `photo-19` Promotion) — not by overwriting the whole file. Same bug, independently, as woojeong.kim.

4. Passcode/lock screen hardening. The unlock inputs use `type="password" inputMode="numeric"` (not `type="tel"`), and `enterProject` / the home rolling timer are gated so they pause while a lock screen (`showProjectLock` / `showCareerLock`) is open. Fresh exports from the design tool regress both of these — watch for it.

5. Added a 4th archive tab: `MODEL` (mediaTypes went from `['photo','video','ai']` to `['model','photo','video','ai']`; mediaLabels/archiveCategoryDesc got a `model` entry). 14 model-shoot folders live in `const realModelFolders = [...]`, inserted right before `const archiveFoldersAll = ...` and concatenated in (`.concat(realModelFolders)`). Images are compressed the same way as everything else and live in `uploads/model/`, named `<product-slug>-<n>.jpg` (slug = English product title in kebab-case, not romanized Korean — e.g. `bare-color-balm-ad-1.jpg`, `soft-filter-powder-pact-03.jpg`). Source raws are on the user's Desktop at `모델컷 최종본/`.

6. Folder cover-photo override: every folder-thumbnail construction (`realAiFolders`, `photoRealFolders`, `realModelFolders`, `videoRealFolders`) reads `enc(f.previewImage || f.media[0].src)` — so a folder object can carry an optional `previewImage: 'uploads/.../file.jpg'` to pin its grid-listing thumbnail to something other than the first media item, without reordering `media`. Originally only used by the AI folders; now wired into all four folder types. This is the mechanism to use when the user says "change category X's cover photo to Y."

7. Archive **detail-page** images used to force every photo/video into a cropped `aspectRatio: '9/16'` box (`objectFit: cover`), which badly cropped landscape photos. Fixed: the wrapping div no longer sets a fixed aspect ratio (just `width: '100%', alignSelf: 'start'` so it doesn't stretch to match the tallest item in its CSS Grid row), and the `<img>`/`<video>` use `height: 'auto'` instead of `objectFit: cover`. An `onLoad`/`onLoadedMetadata` handler checks `naturalWidth > naturalHeight` and sets `el.parentElement.style.gridColumn = 'span 2'` so landscape media renders wider instead of tiny-and-narrow or crop-cut. This logic lives inline in the `archiveImages` construction (search for `onWideLoad`) — don't reintroduce a fixed `aspectRatio` there.

## Critical gotcha: never blindly overwrite index.html

When the user hands over a fresh `index.html` export to "redeploy," do not just copy it over the existing file — it will regress everything above (non-ASCII filenames, passcode input type, lock-gating logic). Instead: diff the fresh export against the currently deployed `index.html` (or the "core files" commit) to see what's actually new. Port over only the genuinely new content, translating paths to match the already-renamed files actually sitting in `uploads/` (check exact case — see note above). Verify `uploads/` already has what's referenced before assuming anything needs re-uploading.

## Git push: no local credentials (per machine)

A fresh machine has no git credential helper / SSH key / `gh` CLI configured for GitHub, so `git push` fails with "could not read Username." Don't ask the user for a token — instead install `gh` (`brew install gh`) and run `gh auth login --hostname github.com --git-protocol https --web` in the background, relay the one-time code + https://github.com/login/device URL to the user in chat and ask them to authorize it themselves (don't click "Authorize" for them — that's an OAuth grant, which needs their explicit action). Once `gh auth status` shows logged in, run `gh auth setup-git` and normal `git clone`/`push` over HTTPS works. This is per-machine — redo it on any new computer. Use `git clone --depth 1` (shallow) when cloning; a full clone pulls the ~3.5GB history noted below and can time out.

## Recurring workflow with this user

The user (woojeongkim-eng) regularly hands over a new folder/zip of raw photos or files and expects it pushed live with minimal back-and-forth. Their stated pattern: they give the folder, say which site(s) it's for ("archive만" / "portfolio만" / "둘 다"), and expect compression + non-ASCII renaming + diffing + `git commit`/`push` to happen without re-confirming each step — treat "here's a folder + site scope" as standing authorization to carry the change all the way to a live push on the site(s) named, following the conventions in this file (don't ask "should I push?" again once scope is given). Still use judgment: if something is genuinely ambiguous (e.g. which folder/category a given photo belongs to, or which specific image within a folder they mean), ask rather than guess — a wrong live thumbnail is a worse outcome than one clarifying question.

They cannot give raw code — they're not a developer. Everything (index.html edits, image compression, git operations) is this assistant's responsibility; the user only supplies files and plain-language direction (e.g. "Pure Glacé 커버를 02번 사진으로 바꿔줘").

If a chat message includes photos as inline attachments (not a folder), there is no way to save those to local disk directly — ask the user to either (a) drop the actual files into a folder ("모델컷 최종본" on their Desktop is the usual source folder for model shoot photos) so they're reachable on disk, or (b) confirm the attached photos are visual references to files *already* in that folder, in which case open and visually compare the folder's candidate files to find the exact match (slow but works — this is how the 9 cover-photo picks in history item 6 above were resolved) rather than guessing from the folder's default ordering.

## Repo size note

`.git` history is ~3.5GB (original uncompressed image blobs from early batch commits) even though the working tree is small. Expected — no need to shrink history.
