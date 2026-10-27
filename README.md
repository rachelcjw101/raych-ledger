# RaychLedger

A private, single-file personal net-worth tracker for **fixed deposits** and **investment platforms**.

No sign-up, no server, no build step. Download one HTML file, open it in your browser, and start tracking.

## Features

- **Overview**: total net worth, allocation donut, net worth over time, upcoming FD maturities, top platforms
- **Fixed deposits**: principal, interest rate, interest earned if held to maturity, tenure, auto-calculated maturity date, status (active / maturing soon / matured), bank and notes. A second "earmarked" bucket lets you track FDs set aside for a specific purpose.
- **Investments**: track any platform (brokerage, retirement fund, robo-advisor, gold, etc.), update the value whenever you check it, and see real gains separately from money you added
- **Growth & cash flow**: log fresh funds and record snapshots, and every rise in net worth is split into *money you added* vs *what your money earned*
- **Data menu**: JSON backup and restore, CSV export (opens in Excel), and erase all data
- Light and dark themes, works on mobile

## Getting started

1. Download `index.html` (or clone this repo).
2. Double-click it to open it in any modern browser (Chrome, Edge, Firefox, Safari).
3. Add your fixed deposits and investment platforms.
4. Every so often, update platform values and click **Record snapshot** on the *Growth & Cash Flow* tab.

Want a link you can open from any device? See [Deploying](#deploying) below.

## Deploying

RaychLedger is one static HTML file, so any free static host works. There's nothing to build or install.

### Option 1: GitHub Pages (recommended)

1. **Create a repository.** On GitHub, click **New repository**, give it a name (e.g. `raychledger`), make it **Public**, and click **Create repository**.
2. **Upload the files.** On the new repo's page, click **uploading an existing file**, drag in `index.html` and `README.md`, then click **Commit changes**.
   `index.html` must be in the top level of the repo, not inside a folder.
3. **Turn on Pages.** Go to **Settings → Pages**. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose the `main` branch and the `/ (root)` folder, and click **Save**.
4. **Open your site.** After a minute or two, the Pages settings show your link, which looks like:
   `https://<your-username>.github.io/raychledger/`

To update the site later, upload the new `index.html` to the repo (or edit it on GitHub). Pages republishes automatically within a minute or two.

Prefer the command line?

```bash
git init
git add index.html README.md
git commit -m "Add RaychLedger"
git branch -M main
git remote add origin https://github.com/<your-username>/raychledger.git
git push -u origin main
```

Then do step 3 above.

> GitHub Pages on a **private** repository needs a paid GitHub plan. A public repo is fine: people can see the code, but **never your data**, because that only lives in your browser.

### Option 2: Netlify Drop (no GitHub account needed)

1. Go to [app.netlify.com/drop](https://app.netlify.com/drop).
2. Drag the folder that contains `index.html` onto the page.
3. Netlify gives you a live link straight away. Sign up for a free account if you want the site to stay up and to choose your own name.

### Option 3: Just use it locally

Keep `index.html` on your computer and double-click it whenever you need it. No hosting required.

### Important: each copy has its own data

Your data is stored per browser **and per address**. The local file, your GitHub Pages link and your Netlify link are three separate places, and each starts empty. To move your data between them, or to your phone:

1. In the old copy: **Data → Download backup (.json)**
2. In the new copy: **Data → Restore from backup…**

Pick one copy as your main one and stick with it.

## Your data stays on your device

Everything is saved in your browser's `localStorage`. Nothing is uploaded anywhere. This means:

- Data is tied to **that browser on that device**. Opening the file in another browser starts empty.
- Clearing your browser's site data will erase it.
- **Use Data → Download backup (.json) regularly.** Use *Restore from backup* to move your data to another device or browser.

The only things the page loads from the internet are the fonts (Google Fonts) and the chart library ([Chart.js](https://www.chartjs.org/) from cdnjs).

## Customising

Open `index.html` in a text editor and change the `CONFIG` block near the top of the `<script>`:

```js
const CONFIG = {
  currency: "RM",           // e.g. "$", "£", "S$"
  locale: "en-MY",          // number formatting, e.g. "en-US", "en-GB", "en-SG"
  altLabel: "Earmarked FD", // name of the second FD bucket, e.g. "Emergency fund FD"
  banks: ["Maybank","CIMB","Public Bank","RHB","Hong Leong","AmBank","Other"],
};
```

The bank list is only a set of suggestions. You can type any bank name.

## How FD interest is calculated

Interest if held to maturity = principal × rate (% p.a.) × days from placement to maturity ÷ 365.

For example, RM 10,000 at 3.50% p.a. for 6 months (184 days) earns about RM 176.44. Your bank's figure may differ by a few cents because of rounding. FDs without a rate show "—" and are left out of the interest totals.

## How gains are calculated

- **Per platform:** Gain = current value − value when you first added it − fresh funds you logged into it.
- **Per period (between snapshots):** Change in net worth − fresh funds logged in that period = gain.

When you log fresh funds into a specific platform, its current value goes up by that amount automatically, so the deposit counts as money added, not as a gain.

## Disclaimer

This is a personal record-keeping tool, not financial advice. Double-check important figures against your bank and platform statements.
