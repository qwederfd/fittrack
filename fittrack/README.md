# FitTrack: setup guide (free)

Includes: email accounts that stay signed in, private data per person, offline logging, home-screen install, 90 preset foods, 7 workout programmes, and AI (coach, meal plan, food scan) once you complete the AI setup below.
Not built yet: barcode scanning, family groups, push notifications.
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


## 7. Turn on the AI (coach, meal plan, food scan)
The AI features do nothing until this is done. I have not been able to test it against a live project.
1. Re-run the extra block at the bottom of `schema.sql` in the Supabase SQL Editor (the `ai_usage` table).
2. Get a Gemini key at aistudio.google.com (sign in with Google, **Get API key**). Never paste it into `index.html`.
3. Supabase dashboard > **Edge Functions** > **Deploy a new function** > **Via Editor**. Name it exactly `ai`, paste in the contents of `supabase/functions/ai/index.ts`, and deploy.
4. Edge Functions > **Secrets**: add `GEMINI_API_KEY` (your key). Optional: `GEMINI_MODEL` (default `gemini-flash-latest`) and `DAILY_AI_CAP` (default 40 requests per person per day).
5. Reload the app and try the Scan tab with a photo of a meal.

Errors on the Scan or Coach screens say what is wrong (not deployed, key missing, daily limit, free-tier limit).
Privacy: on Google's free tier, what you send may be used to improve Google products. Switch your Gemini project to paid billing before the whole family uses scanning regularly.

## Staying signed in
Sign-in is remembered on each device and renews itself. You are only asked again if you tap Sign out, clear the browser data, or Safari removes site data after about a week of not opening the site. **Add it to the Home Screen** on iPhone to avoid that.

## Layout (no More tab)
- Home: calories ring, macros left, tiles; ⚙ opens Profile (targets, why-these-numbers, AI memory, export, sign out).
- Food: Diary, Meal plan, Shopping. Scan: live camera. Train: Workouts (with favourites), Coach. Progress: Weight (with target check), Body, Habits.
- The camera opens by itself on the Scan tab (browsers ask permission the first time; it cannot open before you tap the tab).

## Easiest way to turn on AI (no server)
In the app: ⚙ (top right of Home) > **AI setup**. Paste a free Gemini key from aistudio.google.com/apikey, tap **Save key**, then **Test AI**. The key is stored only on that device and cleared on sign out, so each person does this once on their own phone (and again in the Home Screen app, which has separate storage). The Supabase function in section 7 is the alternative if you later want the key kept off phones.

## Food database
Diary > "Search the food database" uses Open Food Facts (free, community-made, strong on UK supermarket products). Values are per 100 g and scaled to the amount you enter. It needs internet and the site asks users to be gentle with search, so avoid rapid repeated searches.
