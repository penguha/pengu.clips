# FTHR Clips

A static clip site that runs on Vercel or GitHub Pages. There is no build step.

- `https://YOUR-USERNAME.github.io/REPO/` shows all clips, and each clip has its own watch link.
- `https://YOUR-USERNAME.github.io/REPO/upload` is the upload page. Pick a video, press upload, get a link to share.

Example on GitHub Pages: `penguha.github.io/clip/` and `penguha.github.io/clip/upload`. Example on Vercel: `your-project.vercel.app` and `your-project.vercel.app/upload`.

## Part 1A: Put the site online with Vercel (5 minutes)

1. Create a GitHub repo (public or private) and upload everything from this zip to its root (`index.html`, `upload.html`, `404.html`, `style.css`, `config.json`, `clips.json`, `vercel.json`, `.nojekyll`, and the `worker` folder).
2. On vercel.com choose **Add New > Project**, import the repo, and press **Deploy**. Leave Framework Preset as *Other* and keep the build and output settings empty.
3. Every time a clip is uploaded it is saved as a commit in your repo, and Vercel redeploys automatically. A new link goes live after about a minute.

The Vercel project must be connected to the same repo that uploads go to, and Vercel's auto-deploy on pushes must stay on (it is the default).

## Part 1B: Put the site online with GitHub Pages (5 minutes)

1. Create a **public** GitHub repo (for example `clip`).
2. Upload everything from this zip to the repo root: `index.html`, `upload.html`, `404.html`, `style.css`, `config.json`, `clips.json`, `.nojekyll`, and the `worker` folder. All the pages are plain files in the root, so no folders are needed for the site to work. The `clips` folder is created automatically on your first upload.
3. Settings > Pages > Source: *Deploy from a branch* > Branch `main`, folder `/ (root)` > Save. After a minute the site is live.

At this point the upload page asks you to connect once with a GitHub token (Part 2A). To make uploads work for everyone with **no login**, do Part 2B instead.

## Part 2A: Upload with a token (only you can upload)

1. GitHub > your profile Settings > Developer settings > Personal access tokens > **Fine-grained tokens** > Generate new token.
2. Repository access: **Only select repositories**, pick your clip repo. Permissions > Repository permissions > **Contents: Read and write**.
3. Open `/upload`, enter your username, repo name and the token. It is saved in your browser only.

Limit: 50 MB per clip.

## Part 2B: No login for uploads (upload helper)

GitHub Pages cannot receive files by itself, so a tiny free helper holds the GitHub token for you. Anyone who opens the upload page can then upload without logging in.

1. Create the fine-grained token as in Part 2A, step 1 and 2.
2. Sign up at cloudflare.com (free). Workers & Pages > Create > **Create Worker** > name it (for example `fthr-upload`) > Deploy > **Edit code**. Replace everything with the contents of `worker/worker.js`, then Deploy.
3. Worker > Settings > Variables and Secrets. Add:
   - `GH_TOKEN` as a **Secret**: your token
   - `GH_OWNER` as text: your username, for example `penguha`
   - `GH_REPO` as text: your repo name, for example `clip`
   - `ALLOWED_ORIGIN` as text: your site address with no trailing slash and no path, for example `https://your-project.vercel.app` or `https://penguha.github.io`
   - `GH_BRANCH` as text, only if your branch is not `main`
4. Copy the worker address (like `https://fthr-upload.your-name.workers.dev`). In your repo, edit `config.json` to:
   ```json
   { "uploadEndpoint": "https://fthr-upload.your-name.workers.dev" }
   ```
5. Open `/upload`. The connect step is gone; just pick a video and upload.

Limit: 25 MB per clip in this mode.

**Be aware:** with no login, anyone who gets the upload link can add clips to your public repo. Keep that link to people you trust. To remove a clip, delete its file in `clips/` and its entry in `clips.json` on GitHub. You can rotate the token at any time (replace the `GH_TOKEN` secret) or delete the worker to turn uploads off.

## What you get after an upload

- A **share link** (`.../?c=ID`) that opens a watch page.
- A **direct video link** (`.../clips/ID.mp4`), which Discord can usually embed.

The host needs about a minute to publish new files, so a fresh link may show "not found" briefly.

## Why the no-login helper is on Cloudflare and not on Vercel

Vercel Functions reject request bodies larger than about 4.5 MB, which is too small for video clips, so the helper runs on Cloudflare Workers (free). The site itself can be on Vercel.
