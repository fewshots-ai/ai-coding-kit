# Product: from idea to learning

Read when shaping an idea, requirements, or scope. Build the right thing before building it right.

## Lifecycle

| Stage | What you do | Output |
|---|---|---|
| Discover | State the problem, who has it, and why it matters now. | Problem statement |
| Define | Set the goal, success measure, scope, non-goals, and acceptance criteria. | Brief |
| Design | Sketch the screens and the user flow before coding. | Design notes |
| Build | Deliver thin end-to-end slices, most valuable first. | Working increment |
| Validate | Check every acceptance criterion and click through the real flow. | Passing tests, demo |
| Launch | Ship with a working README and known limitations. | Release |
| Learn | Compare results with the success measure and collect feedback. | Updated backlog |

## Rules

- **Brief first.** Before building, write down the problem, target user, goal, success measure, scope, non-goals, and acceptance criteria. An application keeps this in `docs/brief.md`, one page or less. A demo keeps only the assumptions and acceptance criteria, as a short section of the README.
- **Ask or assume, explicitly.** If a missing fact would change what gets built, ask. If you cannot ask, or the gap is small, state the assumption in the brief and continue.
- **Testable acceptance criteria.** Write each as observable behavior: "Given X, when Y, then Z." Each one maps to at least one test.
- **Screens before code.** List the screens, what each shows, and what the user can do on it. A screen with no data to show or no action to take does not exist yet.
- **Smallest valuable slice.** Build the thinnest version that works end to end, then widen. Rank work as must, should, or could, and finish every must first.
- **Presentations tell one story.** When the work will be shown to an audience, define the demo path: the 3 to 5 clicks they see. That path works every time, with realistic sample data and no dead ends.
- **No gold plating.** Ideas outside the brief go to the backlog, not into the code. The backlog is `docs/backlog.md`, or a "Next steps" list in a demo's README.
- **Record decisions.** An application logs each significant product or technical decision in `docs/decisions.md`: date, decision, reason, alternatives rejected.
- **Close the loop.** After release, update the backlog from feedback and usage, and remove features nobody uses.
