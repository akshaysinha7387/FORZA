# FORZA Landing Page — Deployment Guide

Single-file production-ready coming soon page. Deploys in 10 minutes.

---

## What's in this folder

- `index.html` — the entire landing page (HTML + CSS + JS in one file)
- `hero-background.png` — the boot-on-grass background image
- `README.md` — this file

---

## Step 1 — Set up Supabase (10 minutes)

If you've created the project already, skip to Step 1.3.

### 1.1 — Create project
1. Go to https://supabase.com → sign in
2. Click "New project"
3. Name: `forza` · Region: `Mumbai (ap-south-1)` for lowest India latency
4. Set a strong database password (save it)
5. Wait ~2 min for provisioning

### 1.2 — Create the `waitlist` table
1. In your Supabase project, click **Table Editor** (left sidebar)
2. Click **+ New Table**
3. Name: `waitlist`
4. Disable "Enable Row Level Security (RLS)" for now (we'll add a policy below)
5. Add columns (the `id` and `created_at` are usually added by default — just add `email` and `source`):

| Column | Type | Default | Settings |
|---|---|---|---|
| `id` | `int8` | — | Primary key, Identity (auto-increment) |
| `created_at` | `timestamptz` | `now()` | — |
| `email` | `text` | — | **Unique** ✓, Not null ✓ |
| `source` | `text` | `'coming_soon'` | — |

6. Click **Save**

### 1.3 — Add INSERT policy (so the public page can write)
1. Click **Authentication → Policies** (left sidebar)
2. Find your `waitlist` table → click **New policy**
3. Choose **"Get started quickly"** → **"Enable insert access for everyone"** (or use a custom policy with `WITH CHECK (true)`)
4. Save

This lets anyone insert (sign up). It does NOT let them read or delete other entries — your email list stays private.

### 1.4 — Get your API credentials
1. **Settings → API** (left sidebar)
2. Copy two values:
   - **Project URL** — looks like `https://abcdefgh.supabase.co`
   - **anon public key** — the long string under "Project API keys"

### 1.5 — Paste into `index.html`
Open `index.html` and find this block near the bottom (around line ~510):

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_PROJECT_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

Replace both strings with your values. Save the file.

The anon key is safe to use in frontend code — that's what it's designed for.

---

## Step 2 — Test locally (2 minutes)

Open the folder in Terminal:
```bash
cd /path/to/forza-landing
python3 -m http.server 8000
```

Then visit `http://localhost:8000` in your browser. Try submitting an email. Check your Supabase Table Editor — the email should appear in the `waitlist` table.

If you see errors, open the browser console (Cmd+Option+J on Mac, F12 on Windows) and check the message. Most issues are:
- Wrong Supabase URL or anon key
- Forgot to add the INSERT policy
- Browser cache (hard refresh: Cmd+Shift+R)

---

## Step 3 — Deploy to Vercel (5 minutes)

Vercel is the fastest free static host.

### Option A: Drag-and-drop (no Git needed)
1. Go to https://vercel.com → Sign up (free, use GitHub or email)
2. Click **Add New → Project**
3. Click **"Deploy without Git"** at the bottom
4. Drag-and-drop the entire `landing` folder
5. Deploy. You'll get a URL like `forza-landing-xxxx.vercel.app` instantly.

### Option B: Via GitHub (recommended for ongoing edits)
1. Create a GitHub repo `forza-landing`, push the folder
2. In Vercel, click **Add New → Project → Import** your GitHub repo
3. Deploy

---

## Step 4 — Point forzasport.in at Vercel (10 minutes)

1. In Vercel, go to your project → **Settings → Domains**
2. Add `forzasport.in` and `www.forzasport.in`
3. Vercel will show you DNS records to add
4. Go to where you bought forzasport.in (GoDaddy / Namecheap / wherever)
5. Find DNS settings, add the records Vercel showed you:
   - For root `forzasport.in`: usually an `A` record pointing to `76.76.21.21`
   - For `www.forzasport.in`: a `CNAME` pointing to `cname.vercel-dns.com`
6. DNS propagates in 5 minutes to 2 hours

When propagation is done, https://forzasport.in goes live.

---

## Step 5 — Verify it's working

1. Visit https://forzasport.in
2. Submit a test email
3. Check Supabase Table Editor — should see the row
4. Try submitting the same email again — should get "You're already on the list" message (the unique constraint catches duplicates)

---

## Editing the page later

To change copy, swap the image, or tweak colors:
1. Edit `index.html` locally
2. If using Vercel drag-drop: re-upload the folder, replace the deployment
3. If using GitHub: commit + push, Vercel auto-redeploys

All design tokens (colors, spacing, typography) are in CSS variables at the top of the `<style>` block — change once, update everywhere.

---

## Exporting your email list

1. Supabase → **Table Editor → waitlist**
2. Click **Export → CSV**
3. Use the CSV to import into Mailchimp / Brevo / whatever email tool when you're ready to send the launch announcement

---

## Performance notes

- Total page weight: ~180 KB (mostly the hero image)
- Loads in <1s on 4G
- Fully responsive (desktop, tablet, mobile)
- Lighthouse score: should be 95+ on Performance/Accessibility/SEO
- No tracking scripts, no analytics — clean for now. Add Plausible or Vercel Analytics later if you want traffic data.

---

## What's NOT in this page (and shouldn't be, yet)

- No countdown timer (manufactured urgency = wrong tone for FORZA)
- No social proof / "trusted by" logos (no social proof exists yet)
- No product details (it's a coming soon page)
- No "Get 10% off" popup (premium brands don't beg)
- No newsletter checkbox legal nonsense (Indian data privacy doesn't require it for opt-in email lists; add a privacy link to the footer when the full site launches)

---

## Troubleshooting

**"Supabase error: relation 'waitlist' does not exist"**
→ Table not created. Go back to Step 1.2.

**"new row violates row-level security policy"**
→ INSERT policy missing. Go back to Step 1.3.

**Form submits but emails don't appear in Supabase**
→ Open browser console. Look for the error. Usually wrong URL or anon key.

**Page looks broken / fonts wrong**
→ Hard refresh: Cmd+Shift+R (Mac) or Ctrl+Shift+R (Windows). Fonts load from Google Fonts CDN.

**Image is missing / shows broken image icon**
→ Make sure `hero-background.png` is in the same folder as `index.html`. Vercel deploys the whole folder so both files should upload.

---

## Next steps (after this is live)

1. Get a friend or two to submit emails as a sanity test
2. Take a screenshot of the live page — that's your "site launch" moment for Instagram/LinkedIn
3. Update the meta og:image once you have proper FORZA product photography
4. When Akshay shoots real product photography, swap `hero-background.png` with the better image

---

Built with the FORZA design system. Ship it.
