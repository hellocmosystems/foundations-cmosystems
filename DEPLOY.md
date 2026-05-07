# Deploying foundations.cmosystems.co

This folder contains a single-page static site for `foundations.cmosystems.co`. Deploy steps below — read top to bottom, do them in order. Total time: ~20 minutes.

## What's in this folder

- `index.html` — the entire site (HTML + embedded CSS, no build step)
- `CNAME` — tells GitHub Pages which custom domain to serve. Required.
- `.gitignore` — keeps `.DS_Store` and other junk out of the repo
- `DEPLOY.md` — this file

---

## Step 1 — HubSpot form (already wired)

The HubSpot form is already embedded in `index.html` using your account values:

- **Region:** `na2`
- **Portal ID:** `245854612`
- **Form ID:** `f1aeebf1-12bf-4ed0-b784-d7114b808270`

You don't need to do anything for the embed itself. Submissions will land in HubSpot as Contact records under your portal.

### One thing to confirm in HubSpot

HubSpot's newer embed format (the one with `<script src="...js-na2.hsforms.net/forms/embed/245854612.js">`) renders the form **inside an iframe**. This means parent-page CSS doesn't reach inside the form — so the form's visual styling (colors, fonts, button look) is controlled inside HubSpot, not from this site.

To make the form match the site palette:

1. In HubSpot, open the form: **Marketing → Lead Capture → Forms → [your Foundations form] → Edit**.
2. Click the **Style and preview** tab (top of the editor).
3. Set:
   - **Submit button color:** `#1F2A44` (navy — matches the site's primary buttons)
   - **Field label color / form font:** Arial or System (the site uses system fonts; Arial is closest)
   - **Background color:** transparent or `#FFFFFF` (white)
   - **Border / corner radius:** medium rounded (~8px) to match site inputs
4. Click **Update** (top right).

The next page load will pick up the new styling.

> If you'd rather the form render inline (so parent-page CSS controls the look perfectly), HubSpot also offers a legacy embed using `hbspt.forms.create({ region, portalId, formId })`. Tell me and I'll swap it in. Tradeoff: legacy embed is less analytics-rich on HubSpot's side.

---

## Step 2 — Create a GitHub repo for the site

You can do this on github.com directly (no command line needed):

1. Go to **https://github.com/new**
2. Repo name: `foundations-cmosystems` (or anything you like — name doesn't affect the public URL because we're using a custom domain)
3. Set to **Public** (required for free GitHub Pages)
4. Do NOT initialize with a README (this folder already has files)
5. Click **Create repository**

GitHub will show a "Quick setup" page. Keep that tab open — you'll need the remote URL in the next step.

---

## Step 3 — Push the files to GitHub

Open Terminal and run these commands one at a time (or paste them all at once, but **do not include the triple-backtick lines** — those are markdown formatting, not commands). Replace `YOUR-USERNAME` with your actual GitHub username if it's different from `hellocmosystems`.

    cd "/Users/phillipryanblock/Documents/Claude/Projects/CMO Systems/foundations-site"
    git init
    git add .
    git commit -m "Initial site"
    git branch -M main
    git remote add origin https://github.com/hellocmosystems/foundations-cmosystems.git
    git push -u origin main

> **If you see `>` prompts after pasting:** you accidentally pasted a triple-backtick character (`` ``` ``). Press **Ctrl+C** to abort, then paste only the indented lines above.

### Authenticating with GitHub

When `git push` runs, GitHub will ask for a username and password. **Do not use your account password** — GitHub stopped accepting passwords for HTTPS pushes. Use a Personal Access Token instead.

To create one:

1. **github.com** → click your avatar (top right) → **Settings**
2. Scroll down the left sidebar → **Developer settings**
3. **Personal access tokens** → **Tokens (classic)** → **Generate new token (classic)**
4. Note: `foundations-site push` (or anything memorable)
5. Expiration: pick a reasonable window (90 days is fine)
6. **Scopes:** check the **`repo`** checkbox at the top
7. Click **Generate token** at the bottom
8. **Copy the token immediately** — GitHub will not show it again

When `git push` prompts you:
- **Username:** your GitHub username (e.g., `hellocmosystems`)
- **Password:** paste the token you just copied (it'll look like `ghp_xxxxxxxx...`)

> macOS will offer to remember the credentials in Keychain so you only do this once.

---

## Step 4 — Enable GitHub Pages

1. Go to your repo on github.com.
2. Click **Settings** (top tab).
3. Click **Pages** (left sidebar).
4. Under "Build and deployment":
   - **Source:** Deploy from a branch
   - **Branch:** `main` / `/(root)`
   - Click **Save**
5. Wait ~1 minute. GitHub will show a green box that says: "Your site is live at `https://YOUR-USERNAME.github.io/foundations-cmosystems/`". Click that link to confirm the page renders correctly.
6. Still on the Pages settings, find the **Custom domain** field. Enter:
   ```
   foundations.cmosystems.co
   ```
   Click **Save**. GitHub will check the DNS — it will fail at first (we haven't set up DNS yet). That's expected; move on.

> The `CNAME` file in this folder also tells GitHub Pages the custom domain. The Settings field and the CNAME file should match.

---

## Step 5 — Add the DNS record at GoDaddy

This tells the internet to send `foundations.cmosystems.co` traffic to GitHub Pages.

1. Log into **GoDaddy** → **My Products** → next to `cmosystems.co` click **DNS**.
2. Click **Add New Record**.
3. Set:
   - **Type:** `CNAME`
   - **Name:** `foundations`  *(just the subdomain part — not the full URL)*
   - **Value:** `YOUR-USERNAME.github.io`  *(replace with your GitHub username, no `https://`, no trailing slash)*
   - **TTL:** 1 hour (or whatever the default is)
4. Click **Save**.

DNS propagation usually takes 5–30 minutes but can take up to 24 hours.

To check progress, visit **https://www.whatsmydns.net** and look up `foundations.cmosystems.co` with type `CNAME`. When most of the dots turn green showing your GitHub username, the DNS is live.

---

## Step 6 — Enable HTTPS

1. Back at GitHub repo → Settings → Pages.
2. Wait until "DNS check successful" appears next to your custom domain (this can take 10–60 minutes after DNS propagates).
3. Once it's green, the **Enforce HTTPS** checkbox becomes available — check it.
4. Site is live at `https://foundations.cmosystems.co`.

---

## Step 7 — Verify

Visit `https://foundations.cmosystems.co` and check:

- [ ] Page loads with HTTPS (padlock in browser)
- [ ] Hero, sections, pricing tables all render
- [ ] Mobile view works (resize browser or check on phone)
- [ ] Click "Take the Scorecard →" — opens `revfrictionscorecard.cmosystems.co` in new tab
- [ ] Submit the contact form with a test email — confirm a new Contact appears in HubSpot AND a notification lands at `hello@cmosystems.co`

---

## Future updates

Whenever you want to change the page:

1. Edit `index.html` locally.
2. Run:
   ```bash
   cd "/Users/phillipryanblock/Documents/Claude/Projects/CMO Systems/foundations-site"
   git add .
   git commit -m "Brief description of what changed"
   git push
   ```
3. GitHub Pages rebuilds automatically. Changes go live in ~1 minute.

---

## Things to watch out for

- **Don't delete the `CNAME` file.** If you do, GitHub Pages forgets the custom domain and the site goes back to `YOUR-USERNAME.github.io`.
- **Don't add a redirect from `cmosystems.co` to this subdomain.** Foundations is a separate audience; the parent site shouldn't auto-redirect there.
- **To change form fields later,** edit the form inside HubSpot — the embed snippet stays the same. The site picks up your edits the next time the page loads (no redeploy needed).
- **To replace HubSpot with a different form provider later,** swap the embed `<script>` block for the new provider's snippet. The styled `.form-wrap` container around it stays — just paste a new form inside.
- **If you want HubSpot meeting bookings instead of (or in addition to) the contact form,** HubSpot's meeting scheduler also has an embed snippet. Drop it into the same `.form-wrap` container in place of (or alongside) the form embed.

---

## Need to remove or migrate the site later?

To take it down: delete the GoDaddy DNS record. The page goes 404 within minutes.

To migrate to a new platform: the `index.html` is plain HTML/CSS — paste it into Webflow, Framer, or any other tool that accepts HTML.
