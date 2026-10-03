# WadClinic Procurement Dashboard

Purchase forecast, store scorecards, priority items, PR tracking, suppliers and item follow-up,
built from the HMS Master Data workbook. Day/night and English/Arabic.

## What is in this folder

| File | Purpose |
|---|---|
| `index.html` | The finished dashboard. This is the only file Netlify serves. |
| `netlify.toml` | Netlify settings (no build step, no-index headers). |
| `scripts/build.py` | Rebuilds `index.html` from a new Master Data workbook. |
| `scripts/prepare_data.py` | Reads the workbook and produces `dashboard_data.json`. |
| `scripts/template.html` | Dashboard page without data. |
| `scripts/dashboard_data.json` | Unencrypted data from Master Data 01-10-2026 (kept on your computer only, not uploaded). |
| `requirements.txt` | Python packages for the build scripts. |
| `data/` | Put the Master Data workbook here when rebuilding (not uploaded to GitHub). |

## First deployment

1. **Create a private GitHub repository** (for example `wadclinic-procurement-dashboard`).
   Keep it **private**: `index.html` contains hospital purchasing data.
2. Upload the contents of this folder to the repository
   (GitHub → *Add file* → *Upload files*, or `git push`).
3. In **Netlify** → *Add new site* → *Import an existing project* → *GitHub* → choose the repository.
4. Leave *Build command* empty and *Publish directory* as `.` (netlify.toml already sets this). Click **Deploy**.
5. Netlify gives you a URL such as `https://wadclinic-procurement.netlify.app`. You can rename it under
   *Site configuration → Change site name*, or attach your own domain.

## Sign-in

The dashboard opens on a Wad Clinic sign-in page. Default credentials:

- Username: `lcm`
- Password: `wad123`

The data inside `index.html` is **encrypted** with a key made from the username and password, so it
cannot be read from the page source without them. The sign-in is remembered until the browser tab is
closed; **Sign out** in the header ends it.

To change the credentials, rebuild with:
`DASH_USER=newuser DASH_PASS=newpassword python scripts/build.py "data/Master Data.xlsx"`
(on Windows PowerShell: `$env:DASH_USER="newuser"; $env:DASH_PASS="newpassword"; python scripts/build.py "data/Master Data.xlsx"`).

## Restrict who can open it

The sign-in protects the data, but anyone with the link still sees the sign-in page.
Use a strong password (the default is short) and share it only with authorised staff. For extra protection:

- **Password protection**: *Site configuration → Access & security → Visitor access → Password protection*
  (available on Netlify paid plans).
- **Netlify Identity / team login**, or host it on an internal server instead.
- The `noindex` headers stop search engines from listing the page, but do not block access.

## Monthly update

1. Install Python 3.10+ and run once: `pip install -r requirements.txt`
2. Save the new workbook as `data/Master Data.xlsx` (same sheet names and columns).
3. Run: `python scripts/build.py "data/Master Data.xlsx"`
4. Update the date label: in `scripts/template.html` change `HMS 01-10-2026` and `asof:'2026-10-01'`
   in `scripts/prepare_data.py`, then run step 3 again.
5. Commit and push `index.html`. Netlify redeploys automatically.

The workbook itself stays out of GitHub (`.gitignore` excludes `data/*.xlsx`).

## Notes

- Charts load Chart.js from cdnjs and fonts from Google Fonts, so viewers need internet access.
- Suppliers treated as one-off / surgeon-paid in the Actual view are listed in
  `scripts/prepare_data.py` (search for `MAKANA|BOSTON|...`). Edit that list to change them.
