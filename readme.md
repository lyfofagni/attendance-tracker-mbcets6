<div align="center">

# 📋 S7 CS1 · MBCET Attendance Tracker

A single-file, installable attendance tracker for MBCET CSE S7 — mark periods, track subject-wise percentages, and stay ahead of the 75% cutoff.

![Made with](https://img.shields.io/badge/made%20with-HTML%20%7C%20CSS%20%7C%20JS-8ab4f8?style=flat-square)
![Backend](https://img.shields.io/badge/backend-Firebase-ffca28?style=flat-square)
![Deploy](https://img.shields.io/badge/deploy-GitHub%20Pages-2ea44f?style=flat-square)
![Status](https://img.shields.io/badge/status-active-brightgreen?style=flat-square)

</div>

---

## 📑 Table of contents

<details>
<summary>Click to expand</summary>

- [Overview](#-overview)
- [Features](#-features)
- [Screenshots](#-screenshots)
- [Tech stack](#-tech-stack)
- [Getting started](#-getting-started)
  - [1. Clone the repo](#1-clone-the-repo)
  - [2. Set up Firebase](#2-set-up-firebase)
  - [3. Run locally](#3-run-locally)
  - [4. Deploy to GitHub Pages](#4-deploy-to-github-pages)
- [How attendance is calculated](#-how-attendance-is-calculated)
- [Data model](#-data-model)
- [Project structure](#-project-structure)
- [Roadmap](#-roadmap)
- [FAQ](#-faq)
- [License](#-license)

</details>

---

## 🧭 Overview

This is a **single HTML file** (no build step, no framework) that runs entirely in the browser and syncs to **Firebase Authentication + Firestore**, so your attendance data follows you across devices and survives redeploys.

Built around a fixed weekly timetable for S7 CS1, it lets you:

- Mark each period as **Present / Absent / No class / Holiday**
- Swap a period's subject on the fly (for substitutions)
- See live subject-wise and overall attendance percentages
- Browse a **monthly calendar** of your attendance at a glance
- Track **leave days** and **holidays** as whole-day summaries
- Add **special weekend classes** when they happen

> 💡 The whole app lives in one `index.html` file — copy it, plug in your own Firebase project, and it's yours.

---

## ✨ Features

<details open>
<summary><strong>📅 Daily tracker</strong></summary>

- Auto-loads the day's periods from the built-in weekly timetable
- Tap to mark **Present / Absent / No class / Holiday** — tap again to unmark
- **Swap** any period's subject (e.g. a substituted class) without losing the original
- One-tap **"Mark whole day as holiday"**
- Navigate with `‹` / `›` or jump back with **Today**

</details>

<details>
<summary><strong>📊 Attendance stats</strong></summary>

- Overall and per-subject attendance percentage
- **Color-coded thresholds:**

  | Range | Color | Meaning |
  |---|---|---|
  | ≥ 85% | 🟢 Green | Safe |
  | 75% – 84% | 🟡 Yellow | Watch closely |
  | < 75% | 🔴 Red | Below the condonation cutoff |

- Filter stats by **All time** or any individual **month**
- Optional toggle to combine Theory + Lab sessions into one subject line

</details>

<details>
<summary><strong>🗓️ Monthly calendar view</strong></summary>

- Full month grid, color-coded per day:
  - 🟢 Full present · 🟡 Partial · 🔴 Full absent · 🟣 Holiday · ⬜ Weekend / no class
- Navigate month-to-month with `‹` / `›`
- Tap any date to jump straight into that day's period list
- Quick summary of full-present / full-absent / partial / holiday day counts for the month

</details>

<details>
<summary><strong>🎓 Special weekend classes</strong></summary>

- Weekends default to "No classes" and stay untouched
- Flip **"Enable special class day"** when a weekend class actually happens
- Add custom periods (time + subject) just for that day, mark them like any other period, and remove them if plans change

</details>

<details>
<summary><strong>🌴 Leave & holiday log</strong></summary>

- **Leave days** — every day where you were absent for *all* held periods, grouped by month
- **Holidays** — every day marked entirely as a holiday, grouped by month
- Quick way to see exactly how many full days you've missed vs. how many were official holidays

</details>

<details>
<summary><strong>🔐 Auth & sync</strong></summary>

- Email/password sign-in and sign-up via Firebase Auth
- All data scoped per-user in Firestore (`users/{uid}/data/{key}`)
- Installable as a PWA-style home screen app on iOS/Android (Apple touch icon + theme color included)

</details>

---

## 📸 Screenshots

<details>
<summary>Click to preview (add your own screenshots here)</summary>

| Today | Stats | Monthly | Leaves |
|---|---|---|---|
| _add screenshot_ | _add screenshot_ | _add screenshot_ | _add screenshot_ |

</details>

---

## 🛠 Tech stack

- **HTML/CSS/vanilla JS** — no framework, no bundler, one file
- **Firebase Authentication** — email/password sign-in
- **Cloud Firestore** — per-user key/value storage for daily records
- **Google Fonts** — Google Sans / Google Sans Code

---

## 🚀 Getting started

### 1. Clone the repo

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
```

### 2. Set up Firebase

<details>
<summary>Step-by-step</summary>

1. Go to the [Firebase console](https://console.firebase.google.com/) → **Create project**
2. Enable **Authentication → Sign-in method → Email/Password**
3. Enable **Cloud Firestore** (start in production mode; rules below)
4. Go to **Project settings → General → Your apps → Web app**, copy the config object
5. Paste it into `index.html`, replacing the existing `firebaseConfig`:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "...",
  measurementId: "..."
};
```

6. Set Firestore security rules so users can only read/write their own data:

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/data/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

</details>

### 3. Run locally

No build step needed — just open the file, or serve it:

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

### 4. Deploy to GitHub Pages

1. Push `index.html` to your repo's default branch (or a `/docs` folder)
2. Go to **Settings → Pages** → set source to that branch/folder
3. Your tracker will be live at `https://<your-username>.github.io/<your-repo>/`

---

## 🧮 How attendance is calculated

- A period counts toward attendance only if marked **Present** or **Absent** — `No class` and `Holiday` are excluded entirely
- **Subject %** = periods present ÷ periods held, for that subject
- **Overall %** = total periods present ÷ total periods held, across all subjects
- **Leave day** = a day where every counted period was marked Absent
- **Holiday day** = a day where every period was marked Holiday

---

## 🗃 Data model

Each day is stored as a single Firestore document, keyed by date:

```
users/{uid}/data/day:2026-07-24
```

```json
[
  {
    "id": "p1",
    "time": "09:15–10:15",
    "code": "CD",
    "name": "Compiler Design",
    "origCode": "CD",
    "origName": "Compiler Design",
    "status": "present"
  }
]
```

Weekend special-class mode is tracked separately:

```
users/{uid}/data/special:2026-07-25   → "1" | "0"
```

---

## 📂 Project structure

```
.
├── index.html   # entire app — markup, styles, and logic
└── README.md
```

---

## 🗺 Roadmap

- [ ] Export attendance summary as PDF/CSV
- [ ] Push notifications when a subject drops below 75%
- [ ] Dark/light theme toggle
- [ ] Editable timetable (currently hardcoded per weekday)

---

## ❓ FAQ

<details>
<summary>Can I use this for a different timetable/semester?</summary>

Yes — edit the `TEMPLATE`, `SUBJECTS`, `TERM_START`, and `TERM_END` constants near the top of the `<script>` block.

</details>

<details>
<summary>Is my data private?</summary>

Yes, as long as your Firestore security rules restrict access to `request.auth.uid == userId` (see setup above). Each user only sees their own `users/{uid}` subtree.

</details>

<details>
<summary>Does this work offline?</summary>

Not currently — every read/write goes straight to Firestore. Offline persistence could be added via the Firestore SDK's offline cache.

</details>

---

## 📄 License

This project is unlicensed by default — add an [MIT License](https://choosealicense.com/licenses/mit/) or your preferred license here if you plan to share the repo publicly.

<div align="center">

Made for MBCET CSE S7 · 2026

</div>
