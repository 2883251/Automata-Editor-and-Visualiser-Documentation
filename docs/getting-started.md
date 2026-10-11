# Getting Started

Welcome! This guide will help you get up and running with the Automata Editor.

## Prerequisites

Before you begin, ensure you have:

- A modern web browser (Chrome, Firefox, Safari, or Edge)
- Basic understanding of Turing Machines and formal language concepts
- (Optional) Git installed if you want to contribute to the project

## Installation & Setup

### Accessing the Application

The Automata Editor is a web-based application. Open it in your browser:

**<https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net>**

Nothing needs to be installed, and you do not need an account to build and run machines. Signed out, your machines are kept in that browser. **Sign in** to keep them in your account, share them, and work on one together.

### For Development Setup

If you're contributing to the project, follow the [Development Guide](Development%20Guide/index.md) for comprehensive setup instructions including:

- Cloning repositories from Gitea
- Installing dependencies
- Running the development server
- Configuring the backend database — MongoDB (`MONGODB_URI`) is optional to boot, but machine persistence and sharing require it
- Configuring authentication

## Creating Your First Turing Machine

1. **Create a Machine** — On the home page, click **New machine** and give it a name. Or open one of the example machines to start from it
2. **Configure Alphabet** — Open **Machine** from the activity bar on the right edge, and set the input and tape alphabets (and the number of tapes, if you need more than one)
3. **Add States** — Double-click empty space on the diagram, type a name, and press Enter. The **S**, **A** and **R** buttons under a state mark it as the start, an accepting, or a rejecting state
4. **Define Transitions** — Drag from the green dot on one state to another state (or back to the same one for a loop), fill in what it reads, writes and how the head moves, and press **Save transition**. Or type the rule in the Instruction Editor: the diagram follows
5. **Save It** — Press **Save** (or Ctrl+S). Until you do, **Revert to last save** can undo your changes ([Machine Management](Features/machine-management.md))
6. **Run It** — Type an input in the **Simulation** panel along the bottom and press Run; pause, step, and reset stay available, and a long run pauses itself at a checkpoint ([Simulation & Playback](Features/simulation.md))
7. **Prove It** — In the **Test cases** view of the side bar, add test cases with expected outcomes and check them all. The status bar along the bottom sums up the results. Then plot time and space against input length ([Test Cases](Features/test-cases.md))
8. **Share the Result** — Export the diagram as an image or the instructions as a table ([Export](Features/export.md)), or share the machine itself with someone by email ([Sharing](Features/sharing.md))

## Key Concepts

### Visual Editor vs. Code Editor

The Automata Editor provides two synchronized views:

- **Visual Editor**: The state diagram, edited directly (on the left, to start with)
- **Code Editor**: Text-based machine definition (on the right, to start with)

Both views represent the same machine. Changes in one automatically update the other. If the instructions have an error, the diagram fades and is locked until it is fixed; the error is underlined and counted in the status bar. The switch at the top right of the editors shows them side by side, stacked, or one at a time. See [UI Design](ui-design.md) for the whole layout.

### Computation Visualization

Run your machine in the **Simulation** panel, along the bottom of the editor, to:

- Watch the tape update and scroll as the head moves
- Track the current state and the active transition on the diagram
- See the step counter and the count of distinct tape cells visited
- Distinguish *accepted*, *rejected*, and *stuck* outcomes (see [Simulation & Playback](Features/simulation.md))

## Help Inside the App

The **question mark** in the navigation bar opens **Help**. It has seven short topics: Getting started, Diagram, Instructions, Run and debug, Test, Save and share, and API keys. The Instructions topic has five worked examples to copy, one for each kind of machine, each with inputs to try.

## Next Steps

- **Features:** See what the editor can do in the [Features Overview](Features/index.md)
- **Architecture:** Explore the [Technical Architecture](Technical%20Architecture/index.md)
- **Marking from a script:** See [Using the API](API%20Documentation/using-the-api.md)
- **API reference:** See [API Documentation](API%20Documentation/index.md)
- **Contributing:** Read the [Development Guide](Development%20Guide/index.md)

## Need Help?

- **Terminology?** See the [Glossary](glossary.md)
- **Want to contribute?** Read the [Contribution Guidelines](Development%20Guide/contribution-guidelines.md)
- **Looking for API details?** Visit the [API Documentation](API%20Documentation/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto], Claude Code [Claude Opus 5.5].
