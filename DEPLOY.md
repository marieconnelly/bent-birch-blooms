# Bent Birch Blooms — Deployment Guide

## Files in this folder
- `index.html` — the main app
- `sw.js` — service worker (enables offline + installable PWA)
- `manifest.json` — PWA metadata (name, icons, theme)
- `icons/` — add your app icons here (see below)

---

## Step 1: Add app icons

You need two icon files in the `icons/` folder:
- `icon-192.png` — 192×192px
- `icon-512.png` — 512×512px

Use your farm logo. If you don't have PNG exports yet, any square image works temporarily.
Free tool: squoosh.app can resize images in the browser.

---

## Step 2: Enable Google Auth in Firebase

1. Go to Firebase Console → Authentication → Sign-in method
2. Enable **Google** as a provider
3. Add your deployment domain to the **Authorized domains** list (you'll get this after step 3)

---

## Step 3: Deploy with Firebase Hosting

Install Firebase CLI (one time, requires Node.js):
```
npm install -g firebase-tools
```

Then from this folder:
```
firebase login
firebase init hosting
```

When prompted:
- Public directory: `.` (just a dot — the current folder)
- Single-page app: **Yes**
- Overwrite index.html: **No**

Then deploy:
```
firebase deploy
```

Your app will be live at: `https://bent-birch-blooms.web.app`

---

## Step 4: Add your domain to Firebase Auth

After deploying, go to:
Firebase Console → Authentication → Settings → Authorized domains
Add: `bent-birch-blooms.web.app`

---

## Step 5: Install on iPhone

1. Open `https://bent-birch-blooms.web.app` in **Safari** on your iPhone
2. Tap the Share button (box with arrow)
3. Tap "Add to Home Screen"
4. Name it "Bent Birch" and tap Add

It will appear on your home screen and open full-screen like a native app.

---

## Firestore Security Rules (important before real use)

In Firebase Console → Firestore → Rules, replace the default with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

This ensures only you can read/write your own data.

Do the same in Storage → Rules:
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```
