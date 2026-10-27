# RaychLedger

A private, single-file personal net-worth tracker for **fixed deposits** and **investment platforms**.

No server or build step to set up. It's one HTML file.

**Use it online:** https://rachelcjw101.github.io/raych-ledger/

This guide covers using it on your computer and setting up optional cloud sync through your own Firebase project, so your data follows you across devices.

## Contents

1. [Features](#features)
2. [What you'll need](#what-youll-need)
3. [Step 1: Try it on your computer](#step-1-try-it-on-your-computer)
4. [Step 2: Store your data in Firebase (optional, free)](#step-2-store-your-data-in-firebase-optional-free)
5. [Other free hosting options](#other-free-hosting-options)
6. [Using RaychLedger](#using-raychledger)
7. [Backups and moving data](#backups-and-moving-data)
8. [Customising](#customising)
9. [How the numbers are calculated](#how-the-numbers-are-calculated)
10. [Troubleshooting](#troubleshooting)

---

## Features

- **Overview**: total net worth, allocation donut, net worth over time, upcoming FD maturities, top platforms
- **Fixed deposits**: principal, interest rate, interest earned if held to maturity, tenure, auto-calculated maturity date, status (active / maturing soon / matured), bank and notes. A second "earmarked" bucket lets you track FDs set aside for a specific purpose.
- **Investments**: track any platform (brokerage, retirement fund, robo-advisor, gold, etc.), update the value whenever you check it, and see real gains separately from money you added
- **Growth & cash flow**: log fresh funds and record snapshots, and every rise in net worth is split into *money you added* vs *what your money earned*
- **Data menu**: JSON backup and restore, CSV export (opens in Excel), and erase all data
- **Optional cloud sync** with Google sign-in, using your own free Firebase project
- Light and dark themes, works on mobile

## What you'll need

| For | You need | Cost |
|---|---|---|
| Step 1: use it on your computer | A web browser | Free |
| Step 2: sync data to the cloud | A Google account (for [Firebase](https://firebase.google.com)) | Free (no credit card) |

You don't need to know how to code. You'll only paste one block of settings into the file.

---

## Step 1: Try it on your computer

1. Download `index.html` from this repo: open the file on GitHub and click the **Download raw file** button.
2. Double-click it. It opens in your browser (Chrome, Edge, Firefox or Safari).
3. Click **Add a fixed deposit** or **Add a platform** and enter something.

That's it. Your data is saved in that browser on that computer. To have the same data on your phone too, carry on to Step 2.

---

## Step 2: Store your data in Firebase (optional, free)

[Firebase](https://firebase.google.com) is Google's app platform. You'll create your **own** Firebase project, so your data sits in your Google account and nobody else's, including the author of this repo. After this step:

- you sign in to RaychLedger with Google
- your data is saved to the cloud and syncs live across all your devices
- only you can read or change it (enforced by the security rules in 2.5)

The free **Spark plan** is far more than enough for one person and doesn't need a credit card.

### 2.1 Create a Firebase project

1. Go to the [Firebase console](https://console.firebase.google.com) and sign in with your Google account.
2. Click **Create a project** (or **Add project**).
3. Name it, e.g. `raychledger`, accept the terms and click **Continue**.
4. Turning off **Google Analytics** is fine because RaychLedger doesn't use it. Then click **Create project**.

### 2.2 Register a web app and copy its settings

1. On the project's home page, click the **Web** icon (`</>`). It may be under **+ Add app**.
2. Give it a nickname, e.g. `raychledger-web`. Leave **Firebase Hosting** unticked and click **Register app**.
3. Firebase shows some code containing a block like this. **Copy just the part between the curly braces `{ … }`:**

   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "raychledger-xxxx.firebaseapp.com",
     projectId: "raychledger-xxxx",
     storageBucket: "raychledger-xxxx.firebasestorage.app",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abcdef"
   };
   ```

   You can find it again later under **Project settings (gear icon) → General → Your apps**.
4. Click **Continue to console**. You can skip the "install the SDK" instructions; RaychLedger loads Firebase for you.

### 2.3 Turn on Google sign-in

1. In the left menu, go to **Build → Authentication** and click **Get started**.
2. On the **Sign-in method** tab, click **Google**.
3. Switch on **Enable**, choose your email as the **Project support email**, and click **Save**.

### 2.4 Create the database

1. In the left menu, go to **Build → Firestore Database** and click **Create database**.
2. If asked for an edition, pick **Standard**.
3. Pick a **location** close to you, e.g. `asia-southeast1 (Singapore)`. You can't change this later.
4. Choose **Start in production mode** and click **Create**.

### 2.5 Lock the database down to only you

This is the step that keeps your data private. Open the **Rules** tab of Firestore Database, **replace everything** in the editor with the rules below, and click **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Each signed-in user can read and write only their own ledger.
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### 2.6 Allow your website to sign in

Firebase only allows Google sign-in from websites you approve.

1. Go to **Build → Authentication → Settings → Authorized domains**.
2. Click **Add domain** and enter your website's domain **without** `https://` or any folder name, e.g.:
   `your-site.netlify.app`
3. Click **Add**.

`localhost` and your project's own `*.firebaseapp.com` / `*.web.app` domains are already on the list.

### 2.7 Paste your settings into `index.html`

1. Open `index.html` in a text editor such as Notepad or VS Code.
2. Near the top of the `<script>` section, find the `CONFIG` block and its last line:

   ```js
   firebase: null,
   ```

3. Replace `null` with the settings you copied in 2.2, keeping the comma at the end:

   ```js
   firebase: {
     apiKey: "AIza...",
     authDomain: "raychledger-xxxx.firebaseapp.com",
     projectId: "raychledger-xxxx",
     storageBucket: "raychledger-xxxx.firebasestorage.app",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abcdef"
   },
   ```

4. Save the file, then upload it to your site again (see [Other free hosting options](#other-free-hosting-options)).

### 2.8 Sign in and check it works

1. Open your site's link. A **Sign in to sync** card appears at the top.
2. Click **Sign in with Google** and choose your account.
3. The status at the top right turns green and says **synced to cloud**.
   If that browser already had data in it, you're asked whether to copy it to your cloud account.
4. Add an entry, then open the same link on your phone and sign in. The entry should be there.

To confirm that your data is in Firebase, open **Firestore Database → Data** in the console. You'll see a `users` collection containing one document, named with your user ID.

### Is it safe to put the Firebase settings in a public repo?

Yes. The `apiKey` in a Firebase web config is **not a password**. It only tells the app which Firebase project to talk to, and Google designs it to be public. What protects your data is:

- **the security rules from 2.5**: only the signed-in owner can read or write their own document
- **Authorized domains from 2.6**: sign-in only works from the sites you listed

Never paste a *service account* key or private key into the file. RaychLedger doesn't need one.

If someone else uses your copy of the site and signs in, they get their **own** empty ledger in your Firebase project, and they can't see yours. If you'd rather nobody else can use your project, ask anyone who wants RaychLedger to make their own copy with their own Firebase project, as in this guide.

### Free-tier limits (Spark plan)

| Resource | Free allowance | Typical RaychLedger use |
|---|---|---|
| Firestore storage | 1 GiB | A few KB per user |
| Firestore reads | 50,000 per day | A handful each time you open the page |
| Firestore writes | 20,000 per day | One per save |

Each user's whole ledger is stored as a single document (Firestore's limit is 1 MiB per document), which holds thousands of entries.

---

## Other free hosting options

### Firebase Hosting

If you set up Firebase in Step 2, you can host the page there too, at `https://<project-id>.web.app`. That domain is already authorised for sign-in.

1. Install [Node.js](https://nodejs.org) (LTS version).
2. In a terminal, install the Firebase tools and log in:

   ```bash
   npm install -g firebase-tools
   ```

   ```bash
   firebase login
   ```

3. In the folder that contains `index.html`, run:

   ```bash
   firebase init hosting
   ```

   - **Use an existing project** → pick your project
   - **Public directory** → type `.` (a single dot)
   - **Configure as a single-page app?** → `No`
   - **Set up automatic builds and deploys with GitHub?** → `No`
   - **File ./index.html already exists. Overwrite?** → **`No`** (important!)

4. Deploy:

   ```bash
   firebase deploy --only hosting
   ```

   The terminal prints your live link. Run the same command again whenever you change the file.

### Netlify Drop (no account needed to try)

1. Go to [app.netlify.com/drop](https://app.netlify.com/drop).
2. Drag the folder that contains `index.html` onto the page. You get a live link straight away.
3. Sign up for a free account if you want the site to stay up. If you use Firebase sync, add the Netlify domain (e.g. `your-site.netlify.app`) to **Authorized domains** (see 2.6).

---

## Using RaychLedger

1. **Add your fixed deposits** on the *Fixed Deposits* tab: principal, interest rate, date placed and tenure. The maturity date fills in automatically.
2. **Add your investment platforms** on the *Investments* tab with their current values.
3. **When you put in new money**, use *Growth & Cash Flow → Log fresh funds*. If you pick a platform, its value goes up automatically.
4. **Every so often** (monthly works well), update each platform's current value, then click **Record snapshot**. Your net-worth chart and the "fresh funds vs gains" breakdown build up from these snapshots.

## Backups and moving data

- **Data → Download backup (.json)** saves everything to a file.
- **Data → Restore from backup…** loads a backup file and **replaces** the current data.
- **Data → Export to CSV** gives you a spreadsheet you can open in Excel.

**Without Firebase**, data lives in each browser separately, and clearing your browser data erases it. Download backups regularly, and use backup and restore to move data between devices. Note that the local file and each online link count as separate places.

**With Firebase**, your data is in the cloud and syncs automatically wherever you sign in. Backups are still a good idea. While you're signed out, anything you add is saved only in that browser.

## Customising

Edit the `CONFIG` block near the top of the `<script>` in `index.html`:

```js
const CONFIG = {
  currency: "RM",           // e.g. "$", "£", "S$"
  locale: "en-MY",          // number formatting, e.g. "en-US", "en-GB", "en-SG"
  altLabel: "Earmarked FD", // name of the second FD bucket, e.g. "Emergency fund FD"
  banks: ["Maybank","CIMB","Public Bank","RHB","Hong Leong","AmBank","Other"],
  firebase: null,           // your Firebase settings (see Step 2), or null for device-only
};
```

The bank list is only a set of suggestions. You can type any bank name.

## How the numbers are calculated

**FD interest if held to maturity** = principal × rate (% p.a.) × days from placement to maturity ÷ 365.
For example, RM 10,000 at 3.50% p.a. for 6 months (184 days) earns about RM 176.44. Your bank's figure may differ by a few cents because of rounding. FDs without a rate show "—" and are left out of the interest totals.

**Investment gain per platform** = current value − value when you first added it − fresh funds you logged into it.

**Gain per period** (between snapshots) = change in net worth − fresh funds logged in that period.

## Troubleshooting

| Problem | Fix |
|---|---|
| Sign-in says **unauthorized-domain** | Add your site's domain under Authentication → Settings → Authorized domains (2.6). |
| Sign-in doesn't work when I double-click the file | Google sign-in doesn't work from a file opened on your computer (`file://`). Use your online link instead. |
| **Missing or insufficient permissions** | Your Firestore rules aren't set up. Paste the rules from 2.5 and click **Publish**. |
| Status says **sync error** | Hover over it for details. Usually it's the rules (2.5) or the Firebase settings pasted wrongly (2.7). Check for a missing comma or quote. |
| Sign-in popup flashes and closes | Allow popups for your site in the browser, then try again. |
| My data disappeared | Without Firebase, check you're in the same browser and at the same link you used before, then restore from your latest backup. With Firebase, sign in with the same Google account. |

## Disclaimer

This is a personal record-keeping tool, not financial advice. Double-check important figures against your bank and platform statements.
