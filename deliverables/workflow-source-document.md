# SimWard process document: from data request to released synthetic dataset

This is the source document used to generate the workflow diagram (`workflow-diagram.pdf`). It describes the SimWard concept, a student proposal for giving hospital analysts realistic synthetic data without exposing real patients. It is fictional, uses no real data, and is not affiliated with any hospital.

## Purpose

Analysts need data to study patient flow, staffing and capacity. Access to real patient records is slow because privacy law rightly protects it. SimWard replaces most early-stage requests with synthetic datasets: artificial records that keep the statistical patterns of the real data but do not belong to any real person.

## Roles

| Role | Type | Responsibility |
|---|---|---|
| Analyst (requester) | Human | Submits a data request and receives the released dataset |
| Generator agent | AI agent | Builds synthetic candidate datasets inside the secure environment |
| Validator agent | AI agent | Scores each candidate for fidelity, privacy and utility |
| Data steward | Human | Reviews the validation report, records decisions and signs off releases |
| Audit log | System | Records every action with who did it and when |

## Process steps

1. **Submit a request.** The analyst describes the question, lists the variables needed, and sets minimum scores (0 to 100) for fidelity, privacy and utility. The request has a due date.
2. **Generate candidates.** The Generator agent learns patterns from an approved source dataset and produces one or more synthetic candidates, using a method such as Gaussian copula, CTGAN, TVAE or a Bayesian network. Real data never leaves the secure environment.
3. **Validate.** The Validator agent scores each candidate against the request's minimums:
   - *Fidelity*: do the important patterns and relationships hold?
   - *Privacy*: could any synthetic record be matched back to a real person (near-copies, rare combinations)?
   - *Utility*: does the data answer the analyst's question?
4. **Privacy gate.** If privacy is below the minimum, the candidate is rejected with a reason and the Generator produces a new one. No justification can override a privacy failure.
5. **Quality gate.** If fidelity or utility is below the minimum, the candidate may still proceed, but the data steward must record a written justification before approving it.
6. **Steward review and sign-off.** The steward holds a review session (date, steward, agenda/notes), confirms they have read the validation report, and signs off. Sign-off records the steward's name, the time and any decision note.
7. **Release.** The approved dataset is released to the analyst. Released datasets are locked; they can only be withdrawn, with a reason.

## Rules

- Candidates move forward one stage at a time: Generated → Validation → Steward review → Approved → Released.
- A candidate cannot enter steward review without scores.
- Moving an approved candidate backwards withdraws its sign-off.
- If a later score change makes an approved candidate fail privacy, the sign-off is withdrawn automatically and the reason is logged.
- Every step writes an audit-log entry: timestamp, actor, action, candidate and request.

## Out of scope for this flow

The Analyst agent (plain-language querying of an approved dataset) happens after release and is not part of this request-to-release process.
