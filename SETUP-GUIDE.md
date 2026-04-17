# ZBML Website — Complete Setup Guide

Everything needed to go from these files to a live website at a custom
domain with working contact form email notifications.

---

## What You'll End Up With

- Website live at your custom domain (e.g. zackbolandmusiclessons.com)
- Hosted free on GitHub Pages
- HTTPS automatically provided by GitHub
- Contact form submissions trigger a GitHub Action
- GitHub Action emails the inquiry to zbmusiclessons@gmail.com
- Zack hits Reply in Gmail and it goes straight to the prospective student

---

## Files in This Package

```
index.html                         ← the website (edit the 3 config values inside)
CNAME                              ← tells GitHub Pages your custom domain
.github/
  workflows/
    contact-form.yml               ← the email Action (no edits needed)
```

---

## PART 1 — Gmail App Password

Do this first. You need the App Password before you can configure GitHub.

**Step 1.1 — Enable 2-Step Verification on the Gmail account**

1. Open a browser and go to: https://myaccount.google.com
2. Click **Security** in the left sidebar
3. Find **2-Step Verification** under "How you sign in to Google"
4. Click it and follow the prompts to turn it on
   (requires a phone number — Google will send a verification code)
5. Once enabled, return to the Security page

**Step 1.2 — Generate the App Password**

1. On the Security page, click the search bar at the top and type:
   `App Passwords`
2. Click **App Passwords** in the results
   (if you don't see it, 2-Step Verification is not fully enabled yet)
3. Under "Select app" choose: **Mail**
4. Under "Select device" choose: **Other (custom name)**
5. Type a name: `ZBML GitHub Action`
6. Click **Generate**
7. Google shows a 16-character password in a yellow box like:
   `abcd efgh ijkl mnop`
8. **Copy it immediately** — Google will never show it again
   (spaces don't matter — you can include or remove them)
9. Keep this copied value ready for Part 3

---

## PART 2 — Create the GitHub Repository

**Step 2.1 — Create a GitHub account (if needed)**

Go to https://github.com and sign up. Choose a username — it will
appear in the repo URL temporarily before the custom domain is live.

**Step 2.2 — Create a new repository**

1. Click the **+** icon in the top-right corner → **New repository**
2. Fill in the form:
   - **Repository name:** `zbml-website`
     (or any name — just remember it for later)
   - **Description:** Zack Boland Music Lessons website
   - **Public** — must be public for free GitHub Pages
   - Leave everything else unchecked (no README, no .gitignore)
3. Click **Create repository**
4. You land on an empty repo page — leave this tab open

**Step 2.3 — Upload the files**

1. On the empty repo page click **uploading an existing file**
   (the link is in the text that says "...or uploading an existing file")
2. Drag and drop ALL THREE of the following onto the upload area:
   - `index.html`
   - `CNAME`
3. For the workflow file, you need to create the folder structure:
   - Click **Create new file** instead
   - In the filename box type exactly:
     `.github/workflows/contact-form.yml`
     (GitHub will auto-create the folders as you type the slashes)
   - Open the `contact-form.yml` file from this package in a text editor
   - Copy the entire contents and paste into the editor on GitHub
   - Scroll down, add a commit message: `Add contact form workflow`
   - Click **Commit new file**
4. Go back to the repo, click **Add file → Upload files**
5. Drag `index.html` and `CNAME` onto the upload area
6. Commit message: `Initial website upload`
7. Click **Commit changes**

Your repo should now contain:
```
.github/workflows/contact-form.yml
CNAME
index.html
```

---

## PART 3 — Create the GitHub Personal Access Token (PAT)

This is the token the contact form uses to trigger the GitHub Action.
It is scoped to the minimum possible permissions.

**Step 3.1 — Generate a fine-grained PAT**

1. In GitHub, click your profile photo (top-right) → **Settings**
2. Scroll all the way down the left sidebar → **Developer settings**
3. Click **Personal access tokens** → **Fine-grained tokens**
4. Click **Generate new token**
5. Fill in:
   - **Token name:** `ZBML Contact Form`
   - **Expiration:** 1 year (set a calendar reminder to rotate it)
   - **Resource owner:** your username
   - **Repository access:** select **Only select repositories**
     → choose `zbml-website`
   - **Permissions:**
     - Under Repository permissions, find **Contents** → set to **Read and write**
       *(needed to trigger repository_dispatch)*
     - Leave everything else as No access
6. Click **Generate token**
7. GitHub shows the token once — copy it immediately
   It starts with `github_pat_...`

**Step 3.2 — Put the token in index.html**

1. Open `index.html` in a text editor
2. Find these three lines near the bottom of the file in the `<script>` block:

   ```javascript
   const GITHUB_USERNAME = 'YOUR_GITHUB_USERNAME';
   const GITHUB_REPO     = 'YOUR_REPO_NAME';
   const GITHUB_PAT      = 'YOUR_FINE_SCOPED_PAT';
   ```

3. Replace the placeholder values:
   - `YOUR_GITHUB_USERNAME` → your actual GitHub username
   - `YOUR_REPO_NAME`       → `zbml-website` (or whatever you named it)
   - `YOUR_FINE_SCOPED_PAT` → the token you just copied

4. Save the file
5. Go back to GitHub, navigate to `index.html` in the repo
6. Click the pencil (edit) icon
7. Select all the text, delete it, paste the updated file content
8. Click **Commit changes**

---

## PART 4 — Add Gmail Credentials to GitHub Secrets

**Step 4.1 — Open repo secrets**

1. Go to your repo on GitHub
2. Click **Settings** (the tab in the repo, not your account settings)
3. In the left sidebar click **Secrets and variables** → **Actions**
4. Click **New repository secret**

**Step 4.2 — Add GMAIL_USER**

1. Name: `GMAIL_USER`
2. Secret: `zbmusiclessons@gmail.com`
3. Click **Add secret**

**Step 4.3 — Add GMAIL_APP_PASSWORD**

1. Click **New repository secret** again
2. Name: `GMAIL_APP_PASSWORD`
3. Secret: paste the 16-character App Password from Part 1
4. Click **Add secret**

You should now see two secrets listed:
- `GMAIL_APP_PASSWORD`
- `GMAIL_USER`

---

## PART 5 — Enable GitHub Pages

**Step 5.1 — Turn on Pages**

1. In the repo click **Settings** → **Pages** (left sidebar)
2. Under **Source** select **Deploy from a branch**
3. Under **Branch** select `main` and `/ (root)` folder
4. Click **Save**
5. GitHub shows a banner:
   "Your site is ready to be published at https://USERNAME.github.io/zbml-website"
   Give it 1–2 minutes to deploy

**Step 5.2 — Verify it works before adding the custom domain**

1. Visit `https://USERNAME.github.io/zbml-website`
2. Confirm the site loads and looks correct
3. Confirm the mobile hamburger menu works
4. Submit a test inquiry through the contact form
5. Go to the repo → **Actions** tab
6. You should see a workflow run called "Contact Form — Send Email Notification"
7. Click it — if all steps show green checkmarks, check the Gmail inbox
8. You should see the test inquiry email in zbmusiclessons@gmail.com

**Only proceed to Part 6 once the test submission works.**

---

## PART 6 — Custom Domain

You need to own the domain first. If you don't have one yet:
- Go to https://www.namecheap.com or https://cloudflare.com/products/registrar
- Search for your desired domain and purchase it (~$10–12/year)
- Then come back here

**Step 6.1 — Add the domain in GitHub Pages settings**

1. Repo → **Settings** → **Pages**
2. Under **Custom domain** type your domain: `zackbolandmusiclessons.com`
   (no www, no https://)
3. Click **Save**
4. GitHub will attempt a DNS check — it will fail initially, that's expected
   Leave this page open

**Step 6.2 — Add DNS records at your registrar**

Log into wherever you registered the domain (Namecheap, Cloudflare, GoDaddy, etc.)
and find the **DNS Management** or **Advanced DNS** section for the domain.

Add these four A records (for the root domain):

| Type | Host / Name | Value              | TTL  |
|------|-------------|--------------------|------|
| A    | @           | 185.199.108.153    | Auto |
| A    | @           | 185.199.109.153    | Auto |
| A    | @           | 185.199.110.153    | Auto |
| A    | @           | 185.199.111.153    | Auto |

Also add a CNAME record for www:

| Type  | Host / Name | Value                          | TTL  |
|-------|-------------|--------------------------------|------|
| CNAME | www         | YOUR-USERNAME.github.io        | Auto |

Replace `YOUR-USERNAME` with your actual GitHub username.

**Step 6.3 — Wait for DNS propagation**

This takes anywhere from 5 minutes to 48 hours depending on the registrar.
Typically under 1 hour. You can check progress at:
https://dnschecker.org — type your domain and check the A record

**Step 6.4 — Enforce HTTPS**

1. Once DNS propagates, GitHub will show a green checkmark next to your domain
2. The **Enforce HTTPS** checkbox will become clickable
3. Check it
4. GitHub provisions a free SSL certificate automatically
5. Your site is now live at `https://zackbolandmusiclessons.com`

**Step 6.5 — Update the CNAME file**

The `CNAME` file in this package contains `zackbolandmusiclessons.com`.
If you used a different domain, update that file:
1. Go to the repo → click `CNAME` → click the pencil icon
2. Replace the domain name with your actual domain
3. Commit the change

---

## PART 7 — Pointing the Existing Domain (zackbolanddrums.com)

If Zack wants to keep using `zackbolanddrums.com` instead of registering
a new one, the process is the same as Part 6 but you log into wherever
that domain is currently registered (likely GoDaddy or Namecheap since
it was a Wix site) and update the existing A records to GitHub's IPs,
replacing whatever Wix had them set to.

Note: changing the DNS will take the Wix site offline. Make sure the
GitHub Pages site is fully tested before making this switch.

---

## Summary Checklist

- [ ] Gmail 2FA enabled
- [ ] Gmail App Password generated and saved
- [ ] GitHub account created
- [ ] Repository created (public)
- [ ] index.html, CNAME uploaded
- [ ] contact-form.yml uploaded to .github/workflows/
- [ ] Fine-grained PAT created (Contents: read/write, zbml-website only)
- [ ] PAT inserted into the 3 config lines in index.html
- [ ] GMAIL_USER secret added to repo
- [ ] GMAIL_APP_PASSWORD secret added to repo
- [ ] GitHub Pages enabled (main branch, root folder)
- [ ] Test submission sent and email received in Gmail ✓
- [ ] Custom domain DNS records added at registrar
- [ ] DNS propagated (verified at dnschecker.org)
- [ ] Enforce HTTPS checked in Pages settings
- [ ] Site live at custom domain ✓

---

## Ongoing Maintenance Notes

**PAT expiry:** The fine-grained PAT expires in 1 year. Set a calendar
reminder. When it expires, generate a new one (same steps as Part 3)
and update the value in index.html.

**Gmail App Password:** Never expires unless you revoke it or change
your Google password. If you ever change the Gmail password, generate
a new App Password and update the GMAIL_APP_PASSWORD secret.

**Making site updates:** Edit index.html locally, then go to the repo
on GitHub, click index.html, click the pencil icon, paste the updated
content, and commit. GitHub Pages rebuilds in ~30 seconds.

**Action minutes:** Each contact form submission uses ~20–30 seconds of
GitHub Action compute time. The free tier gives 2,000 minutes/month.
At 30 seconds per run that's ~4,000 submissions before hitting the
limit — more than enough for a local music school.
