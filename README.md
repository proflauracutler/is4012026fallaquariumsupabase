# Ocean View Aquarium — Demo Project

A simple web app that demonstrates the difference between a frontend-only app and one connected to a real database.

**Part 1:** Open the file as-is. You can add fish, but they disappear when you refresh. That is the point — there is no database yet.

**Part 2:** Follow the steps below to connect your own Supabase database. After that, fish persist across refreshes because they are stored in a real database, not just in the browser's memory.

---

## What you need

- A web browser (Chrome or Firefox recommended)
- A text editor — [VS Code](https://code.visualstudio.com/) is free and works well
- Your GitHub account (you already have one)
- A Supabase account — free, no credit card, you will create one in Step 3

---

## Part 1 — Get the project running

### Step 1: Download the project from GitHub

1. Go to the GitHub repository your instructor shared.
2. Click the green **Code** button near the top right.
3. Click **Download ZIP**.
4. Unzip the folder somewhere you can find it (your Desktop works fine).

> **If you know how to use git:** `git clone <repo-url>` works too.

### Step 2: Open the app in your browser

Do not double-click the file to open it — that loads it as a `file://` URL, which Chrome blocks from making requests to external services like Supabase. Use **Live Server** instead:

1. Open VS Code.
2. Install the **Live Server** extension if you do not have it yet: click the Extensions icon on the left sidebar (or press Ctrl+Shift+X / Cmd+Shift+X), search for "Live Server" by Ritwick Dey, and click Install.
3. Open the `aquarium-github` folder in VS Code (File → Open Folder).
4. Right-click `index.html` in the Explorer panel on the left → **Open with Live Server**.
5. Your browser should open automatically at `http://127.0.0.1:5500`. You should see the aquarium with three starter fish and an orange **"Demo mode"** banner at the top.

### Step 3: Try the demo

1. Add a fish using the form on the right.
2. Notice the fish appears in the list.
3. Now refresh the page (press F5 or Ctrl+R / Cmd+R).
4. The fish you added is gone. That is because nothing is saved to a database yet.

---

## Part 2 — Connect a real database with Supabase

### Step 4: Create a Supabase account

1. Go to [supabase.com](https://supabase.com) and click **Start your project**.
2. Sign up with your GitHub account (easiest) or create an account with your email.
3. Verify your email if prompted.

### Step 5: Create a new project

1. Once logged in, click **New project**.
2. Give your project a name (e.g. `aquarium-demo`).
3. Set a database password — write it down somewhere, you may need it later.
4. Choose any region.
5. Click **Create new project** and wait about 30 seconds for it to set up.

### Step 6: Create the fish table

This is where you define what data your app will store. Think of it as building the table your ERD described.

1. In the left sidebar, click **Table Editor**.
2. Click **Create a new table**.
3. Name the table: `fish` (lowercase, no spaces).
4. **Uncheck** "Enable Row Level Security (RLS)" — leave it off for now.
5. Supabase automatically adds `id` and `created_at` columns. Keep those.
6. Add these three columns by clicking **Add column** for each:

   | Name      | Type   | Default value | Nullable |
   |-----------|--------|---------------|----------|
   | `name`    | `text` | *(leave blank)* | No     |
   | `species` | `text` | *(leave blank)* | No     |
   | `color`   | `text` | *(leave blank)* | Yes    |

7. Click **Save**.

Your database is ready.

### Step 7: Get your credentials

These are the two pieces of information your app needs to talk to your database.

1. In the left sidebar, click **Project Settings** (the gear icon near the bottom).
2. You need two things from two different sections:

   **Your Project URL** — click **Data API** in the settings menu. Your URL is at the top of that page. It looks like `https://abcdefgh.supabase.co`.

   **Your publishable key** — click **API Keys** in the settings menu. Under **Publishable key**, copy the key that starts with `sb_publishable_...`. (Do not use the Secret key — that one has admin access and should never go in a frontend file.)

Keep this browser tab open — you will need both values in the next step.

### Step 8: Add your credentials to the app

1. Open `index.html` in VS Code (right-click the file → Open with → VS Code, or open VS Code and drag the file in).
2. Find these three lines near the top of the `<script>` section (around line 95):

```js
const SUPABASE_URL = 'YOUR_PROJECT_URL'   // <-- paste here
const SUPABASE_KEY = 'YOUR_ANON_KEY'      // <-- paste here
const USE_SUPABASE = false                // <-- change to true once credentials are filled in
```

3. Replace `'YOUR_PROJECT_URL'` with your Project URL (keep the quotes).

> [!WARNING]
> The URL must end in `.supabase.co` with **nothing after it**. If you copied it from the API settings page it may include `/rest/v1/` at the end — delete that part or the app will not be able to reach your database.
4. Replace `'YOUR_ANON_KEY'` with your publishable key (keep the quotes).
5. Change `false` to `true` on the `USE_SUPABASE` line.
6. Save the file (Ctrl+S / Cmd+S).

Example of what it should look like after your edits:

```js
const SUPABASE_URL = 'https://abcdefgh.supabase.co'
const SUPABASE_KEY = 'sb_publishable_xxxxxxxxxxxxxxxx...'
const USE_SUPABASE = true
```

### Step 9: Test it

1. Save `index.html` in VS Code (Ctrl+S / Cmd+S). Live Server will refresh the page automatically.
2. The banner at the top should turn **green** and say "Connected to Supabase!"
3. Add a fish using the form.
4. Refresh the page.
5. The fish is still there. It survived because it is now stored in your Supabase database.

---

## Troubleshooting

**Banner is still orange after I set USE_SUPABASE to true**
- Double-check that you saved the file after editing it.
- Check your Project URL — it must end in `.supabase.co` with nothing after it. If it includes `/rest/v1/` at the end, delete that part.
- Make sure there are no other typos in your URL or key — they should still be inside single quotes.
- Open the browser's developer tools (F12 → Console tab) and look for an error message.

**I see a Supabase error about RLS or permissions**
- Go back to your Supabase dashboard → Table Editor → click on the `fish` table → go to the **RLS** tab and make sure it is disabled.

**The page is blank or I see a JavaScript error**
- Make sure `USE_SUPABASE` is spelled exactly right (no typos, still inside the script section).
- Try a different browser.

---

## What just happened (the big picture)

Before connecting Supabase, the fish list was stored in a JavaScript array inside the browser. Arrays are temporary — they only exist while the page is open. Refreshing the page creates a new array from scratch, so all the data is gone.

After connecting Supabase, the app sends a request to Supabase's servers when you add a fish. Supabase stores it in a real database table. When the page loads, the app asks Supabase for all the fish and displays them. The browser is just the interface; the data lives somewhere that persists.

The three lines you edited — URL, key, and `USE_SUPABASE = true` — are the only thing that changed. The frontend (the part you can see) is identical. That is what it means for the backend and database to be a separate layer.
