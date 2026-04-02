# PresentScore (Firebase Edition)

Real-time competition management system with live sync across all judges' devices via Firebase Firestore.

## How It Works

- All judges open the same URL
- When any judge enters a score, it appears on every other judge's screen in real-time
- localStorage is used as an offline cache — the app loads instantly even without internet
- A **"Syncing..." / "Live"** indicator in the header shows connection status

## Firebase Project Details

This app uses the **ProjectTally** Firebase project:

| Field | Value |
|---|---|
| Project ID | `projecttally-ad2e2` |
| Firestore Region | (set during creation) |
| Auth | Google Sign-In (Firebase Authentication) |
| Plan | Spark (free) |

## Enable Google Sign-In (Required)

Before using the app, enable Google authentication in the Firebase Console:

1. Go to [Firebase Console](https://console.firebase.google.com/) → **ProjectTally** project
2. Navigate to **Authentication** → **Sign-in method**
3. Click **Google** → Toggle **Enable**
4. Set the **Project support email** (your email)
5. Click **Save**

## Roles & Permissions

| Capability | Admin | Judge N | Viewer (default) |
|---|---|---|---|
| Data Entry — own column | All columns | Column N only | No |
| Registration tab | Yes | No | No |
| Rankings / Leaderboards | Yes | No | Yes (read-only) |
| Settings | Yes | No | No |
| User Management | Yes | No | No |
| Import/Export | Yes | No | No |

- The **first user** to sign in automatically becomes **admin**
- All subsequent users get **viewer** role until admin assigns them a role
- Admin can manage users in the **User Management** tab (change roles, enable/disable accounts)

## Firestore Structure

```
entries/{id}          — One document per presenter entry (scores, metadata)
config/settings       — Single document with all competition settings
users/{uid}           — One document per authenticated user (role, status)
```

### Entry Document Fields
```json
{
  "id": "...",
  "subcounty": "Mabera",
  "category": "MATH",
  "presenter1": "John Doe",
  "presenter2": "",
  "school": "Kegonga",
  "projectTitle": "Smart Irrigation",
  "previousPosition": 0,
  "scores": { "j1": { "A": 22, "B": 20, "C": 18 }, "j2": {...}, "j3": {...} },
  "entryStatus": { "A": "entered", "B": "pending", "C": "pending" },
  "judgeTotals": { "j1": 60, "j2": 55, "j3": 58 },
  "outlierJudge": null,
  "average": 57.67,
  "position": 1,
  "points": 6
}
```

### Settings Document Fields
```json
{
  "categories": ["MATH", "PHY", "COMP", ...],
  "sections": [{ "key": "A", "label": "Innovation" }, ...],
  "numJudges": 3,
  "outlierThreshold": 5,
  "pointsMap": { "1": 6, "2": 5, ... },
  "topN": 3,
  "title": "PresentScore",
  "subtitle": "National Science & Engineering Fair"
}
```

## Deployment to GitHub Pages

1. Create a GitHub repository:
   ```bash
   cd firebase
   git init
   git add index.html README.md
   git commit -m "Initial commit — PresentScore with Firebase"
   git remote add origin https://github.com/YOUR_USERNAME/presentscore.git
   git push -u origin main
   ```

2. Enable GitHub Pages:
   - Go to repo **Settings** > **Pages**
   - Source: **Deploy from a branch**
   - Branch: **main**, folder: **/ (root)**
   - Click **Save**

3. Your app will be live at: `https://YOUR_USERNAME.github.io/presentscore/`

## Firestore Security Rules

The app currently uses **test mode** (open read/write). For production, consider adding rules:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid} {
      allow read: if request.auth != null;
      allow create: if request.auth != null && request.auth.uid == uid;
      allow update: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    match /entries/{entryId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.enabled == true;
    }
    match /config/{docId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
  }
}
```

## Offline Support

- The app loads from localStorage cache first (instant)
- Firestore offline persistence is enabled — changes made offline sync when reconnected
- The sync indicator shows current status: **Syncing...** (orange), **Live** (green), **Offline** (red)

## Local Development

```bash
cd firebase
python3 -m http.server 3000
```

Open http://localhost:3000 — Firebase connects automatically.

## Free Tier Limits (Spark Plan)

| Resource | Daily Limit |
|---|---|
| Firestore reads | 50,000 |
| Firestore writes | 20,000 |
| Firestore storage | 1 GiB |
| Bandwidth | 10 GiB/month |

For a competition with ~200 entries and a few judges, this is more than sufficient.
