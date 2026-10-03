# शाळा माहिती, फोटो अल्बम व भेट नोंदी – केंद्र रविवार पेठ

Mobile-friendly web app. Hosted free on **GitHub Pages**; data and photos stored in your **Google Drive** through a small Google Apps Script.

```
Phone / PC browser ──► GitHub Pages (index.html, config.js: no personal data)
        │
        └─ HTTPS + Access Key ─► Google Apps Script ─► Drive folder
                                                       ├─ seed.json  (base school/staff/bank data, private)
                                                       ├─ db.json    (all edits and visit records)
                                                       ├─ photos/    (uploaded images)
                                                       └─ backups/   (optional daily copies)
```

The public repo holds **no** bank, Aadhaar, mobile or email data. Everything sensitive stays in Drive and is only sent after the correct Access Key.

---

## Step 1 – Put seed.json in Drive and create the backend (about 10 minutes)

1. Sign in to Google with the account that should own the data.
2. Open <https://script.google.com> → **New project**. Name it `RavivarPeth-API`.
3. Delete the sample code, paste the whole of `Code.gs`, click **Save**.
4. In the function dropdown pick **setup** → **Run**. Approve the permissions (Advanced → Go to project → Allow).
5. Open **Execution log**. Note the two lines:
   - the Drive folder link `RavivarPeth-SchoolApp`
   - your **ACCESS_KEY** (10 characters). You can change it under Project Settings → Script Properties → `ACCESS_KEY`.
6. Open the Drive folder link and **upload `seed.json`** into it (drag and drop).

## Step 2 – Deploy the API

1. In Apps Script: **Deploy → New deployment → ⚙ Select type → Web app**.
2. Description: `v1` · **Execute as: Me** · **Who has access: Anyone**.
3. **Deploy**, then copy the **Web app URL** (ends with `/exec`).
   Anyone can reach the URL, but every call without the correct Access Key is rejected.
4. Test: open the URL in a browser. You should see `{"ok":true,"msg":"School app API is running"}`.

> After any later change to `Code.gs`: **Deploy → Manage deployments → ✏ Edit → Version: New version → Deploy** (the URL stays the same).

## Step 3 – Connect the website to the API

Edit `config.js` and replace the placeholder:

```js
window.APP_CONFIG = { API_URL: "https://script.google.com/macros/s/AKfycb.../exec" };
```

## Step 4 – Publish on GitHub Pages

1. On <https://github.com> → **New repository** → name e.g. `ravivar-peth-school` → Public → Create.
2. **Add file → Upload files** and upload only: `index.html`, `config.js`, `README.md`, `.gitignore`.
   **Do not upload `seed.json`.**
3. **Commit changes.**
4. **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main`, folder `/ (root)` → Save.**
5. After 1–2 minutes your site is live at `https://<your-username>.github.io/ravivar-peth-school/`.

## Step 5 – First use

1. Open the site on the phone → enter the **Access Key** → the data loads from Drive.
2. Chrome menu → **Add to Home screen** for an app-like icon.
3. Give staff the site link and the key.

---

## How storage works

| Action | What happens |
|---|---|
| Edit any field / add visit note | Saved on the device instantly, then pushed to Drive (`db.json`) within about 1 second. The pill at the top shows the status. |
| Add photo | Compressed on the phone, uploaded to Drive `photos/`, and the link is stored with the school record. |
| Open on another phone | Pulls the latest from Drive. Newer edits win per school. |
| No internet | The app works from the local cache; pending changes go to Drive on the next Sync. |
| Tap **Sync** | Pull + push immediately. |

## Security notes

- **Photos:** each is shared as "anyone with the link" (long random ID, so it is not guessable). If you use a school Google Workspace account that blocks link sharing, change the sharing policy or the photos will not display.
- **Access Key** is a single shared password. To rotate it, change `ACCESS_KEY` in Script Properties; everyone must log in again.
- Aadhaar numbers are shown masked in the app but are stored in full in `db.json`. Keep the Drive folder private and do not share it.
- Deleting a photo in the app removes it from the record only; the file stays in Drive `photos/` (delete it there if required).

## Optional – daily backup

Apps Script → ⏰ **Triggers → Add Trigger** → function `dailyBackup` → Time-driven → Day timer. Keeps the last 30 copies in `backups/`.

## Local testing

Place `seed.json` next to `index.html`, leave `config.js` unchanged and serve the folder (`python3 -m http.server`). The app then runs offline-only with local storage.

## Updating the app later

Replace `index.html` on GitHub (Add file → Upload files → overwrite → Commit). Data in Drive is unaffected.
