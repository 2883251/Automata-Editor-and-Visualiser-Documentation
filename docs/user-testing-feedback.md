# User Testing Feedback

This page documents the user testing feedback collected in September 2026 and maps each theme to the relevant Gitea issues across the Frontend, Backend, and Core repositories.

---

## Test Demographics

Seven testers participated in the user testing session held 27–29 September 2026.

| # | Date | Role | Familiar with TMs? |
|---|------|------|--------------------|
| 1 | 27 Sep | CS Student | Yes |
| 2 | 28 Sep | CS Student | Yes |
| 3 | 28 Sep | CS Student | Yes |
| 4 | 28 Sep | Developer | Yes |
| 5 | 28 Sep | CS Student | Yes |
| 6 | 29 Sep | Graphic Design Student | **No** |
| 7 | 29 Sep | CS Student | Yes |

Six of seven testers were CS students or a developer familiar with Turing Machines. One tester (a graphic design student) was **not** familiar with TMs, which provides valuable insight into first-time-user experience.

---

## Feedback Themes

### Onboarding & In-App Help (6 of 7 testers)

Six of seven testers mentioned the lack of guidance, tutorials, or in-app help.

| Tester | Quote |
|--------|-------|
| CS Student #3 | "It was a little intimidated at first, since there are no guides on the website on what to do." |
| CS Student #5 | "An initial guide for first time users which revealed features piece by piece might help that feeling of 'woah thats a lot of stuff' when you first open the app" |
| CS Student #6 | "Possibly trying to add a (hint/descriptor) bubble or pop up overlay onto different features on how they properly work may be useful for first time users." |
| Graphic Design #6 | "Yes, I need instructions" |
| CS Student #7 | "Some basic descriptions or help tab would be nice" |
| CS Student #7 | "A user guide for new users/people who aren't familiar with turing machines." |

**Suggested improvements from testers:**
- Initial onboarding guide / tutorial
- Hint/descriptor bubbles or pop-up overlays on features
- Cheat sheet or basic instruction card
- Pre-built drag-and-drop examples
- Feature-by-feature reveal for first-time users

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#48](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/48) | [P3] User documentation and in-app help | **Open** (Sprint 4, must) |
| Frontend | [#83](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/83) | fix(ui): plain, concise interface text | Closed |
| Frontend | [#82](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/82) | refactor(ui): icon buttons with hover descriptions | Closed |
| Documentation | — | No user-facing feature documentation existed at time of testing | Gap |

Issue #48 is open and scoped for Sprint 4 as a **must** item.

---

### Diagram ↔ Code Editor Synchronisation (2 testers)

| Tester | Quote |
|--------|-------|
| CS Student #1 | "I'd appreciate 'sync diagram with code editor' (and vice versa) buttons. The error message '…Fix the instructions to edit the diagram again' isn't too helpful if I can't code" |
| CS Student #1 | (Rating: 2/5 for ease of editor use) |

The tester found the sync error message unhelpful — when the code is invalid the diagram becomes read-only, but the error does not guide a non-coder on how to fix it.

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#18](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/18) | [E4] Keep the diagram usable while the code is invalid | Closed |
| Frontend | [#74](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/74) | feat(diagram): rework auto-arrange, edge routing and label placement | Closed |
| Frontend | [#115](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/115) | Code editor inline suggestions and 'snippets' | Closed |

The foundational sync behaviour is shipped (issue #18). The request for **explicit sync buttons** and **better error guidance when code is invalid** has not been addressed in a tracked issue.

---

### Tedious Machine Creation / Large Alphabets (3 testers)

| Tester | Quote |
|--------|-------|
| CS Student #2 | "It's quite tedious to make complex machines with large alphabets or many transitions" (Rating: 2/5 for create/edit) |
| CS Student #3 | "Multi-tape machine instructions are tedious to set up. Maybe a stationary instruction could help this." |
| CS Student #6 | "Trying to use certain part of the apps, especially trying to add and/or edit saved machines was proving quite challenging without any offer of guidance" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#118](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/118) | [L8] Choose a stationary move in the editor | **Open** (Sprint 4) |
| Core | [#60](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/60) | [L7] Stationary head moves | **Open** |
| Core | [#59](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/59) | Transition wildcards | **Open** |
| Frontend | [#47](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/47) | [P2] Performance with large machines | **Open** (Sprint 4, must) |

Stationary moves (#118 frontend, #60 core) are open and planned for Sprint 4. Transition wildcards (#59 core) could also reduce tedium for large alphabets. Performance with large machines (#47) is a Sprint 4 must.

---

### Import/Export of Full Turing Machine (1 tester)

| Tester | Quote |
|--------|-------|
| CS Student #3 | "Would be cool to import/export the turing machine itself. That way I can store it locally and send the file to others." |

The tool supports exporting the diagram as an image and instructions as a table (CSV/HTML), but not exporting/importing the machine definition as a file.

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#27](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/27) | [H1] Export the diagram as an image | Closed |
| Frontend | [#28](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/28) | [H2] Export the instructions as a table | Closed |
| Core | [#11](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/11) | [B5] Machine serialisation | Closed |

Machine serialisation exists in Core (#11), and diagram/table export is shipped (#27, #28). There is **no tracked issue** for importing/exporting the machine as a standalone file (e.g. JSON).

---

### Sharing UX — Link-Based Sharing (2 testers)

| Tester | Quote |
|--------|-------|
| CS Student #3 | "Copying a link for sharing would also be useful, instead of knowing a user's email." |
| CS Student #7 | "Allow usernames and not emails being shown (google logging in used)" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#68](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/68) | [M1b] Share a machine from the editor | Closed |
| Backend | [#6](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Backend/issues/6) | [M1] Share a machine with another user | Closed |
| Backend | [#27](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Backend/issues/27) | [N4] Share a machine for editing | Closed |

Sharing functionality is shipped, but the UX requires knowing the other user's email. Link-based sharing and displaying usernames instead of emails are **not tracked as issues**.

---

### Tape Visualisation — Animation (1 tester)

| Tester | Quote |
|--------|-------|
| CS Student #5 | "The tape visualization was a bit difficult to follow, a sliding animation of some sort may have helped this, instead of the rows just updating in place to represent movement" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#24](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/24) | [G3] Visualise the current configuration | Closed |

The tape visualisation is shipped (#24) but currently updates cells in place. A sliding animation to show head movement has **not been tracked**.

---

### UI / Colour / Dark Mode (3 testers)

| Tester | Quote |
|--------|-------|
| Graphic Design #6 | "The U.I needs better color choices" (Rating: 1/5 for editor ease of use) |
| CS Student #6 | "Keep a default color mode to dark mode, light doesn't work with your design at all :D" |
| CS Student #2 | (Rating: 2/5 for overall UI feel) |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#97](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/97) | feat(ui): light and dark mode, with the choice remembered | Closed |
| Frontend | [#110](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/110) | fix(diagram): transition Move dropdown is unreadable in dark mode | Closed |

Light/dark mode toggle is shipped (#97) and a dark-mode dropdown bug was fixed (#110). The graphic design student's feedback (1/5) suggests the **colour palette and contrast** still need work beyond just having a dark mode.

---

### Test Cases Scrollbar Bug (1 tester)

| Tester | Quote |
|--------|-------|
| Developer #4 | "The scrollbar seems to render a little too late on the test cases, so it pushes the simulation section up and makes it very small." |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#98](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/98) | fix(test-cases): long test case text runs off screen in the plot tooltip | Closed |

A related test-case display bug was fixed (#98), but the specific **late scrollbar rendering causing layout shift** does not appear to have a dedicated issue.

---

### More / Better Examples (2 testers)

| Tester | Quote |
|--------|-------|
| CS Student #7 | "A larger variety of examples with more detailed explanation" |
| CS Student #7 | "It seems those its should be used by intermediate users as some wording can be confusing" |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#84](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/84) | feat(home): landing page with your, shared and example machines | Closed |
| Frontend | [#48](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/48) | [P3] User documentation and in-app help | **Open** |

Example machines are surfaced on the landing page (#84), but users want **more examples with detailed explanations** and less confusing wording.

---

### Error Messages for Non-Coders (2 testers)

| Tester | Quote |
|--------|-------|
| CS Student #1 | "The error message '…Fix the instructions to edit the diagram again' isn't too helpful if I can't code" |
| Graphic Design #6 | "More numbers would be nice" (in the context of error feedback) |

#### Related Gitea Issues

| Repo | Issue | Title | State |
|------|-------|-------|-------|
| Frontend | [#18](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/18) | [E4] Keep the diagram usable while the code is invalid | Closed |
| Core | [#9](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/9) | [B3] Parse and validation diagnostics | Closed |

Parse diagnostics exist (#9 core) and the diagram stays editable during code errors (#18), but the **wording of error messages** is not specifically addressed.

---

### Collaborative Editing & Computation Trees (Advanced features)

Most testers did not attempt the advanced features. Two relevant data points:

| Tester | Quote |
|--------|-------|
| CS Student #6 | "Won't be able to help here. I have no friends to run a collaborative machine with, and my scope of knowledge doesn't reach computation trees for nondeterministic machines." |
| Frontend | [#114](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/114) | fix(collab): same user shows as multiple presence chips across tabs | **Open** (bug) |

The collaborative editing features are shipped but a presence-chip bug remains open (#114). The difficulty of testing collaboration solo was noted.

---

## Summary: Feedback to Issue Status

| Theme | Testers Affected | Existing Issue(s) | Issue Status | Action Needed |
|-------|-----------------|--------------------|--------------|---------------|
| Onboarding / in-app help | 6/7 | Frontend #48 | **Open** (Sprint 4, must) | Document on docs site |
| Diagram ↔ code sync UX | 2/7 | #18 (closed), no explicit-sync-button issue | Partially addressed | Consider new issue for sync buttons & better error messages |
| Tedious machine creation | 3/7 | #118 (open), #47 (open), Core #59, #60 | **Open** | Stationary moves & wildcards planned |
| Import/export TM file | 1/7 | #27, #28 (closed), Core #11 (closed) | Shipped for diagram/table only | Consider new issue for full TM import/export |
| Sharing UX (link, username) | 2/7 | #68, Backend #6 (closed) | Shipped (email-based) | Consider new issues for link sharing & username display |
| Tape animation | 1/7 | #24 (closed) | Shipped without animation | Consider new issue or UX polish item |
| UI colour / dark mode | 3/7 | #97, #110 (closed) | Shipped | Broader colour/contrast review not tracked |
| Test-cases scrollbar bug | 1/7 | #98 (closed, related) | Related bug fixed | New bug report for layout shift |
| More examples | 2/7 | #84 (closed), #48 (open) | Partially addressed | Fold into #48 scope |
| Error messages for non-coders | 2/7 | #18, Core #9 (closed) | Partially addressed | UX writing pass needed |
| Collab / computation trees | 1/7 (limited) | #114 (open bug) | Shipped, minor bug open | Fix #114 |

---

## Open Issues Relevant to User Feedback

### Frontend (open)

| Issue | Title | Milestone | Priority |
|-------|-------|-----------|----------|
| [#48](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/48) | [P3] User documentation and in-app help | Sprint 4 | **must** |
| [#47](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/47) | [P2] Performance with large machines | Sprint 4 | **must** |
| [#46](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/46) | [P1] Cross-browser, responsive, and accessibility pass | Sprint 4 | **must** |
| [#118](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/118) | [L8] Choose a stationary move in the editor | Sprint 4 | should |
| [#117](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/117) | fix(ui): use the app's own dialogs instead of the browser's pop-ups | — | could |
| [#114](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Frontend/issues/114) | fix(collab): same user shows as multiple presence chips across tabs | — | bug |

### Core (open)

| Issue | Title | Priority |
|-------|-------|----------|
| [#60](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/60) | [L7] Stationary head moves | should |
| [#59](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Core/issues/59) | Transition wildcards | enhancement |

### Backend (open)

| Issue | Title | Milestone | Priority |
|-------|-------|-----------|----------|
| [#7](https://sdp.ms.wits.ac.za/brh/Automata-Editor-and-Visualiser-Backend/issues/7) | [P4] Final release and deployment verification | Sprint 4 | must |

---

## Gaps: Feedback Items Without Tracked Issues

The following pieces of user feedback do not map to any existing open or closed issue and may warrant new issues:

1. **Explicit "sync diagram ↔ code" buttons** — Users want manual sync triggers and better guidance when the code is in an invalid state.
2. **Import/export full Turing Machine as a file** — Users want to save a TM locally and share it as a file (JSON or similar), beyond the existing diagram image and instruction table exports.
3. **Link-based sharing** — Users want to share via a copyable link rather than needing to know another user's email address.
4. **Display usernames instead of emails** — Google login shows email addresses; users prefer usernames or display names.
5. **Tape visualisation animation** — A sliding animation to show head movement instead of cells updating in place.
6. **Colour/contrast review** — Beyond the existing dark mode, the colour palette needs improvement (especially noted by the graphic design student).
7. **Test-cases scrollbar layout shift bug** — Scrollbar renders late, pushing the simulation section up and making it very small.
8. **More examples with detailed explanations** — Current examples are insufficient for beginners; wording can be confusing for non-intermediate users.
9. **Error message UX writing for non-coders** — Existing error messages assume coding knowledge; need plainer language.

---

## Key Takeaways

1. **User onboarding is the primary concern** — 86% of testers flagged the lack of help/guides. The documentation site should include a getting-started tutorial, feature explanations with screenshots, and a cheat sheet.
2. **The tool works well for users already familiar with TMs** — Ratings for simulation, debugging, test cases, and export were consistently high (4–5/5) among CS students.
3. **Non-TM users struggle significantly** — The one tester unfamiliar with TMs rated the editor 1/5 and explicitly stated they need instructions.
4. **Existing shipped features are well-received** — Simulation, debugger, test cases, complexity plots, export, and sharing all received positive ratings (mostly 4–5/5).
5. **UX polish items are the main gap** — The core functionality is solid; the feedback centres on discoverability, guidance, and quality-of-life improvements rather than missing core features.

---

## Appendix: Raw Feedback Scores

### Basic Features (1–5 scale, higher is easier/more useful)

| Tester | Dual Editor Sync | Create/Edit/Delete | Computation Visualisation | Export |
|--------|------------------|--------------------|---------------------------|--------|
| #1 | 2 | 5 | 5 | 5 |
| #2 | 4 | 2 | 5 | 4 |
| #3 | 5 | 5 | 5 | 4 |
| #4 | 5 | 3 | 4 | 3 |
| #5 | 4 | 5 | 4 | 4 |
| #6 | 5 | 2 | 4 | 4 |
| #7 | 5 | 5 | 4 | 5 |
| **Avg** | **4.3** | **3.9** | **4.4** | **4.1** |

### Intermediate Features (1–5 scale, higher is more useful)

| Tester | Test Cases | Debugger | Complexity Plots | Variants | Sharing |
|--------|-----------|----------|------------------|----------|---------|
| #1 | 5 | 5 | 5 | 4 | 5 |
| #2 | 5 | 5 | 4 | 5 | — |
| #3 | 5 | 5 | 4 | 3 | 4 |
| #4 | 4 | 5 | 4 | 4 | 5 |
| #5 | 3 | 5 | 4 | 3 | — |
| #6 | 4 | 5 | 4 | 4 | 3 |
| #7 | 4 | 5 | 3 | 5 | 5 |
| **Avg** | **4.3** | **5.0** | **4.0** | **3.9** | **4.3** |

### Overall Satisfaction & Other Ratings (1–5 scale)

| Tester | Overall Look & Feel | Ease of Use | Performance | Error Handling | Satisfaction |
|--------|---------------------|-------------|-------------|----------------|--------------|
| #1 | 5 | 2 | 5 | — | — |
| #2 | 4 | 4 | 4 | — | 4 |
| #3 | 5 | 5 | 5 | — | 5 |
| #4 | 5 | 5 | 5 | — | 4 |
| #5 | 4 | 4 | 4 | — | 5 |
| #6 | 5 | 4 | 4 | — | 4 |
| #7 | 5 | 5 | 5 | — | 5 |
| **Avg** | **4.6** | **4.1** | **4.6** | — | **4.5** |

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
