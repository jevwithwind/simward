# Week 3 deliverables: SimWard prototype and workflow diagram

AI Prototyping for Business Innovation, Week 3. Individual student concept; all data is fictional.

## What to submit

| # | Deliverable | Where |
|---|---|---|
| 1 | Link to the working prototype | https://jevwithwind.github.io/simward/pipeline/ |
| 2 | Workflow diagram (one page) | [`workflow-diagram.pdf`](workflow-diagram.pdf) (also [PNG](workflow-diagram.png) and [SVG](workflow-diagram.svg)) |
| 3 | Written reflection (300–400 words) | [`reflection.md`](reflection.md) |

## Supporting documents

| File | Brief section | Contents |
|---|---|---|
| [`prompt-log.md`](prompt-log.md) | "Keep a log of every prompt" | Every prompt submitted this week, verbatim, with what changed |
| [`workflow-source-document.md`](workflow-source-document.md) | Part 3: source document | The process document the diagram was built from |
| [`workflow-prompt.md`](workflow-prompt.md) | Part 3: prompt and review | The structured prompt, a reusable version for other tools, and notes on what the output got right and missed |
| [`test-report.md`](test-report.md) | Part 4: test and refine | The brief's four test questions, bugs found and fixed, all 77 automated checks, screenshots |
| [`screenshots/`](screenshots/) | Part 4 | Desktop, phone and dark-theme screenshots |

## Choices made

- **Part 1, platform:** Claude Code. The brief recommends Lovable or Bolt. I chose Claude Code because it edits my existing GitHub repository directly, deploys through the same GitHub Pages site as Week 2, and keeps the Week 2 Chatbase assistant on the new page.
- **Part 2, prototype:** the **hiring assistant** option (Option B, current role), applied to my own work. Synthetic datasets are the candidates:

| Hiring assistant | SimWard prototype |
|---|---|
| Job postings | Data requests: question, variables, minimum scores |
| Candidate tracking | Synthetic dataset candidates per request |
| Evaluation criteria | Fidelity, privacy and utility scores against the minimums |
| Interview scheduling and notes | Steward review sessions with date, steward and notes |
| Pipeline (Applied → Hired) | Generated → Validation → Steward review → Approved → Released |

- **Part 3, workflow diagram:** Claude, using the process document above and a structured prompt. The result is a swimlane diagram with five lanes, two decision gates and an audit-log lane.

## Prototype at a glance

- **Overview:** stage counts, a "Needs attention" list with one-click actions, upcoming reviews, and open requests with stacked stage bars.
- **Pipeline:** a Kanban board with drag and drop and buttons. Stages are enforced: no skipping, Released is locked, and sign-off is blocked when privacy fails.
- **Requests, Reviews and Audit log tabs,** plus a candidate detail panel with a lab-style validation report.
- **Data and agents:** changes are saved in the browser, and "Learn more → Reset demo data" restores the demo. The Generator and Validator agents are simulated.
