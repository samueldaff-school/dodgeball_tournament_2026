# Supabase Setup Guide

## 1. Create Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign up/log in
2. Click "New Project"
3. Choose a project name (e.g., "dodgeball-tournament")
4. Set a strong database password
5. Choose your region
6. Click "Create new project" and wait for it to initialize

## 2. Create Tables

Once your project is ready, go to the SQL Editor and run this script:

```sql
-- Create matches table
CREATE TABLE matches (
  id BIGINT PRIMARY KEY,
  match_date TEXT,
  round TEXT,
  team_a TEXT,
  team_b TEXT,
  winner TEXT,
  updated_at TIMESTAMP
);

-- Create predictions table
CREATE TABLE predictions (
  match_id BIGINT PRIMARY KEY REFERENCES matches(id),
  team_a_votes BIGINT DEFAULT 0,
  team_b_votes BIGINT DEFAULT 0
);

-- Insert all 13 matches
INSERT INTO matches (id, match_date, round, team_a, team_b, winner, updated_at) VALUES
(1, 'Thu 21 May 2026', 'Round 1', '8N', '8M', '', NULL),
(2, 'Thu 28 May 2026', 'Round 1', '8E', '8J', '', NULL),
(3, 'Thu 4 Jun 2026', 'Round 1', '8G', '8A', '', NULL),
(4, 'Thu 11 Jun 2026', 'Round 1', '8L', '8H', '', NULL),
(5, 'Thu 18 Jun 2026', 'Round 1', '8I', '8F', '', NULL),
(6, 'Thu 25 Jun 2026', 'Round 1', '8K', '8C', '', NULL),
(7, 'Thu 16 Jul 2026', 'Quarter-Final', '8B', '~w1', '', NULL),
(8, 'Thu 23 Jul 2026', 'Quarter-Final', '8D', '~w2', '', NULL),
(9, 'Thu 30 Jul 2026', 'Quarter-Final', '~w3', '~w4', '', NULL),
(10, 'Thu 6 Aug 2026', 'Quarter-Final', '~w5', '~w6', '', NULL),
(11, 'Thu 13 Aug 2026', 'Semi-Final', '~w7', '~w8', '', NULL),
(12, 'Thu 20 Aug 2026', 'Semi-Final', '~w9', '~w10', '', NULL),
(13, 'Thu 27 Aug 2026', 'Grand Final', '~w11', '~w12', '', NULL);

-- Insert prediction rows
INSERT INTO predictions (match_id, team_a_votes, team_b_votes) VALUES
(1, 0, 0), (2, 0, 0), (3, 0, 0), (4, 0, 0), (5, 0, 0), (6, 0, 0),
(7, 0, 0), (8, 0, 0), (9, 0, 0), (10, 0, 0), (11, 0, 0), (12, 0, 0), (13, 0, 0);
```

## 3. Get Your Credentials

1. Go to **Settings** → **API** in your Supabase project
2. Copy your **Project URL** (looks like `https://xxxxx.supabase.co`)
3. Copy your **anon public key** under "Project API keys"

## 4. Configure index-supabase.html

In `index-supabase.html`, find the configuration section near the top and replace:

```javascript
const SUPABASE_URL = 'https://your-project.supabase.co';
const SUPABASE_ANON_KEY = 'your-anon-key-here';
```

With your actual credentials from step 3.

## 5. Enable Public Access (RLS Policies)

Go to your Supabase project and run this in the SQL Editor to allow public read/write access:

```sql
-- Enable RLS
ALTER TABLE matches ENABLE ROW LEVEL SECURITY;
ALTER TABLE predictions ENABLE ROW LEVEL SECURITY;

-- Allow anyone to read
CREATE POLICY "Allow public read matches"
  ON matches FOR SELECT
  TO public
  USING (true);

CREATE POLICY "Allow public read predictions"
  ON predictions FOR SELECT
  TO public
  USING (true);

-- Allow anyone to update
CREATE POLICY "Allow public update matches"
  ON matches FOR UPDATE
  TO public
  USING (true)
  WITH CHECK (true);

CREATE POLICY "Allow public update predictions"
  ON predictions FOR UPDATE
  TO public
  USING (true)
  WITH CHECK (true);
```

## 6. Deploy

1. Upload `index-supabase.html` to Netlify (or your hosting provider)
2. Open the site and confirm you see the tournament data
3. Test a vote to ensure the predictions table updates
4. Test the admin panel with password "2026dodgeball"

## Verification Checklist

- [ ] Supabase project created
- [ ] Both `matches` and `predictions` tables exist with data
- [ ] RLS policies configured for public access
- [ ] `index-supabase.html` has correct SUPABASE_URL and SUPABASE_ANON_KEY
- [ ] Site displays all 13 matches
- [ ] Voting works (click vote button, see count increase)
- [ ] Admin panel accepts password "2026dodgeball"
- [ ] Admin can update match winners

## Troubleshooting

**"Unable to fetch data"**: Check that SUPABASE_URL and SUPABASE_ANON_KEY are correct in the HTML file.

**"No permission to read/write"**: Ensure RLS policies are enabled and allow public access.

**Votes don't update**: Check the browser console for errors. Verify the `predictions` table exists and has 13 rows.

**Admin password doesn't work**: The password hash is hardcoded in the HTML. If changed, you need to update the `PASSWORD_HASH` variable.
