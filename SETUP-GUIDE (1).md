# Class Task Tracker — Setup Guide

## What you'll need
- A free Supabase account (supabase.com — no credit card needed)
- Both HTML files: teacher.html and student.html
- About 10 minutes

---

## Step 1 — Create a Supabase project

1. Go to **supabase.com** and sign up (free)
2. Click **New project**
3. Give it a name like "Class Tracker", choose a region close to you (e.g. Asia Pacific)
4. Set a database password and save it somewhere
5. Wait ~2 minutes for the project to provision

---

## Step 2 — Create the database tables

1. In your Supabase project, click **SQL Editor** in the left sidebar
2. Click **New query**
3. Paste ALL of the following SQL and click **Run**:

```sql
-- Students table
create table students (
  id uuid default gen_random_uuid() primary key,
  name text not null,
  code text not null unique,
  created_at timestamptz default now()
);

-- Tasks table
create table tasks (
  id uuid default gen_random_uuid() primary key,
  name text not null,
  week_key date not null,
  created_at timestamptz default now()
);

-- Completions table
create table completions (
  id uuid default gen_random_uuid() primary key,
  student_id uuid references students(id) on delete cascade,
  task_id uuid references tasks(id) on delete cascade,
  week_key date not null,
  status text not null default 'pending', -- 'pending' or 'done'
  flagged_at timestamptz,
  confirmed_at timestamptz,
  created_at timestamptz default now(),
  unique(student_id, task_id, week_key)
);

-- Allow public read/write (the app handles its own logic)
alter table students enable row level security;
alter table tasks enable row level security;
alter table completions enable row level security;

create policy "public access" on students for all using (true) with check (true);
create policy "public access" on tasks for all using (true) with check (true);
create policy "public access" on completions for all using (true) with check (true);
```

---

## Step 3 — Get your API credentials

1. In Supabase, go to **Settings → API** (gear icon in sidebar)
2. Copy your **Project URL** — looks like `https://abcdefghij.supabase.co`
3. Copy your **anon public** key — long string starting with `eyJ...`

---

## Step 4 — Connect the teacher app

1. Open **teacher.html** in your browser
2. Paste your Project URL and anon key into the fields shown
3. Click **Connect & Launch**
4. Your 28 students will be automatically created with login codes
5. Go to the **Students & Codes** tab to see everyone's codes

---

## Step 5 — Set up the student page

1. Open **student.html** in your browser once
2. It will ask for the same Supabase URL and anon key — paste them in
3. That's it — credentials are saved, students just see the login screen

**To host the student page so the whole class can access it:**

**Easiest option — Netlify Drop (free, 2 minutes):**
1. Go to **netlify.com/drop**
2. Drag your **student.html** file onto the page
3. You'll get a URL like `https://sunny-moonbeam-abc123.netlify.app`
4. Share that URL with your class (write it on the board, put it in Google Classroom, etc.)
5. Students open it on their device and enter their code

**Note:** The first time the student page loads on a new device, it will ask for the Supabase credentials once. After that, students just see the code login screen. You may want to set this up on the school computers yourself before the students use it.

---

## How it works day-to-day

**You (teacher):**
- Open teacher.html in your browser
- Add tasks for the week in the sidebar (up to 5)
- The sidebar shows a blue "Flagged" counter when students flag work
- Amber pulsing "CHECK" appears next to a student's checkbox when they've flagged it
- Click the checkbox to confirm their work is done — it turns green ✓

**Students:**
- Go to the student URL on their device
- Enter their personal code (give these out from the Students & Codes tab)
- See their tasks and click **"I've finished this ✓"** when done
- The button changes to "Waiting for teacher to check…"
- Once you confirm it, their task shows "Confirmed by teacher" ✓
- If they made a mistake, they can click "Undo — I'm not done yet"

---

## Student codes

Each student has a unique code automatically generated (e.g. ARCH42, MAYA71).
- Find all codes in the teacher app under **Students & Codes**
- Codes are permanent — same code every week
- You can print or share them however suits your class

---

## Tips

- The teacher app auto-refreshes in real time — you'll see flags appear without needing to reload
- The student page also updates live — when you confirm their work, their screen updates instantly
- Use the **Summary tab** in the teacher app for the traffic light view at a glance
- Export CSV any time for your records

---

## Troubleshooting

**"Connection failed"** — Double-check you copied the full URL and anon key with no extra spaces

**Students not showing up** — Go to teacher.html and reconnect; students are seeded on first connection

**Tasks not appearing for students** — Make sure you added tasks for the *current week* (the week shown in the sidebar)
