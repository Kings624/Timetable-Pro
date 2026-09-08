# TA Timetable Manager

A single-file, no-build web app for a SEND team to assign Teaching Assistants
to student support periods, find cover for absences, and track TA
attendance/cover stats.

Everything lives in **`index.html`** — no build step, no npm install, no
server required. Deploy it by pushing this repo and enabling GitHub Pages
(Settings → Pages → deploy from the `main` branch, root folder).

## What's in the box

- **TA timetable grid** — Week A / Week B, drag-and-drop class placement,
  right-click to cancel a placement, manual entry with autofill from any
  previously-seen class code (and a "multiple matches — pick one" prompt
  when the same code maps to more than one teacher/room).
- **Student timetables** — a searchable list of students; picking one turns
  their Week A/B periods into draggable cards.
- **Class Needs / TA List / Student Timetables** editor (Data Setup modal) —
  add, edit, and tag data by hand; tags drive the cover-matching score.
- **Find Cover** — for any placed class, shows free TAs ranked by tag match.
- **Stats Dashboard** — absences / covers provided / absence rate per TA.
- **Export PDF** — turns the current TA's full Week A/B timetable into a
  clean printable page (use the browser's "Save as PDF" in the print dialog).
- **Data Setup → Export/Import (.json)** — manual backup/restore, and a way
  to move data between browsers if cloud sync isn't set up.

## Data & sync — read this before deploying

By default the app is **local-only**: everything is kept in the browser's
`localStorage`. Each person who opens the page on their own computer has
their own separate copy. Nothing is shared until you connect cloud sync.

### Optional: shared cloud data via JSONBlob

The app can also read/write a single shared JSON document via
[jsonblob.com](https://jsonblob.com) — a free, no-account, CORS-friendly
JSON store (plain HTTP GET to read, PUT to overwrite).

**Setup:**

1. Create a blob once:
   ```
   curl -X POST -H "Content-Type: application/json" -d "{}" https://jsonblob.com/api/jsonBlob
   ```
   The response's `Location` header is your blob's URL, e.g.
   `https://jsonblob.com/api/jsonBlob/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
2. Open `index.html`, search for `Fill this in`, and paste that URL into
   `CLOUD_API_URL`.
3. Commit and redeploy. The connection light next to the title (top-left)
   should turn green ("Connected") once it's working.

**⚠️ No login system in this version.** Anyone who has the page's URL *and*
finds the `CLOUD_API_URL` in the page source can read **and overwrite** the
shared data — there's no per-user access control. This is only appropriate
for an internal tool at an unpublished/unlisted URL. If this repo is public
on GitHub, that blob URL is visible to anyone who looks at the source, which
means anyone who finds it (not just people you've given the page's URL to)
could read or overwrite the shared data. Two ways to reduce that risk:

- Keep this repository **private**, or
- Keep the repo public but don't commit a real `CLOUD_API_URL` — instead
  paste it into the deployed file some other way that isn't in the public
  diff history, or wait until real authentication is added before hosting a
  live shared blob's URL in public source.

### Multi-save conflicts

There's no per-field conflict resolution — "Save Changes" overwrites the
whole shared blob. If two people save around the same time, the second save
wins and the first person's changes to unrelated data can be lost. The
"Refresh from Cloud" button and the periodic "newer data available" hint
next to the connection light help reduce this, but don't eliminate it.

### If you outgrow this

The natural next step is a real backend with authentication and per-field
updates (e.g. Supabase/Firebase with row-level security, or a small custom
API). That's a genuine development project on top of this file, not a
config change — worth planning for once more than a couple of people are
actively editing data at the same time, or once the data needs to be
restricted to specific logged-in staff.

## Local development / testing

Just open `index.html` in a browser — there's no build step. To test from a
local static server instead of `file://` (recommended, since some browsers
restrict `fetch`/`localStorage` under `file://`):

```
python3 -m http.server 8000
# then open http://localhost:8000
```

## File map

- `index.html` — the whole app (this is what GitHub Pages will serve)
- `ta-timetable-scheduler-v*.html` — dated snapshots kept during development
  (student data entry milestones, pre-persistence, pre-cloud-sync, etc.) —
  safe to delete from the repo if you don't want the history; kept here in
  case you need to compare or roll back.
