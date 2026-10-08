<div align="center">

# Deployment guide

**GitHub → Vercel → Firebase**, in the order you will actually do them.

<sub>Each phase works on its own. After Phase 2 the site is live and judge-ready. Phases 3–5 are upgrades.</sub>

</div>

---

## Phase 0 — Five minutes before you push

- [ ] Open `README.md` and `SCORECARD.md`, replace every `https://stub-drmc.vercel.app` with your real Vercel URL *(do this after Phase 2 if you don't know it yet)*
- [ ] Open `index.html`, find `window.STUB_CONFIG` near the top of the `<script>` block, and change `adminEmails` to your own email
- [ ] Decide your demo organizer password and change it in `demoAccounts` (or keep the defaults so judges can sign in)
- [ ] Confirm no API key is anywhere in the repo:

```bash
grep -rn "gsk_" . --exclude-dir=.git
```

Every match should be documentation — this file, and the comment header of `api/assistant.js`, which only say that a key *starts with* `gsk_`. **If you see a real key (a long string after `gsk_`), remove it before pushing.**

---

## Phase 1 — GitHub

The repository must be **public** and carry an **MIT License**. Both are already set up.

### With the GitHub CLI

```bash
cd stub
gh repo create stub --public --source=. --push
```

### Or by hand

Create an empty repository named `stub` on github.com (no README, no .gitignore — this folder has them), then:

```bash
cd stub
git remote add origin https://github.com/<your-username>/stub.git
git branch -M main
git push -u origin main
```

> The folder already contains a git repository with the first commit made, so there is nothing to `git init` or `git add`.

- [ ] Repository exists and the code is on `main`
- [ ] **Settings → General → Danger Zone** shows the repo is **Public**
- [ ] `LICENSE` is visible on the repo home page and GitHub labels it *MIT*
- [ ] The README renders with the animated banner at the top

---

## Phase 2 — Vercel

```bash
cd stub
npx vercel --prod
```

Accept every default. Vercel serves `index.html` as the site and automatically turns `api/assistant.js` into a serverless function at `/api/assistant`.

- [ ] Deployment succeeded and you have a URL like `https://stub-xxxx.vercel.app`
- [ ] Opening it shows the homepage **with sample data already loaded**
- [ ] Register for an event and get a ticket
- [ ] Sign in at **Organizer** with the demo account
- [ ] Go back to Phase 0 and put this URL into `README.md` and `SCORECARD.md`, then commit and push:

```bash
git commit -am "Add live deployment URL" && git push
```

> **At this point you are submission-ready.** The site runs in demo mode: every visitor gets their own full copy of the sample data in their browser. Nothing can break, because there is no backend to break. Phases 3–5 make it a real shared system.

---

## Phase 3 — Groq (the Ask button)

Without a key the Ask button still answers, using built-in logic over the same event data. A key makes it conversational.

1. Create a key at [console.groq.com](https://console.groq.com) — it starts with `gsk_`
2. Vercel → your project → **Settings → Environment Variables** → add:

| Name | Value | Environments |
|---|---|---|
| `GROQ_API_KEY` | your `gsk_…` key | Production, Preview, Development |
| `GROQ_MODEL` | *(optional)* `openai/gpt-oss-120b` | all |

3. Redeploy so the variable is picked up:

```bash
npx vercel --prod
```

4. Verify: sign in as organizer → **Site settings → AI assistant → Check**

- [ ] It reports **Connected to Groq (openai/gpt-oss-120b)**
- [ ] Asking a question on the homepage gives an answer with **no** *"Instant answer"* label under it

> The key lives only in Vercel's environment. It never reaches the browser and is never committed.

---

## Phase 4 — Firebase (the real backend)

This is what turns Stub from "a demo on each visitor's device" into one shared system: registrations from every phone land in one place, the organizer dashboard shows everyone, and the live monitor sees real visitors.

### 4.1 Create the project

1. [console.firebase.google.com](https://console.firebase.google.com) → **Add project**
2. Build → **Firestore Database** → *Create database* → start in **production mode** → pick a region near Dhaka (`asia-south1`)
3. Build → **Authentication** → *Get started* → enable **Email/Password**

### 4.2 Create your organizer login

**Authentication → Users → Add user**

- [ ] Email: the same address you put in `adminEmails`
- [ ] Password: something only you know (this replaces the demo password in live mode)

### 4.3 Allow your domain

**Authentication → Settings → Authorized domains → Add domain**

- [ ] Add your Vercel domain, e.g. `stub-xxxx.vercel.app`

### 4.4 Publish the security rules

**Firestore Database → Rules** — delete what is there, paste the whole of [`firestore.rules`](firestore.rules), then:

- [ ] Change `organizer@stub.demo` on the `bootstrap()` line to **your** email
- [ ] Press **Publish**

<details>
<summary>What these rules actually protect — worth knowing before a judge asks</summary>

<br>

| Collection | Public can | Organizers can |
|---|---|---|
| `fests` · `events` · `notes` · `settings` | read | write |
| `regs` | read seat counts only — **no personal data** | everything |
| `tickets` | read **one** ticket if they know its code; edit or cancel only their own | list and manage all |
| `trx` | create only — **a used transaction ID can never be written again**, so one payment cannot be reused | read and clean up |
| `promos` | look up one code by name | list, create, edit |
| `invites` | read by key, add **one** member at a time up to the free slots | everything |
| `accounts` | read and edit only their own; `blocked` is not self-editable | read all, block, delete |
| `activity` · `drafts` | append only | read |
| `mail` | queue only their own confirmation | read all |

</details>

### 4.5 Point the app at Firebase

Firebase console → **Project settings** (gear) → *Your apps* → **Web app** (`</>`) → register → copy the `firebaseConfig` object.

In `index.html`, find `window.STUB_CONFIG` and fill it in:

```js
window.STUB_CONFIG = {
  firebase: {
    apiKey: "AIza…",
    authDomain: "your-project.firebaseapp.com",
    projectId: "your-project",
    storageBucket: "your-project.appspot.com",
    messagingSenderId: "…",
    appId: "1:…"
  },

  adminEmails: ['you@example.com'],   // must match the Authentication user you created

  assistantEndpoint: 'api/assistant',
  demoAccounts: [ /* leave for judges, or empty this array for a private launch */ ]
};
```

> These Firebase values are **meant to be public** — they identify your project, they do not grant access. Your security rules are what protect the data. This is why they can live in `index.html`, while the Groq key cannot.

```bash
git commit -am "Connect Firebase" && git push && npx vercel --prod
```

### 4.6 Load the data

Open the site → **Organizer** → sign in with the Firebase user you created.

You will land on the **setup wizard** because Firestore is empty. Either:

- **Run the wizard** — club name, payment number, first fest. Best for real use.
- **Or load the sample set** — *Backup and rebuild → Load demo data*. Best for judging: it writes all 4 fests, 11 events and ~300 registrations into Firestore.

- [ ] Firestore → Data now shows collections: `fests`, `events`, `tickets`, `regs`, `settings`, `accounts`, …
- [ ] Open the site in a **different browser** — the same events appear
- [ ] Register there, then check the organizer dashboard in the first browser — the registration is visible

### 4.7 Configure payments

**Site settings → Payments** — replace the demo numbers with your club's real bKash / Nagad / Rocket numbers, then set each event's fee under *Fests and events*.

- [ ] Your own numbers appear on the registration form

---

## Phase 5 — Confirmation emails (optional)

1. Firebase console → **Extensions** → install **Trigger Email from Firestore**
2. Configure it:

| Setting | Value |
|---|---|
| Email documents collection | `mail` |
| SMTP connection URI | your provider's — for Gmail, `smtps://you@gmail.com@smtp.gmail.com:465` with an **app password** |
| Default FROM address | your no-reply address |

3. In the app: **Site settings → Emails** → turn on *Send automatic emails*
4. Test: dashboard → **Emails** → *Send a test to me*

- [ ] The test email arrives
- [ ] Confirming a pending registration sends the participant a ticket email

---

## Final pre-submission check

- [ ] Repo is **public** and the link opens for a logged-out visitor
- [ ] `LICENSE` present, GitHub shows *MIT*
- [ ] README has all 13 sections and the real deployment URL
- [ ] Deployment URL opens with sample data, no login wall
- [ ] Demo organizer credentials in the README actually sign in
- [ ] Register → confirm → check-in works end to end on the live URL
- [ ] Open the live URL on a phone and a tablet
- [ ] Submitted the repository link via the official Google Form **before 9 October, 11:59 PM**

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Site loads but says *"Demo mode"* after Phase 4 | `STUB_CONFIG.firebase` still `null`, or Firebase failed to start | Check the browser console. The app falls back to demo mode on purpose rather than showing a blank page |
| *"This email is not on the organizer team"* | Email missing from `adminEmails`, or a different address than the Authentication user | Make the two match exactly, lowercase |
| Sign-in fails with a correct password | Vercel domain not in **Authorized domains** | Phase 4.3 |
| Dashboard is empty after connecting Firebase | Firestore has no data yet | *Backup and rebuild → Load demo data*, or run the wizard |
| *Missing or insufficient permissions* in the console | Rules not published, or `bootstrap()` still has the demo email | Phase 4.4 |
| Ask button always shows *"Instant answer"* | `GROQ_API_KEY` missing, or you didn't redeploy after adding it | Phase 3, then **Site settings → AI assistant → Check** |
| Camera check-in won't open | Needs HTTPS and camera permission | Use the live Vercel URL, not a local file. The manual code box always works |
| Emails never arrive | Extension not installed, or SMTP rejected | Firebase → Extensions → logs. Phase 5 |

---

<div align="center">
<sub>Back to <a href="README.md">README</a> · <a href="SCORECARD.md">Judge's scorecard</a></sub>
</div>
