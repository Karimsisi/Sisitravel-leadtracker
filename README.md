# Sisi Travel — Leads CRM

A multi-agent travel CRM you host yourself for free on GitHub Pages. Agents log in with email + password; all leads are stored in Firebase Firestore and sync live across every signed-in agent.

**What you get**

- Shared lead pipeline (no more duplicated spreadsheets)
- Real-time sync — when one agent edits a lead, others see it instantly
- Email + password authentication, with you in control of who has accounts
- Pipeline dashboard with KPIs and charts (status, value, source, agent, nationality)
- Follow-up reminders (overdue / due soon / later)
- CSV export
- Free hosting (GitHub Pages) and free database (Firebase Spark plan)

Total cost for a small agency (under 20 agents, normal usage): **$0/month**.

---

## What's in this folder

| File | Purpose |
|---|---|
| `index.html` | The CRM web app. Single self-contained file. |
| `firestore.rules` | Database security rules — paste into Firebase. |
| `README.md` | This file. |

---

## Setup checklist (one-time, ~20 minutes)

You'll do five things:

1. Create a Firebase project (free).
2. Enable Authentication and Firestore inside that project.
3. Copy the Firebase config keys into `index.html`.
4. Create a GitHub repository, upload the files, turn on GitHub Pages.
5. Add an account for each agent.

You only do this once. After that, day-to-day is just sharing the URL.

---

### Step 1 — Create the Firebase project

1. Go to **https://console.firebase.google.com/** and sign in with a Google account (use a company account if you have one — that account becomes the project owner).
2. Click **Add project** → name it `sisi-travel-crm` → continue.
3. **Disable** Google Analytics for this project (you don't need it for a CRM) → **Create project** → wait for it to finish, then **Continue**.

### Step 2 — Add a Web App

1. On the project home, click the **`</>`** (Web) icon to register a new Web App.
2. Nickname: `Sisi CRM Web`. **Don't** check "Set up Firebase Hosting" (we'll use GitHub Pages instead). Click **Register app**.
3. Firebase will show a `firebaseConfig = { ... }` block. **Keep this tab open** — you'll copy from it in Step 4.
4. Click **Continue to console**.

### Step 3 — Enable Authentication and Firestore

**Authentication:**
1. Left sidebar → **Build → Authentication** → **Get started**.
2. Choose **Email/Password**, toggle the first switch on (you do **not** need passwordless email link), **Save**.

**Firestore Database:**
1. Left sidebar → **Build → Firestore Database** → **Create database**.
2. Pick a location near your agents (e.g. `eur3 (europe-west)` for Egypt/Europe). **Cannot be changed later** — pick once and move on.
3. Choose **Start in production mode** → **Enable**.
4. Once the database opens, click the **Rules** tab.
5. Open `firestore.rules` from this folder, copy the entire contents, paste it over the default rules in the Firebase editor, click **Publish**.

### Step 4 — Paste the config into `index.html`

1. Open `index.html` in any text editor (VS Code, Notepad, TextEdit).
2. Find the block near the top of the `<script type="module">` section that looks like:

   ```js
   const firebaseConfig = {
     apiKey: "REPLACE_ME",
     authDomain: "REPLACE_ME.firebaseapp.com",
     projectId: "REPLACE_ME",
     storageBucket: "REPLACE_ME.appspot.com",
     messagingSenderId: "REPLACE_ME",
     appId: "REPLACE_ME"
   };
   ```

3. Go back to Firebase Console → **Project settings** (gear icon, top-left) → scroll to **Your apps** → **SDK setup and configuration** → **Config**. Copy the values for `apiKey`, `authDomain`, `projectId`, `storageBucket`, `messagingSenderId`, and `appId`.
4. Paste them into `index.html`, replacing each `REPLACE_ME`. Save the file.

> The `apiKey` is **not** a secret — it's safe to commit to a public repo. Security is enforced by the Firestore rules you published in Step 3.

### Step 5 — Put it on GitHub Pages

If you've never used GitHub before, do this in the browser — no command line needed.

1. Go to **https://github.com/** and sign up / sign in.
2. Top-right **+ → New repository**.
   - Repository name: `sisi-crm`
   - Visibility: **Public** (required for free GitHub Pages)
   - Tick **Add a README file**
   - **Create repository**
3. On the new repo page, click **Add file → Upload files**.
4. Drag `index.html` and `firestore.rules` into the box. Scroll down → **Commit changes**.
5. Top of the repo, click **Settings** → left sidebar **Pages**.
6. Under **Build and deployment**:
   - Source: **Deploy from a branch**
   - Branch: **main** / **/ (root)** → **Save**.
7. Wait ~1 minute. Refresh the Pages settings page — you'll see a banner at the top: **"Your site is live at https://YOUR-USERNAME.github.io/sisi-crm/"**. That's your CRM URL. Open it.

You should see the orange Sisi Travel sign-in screen.

### Step 6 — Authorize the GitHub Pages domain in Firebase

Firebase blocks logins from domains it doesn't know about. Add yours:

1. Firebase Console → **Authentication** → **Settings** tab → **Authorized domains** → **Add domain**.
2. Enter `YOUR-USERNAME.github.io` (no `https://`, no path) → **Add**.

### Step 7 — Create an account for each agent

1. Firebase Console → **Authentication** → **Users** tab → **Add user**.
2. Enter the agent's email and a temporary password (e.g. `SisiTemp2026!`) → **Add user**.
3. Send the agent the URL, their email, and the temporary password. Tell them to sign in and (recommended) change their password later — see "Letting agents reset their own password" below.
4. Repeat for every agent.

To **revoke access**, just delete the user from this same screen. They lose access immediately.

---

## Day-to-day usage

- Bookmark the URL on every agent's browser.
- Each agent signs in with their email + password.
- Adding/editing/deleting a lead syncs in real time to every other signed-in agent. No save button needed beyond the modal.
- The "Assigned agent" field is free text — type the agent's name. (You could later swap this for a dropdown of fixed names.)
- Use **Export CSV** weekly for a backup.

---

## Letting agents reset their own password

The CRM doesn't currently have a "forgot password" link, but Firebase has a built-in flow. Until you add one, agents who forget their password should ask you to:

1. Firebase Console → **Authentication → Users** → click the agent → **Reset password** → enter a new temporary password.

Or if you want to add a self-serve reset link, ask Claude to "add a forgot password link to the login screen" and Claude will wire `sendPasswordResetEmail` to a button.

---

## Optional: restrict access to your company email domain

If every agent has a `@sisitravel.com.eg` email, you can lock the database down so only those addresses can read/write — even if some random Firebase account somehow gets created. Open `firestore.rules`, follow the comment block in there, and republish. Note: you also need to manually create the `@sisitravel.com.eg` accounts in Firebase Auth — Firebase doesn't automatically allow signups, which is what you want.

---

## Cost & limits (Firebase Spark / free plan)

- 50,000 document reads / day
- 20,000 writes / day
- 1 GB total storage
- Authentication: unlimited free users on email/password

A 15-agent team adding/editing ~100 leads/day uses well under 5% of the free tier. You only pay if you cross those limits, and Firebase will warn you long before you do.

---

## Troubleshooting

**"Setup needed" appears on first load** — You haven't replaced the `REPLACE_ME` placeholders in `index.html`. Re-do Step 4.

**"auth/operation-not-allowed"** — Email/password sign-in isn't enabled in Firebase. Re-do Step 3.

**"auth/unauthorized-domain"** — You haven't added your `github.io` domain to Firebase Authorized domains. Re-do Step 6.

**Permission denied on read/write** — Your Firestore rules weren't published. Re-do Step 3 (the Rules tab part) and make sure you clicked **Publish**.

**Page is blank** — Open the browser console (right-click → Inspect → Console tab) and read the error. Most often it's a typo in the config block.

**Agent can sign in but sees nothing** — That's normal on a fresh database. Click **+ New lead** and add one. It will sync to every other agent immediately.

---

## Updating the CRM later

When you want to change anything (add a column, change colors, add a feature):

1. Edit `index.html` locally (or ask Claude to do it).
2. In your GitHub repo, click `index.html` → pencil icon → paste the new contents → **Commit changes**.
3. GitHub Pages redeploys automatically in ~30 seconds.

---

Drafted for Sisi Travel · Karim · karim@sisitravel.com.eg
