# Lumiere Student Profiles — Cloudflare Pages deploy

## What changed from Vercel

Only one file changed: `data.js` → moved to `functions/api/data.js` and rewritten
for Cloudflare's function format (Workers `Request`/`Response` instead of Node's
`(req, res)`, and `context.env.AIRTABLE_TOKEN` instead of `process.env`). The
Airtable query logic, field cleaning, and response shape are identical.

**`index.html` needs no changes** — it already calls `fetch('/api/data')`, and
`functions/api/data.js` automatically serves that exact route.

Required folder layout (functions must sit at the project root, as siblings of
your static files — not inside a build output folder):

```
your-repo/
├── index.html
├── Lumiere_logo.png      ← wasn't in your upload, add it back here
└── functions/
    └── api/
        └── data.js
```

## Deploy via the dashboard (recommended)

1. Push this folder to a GitHub (or GitLab) repo.
2. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** → pick the repo.
3. Build settings: Framework preset **None**, build command **(leave blank)**,
   build output directory **`/`**.
4. Deploy. You'll get a `*.pages.dev` URL immediately — add your own domain
   later under **Custom domains** if you want one.
5. **Set the secret:** project → **Settings** → **Environment variables** →
   add `AIRTABLE_TOKEN` as a **Secret** (not plain text) for both Production
   and Preview. Redeploy once after adding it.

From then on, every push to your main branch auto-deploys, same as Vercel.

## Deploy via CLI instead (optional)

```bash
npm install -g wrangler
wrangler login
cd your-repo
wrangler pages deploy . --project-name=lumiere-profiles
wrangler pages secret put AIRTABLE_TOKEN --project-name=lumiere-profiles
```

## Local testing (optional)

Create a `.dev.vars` file in the project root (don't commit it):

```
AIRTABLE_TOKEN=your_token_here
```

Then run:

```bash
wrangler pages dev .
```

This serves the site at `localhost:8788` with the function live, using your
local token.

## Notes

- The refresh button and hourly `setInterval` in `index.html` work exactly as
  before — no frontend changes needed.
- Cloudflare's free tier explicitly permits commercial/business use, so this
  resolves the ToS concern that prompted the move off Vercel Hobby.
- Pages Functions don't spin down between requests (unlike free-tier
  containers on platforms like Render), so there's no cold-start delay on the
  refresh button.
