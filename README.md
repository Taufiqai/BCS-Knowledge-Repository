# BCSKR Dashboard — Deployment Guide / ডিপ্লয়মেন্ট গাইড

A self-contained bilingual (বাংলা ⇄ English) dashboard for the **Bangladesh Civil Service Knowledge Repository (BCSKR)**. No build step, no backend — a single `index.html` plus `vercel.json`.

একটি স্বয়ংসম্পূর্ণ দ্বিভাষিক (বাংলা ⇄ ইংরেজি) ড্যাশবোর্ড। কোনো build বা backend লাগে না — শুধু `index.html` ও `vercel.json`।

---

## Files / ফাইল
- `index.html` — the entire dashboard (HTML + CSS + JS, single file)
- `vercel.json` — static hosting config + security headers
- `README.md` — this file

---

## Deploy to Vercel — 3 ways / Vercel-এ ডিপ্লয়ের ৩টি উপায়

### Option A — Drag & drop (easiest / সবচেয়ে সহজ)
1. Go to **https://vercel.com** and sign in (GitHub/GitLab/email).
2. Click **Add New… → Project → Deploy** (or use **vercel.com/new**).
3. Drag the whole `bcskr` folder into the upload area.
4. Framework Preset: choose **Other** (it is plain static HTML).
5. Click **Deploy**. You get a live URL like `https://bcskr-xxxx.vercel.app` in ~30 seconds.

### Option B — Vercel CLI / কমান্ড লাইন
```bash
npm i -g vercel        # একবার ইনস্টল
cd bcskr               # ফোল্ডারে ঢুকুন
vercel                 # preview deploy (প্রশ্নগুলোতে Enter চাপুন)
vercel --prod          # production deploy
```
When asked "In which directory is your code located?" press Enter (current folder). When asked about build settings, accept defaults — there is no build command.

### Option C — GitHub (recommended for updates / আপডেটের জন্য উত্তম)
1. Create a GitHub repo and push these files.
2. In Vercel: **Add New → Project → Import** the repo.
3. Framework Preset = **Other**, Build Command = *(leave empty)*, Output Directory = `./`.
4. **Deploy.** Every future `git push` auto-deploys.

---

## Troubleshooting / সমস্যা সমাধান

**1. "No Output Directory named 'public' found"**
Set Output Directory to `./` in Project Settings → Build & Output, OR keep `index.html` at the repo root. Vercel serves root-level `index.html` automatically for static projects.
সমাধান: Output Directory `./` দিন অথবা `index.html` রুট ফোল্ডারে রাখুন।

**2. Vercel tries to run a build and fails**
This is a static site — there is no build. Set **Framework Preset = Other** and leave **Build Command empty**. Remove any `package.json` "build" script if present.

**3. Bengali text shows boxes (□□□) / বাংলা অক্ষর ভাঙা দেখায়**
The dashboard loads the *Hind Siliguri* font from Google Fonts, which needs internet. If your network blocks Google Fonts, the OS fallback renders. To self-host: download the font, place the `.woff2` files in a `/fonts` folder, and replace the `<link>` tag with an `@font-face` rule. The Unicode text itself is always correct — only the typeface changes.

**4. 404 on the deployed URL**
Make sure the file is named exactly `index.html` (lowercase) and sits at the project root, not inside a subfolder.

**5. Language toggle / search not working**
These are client-side JS. Open the browser console (F12) — if you see a script error, confirm `index.html` was uploaded whole and not truncated. Hard-refresh with Ctrl+Shift+R to clear cache.

**6. Custom domain / কাস্টম ডোমেইন (e.g. bcskr.gov.bd)**
Project → Settings → Domains → Add. Then in your DNS, add the CNAME/A record Vercel shows. For a `.gov.bd` domain, coordinate with BCC/BTCL for DNS changes.

**7. Free-tier limits**
Vercel's Hobby (free) plan is fine for a static dashboard. For an official government deployment, a Pro/Enterprise plan or a government-hosted server (e.g. National Data Center) is recommended for SLA, audit logs, and data-residency compliance.

---

## Notes on the "full stack" version / ফুল-স্ট্যাক সংস্করণ প্রসঙ্গে

This is the **presentation layer** (a static, illustrative dashboard with sample data). A production BCSKR full stack additionally requires:
- **Backend/API** (e.g. Node/NestJS or Django) + **PostgreSQL** for documents, users, deposits
- **Auth & RBAC + MFA** (per the KM Policy security section)
- **Bangla NLP search** (e.g. a vector DB + an embedding/LLM service) — the search box here is a front-end demo
- **Object storage** for digitized files; **audit logs**; **ISO 27001** controls

When you are ready for that, this same UI can sit in front of a Next.js app on Vercel with the database and AI services connected via environment variables. The data arrays at the top of the `<script>` block are the integration points — replace them with `fetch()` calls to your API.

এই সংস্করণটি উপস্থাপন স্তর (নমুনা তথ্যসহ static dashboard)। বাস্তব full stack-এ backend, database, RBAC+MFA, Bangla NLP সার্চ ও নিরাপত্তা নিয়ন্ত্রণ যুক্ত করতে হবে; তখন `<script>`-এর উপরের data array গুলো API-এর `fetch()` কল দিয়ে প্রতিস্থাপন করলেই UI সংযুক্ত হবে।
