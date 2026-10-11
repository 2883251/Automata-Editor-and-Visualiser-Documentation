# User Testing Feedback

This page documents the user testing feedback collected between 27 September and 10 October 2026, and maps each theme to the relevant Gitea issues across the Frontend, Backend, and Core repositories. It shows what the testers said and what the team did about it. Issue states are as of 11 October 2026.

Testers used the deployed app and then answered a feedback form: a 1–5 rating for each feature, and free-text questions about what was unclear or missing. Testers are identified by a number, in the order they responded, and their role.

---

## Test Demographics

11 people responded. Nine responded between 27 and 29 September, during sprint 3, and two more on 9 and 10 October, at the end of sprint 4.

| # | Date | Role | Familiar with TMs? |
|---|------|------|--------------------|
| 1 | 27 Sep | CS Student | Yes |
| 2 | 28 Sep | CS Student | Yes |
| 3 | 28 Sep | CS Student | Yes |
| 4 | 28 Sep | Developer | Yes |
| 5 | 28 Sep | CS Student | Yes |
| 6 | 28 Sep | CS Student | Yes |
| 7 | 29 Sep | Graphic Design Student | **No** |
| 8 | 29 Sep | CS Student | Yes |
| 9 | 29 Sep | CS Student | Yes |
| 10 | 9 Oct | CS Student | Yes |
| 11 | 10 Oct | CS Student | Yes |

9 of the 11 were CS students and one was a developer, all familiar with Turing Machines. One tester (a graphic design student) was **not** familiar with TMs, which provides valuable insight into first-time-user experience.

---

## Feedback Themes

### Onboarding & In-App Help (7 of 11 testers)

The most common request by far: seven testers asked for guidance, a tutorial, or a help page.

| Tester | Quote |
|--------|-------|
| CS Student #3 | "It was a little intimidating at first, since there are no guides on the website on what to do." |
| CS Student #5 | "an initial guide for first time users which revealed features piece by piece might help that feeling of "woah thats a lot of stuff" when you first open the app" |
| CS Student #6 | "Possibly trying to add a (hint/descriptor) bubble or pop up overlay onto different features on how they properly work may be useful for first time users." |
| Graphic Design #7 | "Yes, I need instructions" |
| CS Student #8 | "Quick to understand but some basic descriptions or help tab would be nice" |
| CS Student #10 | "maybe add a help page that explains to tap the space to add nodes to the automata" |
| CS Student #11 | "There were no instructions or tutorials or a help page to learn the language being used. I had to infer it from the pre-made examples." |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#48](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/48) | [P3] User documentation and in-app help | Closed (Sprint 4) |
| Frontend | [#83](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/83) | fix(ui): plain, concise interface text | Closed |
| Frontend | [#82](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/82) | refactor(ui): icon buttons with hover descriptions | Closed |

**What we did.** Sprint 4 added a **Help** dialog, opened from the question mark in the navigation bar. It has seven short topics: Getting started, Diagram, Instructions, Run and debug, Test, Save and share, and API keys (see [Getting Started](getting-started.md#help-inside-the-app)). Every icon button also shows its name on hover or keyboard focus, and this site gained a page for each feature ([Features](Features/index.md)).

The two October responses were given before the Help dialog was released on the evening of 10 October. Both ask for what it now provides: how to add a state, and a guide to the instruction language.

---

### Adding States and Writing Instructions (3 testers)

Three testers found it hard to work out how to add a state or how to write the instructions.

| Tester | Quote |
|--------|-------|
| CS Student #6 | "Only issue I was having is with the creation and editing of states. That could be me just having a lack of knowledge on notations though." |
| CS Student #10 | "i had a bit of trouble figuring out how to add the states to the automata, a quick visual or help page would be useful." |
| CS Student #11 | "It's hard to code the states and transitions because I have to infer how to use the language from the examples." |

**What we did.** The Help dialog's **Diagram** topic says how to add, rename and delete a state and how to draw a transition. Its **Instructions** topic lists each section of the language and the form of a rule, with five worked examples to copy: single tape, multi-step moves, a stationary move, multiple tapes, and a nondeterministic machine. Both come under #48.

---

### Tedious Machine Creation (2 testers)

Create, edit and delete was the lowest-rated basic feature (4.0 of 5).

| Tester | Quote |
|--------|-------|
| CS Student #2 | "It's quite tedious to make complex machines with large alphabets or many transitions" |
| CS Student #3 | "multi-tape machine instructions are tedious to set up. maybe a stationary instruction could help this." |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#118](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/118) | [L8] Choose a stationary move in the editor | Closed (Sprint 4) |
| Core | [#60](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/60) | [L7] Stationary head moves | Closed (Core 4.0.0) |
| Frontend | [#115](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/115) | Code editor inline suggestions and 'snippets' | Closed |
| Core | [#59](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/59) | Transition wildcards | Closed, not built |

**What we did.** Stationary moves shipped in sprint 4, the stationary instruction CS Student #3 asked for (see [TM Variants](Features/tm-variants.md#stationary-moves-l7-l8)). For large alphabets, the instruction editor now suggests states, symbols and directions inside a rule (#115) and offers a quick fix that **fills in the missing transitions** for a state or for the whole machine. That did the job transition wildcards (#59) were proposed for, so they were not built.

---

### Examples (2 testers)

| Tester | Quote |
|--------|-------|
| CS Student #8 | "a larger variety of examples with more detailed explanation" |
| CS Student #11 | "no example non-deterministic Turing Machine is bad" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#84](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/84) | feat(home): landing page with your, shared and example machines | Closed |
| Frontend | [#48](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/48) | [P3] User documentation and in-app help | Closed (Sprint 4) |

**What we did.** The home page now has **seven** examples, one for each kind of machine, each with a one-line description. One of them, **Contains a 1**, is nondeterministic. The Help dialog adds five worked examples with inputs to try.

---

### Colour and Dark Mode (2 testers)

Overall look and feel was rated 4.5 of 5. Two testers commented on colour.

| Tester | Quote |
|--------|-------|
| Graphic Design #7 | "The U.I needs better color choices" |
| CS Student #6 | "Keep a default color mode to dark mode, light doesn't work with your design at all :D" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#97](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/97) | feat(ui): light and dark mode, with the choice remembered | Closed |
| Frontend | [#110](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/110) | fix(diagram): transition Move dropdown is unreadable in dark mode | Closed |

**What we did.** Light and dark mode, with the choice remembered and the device's setting followed by default, shipped just before testing (#97). A dropdown that was unreadable in dark mode was fixed during it (#110). The colours are described in [UI Design](ui-design.md#visual-design-system).

---

### Error Messages (2 testers)

Error handling was the **lowest-rated question** (3.8 of 5). Two testers rated it 2.

| Tester | Quote |
|--------|-------|
| CS Student #1 | "The error message "...Fix the instructions to edit the diagram again" isn't too helpful if I can't code" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#18](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/18) | [E4] Keep the diagram usable while the code is invalid | Closed |
| Core | [#9](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/9) | [B3] Parse and validation diagnostics | Closed |

**What we did.** When the instructions have an error, the diagram shows the last machine that was valid and is locked until the error is fixed. The error is underlined in the instruction editor, and the status bar counts the errors and jumps to the first one. The Help dialog now explains this and the instruction language. A machine that was saved with an error now opens so it can be fixed, where before it was refused. The wording of the messages themselves has not changed.

---

### Collaborative Editing & Computation Trees (Advanced features)

These questions were optional. Eight testers rated collaborative editing (4.0 of 5) and seven rated the computation tree (4.0 of 5).

| Tester | Quote |
|--------|-------|
| CS Student #11 | "Collaborative edit syncs changes near instantly, very nice." |
| CS Student #9 | "Well they've really added quite a lot of advanced features" |
| CS Student #6 | "I have no friends to run  a collaborative machine with, and my scope of knowledge doesn't reach computations trees for nondeterministic machines." |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#114](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/114) | fix(collab): same user shows as multiple presence chips across tabs | Closed (fixed) |

**What we did.** A bug that showed one person as several people in a room was fixed (#114). A nondeterministic example and a Help topic now give a way in to the computation tree for someone who has not met one, and [Nondeterministic Machines](Features/nondeterminism.md) describes it on this site.

---

## Suggestions Not Taken Up

These were raised by one or two testers and were not built.

| Suggestion | From |
|---|---|
| Buttons to sync the diagram and the code by hand | CS Student #1 |
| Save a machine as a file, and load one | CS Student #3 |
| Share by a link, or invite someone who has no account yet | CS Student #3, CS Student #11 |
| Show usernames in place of email addresses | CS Student #8 |
| A sliding animation when the tape head moves | CS Student #5 |
| A wider review of the colour palette | Graphic Design #7 |
| Simpler automata (DFA, NFA, CFG), and saying more plainly that the tool is for Turing Machines | CS Student #11 |
| Clearer wording on the sign-in page, and hiding the React Flow link on the diagram | CS Student #10 |

---

## Summary: Feedback to Outcome

| Theme | Testers | Issues | Outcome |
|-------|---------|--------|---------|
| Onboarding / in-app help | 7/11 | Frontend #48, #82, #83 | Shipped: Help dialog, tooltips, feature pages |
| Adding states, writing instructions | 3/11 | Frontend #48 | Shipped: Diagram and Instructions help topics, worked examples |
| Tedious machine creation | 2/11 | Frontend #118, #115, Core #60 | Shipped: stationary moves, suggestions, fill missing transitions |
| Examples | 2/11 | Frontend #84, #48 | Shipped: seven examples, one nondeterministic |
| Colour and dark mode | 2/11 | Frontend #97, #110 | Shipped: light and dark mode. Palette review not taken up |
| Error messages | 2/11 | Frontend #18, Core #9 | Partly: explained in Help, and a machine with an error now opens. Wording unchanged |
| Collaboration / computation trees | optional | Frontend #114 | Shipped, bug fixed |

Every issue on this page is closed.

---

## Key Takeaways

1. **User onboarding was the primary concern.** 7 of 11 testers asked for help or a guide. This drove the sprint 4 Help dialog (#48), the larger set of examples, and the feature pages on this site.
2. **The tool works well for users already familiar with TMs.** Responsiveness (4.9), the debugger (4.7) and the computation visualisation (4.6) were among the highest-rated, and overall satisfaction averaged 4.6 of 5.
3. **A first-time user needs more support.** The one tester unfamiliar with TMs rated keeping the two editors in sync 1 of 5 and said plainly that they needed instructions.
4. **Error messages are the weakest area.** At 3.8 of 5 it is the lowest-rated question, and the messages still assume the reader can fix the instructions.
5. **The feedback was about guidance and polish, not missing features.** The core functionality was well received; what testers asked for was discoverability and quality-of-life improvements.

---

## Appendix: Raw Feedback Scores

All ratings are on a 1–5 scale, where higher is better. A dash means the tester left the question blank. Averages are over the testers who answered.

### Basic Features

| Tester | Dual Editor Sync | Create/Edit/Delete | Computation Visualisation | Export |
|--------|---|---|---|---|
| #1 | 2 | 5 | 5 | 5 |
| #2 | 4 | 2 | 5 | 4 |
| #3 | 5 | 5 | 5 | 4 |
| #4 | 5 | 3 | 4 | 3 |
| #5 | 4 | 5 | 4 | 4 |
| #6 | 5 | 2 | 4 | 4 |
| #7 | 1 | 4 | 5 | 4 |
| #8 | 5 | 5 | 4 | 5 |
| #9 | 5 | 5 | 5 | 5 |
| #10 | 4 | 5 | 5 | 5 |
| #11 | 5 | 3 | 5 | 2 |
| **Avg** | **4.1** | **4.0** | **4.6** | **4.1** |

### Intermediate Features

| Tester | Test Cases | Debugger | Complexity Plots | Variants | Sharing |
|--------|---|---|---|---|---|
| #1 | 5 | 5 | 5 | 4 | 5 |
| #2 | — | 5 | 3 | 4 | 5 |
| #3 | 5 | 5 | 4 | 3 | 4 |
| #4 | 4 | 5 | 4 | 4 | 5 |
| #5 | 3 | 5 | 4 | 3 | — |
| #6 | 4 | 5 | 4 | 4 | 3 |
| #7 | 4 | 3 | 3 | 5 | 5 |
| #8 | 4 | 5 | 3 | 5 | 5 |
| #9 | 5 | 5 | 5 | 5 | 5 |
| #10 | 5 | 5 | 5 | 5 | 5 |
| #11 | 5 | 4 | 5 | 5 | 4 |
| **Avg** | **4.4** | **4.7** | **4.1** | **4.3** | **4.6** |

### Advanced Features (optional)

| Tester | Collaborative Editing | Computation Tree |
|--------|---|---|
| #1 | 3 | 4 |
| #2 | 4 | 2 |
| #3 | 5 | 4 |
| #4 | — | — |
| #5 | — | — |
| #6 | 3 | 3 |
| #7 | 2 | 5 |
| #8 | — | — |
| #9 | 5 | 5 |
| #10 | 5 | 5 |
| #11 | 5 | — |
| **Avg** | **4.0** | **4.0** |

### Overall

| Tester | Look & Feel | Performance | Error Handling | Satisfaction |
|--------|---|---|---|---|
| #1 | 5 | 5 | 2 | 5 |
| #2 | 4 | 4 | 4 | 4 |
| #3 | 5 | 5 | 3 | 5 |
| #4 | 5 | 5 | 5 | 4 |
| #5 | 4 | 5 | 4 | 5 |
| #6 | 5 | 5 | 4 | 4 |
| #7 | 4 | 5 | 3 | 5 |
| #8 | 5 | 5 | 5 | 5 |
| #9 | 5 | 5 | 5 | 5 |
| #10 | 4 | 5 | 5 | 5 |
| #11 | 4 | 5 | 2 | 4 |
| **Avg** | **4.5** | **4.9** | **3.8** | **4.6** |

Ease of use was a written question with no rating. Its answers are quoted in the themes above.

---

**Related**: [Features Overview](Features/index.md) | [Getting Started](getting-started.md) | [Rubric Proof](rubric-proof.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
The page was rebuilt from all 11 form responses, with the scores and averages computed from the export, with the assistance of: Claude Code [Claude Opus 5.5].
