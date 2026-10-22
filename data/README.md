# Editor Board data

`jobs.json` is the single source of truth for the board at https://davidlaniado.github.io/eb-4f9q2/.
Edit it, commit, push. Pages redeploys in ~1 min. The page reads it live (no build step).

## Top level
- `updated` ISO time of the last edit (ET). `updated_by` = "manual (session)" or "auto (routine)".
- `rules.default_due_days` (5): when an editor gave no date, due = brief date + 5.
- `rules.reply_due_hours` (24): David's reply/follow-up is due 24h after their last message.

## editors[]
`id, name, people, rating, rating_by (David|proposed), where, rate, higgsfield (true/false/text), source (auto|manual), channel {ws: dfo|onpoint|telegram, id}`.
`source: auto` = the routine reads this channel. `manual` = only updated in session.

## jobs[]
- `editor` = editors[].id · `brand` = Nuestra / DirectMax / Syra Rx tesa / RightWell / Zyvo / DMSP / "ED (brand-agnostic)" / "—"
- `started` YYYY-MM-DD (brief or delivery date). Drives the "day N" counter.
- `ball` = `you` (David owes an action) · `them` (editor owes a delivery) · `live` (ad running) · `off` (paused / parked)
- `due` YYYY-MM-DD or null, `due_src` = promised | default | milestone. Used when ball=them.
- `follow_up` YYYY-MM-DD or null. ball=you: when David's reply was due. ball=them: when to ping if silent.
- `need` = one line, what David has to do (only when ball=you). Minimal HTML allowed: a, b, code.
- `status` = one or two lines of plain facts with dates. Same HTML.

## Red / amber (computed by the page, from dates)
- ball=you: follow_up < today = RED (late), today or tomorrow = AMBER.
- ball=them: due < today = RED, today or tomorrow = AMBER.

## Flipping rules for the routine
- Editor delivers (link/file) → ball=you, follow_up = that day + 1, need = "Review ...".
- David replies with feedback/brief → ball=them, due = promised date or +5 days.
- David asks a question → ball=them, follow_up = +1 day.
- Editor asks a question → ball=you, follow_up = +1 day, need = the question.
- Ad launched → ball=live with the campaign id in status.
- Never delete a job; set ball=off with a note when it dies.

## Deploy gotcha (seen 10/22)
GitHub Pages (legacy build) did NOT auto-build after `git push`. After every push run:
`gh api -X POST repos/davidlaniado/eb-4f9q2/pages/builds` then poll `https://davidlaniado.github.io/eb-4f9q2/data/jobs.json?t=<random>` until the `updated` stamp matches. ~70s.

## READ THREADS (David 10/22: "I replied to it directly, you're not seeing those")
David answers in Slack THREADS. A channel/DM read without threads shows his questions as unanswered.
Every sweep: read in `detailed` mode, and for every message with "Thread: N replies" read the thread before deciding the ball.
