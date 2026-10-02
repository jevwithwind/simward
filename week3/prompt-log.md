# Week 3 prompt log

Prompts sent to Claude Code for the Week 3 prototype and workflow diagram, in order.

---

## 1. 2026-09-28: initial build prompt (verbatim)

````markdown
# Task: Week 3 prototype + workflow diagram for SimWard (AI Prototyping course)

## Context
This repo (jevwithwind/simward) is my SimWard concept site, deployed on GitHub Pages. In Week 2 I built the homepage and embedded a Chatbase chatbot (the SimWard Assistant).

SimWard is a concept for giving hospital analysts realistic synthetic data without exposing real patients:
- A Generator agent creates synthetic datasets inside a secure environment. Real data never leaves.
- A Validator agent scores each dataset for fidelity and privacy.
- An Analyst agent helps staff query approved data.
- A human data steward signs off before anything is released.
- Every step is logged.

This week's assignment is to vibe code ONE working prototype, either a Project Management Tool or a Hiring Assistant. I'm building the Hiring Assistant, applied to my own work: a pipeline where synthetic datasets are the "candidates". The required features map like this:

| Hiring assistant requirement | SimWard version |
|---|---|
| Job posting management | Data requests: question, variables needed, minimum scores |
| Candidate tracking | Synthetic dataset candidates per request |
| Evaluation criteria | Fidelity, privacy and utility scores (0–100) against the request's minimums |
| Interview scheduling and notes | Steward review sessions with date, steward and notes |
| Pipeline view (Applied → Hired) | Generated → Validation → Steward review → Approved → Released |

The grader checks that:
- the prototype works and includes the required features
- I can add, edit and delete items
- status tracking works
- the interface is intuitive
- it handles edge cases: empty states, long text and many items

## Hard constraints
1. **Keep everything from Week 2.**
   - Do not delete, rename or rewrite existing files, homepage content, or the Chatbase embed (script, chatbot ID, config).
   - The only change allowed to existing files is adding a link to the new page in the homepage navigation or footer, matching the existing markup.
2. **Additive only.**
   - Put the prototype in `pipeline/index.html`. Check how Pages is served (repo root or /docs) and place it so it loads at `https://jevwithwind.github.io/simward/pipeline/`.
   - Use relative paths only.
3. **Static site.**
   - One HTML file with inline CSS and vanilla JS.
   - No framework, no build step, no backend.
   - No external requests beyond assets the site already uses and the Chatbase snippet.
4. **Chatbase on the new page.** Copy the existing embed snippet verbatim from the homepage so the assistant bubble appears there too. Don't modify it.
5. **Fictional content only.** Don't name any real hospital, team, course cohort or person in new content. Label the agents as simulated.
6. **Match the existing site's style.** Read its CSS first and reuse its fonts, colours and spacing so both pages feel like one product.

## Before building
Read the repo, then give me a short summary covering:
- the file structure
- how the homepage and Chatbase are wired
- which style tokens you'll reuse
- your file plan

Then proceed without waiting, unless something conflicts with the constraints above.

## Build spec

### Data model
- **Request:** id, code (2–4 letters, used in dataset names), title, requester, question, variables[], minFidelity, minPrivacy, minUtility (each 50–100), due date, status (open or closed).
- **Candidate:** id, requestId, name (`SYN-<code>-01`, `-02`, …), method (Gaussian copula, CTGAN, TVAE or Bayesian network), rows, generated timestamp, stage, scores {fidelity, privacy, utility} or null, review {datetime, steward, notes} or null, decision {by, at, note} or null, rejectReason.
- **Audit log entry:** timestamp, actor (You, Generator agent, Validator agent, "Steward: <name>", or System), text, candidate id, request id.
- **Persistence:** save to localStorage under a namespaced key, wrapped in try/catch. Fall back to seed data if storage is empty or corrupt. Include a "Reset demo data" action.

### Stages and rules
Stages: Generated → Validation → Steward review → Approved → Released, plus Rejected (hidden by default, with a toggle to show it).

- **Moving candidates**
  - Forward moves go one stage at a time. If someone tries to skip a stage, show a toast naming the next valid stage.
  - Backward moves are allowed. Moving back from Approved withdraws the sign-off.
  - Released is locked. The only action is "Withdraw", which rejects with a reason.
- **Validation:** moving a candidate to Validation auto-runs the Validator agent if it has no scores.
- **Steward review:** a candidate can't enter this stage without scores.
- **Approve = sign-off dialog**
  - Steward name and an "I have read the validation report" checkbox are both required.
  - If fidelity or utility is below the request minimum, a written decision note is required.
  - If privacy is below the minimum, sign-off is blocked with an explanation and a "Reject candidate" action that pre-fills the reason.
- **Release:** a confirm dialog showing who signed off and their note.
- **Reject:** needs a reason. Rejected candidates can be restored to Generated.
- **Score changes:** if a re-run or manual edit makes an Approved candidate fail privacy, withdraw its sign-off and log why.
- Every action writes to the audit log.

### Simulated agents
- **Generator agent** (button on each open request): after about 700 ms, creates a candidate with the next name, a random method and 20k–80k rows.
- **Validator agent** (after about 800 ms): assigns scores from these ranges by method:

| Method | Fidelity | Privacy | Utility |
|---|---|---|---|
| Gaussian copula | 79–90 | 90–98 | 74–86 |
| CTGAN | 86–95 | 74–92 | 80–91 |
| TVAE | 82–93 | 83–95 | 78–89 |
| Bayesian network | 80–90 | 89–97 | 75–87 |

- Show a loading state on agent buttons while they run.
- Add an "Edit scores" manual override, logged as a manual override.

### Views (tabs)
1. **Overview**
   - A 5-step stage strip with counts. Clicking a step opens that stage in the pipeline.
   - A "Needs attention" list, each item with a one-click action:
     - privacy fails
     - scores below minimum
     - Validator not run
     - in steward review with no review booked
     - approved but not yet released
     - open request with no candidates
     - overdue request with nothing released
   - Upcoming steward reviews.
   - An open-requests table with a stacked bar of candidates per stage, plus a colour legend.
2. **Pipeline**
   - A Kanban column per stage, each with a count and a one-line hint.
   - A toolbar with: filter by request, search, show-rejected toggle, and Add candidate.
   - Each card shows:
     - name
     - request title, clamped to 2 lines
     - method and row count
     - a compact score strip: one bar per metric, a tick at the request minimum, the value, and a red "Low" flag when below
     - the review date, or a "Book a steward review" link
     - a back button, and a next-action button (Validate / Send to review / Sign off / Release)
   - Drag and drop between columns on desktop, following the same rules. Buttons must work for touch.
3. **Requests**
   - Each request shows its question, variable chips, minimum scores and stage bar.
   - Actions: New, Edit, Close/Reopen, Delete (with confirm; also deletes its candidates), Run Generator agent, View in pipeline.
   - Filter: open, closed or all.
4. **Reviews**
   - A schedule dialog: candidate, date and time, steward, agenda.
   - Upcoming and past lists.
   - A notice listing candidates in steward review with no session booked.
5. **Audit log:** newest first, searchable, with actor tags styled by actor type.

**Candidate detail panel** (slide-over from the right):
- facts
- the validation report as a lab-result style table: check, result, reference ("≥ min"), Low flag
- Run / Re-run Validator, and Edit scores
- steward review fields (date and time, steward, notes) with Save and Remove
- the sign-off record
- move actions
- per-candidate history
- Delete

**About dialog:**
- what SimWard is
- the hiring-assistant mapping table above
- "All data is fictional, the agents are simulated, and changes are saved in this browser only"
- Reset demo data

### Seed data
Set all dates relative to today so the demo never goes stale.

Requests:

| Code | Title | Requester | Minimums (F/P/U) | Due |
|---|---|---|---|---|
| ED | Emergency wait time by arrival hour | Patient flow analyst | 85 / 90 / 80 | +18 days |
| LOS | Inpatient length-of-stay drivers | Capacity planning lead | 85 / 90 / 80 | +32 days |
| NS | Outpatient no-show model sandbox | External vendor pilot | 80 / 95 / 75 | +46 days |
| STF | Nurse staffing versus census | Nursing workforce planning | 85 / 85 / 80 | +60 days |

STF has no candidates, so it demonstrates the empty state.

Candidates:

| Name | Method | Stage | Scores (F/P/U) | Notes |
|---|---|---|---|---|
| SYN-ED-01 | Gaussian copula | Released | 88 / 94 / 83 | Past review and sign-off |
| SYN-ED-02 | CTGAN | Rejected | 91 / 78 / 86 | Reason: privacy below 90, near-copies of source records found |
| SYN-ED-03 | TVAE | Generated | — | |
| SYN-LOS-01 | Bayesian network | Steward review | 87 / 93 / 82 | Review booked in +2 days |
| SYN-LOS-02 | CTGAN | Validation | Not yet scored | |
| SYN-LOS-03 | TVAE | Generated | — | |
| SYN-NS-01 | Gaussian copula | Steward review | 79 / 97 / 74 | Two Low flags; review booked in +5 days |
| SYN-NS-02 | Bayesian network | Approved | 84 / 96 / 78 | Signed off with the note "Vendor sandbox only" |

Also seed a matching audit-log history. Use fictional steward names such as "M. Okafor" and "J. Tremblay".

### Quality bar
- Empty states tell the user what to do next.
- Long text wraps or clamps without breaking the layout.
- The board scrolls horizontally, but the page never scrolls sideways.
- Works at 390px width.
- Light and dark mode.
- Visible keyboard focus.
- Escape closes panels and dialogs.
- Buttons use verb labels.
- Toasts confirm every action.
- Respects prefers-reduced-motion.
- Escape all user-entered text before inserting it into HTML.

## Part 2: Workflow diagram
Create `week3/workflow.svg`: a one-page (A4 landscape) swimlane diagram titled "SimWard: from data request to released synthetic dataset". Keep it out of the site navigation.

- **Lanes:** Analyst | Generator agent | Validator agent | Data steward | Audit log
- **Flow:**
  1. Analyst submits a request (question, variables, minimum scores).
  2. Generator builds candidates inside the secure environment.
  3. Validator scores fidelity, privacy and utility against the minimums.
  4. Decision: privacy below minimum? If yes, reject and regenerate.
  5. Decision: fidelity or utility below minimum? If yes, the steward must record a justification.
  6. Steward review session, then sign-off.
  7. Release to the analyst.
  - The Audit log lane records every step.
- **Style rules:**
  - Max 14 shapes, labels under 6 words.
  - Diamonds for decisions.
  - One colour for agent steps and another for human steps.
  - A small legend.
  - Colours consistent with the site.

Before drawing, list your assumptions in 3 bullets. Export `workflow.png` and `workflow.pdf` using whatever the container has (Playwright/Chromium, rsvg-convert, etc.). If export isn't possible, tell me.

## Test and verify
- Syntax-check the JS.
- If a headless browser is available, script these checks, take screenshots, and fix what you find:
  - add, edit and delete a request
  - generate and validate a candidate
  - move a candidate through every stage
  - sign-off is blocked when privacy fails
  - a decision note is required for low scores
  - data persists after reload
  - no sideways page scroll at 390px
  - no console errors, other than blocked third-party requests
- Show me a `git diff --stat` against main confirming no Week 2 file changed except the one added nav link.

## Deliver
- Commit on a new branch `week3-pipeline` with clear commit messages, push it, and open a PR to main. Don't force-push or change Pages settings.
- Create `week3/prompt-log.md`. Record this prompt verbatim with today's date, then append every later prompt I send in this session with one line on what changed.
- Report back with:
  - the live URL after merge
  - a 5-line summary of what was built
  - test results
  - screenshots
  - any assumptions you made
````

**What changed:** Built `pipeline/index.html` (the hiring-assistant-style pipeline prototype), `week3/workflow.svg` with PNG and PDF exports, and this log. Added one footer link on the homepage.

---

<!-- Later prompts in this session are appended below, each with one line on what changed. -->

## 2. 2026-10-02: colour tone

> merged, the chatbase bubble shows on the pipeline page, though the website is a little too dark in color tone

(Follow-up answer: "Light page, heavy colours".)

**What changed:** Lightened the pipeline page's light theme: soft tinted action buttons instead of solid dark teal, light toasts, thinner stage colour bars, and a lighter validated stage scale ending at the brand teal.
