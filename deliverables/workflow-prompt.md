# Workflow diagram: tool, prompt and review

## Tool and inputs

- **Tool:** Claude (Claude Code, the same session that built the prototype).
- **Source document:** the SimWard process description: the concept summary in the build prompt plus the Week 2 SimWard knowledge base. It is written up as [`workflow-source-document.md`](workflow-source-document.md), so it can be uploaded to any AI tool.
- **Output:** [`workflow-diagram.pdf`](workflow-diagram.pdf) (A4 landscape), with PNG and SVG copies.

## Structured prompt used (verbatim, from prompt 1 in the [prompt log](prompt-log.md))

```markdown
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
```

## Reusable version for ChatGPT, Gemini or Claude

To compare tools, attach `workflow-source-document.md` and paste:

```text
You are a business process analyst. Using only the attached process document, create a one-page
swimlane workflow diagram (A4 landscape) titled "SimWard: from data request to released synthetic dataset".

Lanes, top to bottom: Analyst | Generator agent | Validator agent | Data steward | Audit log.
Show these steps in order:
1. Analyst submits a request (question, variables, minimum scores).
2. Generator agent builds candidates inside the secure environment.
3. Validator agent scores fidelity, privacy and utility against the minimums.
4. Decision diamond: privacy below minimum? Yes: reject and regenerate (loop back to step 2).
5. Decision diamond: fidelity or utility below minimum? Yes: steward records a written justification.
6. Steward review session, then sign-off.
7. Release to the analyst.
The Audit log lane records every step.

Style: at most 14 shapes; every label under 6 words; diamonds for decisions; one colour for AI-agent
steps (violet #5A48C8) and another for human steps (teal #0B6B66); a small legend; white background.
Before drawing, list any assumptions or ask me up to 3 clarifying questions. Output the diagram as
an image or as Mermaid/SVG code I can render.
```

## Reviewing the output

- **Did it ask clarifying questions?** No. As instructed, it listed three assumptions and then drew straight away:
  1. Both decisions are automated checks, so they sit in the Validator lane.
  2. "Reject and regenerate" is a labelled loop back to the Generator, not a separate shape.
  3. The page is A4 landscape, agent steps are violet, human steps are teal, and the site font is embedded.
- **What it got right:**
  - All five lanes and all seven steps are present, in order.
  - Both decision diamonds are there, with the privacy gate before the quality gate.
  - A dashed "secure environment" boundary shows that real data never leaves.
  - It uses 11 shapes and no label is longer than 5 words.
  - Colours match the site, and it exported to a vector PDF, a PNG and an SVG.
  - Automated checks confirmed that no text overflows its shape.
- **What is missing or simplified:**
  - The concept's **Analyst agent** (plain-language querying after release) is not shown, because the prompt's flow ended at release.
  - The audit log is one bar with dotted connectors, not an entry for each step.
  - "Withdraw after release" and "restore a rejected candidate" are rules in the prototype but not in the diagram.
- **What I learned:** the diagram followed the prompt's structure almost exactly. A structured prompt gives a predictable result, but the diagram can only be as complete as the prompt and source document. Anything I did not list was left out.
