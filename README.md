# Lumiere Student Profiles — Cloudflare Pages deploy

## What changed from Vercel

Only one file changed: `data.js` → moved to `functions/api/data.js` and rewritten
for Cloudflare's function format (Workers `Request`/`Response` instead of Node's
`(req, res)`, and `context.env.AIRTABLE_TOKEN` instead of `process.env`). The
Airtable query logic, field cleaning, and response shape are identical.

**`index.html` needs no changes** — it already calls `fetch('/api/data')`, and
`functions/api/data.js` automatically serves that exact route.

Required folder layout (functions must sit at the project root, as siblings of
the static files — not inside a build output folder):

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
