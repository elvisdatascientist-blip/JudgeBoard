# KSEF ANALYSIS

Kenya Science & Engineering Fair — Competition Analysis System

## Live Application

**Access the application here:** [https://judge-board-ibtd.vercel.app/](https://judge-board-ibtd.vercel.app/)

## Features

- **Registration**: Import and manage competitor entries
- **Data Entry**: Enter scores for all judges and sections
- **Rankings**: 
  - Presenter Rankings
  - Sub-County Rankings
  - School Rankings (All, Girls, Boys, Mixed, Public, Private)
- **User Management**: Role-based access control
- **Offline Support**: Works offline with automatic synchronization

## User Roles

- **Admin**: Full access to all features
- **Data Entry**: Can enter scores for all judges but cannot view totals, averages, or positions
- **Judge 1/2/3**: Can only enter scores for their assigned column, scores disappear after entry
- **Viewer**: Read-only access to rankings

## Technical Details

- Built with vanilla JavaScript and Firebase
- Deployed on Vercel
- Real-time synchronization with Firestore
- Offline-first architecture with persistence

---

**Login:** Use your Google account to sign in at the link above.
