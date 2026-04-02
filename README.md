# PresentScore - Competition Scoring System

A web app for managing science fair and academic competitions. Judges score presenters in real-time, and the system automatically ranks them, builds leaderboards, and exports reports.

Built for Kenya's sub-county science & engineering fairs, but works for any scored competition.

## How It Works

### The Basics

PresentScore runs as a single web page. Multiple judges open the same URL on their phones or laptops. When one judge enters a score, it appears on everyone else's screen instantly via Firebase.

### Roles

| Role | What they can do |
|------|-----------------|
| **Admin** | Everything -- register presenters, manage settings, assign judge roles, import/export data |
| **Judge 1/2/3** | Enter scores in their assigned column only |
| **Viewer** | See rankings and leaderboards (read-only) |

The first person to sign in becomes admin. Everyone else starts as a viewer until the admin assigns them a role.

### Scoring Flow

1. **Admin registers presenters** -- Enter names, school, sub-county, category, and project title. Or bulk-import from an Excel file.
2. **Judges score independently** -- Each judge rates every presenter on 3 criteria (default: Innovation, Methodology, Presentation). Scores are out of 25 each.
3. **System calculates automatically** -- Totals per judge, averages across judges, outlier detection (flags wildly different scores), rankings by category.
4. **Leaderboards update live** -- Sub-county and school leaderboards aggregate points from individual presenter rankings.

### The 7 Tabs

| Tab | Purpose |
|-----|---------|
| **Registration** | Add/edit/delete presenters. Import from Excel. *(Admin only)* |
| **Data Entry** | Score entry interface. Filter by section (judge) and category. Tracks status: pending, entered, printed, verified. |
| **Rankings** | Presenters ranked by average score per category. Medal badges for top 3. Export top qualifiers to Excel. |
| **Sub-County Board** | Cumulative points per sub-county across all categories. Auto-ranked. |
| **School Board** | Same as sub-county but for individual schools. |
| **Settings** | Competition title, scoring criteria labels, categories, points mapping. *(Admin only)* |
| **User Management** | Assign roles, enable/disable accounts. *(Admin only)* |

### Points System

Presenters are ranked by average score within their category. Rankings convert to points (default: 1st = 6 pts, 2nd = 5, ... 6th = 1). These points feed into the sub-county and school leaderboards.

### Outlier Detection

If one judge's total is wildly different from the others (beyond the threshold set in Settings), that judge's score is struck out and excluded from the average. This prevents a single bad score from skewing results.

### Offline Support

The app caches everything in localStorage. If you lose internet, you can keep working -- scores sync back when you reconnect. The header shows connection status: **Live** (green), **Syncing...** (orange), **Offline** (red).

## Project Structure

```
JudgeBoard/
├── firebase/
│   ├── index.html          # Main app (production, Firebase-enabled)
│   └── README.md           # Firebase-specific docs
├── demo/
│   └── index.html          # Simplified demo (localStorage only)
├── index.html              # Original version (localStorage only)
├── DATA.xlsx               # Sample competition data
├── vercel.json             # Vercel deployment config
└── README.md               # This file
```

The entire app is a single HTML file -- no build tools, no npm, no bundling.

## Setup

### Prerequisites

- A web browser
- Python 3 (for local server) OR any static file server
- A Firebase project (for multi-device sync)

### Step 1: Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click **Add project** > Name it > Skip analytics > **Create project**
3. Click the **web icon (`</>`)** > Register app as "PresentScore" > Copy the config object
4. Go to **Firestore Database** > **Create database** > **Start in test mode** > Pick a region > **Enable**
5. Go to **Authentication** > **Sign-in method** > Enable **Google** > Set support email > **Save**

### Step 2: Add Your Firebase Config

Open `firebase/index.html` and find the `firebaseConfig` object (around line 826). Replace it with your config:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.firebasestorage.app",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

### Step 3: Run Locally

```bash
cd JudgeBoard/firebase
python3 -m http.server 3000
```

Open **http://localhost:3000**. Sign in with Google. You're the first user, so you're automatically admin.

### Without Firebase

If you just want to try it locally without multi-device sync:

```bash
cd JudgeBoard/demo
python3 -m http.server 3000
```

This version uses localStorage only -- data stays on that one browser.

## Data Import/Export

| Feature | Format | How |
|---------|--------|-----|
| **Import presenters** | Excel (.xlsx) | Registration tab > Import button. Columns: ID, SubCounty, Category, Presenter1, Presenter2, School, ProjectTitle |
| **Export qualifiers** | Excel (.xlsx) | Rankings tab > Export button. Exports top N per category. |
| **Full backup** | JSON | Export All Data button. Saves everything. |
| **Restore backup** | JSON | Import JSON button. Overwrites current data. |

A sample data file (`DATA.xlsx`) is included in the repo.

## Printing

Click **Print / PDF** on any tab. The print layout hides navigation and buttons, showing only the data tables. Works for rankings, leaderboards, and verification sheets.

---

## Deploying to Vercel (Free)

Vercel is the easiest free hosting for this app. Zero config, instant deploys, no spin-down, custom domains.

### What you need

- A [Vercel account](https://vercel.com) (sign up free with GitHub)
- Your code pushed to GitHub

### Step 1: Push to GitHub

If you haven't already:

```bash
cd JudgeBoard
git add -A
git commit -m "ready for deployment"
git push origin main
```

### Step 2: Import on Vercel

1. Go to [vercel.com/new](https://vercel.com/new)
2. Click **Import Git Repository**
3. Select your **JudgeBoard** repo
4. Vercel auto-detects the `vercel.json` config. You don't need to change anything:
   - **Build Command**: (empty -- no build needed)
   - **Output Directory**: `firebase`
5. Click **Deploy**

That's it. In about 30 seconds you'll get a URL like `https://judgeboard-xxxxx.vercel.app`.

### Step 3: Add your domain to Firebase (important!)

For Google Sign-In to work on your Vercel URL, you need to authorize the domain:

1. Go to [Firebase Console](https://console.firebase.google.com) > Your project
2. Navigate to **Authentication** > **Settings** > **Authorized domains**
3. Click **Add domain**
4. Add your Vercel URL: `judgeboard-xxxxx.vercel.app`
5. If you add a custom domain later, add that too

### Step 4: Custom domain (optional)

1. In your Vercel project dashboard, go to **Settings** > **Domains**
2. Add your domain (e.g., `score.yourschool.ac.ke`)
3. Follow Vercel's DNS instructions (add a CNAME record)
4. Don't forget to add the custom domain to Firebase Authorized Domains (Step 3)

### Automatic deploys

Every time you push to `main`, Vercel rebuilds and deploys automatically. No manual steps.

### Vercel free tier limits

| Resource | Limit |
|----------|-------|
| Bandwidth | 100 GB/month |
| Deployments | Unlimited |
| Custom domains | Unlimited |
| SSL | Automatic |
| Spin-down | None (always on) |

For a competition app used by a few judges, this is more than enough.

### Troubleshooting

| Problem | Solution |
|---------|----------|
| Google Sign-In fails on Vercel | Add your `.vercel.app` domain to Firebase > Authentication > Authorized domains |
| Blank page | Check browser console (F12). Usually a Firebase config issue. |
| Data not syncing | Verify Firestore is enabled in Firebase Console and rules allow read/write |
| "Permission denied" errors | Make sure Firestore is in test mode, or add proper security rules (see `firebase/README.md`) |

## Firebase Free Tier Limits

| Resource | Limit |
|----------|-------|
| Firestore reads | 50,000/day |
| Firestore writes | 20,000/day |
| Firestore storage | 1 GiB |
| Authentication | Unlimited users |
| Bandwidth | 10 GiB/month |

A typical competition with ~200 presenters and 3-5 judges stays well within these limits.

## License

MIT
