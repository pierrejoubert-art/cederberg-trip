# Cederberg Easter 2026 — Trip Planner

A mobile-friendly trip planner for our 4-day Cederberg hike. Features real-time synced checklists (packing + shopping) with add/edit/delete, password protection, and offline support.

## Quick Start

### 1. Create a Firebase project (free)

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click **Add project** → name it anything (e.g. `cederberg-trip`) → disable Google Analytics → **Create**
3. In the left sidebar, go to **Build → Authentication**
   - Click **Get started**
   - Under **Sign-in method**, enable **Anonymous**
4. Go to **Build → Firestore Database**
   - Click **Create database**
   - Choose a location close to you (e.g. `europe-west1` for South Africa)
   - Start in **test mode** for now (we'll add proper rules below)
5. Go to **⚙️ Project Settings** (gear icon, top-left) → scroll to **Your apps** → click the **Web** icon (`</>`)
   - Register an app (nickname: anything)
   - Copy the `firebaseConfig` object

### 2. Add your Firebase config

Open `index.html` and find the `FIREBASE_CONFIG` object near the top of the `<script>` section. Replace the placeholders:

```js
const FIREBASE_CONFIG = {
  apiKey:            "AIzaSyB...",          // from Firebase console
  authDomain:        "cederberg-trip.firebaseapp.com",
  projectId:         "cederberg-trip",
  storageBucket:     "cederberg-trip.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abc123"
};
```

### 3. Set a password

The default password is **`cederberg2026`**. To change it:

1. Open the page in a browser
2. Open the browser console (F12 → Console)
3. Run: `hashPassword("your-new-password").then(console.log)`
4. Copy the hash
5. Replace the `PASSWORD_HASH` value in `index.html`

### 4. Set Firestore security rules

In Firebase Console → **Firestore Database → Rules**, paste:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /lists/{listId}/items/{itemId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

Click **Publish**. This allows any authenticated user (anonymous auth) to read/write list items.

### 5. Deploy to GitHub Pages

1. Create a GitHub repo:
   ```bash
   cd cederberg-trip
   git init
   git add index.html README.md firestore.rules
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin git@github.com:YOUR_USERNAME/cederberg-trip.git
   git push -u origin main
   ```

2. In GitHub → **Settings → Pages**:
   - Source: **Deploy from a branch**
   - Branch: **main** / **/ (root)**
   - Click **Save**

3. Your site will be live at: `https://YOUR_USERNAME.github.io/cederberg-trip/`

## Features

- **Real-time sync** — Both hikers see changes instantly via Firestore
- **Add/edit/delete** items in packing and shopping lists
- **Password protection** — Simple password gate to prevent random edits
- **Offline support** — Firestore persistence means it works on the trail with no signal; changes sync when you're back online
- **Progress bars** — Track packing and shopping completion
- **Mobile-first** — Designed for phones

## Files

| File | Purpose |
|------|---------|
| `index.html` | The entire app (single-file, no build step) |
| `firestore.rules` | Firestore security rules |
| `README.md` | This file |
| `index-old.html` | Original static version (backup) |
| `serve.py` | Old local HTTPS server (no longer needed) |

## Architecture

- **Hosting**: GitHub Pages (static files)
- **Database**: Firebase Firestore (real-time NoSQL)
- **Auth**: Firebase Anonymous Auth + client-side password gate
- **Offline**: Firestore offline persistence
- **No build step**: Everything is in one HTML file with CDN-loaded Firebase SDK
