<div align="center">

<img src="docs/banner.svg" alt="Stub — pick an event, get a ticket, show it at the gate" width="100%">

<br>

**The registration desk for a school or college club.**
Browse fests, register in a minute, pay by bKash, and get a QR ticket that gets scanned at the gate.

<br>

[![Live demo](https://img.shields.io/badge/Live_demo-stub--drmc.vercel.app-FF5A2C?style=for-the-badge&logoColor=white)](https://stub-drmc.vercel.app)
[![License](https://img.shields.io/badge/License-MIT-1B1210?style=for-the-badge)](LICENSE)
[![Single file](https://img.shields.io/badge/Frontend-1_HTML_file-6A5848?style=for-the-badge)](index.html)
[![No build](https://img.shields.io/badge/Build_step-none-2f7a3a?style=for-the-badge)](#5-setup-instructions)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/tahsanmasum/stub)

<sub>Built for the **AI Web Development Contest**, 9th DRMC International Tech Carnival 2026 · theme: *Smart Club Operations*</sub>

</div>

<br>

<img src="docs/flow.svg" alt="How a registration travels: browse, register, pay, verify, check in" width="100%">

<br>

<div align="center">

### Try it in 60 seconds

| | |
|---|---|
| **1** | Open the [live demo](https://stub-drmc.vercel.app) — it loads with 4 fests, 11 events and ~300 registrations already in it |
| **2** | Register for **AI Web Development Contest**. On the payment step, type the promo code `VOLUNTEER` |
| **3** | Open **Organizer** → sign in with `organizer@stub.demo` / `carnival2026` |
| **4** | Go to **Check-in**, paste the ticket code you just got, and watch the ticket get punched |

</div>

---

## Contents

<table>
<tr>
<td valign="top" width="50%">

1. [Project name](#1-project-name)
2. [Project description](#2-project-description)
3. [Features](#3-features)
4. [Tech stack](#4-tech-stack)
5. [Setup instructions](#5-setup-instructions)
6. [Deployment URL](#6-deployment-url)
7. [Demo credentials](#7-demo-credentials)

</td>
<td valign="top" width="50%">

8. [Third-party services and APIs](#8-third-party-services-and-apis)
9. [AI tools used](#9-ai-tools-used)
10. [Screenshots](#10-screenshots)
11. [Known limitations](#11-known-limitations)
12. [License](#12-license)
13. [Rules and decisions](#13-rules-and-decisions)

</td>
</tr>
</table>

---

## 1. Project name

# Stub

Named after the part of a ticket you keep.

---

## 2. Project description

Most school and college clubs run registration on a Google Form, then copy the rows into a spreadsheet, then chase people on Messenger to confirm payments, then print an attendance sheet and tick names off with a pen at the gate.

**Stub replaces that whole chain** with one site that follows the structure clubs actually have:

```
Organization        →  Fest                →  Event                      →  Registration
DRMC IT Club           Tech Carnival 2026     AI Web Development Contest    TC26-9F7A2B
```

A visitor browses fests, opens an event, fills a short form, sends the entry fee by bKash and types the transaction ID. The organizer matches that TrxID against their bKash statement with one click, and the registration turns into a confirmed QR ticket. On the day, a volunteer scans that QR at the gate with a phone.

Everything in between — waitlists, team invite links, promo codes, refunded seats, unfinished forms, Bangla, confirmation emails, printable ID badges — is handled by the same single file.

<br>

<div align="center">
<img src="docs/gif/register.gif" alt="Registering for an event: form, bKash payment with transaction ID, and the ticket that comes out" width="90%">
<br><sub><b>Register → pay by bKash → get a ticket.</b> Recorded from the running app.</sub>
</div>

---

## 3. Features

### The public site

<table>
<tr><td width="60%">

- **Fest directory** sorted by what matters now: happening → upcoming → past
- **Event cards** showing date, category, team size, deadline, live seat meter, fee and state (`Open` · `Closes tomorrow` · `Full, waitlist open` · `Registration closed`)
- **Live search** plus category chips, fest / availability filters and three sort orders
- **Day-by-day fest programme**
- **Homepage news**, a countdown to the next fest, a school leaderboard and trending events
- **FAQ** and partners, all editable by organizers

</td><td width="40%">

<img src="docs/img/02-events.jpg" alt="Event directory with filters">

</td></tr>
</table>

### Registration

- Multi-step form — **about you → team → payment → review** — with inline validation for Bangladeshi phone numbers, team sizes, links and transaction IDs
- **Capacity, deadlines and duplicates enforced.** Full events offer a waitlist; closed, paused and not-yet-open events explain exactly why
- **Team invite links** — the lead registers, shares a link, and each teammate adds their own details
- **Promo codes** with percent or taka off, per-event limits, expiry, and a 100% code that skips payment entirely
- **Progress is saved** — a half-filled form comes back after a reload, and organizers can see who started but did not finish
- **Schedule clash warning** when two of your events overlap
- Tickets can be **edited or cancelled** until the deadline, added to a calendar, printed as PDF, or recovered on another device with code + email

### Payments

Participants pay by **bKash, Nagad, Rocket, Upay or bank**, then enter the TrxID and the number they paid from. Cash at the desk is allowed per event.

<table>
<tr><td width="42%"><img src="docs/img/07-payment.jpg" alt="bKash payment step"></td>
<td width="58%">

- The exact amount, the number with a **copy button**, and step-by-step instructions
- **A transaction ID can never be used twice** — blocked in the browser *and* by a Firestore rule
- Organizers verify from a dedicated desk, or paste their **whole bKash statement** and every matching TrxID is verified at once
- Rejected payments come back with a reason and a **resubmit** form on the participant's own ticket
- Check-in refuses to let an unpaid ticket through until the fee is collected

</td></tr>
</table>

### Organizer dashboard

<div align="center">
<img src="docs/gif/admin.gif" alt="Organizer dashboard tour: overview, live monitor, payments, check-in" width="90%">
</div>

<details>
<summary><b>Overview, live monitor and accounts</b> — click to expand</summary>

<br>

- **Today's briefing** — a plain-language summary written by ordinary code (no AI, no API): registrations today vs yesterday, the most popular event, what closes within 48 hours, which events are full, how much money is waiting to be matched, and who did not finish their form
- **Live monitor** — who is on the site right now, a live activity feed, and a drop-off funnel: *visited → opened an event → started a form → registered → paid*
- **Unfinished registrations** with name, phone, the step they reached, and a one-click reminder email
- **Visitor analytics** — devices, traffic sources (Facebook, Messenger, Google, WhatsApp, poster QR), a visits-by-hour heatmap, and conversion per event
- **Accounts** — everyone who signed up, when they were last seen, their tickets, and block or delete

<img src="docs/img/13-monitor.jpg" alt="Live monitor">

</details>

<details>
<summary><b>Running the fest</b> — events, participants, check-in, badges</summary>

<br>

- Create, edit, duplicate, pause or delete fests and events; set capacity, waitlist size, team size, fee, open/close times, auto-confirm and **custom questions per event**
- **Participants table** with search across name, email, phone, team and ticket code; filters by fest, event, status and payment; inline status changes and bulk actions
- **Walk-in registration** at the desk, printable sign-in sheets, and **printable ID badges** for every confirmed person including teammates
- **QR check-in** from a phone camera, with a manual code box when there is no camera; already-checked-in, cancelled and unpaid tickets are all caught
- **Waitlist promotion is automatic** when a seat frees up
- **Share an event** as a printable QR poster, or to Facebook and WhatsApp — poster scans are tracked as their own traffic source
- CSV export everywhere

<img src="docs/img/22-badges.jpg" alt="Printable ID badges">

</details>

<details>
<summary><b>Running the site</b> — settings, roles, backups</summary>

<br>

- **Site settings without code** — club name, logo upload, headline, accent colour, default theme, homepage sections on/off, FAQ, partners, contact and socials, announcement bar
- **Team roles** — Owner, Manager, Volunteer. A volunteer only ever sees check-in and a read-only participant list
- **Backup and restore** the whole site as one JSON file
- **Recreate a fest for next year** — copies the fest and all its events with every date shifted forward
- **Start fresh** with a three-step setup wizard, or reload the demo data for training volunteers
- **Confirmation emails** on confirm, payment rejection and waitlist promotion, sent from your own no-reply address

<img src="docs/img/18-settings.jpg" alt="Site settings">

</details>

### বাংলা and English

<table>
<tr><td width="50%">

One button in the header switches the entire interface. Dates, seat counts, statuses, form errors and the FAQ all change language. Organizers can enter Bangla versions of the headline, the announcement bar, each FAQ entry and every news post.

Bangla typography uses Hind Siliguri and Noto Serif Bengali with its own line-height, so it never looks like English text with Bangla glyphs dropped in.

</td><td width="50%">

<img src="docs/gif/theme.gif" alt="Switching dark/light theme and English/Bangla">

</td></tr>
</table>

### AI help desk

<table>
<tr><td width="50%">

<img src="docs/gif/assistant.gif" alt="Asking the help desk assistant a question">

</td><td width="50%">

An **Ask** button on every public page answers from the live event data — fees, deadlines, seats left, team sizes, payment numbers, the FAQ, the news, and an *extra knowledge* box the organizer fills in.

It runs on **Groq** (`openai/gpt-oss-120b`) through a serverless function, so the API key never reaches the browser. It replies in Bangla when the site is in Bangla.

With no key configured it falls back to **built-in instant answers** computed from the same data, so the button never breaks — and every question asked shows up on the Live monitor, so organizers learn what belongs in the FAQ.

</td></tr>
</table>

### Built to a quality floor

`Keyboard navigable` · `Focus trapped in dialogs` · `Visible focus rings` · `prefers-reduced-motion respected` · `Semantic landmarks` · `Responsive to 390 px` · `Print stylesheets for tickets, badges, posters and certificates`

---

## 4. Tech stack

| Layer | Choice | Why |
|---|---|---|
| **Frontend** | Plain HTML, CSS and JavaScript — **no framework, no build step** | The repo is exactly what runs. Nothing to compile, nothing to go stale |
| **Routing** | Hash-based router | Deploys on any static host with zero config |
| **Data** | `Store` module with two adapters | Same interface, two backends |
| ↳ *demo* | `localStorage`, seeded on first visit, synced across tabs | Judges get a full site with no setup |
| ↳ *live* | **Cloud Firestore** with realtime listeners + **Firebase Auth** | Shared data across every device |
| **AI** | **Groq** `openai/gpt-oss-120b` via a Vercel serverless function | Key stays server-side |
| **Email** | Firebase **Trigger Email** extension | Sends from your own no-reply address |
| **Hosting** | Vercel (static + one function) | One command to deploy |
| **Type** | Fraunces · Plus Jakarta Sans · Hind Siliguri · Noto Serif Bengali | Loaded from Google Fonts |

<details>
<summary><b>Repo map</b></summary>

```
stub/
├── index.html           the entire app — UI, data layer, both backends, Firestore rules
├── api/
│   └── assistant.js     Vercel serverless function; holds the Groq key server-side
├── firestore.rules      same rules as the block inside index.html, split out for reference
├── docs/
│   ├── banner.svg       animated hero (the QR in it is real — it scans to the live site)
│   ├── flow.svg         animated pipeline diagram
│   ├── gif/             screen recordings of the running app
│   └── img/             screenshots
├── LICENSE
└── README.md
```

Inside `index.html`, the code is organised in labelled sections, in load order:

| Section | What it holds |
|---|---|
| `config` | the only part you normally edit |
| `seed` | deterministic demo data — 4 fests, 11 events, ~300 registrations |
| `store` | data layer, rules for seats/deadlines/waitlists/payments, both adapters |
| `i18n` | বাংলা / English switch |
| `ui` | drawers, dialogs, toasts, QR, calendar files, CSV |
| `public` | directory, fest, event, registration, ticket, team invites |
| `account` | participant accounts and certificates |
| `assistant` | help desk widget and the built-in answer engine |
| `admin` · `admin2` · `admin3` | organizer dashboard |
| `main` | router and app shell |

</details>

---

## 5. Setup instructions

### Run it locally

```bash
git clone https://github.com/tahsanmasum/stub.git
cd stub
python3 -m http.server 8080      # or: npx serve .
# open http://localhost:8080
```

The site opens with sample data straight away. No install, no build, no database.

### Deploy to Vercel

```bash
npx vercel --prod
```

Accept the defaults. Vercel serves `index.html` and turns `api/assistant.js` into the assistant's endpoint automatically.

<details>
<summary><b>Optional — turn on the Groq assistant</b></summary>

<br>

1. Get a key from [console.groq.com](https://console.groq.com) (it starts with `gsk_`)
2. In Vercel → your project → **Settings → Environment Variables**, add:

   | Name | Value |
   |---|---|
   | `GROQ_API_KEY` | your key |
   | `GROQ_MODEL` | *(optional)* defaults to `openai/gpt-oss-120b` |

3. Redeploy with `npx vercel --prod`
4. Sign in as organizer → **Site settings → AI assistant → Check**. It should say *Connected to Groq*

> The key lives only in Vercel's environment. It is never in `index.html`, never in this repo, and never sent to the browser. Without it the assistant still answers from built-in logic.

</details>

<details>
<summary><b>Optional — switch to Firebase for shared live data</b></summary>

<br>

Demo mode keeps data in each visitor's own browser. Firebase makes it shared and realtime across every device.

1. Create a Firebase project. Enable **Cloud Firestore** and **Authentication → Email/Password**
2. Under Authentication, add your owner email and a password
3. Under **Authentication → Settings → Authorized domains**, add your Vercel domain
4. Open **Firestore → Rules**, paste the contents of [`firestore.rules`](firestore.rules), change `organizer@stub.demo` to your email, and publish
5. In `index.html`, find `STUB_CONFIG` near the top of the script and fill in:

   ```js
   window.STUB_CONFIG = {
     firebase: {
       apiKey: "…", authDomain: "your-project.firebaseapp.com",
       projectId: "your-project", storageBucket: "your-project.appspot.com",
       messagingSenderId: "…", appId: "…"
     },
     adminEmails: ['you@example.com'],
     …
   };
   ```

6. Redeploy, sign in as organizer, then either follow the setup wizard or use **Backup and rebuild → Load demo data**

If Firebase fails to start for any reason, the app falls back to demo mode instead of showing a blank page.

</details>

<details>
<summary><b>Optional — confirmation emails from your own address</b></summary>

<br>

1. In the Firebase console install the **Trigger Email from Firestore** extension
2. Set the collection to `mail`
3. Enter the SMTP details for your no-reply address (for Gmail, an app password works)

Stub writes each email into `mail`, the extension sends it, and the delivery status shows on the dashboard's **Emails** page. In demo mode the same emails are recorded there with a preview instead of being sent.

</details>

---

## 6. Deployment URL

> ### 🔗 **https://stub-drmc.vercel.app**

Demo mode is on, so judges get a complete site with sample data on first load and nothing to sign up for.

---

## 7. Demo credentials

Participants do **not** need an account — registration works as a guest. These are for the organizer dashboard. The sign-in page also has a one-click **Use this** link for each.

| Role | Email | Password | Sees |
|---|---|---|---|
| **Owner** | `organizer@stub.demo` | `carnival2026` | Everything, including settings, team and data |
| **Manager** | `manager@stub.demo` | `manage2026` | Events, participants, payments, promos, accounts, monitor, news, emails, check-in |
| **Volunteer** | `volunteer@stub.demo` | `gate2026` | Gate check-in and a read-only participant list |
| **Participant** | `student@stub.demo` | `ticket2026` | A sample account with saved tickets |

<details>
<summary><b>Things worth trying as a judge</b></summary>

<br>

| Try this | What it shows |
|---|---|
| Register for **AI Web Development Contest** | Team event with a custom question and an invite link |
| On the payment step, enter `VOLUNTEER` | A 100% promo code that skips payment entirely |
| Or enter `WINTER30` on a Winter Tech Fest event | 30% off, with the discounted amount recalculated live |
| Register for **Programming Contest**, then **Project Showcase** | The schedule clash warning |
| Open **Robotics Challenge** | Full event → waitlist, with automatic promotion |
| Open **Gaming Tournament** / **Coding Challenge** | Deadline passed / not open yet, each with a reason |
| Reuse a transaction ID | Blocked, with a clear message |
| Dashboard → **Payments** → *Match from statement* | Paste statement text, every matching TrxID verifies at once |
| Dashboard → **Live monitor** | The funnel, the heatmap, and who did not finish their form |
| Dashboard → **Backup and rebuild** → *Recreate a fest* | Next year's fest built from this year's, dates shifted |
| Sign in as the **volunteer** account | Role limits in action |
| Press the **বাং** button anywhere | The whole site in Bangla |

</details>

---

## 8. Third-party services and APIs

| Service | Used for | License / terms |
|---|---|---|
| [Groq](https://groq.com) · `openai/gpt-oss-120b` | AI help desk answers | Groq API terms |
| [Firebase](https://firebase.google.com) — Firestore, Auth, Trigger Email | Shared live data, organizer and participant accounts, outgoing email | Firebase terms |
| [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) | Ticket, poster and badge QR codes | MIT |
| [jsQR](https://github.com/cozmo/jsQR) | QR scanning fallback when `BarcodeDetector` is unavailable | Apache 2.0 |
| [Fraunces](https://github.com/undercasetype/Fraunces) | Display typeface | SIL OFL 1.1 |
| [Plus Jakarta Sans](https://github.com/tokotype/PlusJakartaSans) | Interface typeface | SIL OFL 1.1 |
| [Hind Siliguri](https://github.com/itfoundry/hind-siliguri) · [Noto Serif Bengali](https://fonts.google.com/noto/specimen/Noto+Serif+Bengali) | Bangla typefaces | SIL OFL 1.1 |
| Browser APIs | `BarcodeDetector`, `MediaDevices` (camera), `Clipboard`, `Web Crypto`, `IntersectionObserver`, `MutationObserver` | — |
| [Vercel](https://vercel.com) | Hosting and the serverless function | — |

---

## 9. AI tools used

**Claude (Anthropic)** was used throughout development to plan the architecture, write the code, design the interface, generate the sample data, write this README, and run automated browser tests with Playwright that walk through registration, promo codes, waitlists, team invites, payments, cancellation, organizer actions and check-in — on desktop and mobile, in both themes and both languages.

**Groq** (`openai/gpt-oss-120b`) runs *inside the product* as the help desk assistant described above. It is the only AI that runs at runtime, it only ever sees the public event data the page sends it, and the site works fully without it.

Every participant name, school, team name and transaction ID in the demo data is fictional and generated.

---

## 10. Screenshots

> All screenshots are the light theme. The site also ships a dark theme — the toggle is in the header.

<div align="center">

### The public site

<table>
<tr>
<td width="50%"><img src="docs/img/01-home.jpg" alt="Homepage"><br><sub align="center"><b>Homepage</b> — countdown, fanned tickets closing soonest</sub></td>
<td width="50%"><img src="docs/img/05-event.jpg" alt="Event page"><br><sub><b>Event page</b> — rules, prizes, seat meter, live countdown</sub></td>
</tr>
<tr>
<td width="50%"><img src="docs/img/04-fest.jpg" alt="Fest programme"><br><sub><b>Fest programme</b> — day by day</sub></td>
<td width="50%"><img src="docs/img/03-news.jpg" alt="Homepage news"><br><sub><b>News</b> — posted by organizers, pinned to the top</sub></td>
</tr>
<tr>
<td width="50%"><img src="docs/img/08-ticket.jpg" alt="Ticket"><br><sub><b>The ticket</b> — QR, code, stamp, invite link</sub></td>
<td width="50%"><img src="docs/img/10-bangla.jpg" alt="Homepage in Bangla"><br><sub><b>বাংলা</b> — the whole interface, not just labels</sub></td>
</tr>
</table>

### The organizer dashboard

<table>
<tr>
<td width="50%"><img src="docs/img/12-dashboard.jpg" alt="Overview"><br><sub><b>Overview</b> — today's briefing, KPIs, 14-day chart</sub></td>
<td width="50%"><img src="docs/img/13-monitor.jpg" alt="Live monitor"><br><sub><b>Live monitor</b> — funnel, live feed, unfinished forms</sub></td>
</tr>
<tr>
<td width="50%"><img src="docs/img/14-payments.jpg" alt="Payments desk"><br><sub><b>Payments</b> — match TrxIDs against your statement</sub></td>
<td width="50%"><img src="docs/img/15-participants.jpg" alt="Participants"><br><sub><b>Participants</b> — search, filter, bulk actions</sub></td>
</tr>
<tr>
<td width="50%"><img src="docs/img/17-checkin.jpg" alt="Check-in"><br><sub><b>Check-in</b> — camera scan or typed code</sub></td>
<td width="50%"><img src="docs/img/19-promos.jpg" alt="Promo codes"><br><sub><b>Promo codes</b> — percent or taka, limits, expiry</sub></td>
</tr>
</table>

### On a phone

<img src="docs/img/mobile.jpg" alt="Stub on a phone: homepage, event page, Bangla homepage, organizer dashboard" width="100%">

<sub>Homepage · Event page · বাংলা · Organizer dashboard — the sidebar becomes a tab strip</sub>

</div>

---

## 11. Known limitations

Written honestly, because a judge will find these anyway.

| Limitation | Detail |
|---|---|
| **Demo mode is per-browser** | Judges see their own registrations in the dashboard on the same browser and across tabs, but not across devices. Switching to Firebase makes the data shared |
| **No online payment gateway** | There is no bKash merchant API integration — that needs a registered merchant account. Participants send money manually and submit the TrxID, and an organizer verifies it. This is how most Dhaka club fests already work |
| **Email needs the Firebase extension** | Without it, emails are recorded on the Emails page with a preview instead of being delivered |
| **Seat checks happen in the browser** | In Firebase mode two people could take the very last seat at the same instant. A Cloud Function with a transaction would close this; the Firestore rules already prevent the more damaging case of a reused transaction ID |
| **Waitlist promotion runs while an organizer is signed in** | In Firebase mode only organizers can edit someone else's ticket, so promotion happens on their next dashboard load |
| **Camera check-in needs HTTPS** | And camera permission. The manual code box always works |
| **Times use the viewer's time zone** | Fine for one city, worth noting for anyone else |
| **Roles are enforced in the dashboard** | The Firestore rules separate owners from other organizers; the Manager/Volunteer split is enforced in the app, not in the database |
| **Groq answers need the serverless function** | Opened as a bare file or without a key, the assistant uses its built-in answers instead |

---

## 12. License

[MIT](LICENSE) © 2026 **Tahsan Masum Fahim**

Bundled fonts and libraries keep their own licenses, listed in [section 8](#8-third-party-services-and-apis).

---

## 13. Rules and decisions

> The organizing authority reserves the right to make the final decision regarding rule interpretation, eligibility, judging, scoring, and any matters not explicitly covered in the contest guidelines. All decisions made by the judging panel and organizing authority are final.

---

<div align="center">
<br>

**Tahsan Masum Fahim**
Department of Electrical and Electronic Engineering
Independent University, Bangladesh

[![GitHub](https://img.shields.io/badge/GitHub-tahsanmasum-1B1210?style=flat-square)](https://github.com/tahsanmasum)
[![Portfolio](https://img.shields.io/badge/Portfolio-tahsanmasum.vercel.app-FF5A2C?style=flat-square)](https://tahsanmasum.vercel.app)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-tahsanmasum-6A5848?style=flat-square)](https://linkedin.com/in/tahsanmasum)

<br>
<sub>Built for the 9th DRMC International Tech Carnival 2026</sub>

</div>
