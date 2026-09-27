# 💰 Budget Tracker

A self-contained expense dashboard that stores its data as an **encrypted Excel workbook in a GitHub repo** you control — not in browser storage — so you can open it from any device and see the same data, as long as you have your GitHub token and passphrase.

## Features
- 🔗 **GitHub-backed storage, connect on your terms** — the app doesn't force a connect screen on you. Click **🔗 Connect to GitHub** (top bar or sidebar) whenever you want to load your real data or make changes.
- 👀 **View without connecting** — if you've connected before on this browser, the app shows a cached copy of the data from your last session right away, no login needed. This is read-only in spirit: browsing works, but any add/edit/delete/import/budget change or new year requires connecting first (so you're never silently editing a stale copy that could diverge from what's really on GitHub). A **🔌 Not connected** indicator (click it to connect) makes it obvious when you're viewing cached data.
- 📗 **Real Excel structure** — internally, the data is a genuine `.xlsx` workbook (one sheet per month per year, a Dashboard summary sheet, a Meta sheet for settings) — the same structure the **Export Excel** button produces.
- 🔒 **Client-side encryption** — before that workbook is ever sent to GitHub, its bytes are encrypted (AES-256-GCM, key derived from your passphrase via PBKDF2). The repo can stay public; the committed file is unreadable — even in Excel — without your passphrase, which is never sent to GitHub or stored anywhere.
- 🔄 **Auto-sync** — once connected, adding, editing, or deleting an expense, importing from Excel, or changing your budget automatically pushes to GitHub afterward. A **🔄 Sync to GitHub** button is still there for manual retries.
- 🔔 **Notifications panel** — a bell icon in the top bar shows the last 5 sync attempts with their outcome: ⏳ pending, ✅ pass, or ❌ fail (with the reason).
- 📅 **Multi-year support**, 📊 **Dashboard**, 📈 **Compare Years**, 📋 **All Expenses**, ➕ **Add / ✏️ Edit / 🗑 Delete** with description autocomplete, ⚠️ over-budget warnings, 🔍 search & filter.

## How the "connected" vs "not connected" states work
- **Never connected on this browser before**: the app shows the built-in 2026 starter data so there's something to look at. A toast tells you this plainly. Connect to load your real data.
- **Connected before, not connected right now** (e.g. you just opened a new tab, or clicked Disconnect): the app shows a local cache of whatever was last successfully loaded or synced — refreshed automatically every time you connect, sync, or resolve a conflict. It's clearly marked as cached; nothing you do to it while disconnected is saved anywhere.
- **Connected**: every change syncs to GitHub automatically. This is the only state where edits actually persist.

Your GitHub token and passphrase themselves are never cached — only the resulting *data* is, for convenient viewing. You'll re-enter your credentials each time you want to actually connect.

## One-time GitHub setup

1. **Create (or pick) a repo** to hold your data — it can be the same repo that hosts this site.
2. **Create a fine-grained Personal Access Token**: GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token.
   - **Repository access**: "Only select repositories" → pick this one repo only.
   - **Permissions**: Repository permissions → **Contents: Read and write**. Nothing else.
   - Copy the token now — you won't be able to see it again.
3. **Pick a passphrase** for encrypting your data — save it in a password manager. If you lose it, your data on GitHub is unrecoverable ciphertext.
4. Click **🔗 Connect to GitHub**, and enter your **Token**, **Repository** (`owner/name`), **Branch**, **File Path** (default `data/budget-data.xlsx`), and **Passphrase**.
5. Click **Connect & Load**. If the file doesn't exist yet, the app starts you off with the built-in 2026 seed data and auto-syncs it to GitHub the moment you make your first change.

## Why your data isn't fully private just by using a token
Reading a file from a **public** GitHub repo requires no authentication at all — anyone can view it via the GitHub UI or a raw file URL, token or not. The token here is only used to **write** (commit) changes. The real protection for your data's *contents* is the **client-side encryption** — the committed workbook is unreadable without your passphrase. If you also want the file itself to be inaccessible, that needs a private repo (GitHub Pro/Team/Enterprise for private Pages hosting) — happy to help set that up if you want it later.

## Syncing across devices
Auto-sync fires after every change while connected, so conflicts are rare. If GitHub rejects a sync because the file changed elsewhere since you last loaded it, the app detects the conflict, logs it in the notifications panel, and asks whether to reload the latest version (discarding your local unsynced edit) or cancel and retry later.

## Categories
Food · Utilities · Healthcare · Rent · Misc · Transport · Shopping · Entertainment · Pujo · Travel

## Local usage
Double-click `index.html` — it still works without hosting it anywhere, since all it needs is a browser and a connection to `api.github.com`.

## How to host on GitHub Pages
1. Upload `index.html` and `README.md` to your repo root
2. Settings → Pages → Source: `main` branch, `/ (root)` folder → Save
3. Live at `https://<your-username>.github.io/<repo-name>/`

## Importing from Excel
Requires connecting first (import always writes data). Sheet names should contain a month name and a year, e.g. `March 2026`. Columns expected: **Date · Category · Description · Amount · Mode**. Choose **Replace** to overwrite a month's data, or **Merge** to add only new rows.
