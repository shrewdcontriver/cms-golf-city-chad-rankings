# CMS Golf City Chad Rankings 🏆

Vote for the top **Chads** of CMS Golf City Campus!  
Separate rankings for **Boys** and **Girls**.  
Upload your photo, get votes, climb the leaderboard.

**Live site (after setup):** https://shrewdcontriver.github.io/cms-golf-city-chad-rankings/

---

## Features
- Register with Name, Class, Gender + Photo
- Separate Boys / Girls leaderboards
- Top 3 shown big at the top (podium style)
- Rankings list with live vote counts
- 1 vote per week (enforced via browser localStorage)
- After voting → Share popup appears automatically
- Credits box top-right: Anany (@shrewdcontriver)

---

## Setup (Required – ~5 minutes)

### 1. Create a Firebase project
1. Go to [https://console.firebase.google.com/](https://console.firebase.google.com/)
2. Create a new project (name it anything, e.g. `cms-chad-rankings`)
3. You can disable Google Analytics

### 2. Enable services
- **Authentication** → Sign-in method → **Anonymous** → Enable
- **Firestore Database** → Create database → Start in **test mode**
- **Storage** → Get started → Start in **test mode**

### 3. Get your config
1. Project Settings (gear) → General → Your apps → Add web app (`</>`)
2. Register the app → Copy the `firebaseConfig` object

### 4. Paste config into the website
Open the file `js/firebase-config.js` in this repo and replace the placeholder values with your real Firebase config.

### 5. Security Rules (important for production)

**Firestore Rules** (Firestore → Rules tab):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /people/{personId} {
      allow read: if true;
      allow create: if request.auth != null 
                    && request.resource.data.keys().hasAll(['name','class','gender','photoURL','votes','createdAt'])
                    && request.resource.data.votes == 0
                    && request.resource.data.name is string
                    && request.resource.data.name.size() > 0
                    && request.resource.data.name.size() < 50
                    && request.resource.data.gender in ['boy', 'girl'];
      allow update: if request.auth != null 
                    && request.resource.data.diff(resource.data).affectedKeys().hasOnly(['votes']);
    }
  }
}
```

**Storage Rules** (Storage → Rules):
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /photos/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null
                   && request.resource.size < 5 * 1024 * 1024
                   && request.resource.contentType.matches('image/.*');
    }
  }
}
```

### 6. Enable GitHub Pages
1. Repo **Settings** → **Pages**
2. Source: Deploy from a branch → Branch `main` → Folder `/ (root)`
3. Save. Your site goes live in about 1 minute.

---

## How the 1-vote-per-week limit works
Uses `localStorage` key `lastVoteTimestamp`.  
After you vote, the site remembers it for 7 days on that browser.  
Clearing browser data / using another device resets the limit (as requested).

---

Made with ❤️ by **Anany**  
Instagram: [@shrewdcontriver](https://instagram.com/shrewdcontriver)
