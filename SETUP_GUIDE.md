# Shreesyam Cricket Academy — Live Website Setup Guide
(Live scoreboard + admin login + registrations database)

Aapki site ab **real database aur real live scoreboard** ke saath ready hai — bilkul CricHeroes jaisa: admin match start/control karega, aur website pe koi bhi visitor live score dekh payega, chahe wo kahin bhi ho. Registration aur inquiry ka data bhi ek real database me save hoga jo sirf admin dekh payega.

Isko chalane ke liye aapko **Firebase** (Google ka free tool) se apni khud ki free database banani hogi — 10 minute ka kaam hai, koi coding nahi karni padegi. Neeche step-by-step likha hai.

---

## STEP 1 — Firebase Project Banayein (Free)

1. https://console.firebase.google.com par jayein aur apne Google account se login karein.
2. **"Add project"** par click karein.
3. Naam dein: `shreesyam-cricket-academy` (ya koi bhi naam) → Continue.
4. Google Analytics ka option **off** kar dein (zaroori nahi hai) → **Create project**.

## STEP 2 — Web App Add Karein aur Config Copy Karein

1. Project dashboard par **`</>`** (Web) icon par click karein.
2. App ka nickname dein (e.g. "SCA Website") → **Register app**.
3. Aapko ek code dikhega jisme `const firebaseConfig = { ... }` hoga. **Ye pura object copy kar lein.**
4. Apni `index.html` file kholein, `<script>` ke shuru me ye line dhundein:

   ```js
   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     ...
   };
   ```

5. Ise apne copy kiye hue asli config se **replace** kar dein. Save karein.

## STEP 3 — Admin Login Banayein (Authentication)

1. Firebase console ke left menu me **Build → Authentication** par jayein → **Get started**.
2. **Email/Password** provider par click karke **Enable** karein → Save.
3. **Users** tab me jayein → **Add user**.
4. Yahan apna admin email aur password dalein (e.g. `admin@shreesyamcricket.com` / apna strong password) → **Add user**.
5. **Yahi email + password aapka Admin Panel ka ID/PWD hai.** Website par "⚙️ Admin Panel" button dabakar isi se login hoga.

   > Aap yahan jitne chahein utne admin users add kar sakte hain (e.g. coach ke liye alag login).

## STEP 4 — Database Banayein (Firestore)

1. Left menu me **Build → Firestore Database** → **Create database**.
2. **Production mode** select karein → Next.
3. Location: **asia-south1 (Mumbai)** select karein (India ke liye sabse tez) → Enable.

## STEP 5 — Security Rules Paste Karein

Firestore ke **Rules** tab me jayein, jo bhi likha hai use pura mita kar neeche diya hua paste karein, phir **Publish** dabayein:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Live match — sab dekh sakte hain, sirf logged-in admin update kar sakta hai
    match /matches/{matchId} {
      allow read: if true;
      allow write: if request.auth != null;
    }

    // News aur Results — sab dekh sakte hain, sirf admin edit kar sakta hai
    match /news/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    match /results/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }

    // Admission Registrations — koi bhi submit kar sakta hai, sirf admin dekh/delete kar sakta hai
    match /registrations/{docId} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }

    // Contact Inquiries — koi bhi submit kar sakta hai, sirf admin dekh/delete kar sakta hai
    match /inquiries/{docId} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }
  }
}
```

Ye rules ensure karte hain ki: koi bhi visitor apna registration/inquiry submit kar sakta hai, live score aur news dekh sakta hai — lekin **sirf aapka logged-in admin account hi data dekh sakta hai, delete kar sakta hai, ya live match/news/results update kar sakta hai.**

## STEP 6 — Website Host Karein

Aapke paas already ye files hain: `index.html`, `manifest.json`, `sw.js`, `logo.png` — inhe kahin bhi static hosting par daal sakte hain:

**Option A — Firebase Hosting (sabse aasan, free, same Firebase project use hoga):**
1. Terminal me: `npm install -g firebase-tools`
2. `firebase login`
3. Apni site files ke folder me: `firebase init hosting` → apna project select karein → public directory jahan aapki files hain wahi batayein.
4. `firebase deploy`
5. Aapko ek free `.web.app` link milega jo turant live ho jayega.

**Option B — Netlify / GitHub Pages:** Bas apni 4 files ko unke free static hosting me upload/drag-drop kar dein — Firebase alag se connected rahega, koi extra setup nahi chahiye.

---

## Admin Panel Kaise Use Karein

1. Website par neeche-left "⚙️ Admin Panel" button dabayein → apna admin email/password se login karein.
2. **🔴 Live Match tab:** Team names, venue daal kar **"Start Live Match"** dabayein. Fir har ball ke baad 0/1/2/3/4/6/Wicket/Wide/No Ball button dabayein — score turant sabhi visitors ki screen par update hoga. Galti se ball daal di to **Undo** dabayein. 2nd innings me target set karne ke liye **"End Innings"** dabayein, phir target daal kar **"Set"** dabayein. Match khatam hone par **"End Match"** dabayein.
3. **📋 Registrations tab:** Jitne bhi log admission form bharenge, wo yahan real-time me dikhenge — naam, age, phone, email, batch sab kuch. **Export CSV** se Excel me download bhi kar sakte hain.
4. **✉️ Inquiries tab:** Contact form se aayi saari inquiries yahan dikhengi.
5. **📰 News tab / 🏆 Results tab:** Naya notice ya match result add karein — turant website par sabko dikhega.
6. **🧩 Sections tab:** Kisi bhi section ko on/off (show/hide) kar sakte hain.

Bas itna hi — ab ye ek asli, live, database-backed website hai, demo nahi. 🏏
