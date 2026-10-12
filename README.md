# PomoWrite

PomoWrite is a responsive academic English writing workspace built on the original vanilla HTML/CSS/JavaScript app. Existing brainstorming, drafting, polishing checklist, local review, Pomodoro timer, profile, streaks, and browser `localStorage` persistence remain available. Supabase cloud sync and AI are optional integrations; neither pretends to work when credentials are missing.

## Requirements

- Node.js 20 or newer and npm
- A Supabase project for authentication and database storage
- An OpenAI API key with API billing/usage enabled for AI features

## 1. Install and run

```bash
cd pomowrite
npm install
cp .env.example .env
npm start
```

Open <http://localhost:4173>. `npm start` serves the app and the same-origin `/api/ai` endpoint. Do not use `python -m http.server` for the integrated version because it cannot run the server-side AI endpoint.

## 2. Configure Supabase

1. Create a project at <https://supabase.com/dashboard>.
2. In Project Settings / API, copy the **Project URL** and the **publishable key** (or legacy `anon` key).
3. Put those two **public client values** in `config.js` as `supabaseUrl` and `supabaseAnonKey`. These are designed for browser use; database security comes from RLS, not from hiding this key.
4. In Supabase Authentication, enable Email provider. Configure email confirmation and your local/site redirect URLs as appropriate (for local development, add `http://localhost:4173/**` to the allowed redirect URLs if your project requires it).
5. Open the SQL Editor and run both migrations in order: `supabase/migrations/202610110001_pomowrite_cloud.sql`, then `supabase/migrations/202610120001_pomowrite_works.sql`. The second migration removes the one-work-per-user constraint while preserving existing rows and adds per-essay stage/checklist fields.
6. Put the Supabase Project URL and the same publishable/anon key in `.env` as `SUPABASE_URL` and `SUPABASE_ANON_KEY`. These are not service-role secrets. **Never put a Supabase `service_role` key in `config.js` or expose it to the browser.**

The migrations create `essays`, `essay_plans`, and `user_progress`, enable RLS, and restrict rows to `auth.uid()`. `user_progress.profile` stores each account’s profile, while `essays` and `essay_plans` store a user’s collection of works and their planning/revision state. The browser uses the signed-in user’s session for all database operations. A user cannot read or write another user’s rows under these policies.

## 3. Configure OpenAI (server only)

1. Create an API key at <https://platform.openai.com/api-keys> and verify API billing/limits at <https://platform.openai.com/usage>.
2. Put the key in `.env` as `OPENAI_API_KEY`. Optionally change `OPENAI_MODEL` (default `gpt-4.1-mini`).
3. Restart `npm start` after changing `.env`.

**Never put `OPENAI_API_KEY` in `config.js`, HTML, or client-side JavaScript, and do not commit `.env`.** The `/api/ai` endpoint requires a valid Supabase access token, validates task inputs, rate-limits requests per IP, and only then calls OpenAI.

## Features added

- Auto theme follows the operating system light/dark preference; the top-bar toggle cycles Auto → Dark → Light and persists the choice locally
- My Works library: create, open, rename, and delete multiple essays/drafts with automatic local saving
- Supabase email registration, sign-in, session restoration, and sign-out
- Cloud save/load for multiple essays and their plans/checklists, plus the user profile, stage completion, focus sessions, and streak progress
- SQL migration with user-owned tables and row-level security policies
- AI-generated four-part plan based on the student's topic and ideas
- Context-aware academic synonym suggestions, with click-to-insert into the draft
- AI essay analysis with strengths, priority improvements, and specific language corrections/explanations
- Server-side key handling and explicit error messages when a service is not configured or a provider rejects a request
- Local browser saving remains enabled whether or not Supabase is configured

## Data and deployment notes

- The app supports multiple works per account. Existing single-work records are preserved by the second migration; local browser work is retained and can be merged after sign-in.
- Cloud sync is tied to the signed-in user. On login, local and cloud works are merged; when prompted, choosing Cancel keeps the local copy and uploads it.
- AI requests send the current topic/draft or word to OpenAI. Avoid submitting personal or confidential text unless you are comfortable processing it through the provider.
- This app is prepared for local development. Before public deployment, add production hosting/HTTPS, stricter origin policy, durable distributed rate limiting, and operational monitoring. Do not deploy the development server as a production service.
- Email confirmation behaviour depends on the Supabase Auth settings. If confirmation is required, the user must click the email link before signing in.

## Tests

The automated suite covers the AI prompt validation plus structural checks for themes, My Works actions, cloud persistence, RLS migration policy, and retained original controls. Supabase login and cross-device sync still require your configured project to test end-to-end.

```bash
npm test
node --check app.js
node --check server.js
node --check src/ai.js
```
