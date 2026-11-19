# NAS Game — free-hosted starter

A responsive browser-game community starter built with React + Vite + Supabase and deployable on Netlify.

## Included
- Dark responsive home, explore, profile, upload and play pages
- Google OAuth login
- HTML code upload (stored in Supabase Storage) or HTTPS game URL
- Sandboxed game iframe and live HTML preview
- Admin approval workflow (starter admin UI)
- Likes, comments, view counter, developer profile
- Supabase SQL schema and Netlify SPA redirect

## Important limitations / security notes
1. This is a starter MVP, not a finished large-scale platform.
2. The included client-side admin check is suitable only for a private prototype. A production admin approval system MUST use a server-side role/Edge Function and RLS admin policies. Do not rely on a secret email in frontend code as an authorization boundary.
3. Publicly hosted HTML is untrusted. The app embeds it in a sandboxed iframe; do not remove the sandbox. Consider stronger isolation on a separate domain before accepting public uploads at scale.
4. External URLs can block iframe embedding (X-Frame-Options / CSP). For reliable play, accept uploaded HTML packages or use a trusted embed provider.
5. HTML-only uploads support single-file games. Multi-file games (JS/CSS/images/assets) need ZIP extraction / asset management, which is not included in this MVP.
6. Google indexing is not instant. Add a sitemap, robots.txt, canonical URLs and per-game public pages before serious SEO work.
7. Free tiers have limits and may pause inactive projects. Check current provider limits before launch.

## Setup

### A. Create Supabase project
1. Create a project at https://supabase.com.
2. Open SQL Editor and run `supabase/schema.sql`.
3. Go to Project Settings → API. Copy Project URL and anon/public key.
4. Authentication → Providers → Google: enable Google and enter the OAuth Client ID and Secret.
5. In Google Cloud Console, create an OAuth 2.0 Web Client. Add the Supabase callback URL shown in Supabase's Google provider settings as an Authorized redirect URI.
6. In Supabase Authentication → URL Configuration, set Site URL to your Netlify site URL and add local redirect `http://localhost:5173/**` plus your deployed site URL to Redirect URLs.

### B. Configure app
1. Copy `.env.example` to `.env`.
2. Fill `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY`.
3. Set `VITE_ADMIN_EMAIL` to the Google email you will use as the site administrator. Also add that same email to the protected `admin_users` table using the Supabase SQL Editor: `insert into public.admin_users(email) values ('you@example.com');`. Only the project owner should be allowed to change this table.
4. Run:
   ```bash
   npm install
   npm run dev
   ```

### C. Deploy to Netlify
1. Push this folder to a GitHub repository.
2. Netlify → Add new site → Import from Git.
3. Build command: `npm run build`; publish directory: `dist`.
4. Add the same `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` environment variables in Netlify Site configuration.
5. Deploy. Add the Netlify domain to Supabase Auth redirect URLs.
6. Connect a custom domain later if desired.

## Admin moderation caveat
The demo UI reads pending games only when the current email matches `VITE_ADMIN_EMAIL`, but the SQL intentionally does not grant admin database writes. That prevents a client-side email check from becoming a false security promise. Before public launch, implement a secure admin role using a server-side Edge Function and RLS policy based on a protected `app_metadata` claim or admin table that users cannot edit. Until then, use the Supabase dashboard to review submissions and change `games.status` to `approved` or `rejected`.

## Next upgrades
- Secure server-side admin role and moderation endpoint
- Multi-file ZIP game upload and extraction
- Report/flag system and rate limits
- Per-game SEO pages and sitemap
- Pagination, search indexing, spam moderation and backups
