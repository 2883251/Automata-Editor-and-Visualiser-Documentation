# Project Methodology

## 1. Purpose

This document defines how the team plans, coordinates, and delivers work: the methodology we follow and why, how sprints are bounded, which ceremonies we hold and in what medium, and how the backlog is prioritised.

It is the planning counterpart to the [Git Methodology](./git-methodology.md), which governs how that work reaches the repositories. Where the two overlap, the Git Methodology is authoritative on version control mechanics and this document is authoritative on process.

## 2. Methodology

We follow a **Scrum-derived agile process, adapted for a small part-time team**. The framework is Scrum in its essentials — fixed sprints, a prioritised backlog, a sprint goal, regular review with the client, and a retrospective — with three deliberate departures: the daily standup is replaced by asynchronous written updates, the sprint review and retrospective are folded into a single weekly meeting, and the process roles are held collectively rather than by named individuals.

We are explicit that this is *adapted* Scrum rather than Scrum by the book. Claiming full Scrum while holding one meeting a week and no dedicated Scrum Master would misdescribe what we actually do.

### 2.1 Why agile

**The requirements specify capabilities, not a design.** What we have been given describes what a student using the tool should be able to do, not how the system should be built. There is no design specification to implement against, so the design has to emerge from working software. That is precisely the condition agile methods are built for.

**The requirements are already incremental.** They arrive in three cumulative levels — basic, intermediate, and advanced. The intermediate features extend the basic ones, requiring everything earlier to keep working across each new machine variant, and the advanced features extend the intermediate, turning sharing into simultaneous editing. The requirements therefore describe a sequence of increments, each of which is a usable product on its own. Mapping those levels onto sprints costs us nothing and follows the shape of the problem exactly.

**The client is available and expects to be involved.** We have a stakeholder who will review progress and refine priorities as the work develops, rather than a fixed specification handed over once. Regular demonstrations of progress let us correct course while correcting is still cheap. A process that deferred client contact to a single hand-over would waste the one advantage we have.

**Significant technical uncertainty exists up front.** Bidirectional synchronisation between a visual editor and a code editor, conflict-free simultaneous editing, and rendering nondeterministic computation trees are all problems whose difficulty we cannot reliably estimate before attempting them. Empirical process control — plan a short horizon, build, inspect the result, adapt the next plan — handles that uncertainty better than a plan written when we know least.

**Scope must be able to flex.** The delivery window is fixed and the team is part-time; the feature set is not. A prioritised backlog means that when time runs short, we drop the lowest-priority advanced item and still deliver a coherent working product. Under a plan-driven process the same pressure produces a half-finished system across the board.

## 3. Team and Roles

The team has four members and operates as a flat, collectively accountable unit. **There is no designated Scrum Master and no designated Release Manager.** Every member is a developer, and the coordination work that those roles would otherwise carry is distributed across the whole team:

- The sprint goal and backlog are agreed by the team together at sprint planning, not set by one person.
- Every member keeps their own board items current; anyone may correct the board where it has drifted.
- Accountability is mutual — members hold each other to what was committed at planning, in the weekly scrum and in Discord, rather than reporting upward to a single coordinator.
- Blockers are raised by whoever is blocked, to the team, and are unblocked by whoever is able to.
- Responsibility for a specific release is assigned ad hoc at the scrum preceding it, per Git Methodology §7. This is a task attached to one release, not a standing role.

The **client** acts as the product stakeholder: setting and confirming priorities, and accepting or rejecting what we produce.

**Justification.** In a four-person team of peers no member has any actual authority over another, so a named Scrum Master would carry the responsibility without the standing to enforce anything — the role would be nominal, and the work would in practice be done collectively anyway. Naming it would therefore describe the team inaccurately. Distributing it also removes a single point of failure: no one week of the project depends on one person's availability, which matters when all four of us work on this around other commitments. The trade-off is the familiar one for flat teams — work that is everyone's responsibility can become nobody's — which we mitigate by making the board the shared record and by reviewing commitments against it at every scrum.

## 4. Sprints

### 4.1 Sprint boundaries

Sprints are bounded by the project's milestone deadlines rather than by a uniform calendar interval. Sprint length therefore varies.

**Justification.** The milestones are the real constraint on this project: they are fixed and externally imposed, and each one is a point at which the work is reviewed. A uniform two-week cadence would inevitably drift out of alignment with them, producing sprints that end days before or after a milestone and an increment that is not the one being delivered. Binding the sprint to the deadline guarantees that every sprint ends in a demonstrable, deliverable increment, which is the property a sprint is supposed to have.

### 4.2 Schedule

| Sprint | Starts | Ends | Theme |
| --- | --- | --- | --- |
| **Sprint 1** | Project start | Tue 25 Aug 2026 | Basic features |
| **Sprint 2** | Wed 26 Aug 2026 | Tue 15 Sep 2026 | Intermediate features |
| **Sprint 3** | Wed 16 Sep 2026 | Tue 29 Sep 2026 | Intermediate completion and advanced features |
| **Sprint 4** | Wed 30 Sep 2026 | Sun 11 Oct 2026 | Completion and finishing touches |

### 4.3 Sprint content

The mapping below is indicative — the backlog for each sprint is confirmed at its planning meeting and may be adjusted on the client's instruction.

| Sprint | Intended outcome |
| --- | --- |
| **1** | Repositories, CI, branch protection, and board in place; architecture agreed. Most, and ideally all, of the basic feature set: dual visual/code editor with bidirectional sync; create, edit, delete, and save machines; interactive draggable state diagram; computation visualisation with tape, head, and highlighted state; export of diagram and instruction table |
| **2** | Any outstanding basic work closed out, then the intermediate features: test cases with expected outputs; step debugger; time and space plots; machine variants (computational, multi-tape, multi-step); sharing a machine with another user |
| **3** | Intermediate features finalised where not already complete, then the advanced ones: simultaneous multi-user editing; nondeterministic machines with a traversable computation tree |
| **4** | Finishing touches: any feature still missing, defect fixing, performance, documentation, and final delivery |

### 4.4 Front-loading the schedule

The plan is deliberately front-loaded. Sprint 1 targets the entire basic feature set and Sprint 2 the entire intermediate set, which is an aggressive pace for the opening weeks, and it is intended to be.

**Justification.** Team availability is not constant across the project — it falls off in the later weeks as competing commitments accumulate. Planning uniform effort across four sprints would therefore plan against capacity we will not have. By pushing hard while capacity is highest, we convert time we actually have into completed features, and we reach the compressed end of the project with a working product and a backlog of refinements rather than a backlog of core functionality. It also front-loads the technical risk: the hardest problem in the project, bidirectional editor synchronisation, is attempted first, when there is still time to recover from being wrong about it.

The consequence is that Sprint 4 is reserved for completion and hardening rather than new work. The final increment is what is delivered as a whole, and the risk of entering it with an unstable intermediate feature set is higher than the value of one additional advanced feature.

### 4.5 Relationship to releases

Each sprint corresponds to one release in the Git Methodology. A release branch is cut from `main` at sprint planning and merged into `main` at sprint close, which is what deploys the increment. Every sprint therefore ends with working, deployed software — not merely with merged code.

Release branches are named after the sprint they serve: `dev/sprint-<number>`, for example `dev/sprint-2`. The number is used rather than a descriptive name because a sprint's contents can shift during it — work carried over or descoped would leave a name like `dev/intermediate-features` inaccurate — whereas the sprint it belongs to never changes.

## 5. Ceremonies

| Ceremony | When | Medium | Output |
| --- | --- | --- | --- |
| **Sprint planning** | At the start of each sprint | Online | Sprint goal; agreed backlog for the sprint |
| **Weekly scrum** | Once a week, day varies | Online | Progress review; blockers raised; board reconciled |
| **Async updates** | Any day work is done | Discord `#general` | Written note: done / next / blocked |
| **Client review** | Approximately fortnightly | Email, occasionally in person | Progress reported; client feedback recorded as backlog items |
| **Retrospective** | Final weekly scrum of a sprint | Online, sometimes in person | Two or three concrete process actions for the next sprint |

Internal ceremonies are held online by default. Getting four people into one room reliably is harder than getting them into one call, and the meetings themselves — reviewing a board, walking through blockers, agreeing a plan — lose nothing by being remote. Client reviews are the exception, since those are conducted with the client and on their terms (§5.3), and a retrospective is worth holding in person when the opportunity arises, as it is the one conversation that benefits from being a little less formal.

### 5.1 Weekly scrum

The team meets once a week, online. The day is not fixed: it is agreed in advance in Discord each week around the members' schedules. This meeting carries the work of three Scrum events — it is the progress checkpoint, the internal sprint review, and, in the last week of a sprint, the retrospective.

**Justification for meeting weekly.** A daily standup requires the team to be available at the same time every day. Every member works on this part-time around other commitments, so we do not have that availability, and a daily ceremony that half the team misses is worse than no ceremony, because the board stops being trusted. One reliably attended weekly meeting produces better coordination than five unreliable daily ones.

**Justification for a floating day.** The four members have different schedules, and those schedules change from week to week. Fixing the scrum to a particular weekday would systematically exclude whichever member has a recurring clash with it, so the one meeting we do hold would be the one meeting someone always misses. Agreeing the day a week ahead costs a short exchange in Discord and buys full attendance, which is the only property of this meeting that actually matters. Holding it online is what makes a floating day practical: there is no venue to book and no travel to plan around, so a slot only has to be free, not convenient.

The risk this creates is a longer feedback latency: a member can be blocked for up to seven days before it surfaces at a meeting. Section 5.2 exists specifically to mitigate that.

### 5.2 Asynchronous coordination

Day-to-day coordination happens in a Discord server, not in meetings. Members post a short written update in `#general` on any day they do project work, stating what they finished, what they are moving to, and what is blocking them. Blockers are the priority: anything blocking is raised immediately in Discord rather than held until the next scrum.

| Channel | Use |
| --- | --- |
| `#general` | All day-to-day discussion: scheduling, technical questions, and the written done / next / blocked updates |
| `#information` | Reference material — methodology documents, architecture notes, and anything the team needs to look up |
| `#git-logs` | Gitea webhook feed: commits, pull requests, and review requests |

**Justification.** This is the deliberate trade: we give up the synchronous daily standup, so the information it would have carried has to move in writing instead. Written updates have a compensating advantage over a spoken standup — they are durable and searchable, so the team can reconstruct the week before the scrum and the meeting can be spent on decisions rather than status recitation.

The channel structure is kept intentionally small. Splitting discussion by repository would fragment a four-person conversation across three channels that each see traffic a few times a week, which makes the discussion harder to follow rather than easier; at this team size a single active channel is the better default. `#information` exists so that reference material does not scroll out of reach, and `#git-logs` keeps repository activity visible without anyone having to poll Gitea, which supports the one-working-day review turnaround set in Git Methodology §5.

### 5.3 Client review

We report progress to the client approximately every two weeks. The client has elected to communicate **by email**, so reviews are ordinarily conducted asynchronously: we send a written summary of the increment, what has changed since the last report, and any decisions we need. Where a synchronous meeting is arranged, it is held on a Tuesday.

Review timing is approximate rather than scheduled to fixed dates. It follows the progress of the work and the client's availability, and is confirmed as each report falls due.

**Justification.** Fortnightly is frequent enough that a misunderstanding costs at most two weeks of work, and infrequent enough that there is a meaningful increment to show each time. Email is the client's stated preference and we accommodate it rather than imposing a cadence of meetings on someone with limited availability; it also has the incidental benefit of leaving a written record of every decision and priority change, which a verbal meeting would not. The trade-off is slower turnaround on questions and no opportunity for the client to interact with the tool during the review, which we offset by deploying each increment so it can be used directly and by keeping the written reports concrete about what is and is not working.

Client feedback is recorded as backlog items before the next planning meeting, so that it enters the sprint rather than sitting in an inbox.

### 5.4 Retrospective

The last weekly scrum of each sprint reserves time at the end for a retrospective: what went well, what did not, and what we will change. The output is two or three specific, assignable actions — not a list of observations. Actions carry into the next sprint's planning and are reviewed at the following retrospective, so that a problem raised twice without change becomes visible.

**Justification.** With only four sprints, a process failure that goes uncorrected costs a quarter of the project. The retrospective is the only mechanism that lets the process itself improve, and it is the first ceremony teams drop under pressure; attaching it to a meeting we are already holding makes it hard to skip.

## 6. Backlog and Prioritisation

### 6.1 Board

Work is tracked on a **Gitea project board** spanning all three repositories. Columns are `Backlog → Ready → In Progress → In Review → Done`.

**Justification.** The board lives on the same Gitea instance as the code, which is what makes it cheap to keep accurate. Issue numbers flow directly into the branch names required by Git Methodology §3 (`feat/42-import-automata`), pull requests close their issues on merge, and one board covers the `package`, `frontend`, and `backend` repositories without duplicated bookkeeping. A separate tracker would require the team to maintain the link between board and code by hand, which is exactly the maintenance that lapses first when deadlines approach. With no single person owning the board, low-friction and largely automatic upkeep is not a convenience but a requirement.

### 6.2 Work items

Backlog items are written as user stories — *As a student, I want to step through a computation one configuration at a time, so that I can see where my machine goes wrong* — with acceptance criteria attached. An item should have a clear story and acceptance criteria before it is pulled into a sprint, so that it is unambiguous who has finished what. Items too large for a single sprint are treated as epics and broken down at planning.

### 6.3 Prioritisation

Priority uses **MoSCoW**, mapped onto the three levels of the requirements:

| Priority | Corresponds to | Treatment |
| --- | --- | --- |
| **Must** | Basic features | The product is not viable without these; they are scheduled first and are not dropped |
| **Should** | Intermediate features | Substantial value; dropped only under real schedule pressure |
| **Could** | Advanced features | Delivered if capacity allows; the first candidates for descoping |
| **Won't** | Out of scope this project | Recorded so the boundary is explicit and not revisited |

**Justification.** The requirements already imply a priority ordering; MoSCoW makes it explicit and gives the team a pre-agreed answer to the question of what gets cut. Deciding descoping rules in advance, calmly, is considerably better than deciding them in the last week under pressure. It also gives the client a clear vocabulary for reordering priorities in a written exchange, where a longer negotiation is impractical.

## 7. Integration with the Git Methodology

The two documents describe one process from two angles. The mapping is:

| Planning concept | Version control counterpart |
| --- | --- |
| Sprint | Release, with branch `dev/sprint-<number>` (Git §3) |
| Backlog item | Working branch `<type>/<number>-<description>` (Git §3) |
| Item complete | Squashed pull request into the release branch (Git §5) |
| Sprint close | Release branch merged into `main` and tagged; deployed (Git §7) |
| Mutual accountability | Mandatory peer review on every pull request (Git §5) |
| Release responsibility for a sprint | Assigned at the scrum preceding the release (Git §7) |
| Defect found in a demonstrated increment | `hotfix/` branch from `main` (Git §3) |

## 8. Risks

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Sprint 1 targets the whole basic feature set in a short sprint | Basic work spills into Sprint 2 and compresses the intermediate features | Accepted deliberately (§4.4); Sprint 2 explicitly opens by closing out any outstanding basic work rather than treating it as overflow |
| Availability falls off later in the project as competing commitments land | Sprints 3 and 4 under-deliver | Schedule front-loaded so the later sprints carry completion rather than core functionality |
| Weekly-only meetings let a blocker sit for days | Lost days | Blockers raised in Discord immediately, not held for the meeting |
| No single person owns the board or the process | Coordination work is everyone's and therefore nobody's | Board is the shared record; commitments reviewed against it at every scrum; anyone may correct it |
| Bidirectional editor synchronisation proves harder than estimated | Core basic feature at risk | Attempted first, in Sprint 1, as the highest-risk Must item; architecture chosen so the collaborative editing layer reuses the same document model |
| Email-only client contact slows feedback | Wrong direction pursued for longer | Each increment deployed so the client can use it directly; written reports state explicitly what is and is not working |
| Simultaneous editing does not fit | Advanced features incomplete | Classified `Could`; a working single-user product is delivered regardless |
| Non-halting machines freeze the application during a demonstration | Client review derailed | Simulation runs off the main thread with a step budget; treated as a Must, not an optimisation |
| A team member becomes unavailable | Knowledge concentrated in one person | No standing roles; every change reviewed by a second member, so no area has only one informed developer |

<br>

**AI Declaration:** The preceding document was drafted and edited with the assistance of: Claude Cowork [Claude Opus 5].
