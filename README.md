# Your Attn Buddy

A student attendance and assignment tracker built around how college timetables actually work — theory and lab classes tracked (and targeted) separately, labs occupying their real two-hour block, and every subject derived from a single timetable so nothing is entered twice.

Built as a single-page web app with vanilla JavaScript and Firebase, with no build step or framework dependency.

---

## Features

- **Custom timetable builder** — a Monday–Saturday × 7-period grid where each slot is set as Theory (1 period), Lab (auto-spans 2 back-to-back periods), or No-requirement.
- **Separate attendance targets** — independent minimum-percentage goals for theory and lab classes.
- **Per-period attendance logging** — an infinite-scroll daily log generated from the timetable, with Present / Absent / No-class marking. Duplicate same-day classes for one subject are tracked independently rather than merged.
- **Subject-level analytics** — live present/absent/total counts and percentage per subject, plus a plain-language readout of how many classes can be missed (or must be attended) to stay on target.
- **Assignment tracker** — add, complete, and delete assignments per subject, with due-date urgency highlighting (overdue / due soon / on track) in both a flat list and a per-subject summary view.
- **Multi-device accounts** — Email/Password and Google sign-in, with password reset, so the same data syncs across devices instead of being tied to one browser.
<img width="453.5" height="214.5" alt="image" src="https://github.com/user-attachments/assets/5ed77a1d-c171-4906-a4cf-1dbeb9cce156" />
<img width="453.5" height="214.5" alt="image" src="https://github.com/user-attachments/assets/4891ad29-40a7-4fb7-972d-95408542a7bb" />
<img width="453.5" height="214.5" alt="image" src="https://github.com/user-attachments/assets/a13a03ba-8df2-46ae-944a-bc2f7706859a" />
<img width="453.5" height="214.5" alt="image" src="https://github.com/user-attachments/assets/468764f6-a0e2-4655-a3f3-b7bc269372c9" />


## Tech Stack

- **Frontend:** Vanilla JavaScript, HTML, CSS (no framework, no bundler)
- **Backend:** [Firebase](https://firebase.google.com/) — Authentication (Email/Password, Google) and Cloud Firestore
- **Hosting:** Static hosting (GitHub Pages / Firebase Hosting / Netlify / Vercel — any static host works)

## Getting Started

### Prerequisites
A free [Firebase](https://console.firebase.google.com) project.

### Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/<your-repo>.git
   ```
2. In the Firebase console, create a project (or use an existing one) and:
   - Enable **Authentication → Sign-in method → Email/Password** and **Google**.
   - Add your deployment domain under **Authentication → Settings → Authorized domains**.
   - Create a **Firestore Database**.
   - Under **Firestore Database → Rules**, publish the rules from `firestore.rules` in this repo.
   - Under **Project settings → Your apps**, register a Web app and copy the generated config object.
3. Open `index.html` and paste your config into the `firebaseConfig` object near the top of the `<script type="module">` block:
   ```js
   const firebaseConfig = {
     apiKey: "...",
     authDomain: "...",
     projectId: "...",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
4. Open `index.html` directly in a browser, or deploy it to any static host.

### Deployment
As a single static file, it can be deployed anywhere:
- **GitHub Pages** — Settings → Pages → Deploy from branch.
- **Firebase Hosting** — `firebase init hosting && firebase deploy`.
- **Netlify / Vercel** — connect the repository or drag-and-drop the folder.

## Data Model

```
users/{uid}
  { theoryTarget, labTarget, semesterStart }

users/{uid}/meta/timetable
  { Monday: [ 7 slots ], ..., Saturday: [ 7 slots ] }

  slot shape:
    null                                → free period
    { name, type: "theory" | "none" }   → single-period class
    { name, type: "lab", span: 2 }      → start of a two-period lab block
    { linked: true, start: <index> }    → second hour of the lab starting at `start`

users/{uid}/attendance/{YYYY-MM-DD}
  { day, periods: { "<periodIndex>": "present" | "absent" | "cancelled" } }

users/{uid}/assignments/{id}
  { title, subject, due, done }
```

## Security

Firestore access is restricted entirely by `firestore.rules`: every read and write requires `request.auth.uid == userId` on that user's own document tree, so no user can access another user's data, and nothing is reachable while signed out. The Firebase Web API key embedded in the client config is a public identifier by design, not a secret — it does not grant access on its own.

## License

MIT — feel free to fork and adapt.
