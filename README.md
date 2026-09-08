# UOB IT PMO — Project Kanban

A single-file Kanban board for tracking IT project management office work across
four columns: **Backlog → In Progress → Blocked → Done**. It is one `index.html`
with no build step, no framework and no dependencies — open the file and it runs.

**Live: https://super211.github.io/newrepo/**

## What it does

- Cards carry a title, description, project, category, assignee, priority, due
  date and status; overdue cards (past due and not Done) are flagged.
- Filter the board by project, assignee or priority.
- Move a card between columns, or delete it with a confirmation step.
- Ships with 8 fictional demo tasks so the board is never empty on first load.
  The names, projects and CVE reference in the seed data are invented — there is
  no real project or personnel data in this repository.

Two things worth knowing before relying on it:

- **Nothing is persisted.** State lives in memory only — no localStorage,
  sessionStorage, IndexedDB or cookies — so a page refresh restores the demo
  seed and discards anything you added.
- Every user-supplied string is escaped before it reaches `innerHTML`.

## Running it locally

```bash
git clone https://github.com/super211/newrepo.git
cd newrepo
```

Then open `index.html` in a browser. There is nothing to install or compile. If
you prefer to serve it over HTTP:

```bash
python -m http.server 8000   # then visit http://localhost:8000/
```

## Configuration — email notifications

New cards can optionally be posted to [FormSubmit](https://formsubmit.co), which
relays them as email. It is off until you configure it. Edit the one constant
near the top of the script block in `index.html`:

```js
const FORMSUBMIT_ENDPOINT = "https://formsubmit.co/ajax/YOUR_EMAIL@example.com";
```

While that placeholder address is still in place the board skips the request
entirely rather than firing one that is certain to fail. Once you substitute a
real address, FormSubmit requires a **one-time activation**: the first submission
sends a confirmation email to that address, and nothing is delivered until the
link inside it is clicked.

The card is added to the board first and the email is sent afterwards, so a
failed or unconfirmed notification never blocks the board — you get a toast
saying the notification failed, and the card stays.

That constant is the only place the address appears. It is not a secret, but it
is a real inbox: it lands in a public repository, so use one you are willing to
publish.

## How it deploys

`.github/workflows/deploy-pages.yml` runs on every push to `main` (and on manual
dispatch). It copies `index.html`, `.nojekyll` and — if present — `404.html`
into `_site/`, then force-pushes that directory to the `gh-pages` branch as a
single fresh commit, which GitHub Pages serves.

It publishes via `gh-pages` rather than the Pages deployment API on purpose:
`actions/configure-pages` with `enablement: true` fails with *"Resource not
accessible by integration"* when Pages has never been enabled on a repo, because
creating a Pages site needs repository administration rights that `GITHUB_TOKEN`
cannot be granted. Pushing a `gh-pages` branch enables Pages automatically on a
public repo and needs only `contents: write`.
