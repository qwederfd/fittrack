# FitTrack: setup guide (free)

Phase 1: email accounts, private data per person, offline logging, home-screen install.
Not built yet: AI coach, meal plan, food scan (the screens exist but say "coming next phase"), barcode, family groups, push notifications.
I could not test this against a live Supabase project, so follow the checks in step 6.

## 1. Supabase (accounts and database)
1. Create a free account at supabase.com, then **New project** (pick a UK/EU region, save the database password).
2. **SQL Editor > New query**, paste all of `schema.sql`, click **Run**. This creates your data table with row-level security, so each person can only read and write their own row.
3. **Project Settings > API**: copy the **Project URL** and the **anon public** key. (The anon key is meant to be public. Never use the `service_role` key anywhere in this app.)

## 2. Add your keys
Open `index.html`, find these two lines near the top of the script, and paste your values:
`const SB_URL="PASTE_YOUR_SUPABASE_URL",SB_KEY="PASTE_YOUR_SUPABASE_ANON_KEY";`

## 3. GitHub Pages (hosting)
1. Create a free GitHub account, then a **new public repository** (e.g. `fittrack`).
2. Upload everything in this folder (index.html, manifest.json, sw.js, icons/) using **Add file > Upload files**.
3. **Settings > Pages**: Source = *Deploy from a branch*, Branch = `main`, folder `/ (root)`, Save.
4. After a minute your app is live at `https://YOURNAME.github.io/fittrack/`.

## 4. Tell Supabase your site address
**Authentication > URL Configuration**: set **Site URL** to your Pages address, and add the same address under **Redirect URLs**. This makes confirmation and password-reset emails link back to your app.

## 5. Install on a phone
- iPhone: open the address in Safari, Share, **Add to Home Screen**.
- Android: open in Chrome, menu, **Install app** / **Add to Home Screen**.

## 6. Check it works
1. Create an account (confirm by email), fill in the welcome screen, log a food and a weight.
2. Create a second account on another browser or phone and confirm it sees none of the first account's data.
3. Turn on airplane mode, log something, turn it off, and check the top-right label says "synced".
4. Test "Forgot password".

## Known limits
- Free Supabase projects can pause after about a week of inactivity; unpause from the dashboard.
- Free email sending is rate-limited; for a family it is fine.
- "Delete all my data" clears your saved data. Fully deleting the login itself needs a small server function (planned for a later phase).
- Sync is last-write-wins per account: if you edit on two offline devices, the newer save wins.
- The public Supabase anon key in the page is normal; privacy comes from the row-level security in `schema.sql`.

## Next phases
AI (Gemini via a Supabase Edge Function, key kept secret, free tier first), barcode via Open Food Facts, family groups and invites, push notifications.
