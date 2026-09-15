# Getting Started

Welcome! This guide will help you get up and running with the Automata Editor.

## Prerequisites

Before you begin, ensure you have:

- A modern web browser (Chrome, Firefox, Safari, or Edge)
- Basic understanding of Turing Machines and formal language concepts
- (Optional) Git installed if you want to contribute to the project

## Installation & Setup

### Accessing the Application

The Automata Editor is a web-based application. Simply navigate to the deployed application URL in your browser. No installation required for users.

### For Development Setup

If you're contributing to the project, follow the [Development Guide](Development%20Guide/index.md) for comprehensive setup instructions including:

- Cloning repositories from Gitea
- Installing dependencies
- Running the development server
- Configuring the backend database — MongoDB (`MONGODB_URI`) is optional to boot, but machine persistence and sharing require it
- Configuring authentication

## Creating Your First Turing Machine

1. **Create a Machine** — Click "New Machine" from the machines list to start
2. **Configure Alphabet** — Define the input and tape alphabets
3. **Add States** — Use the visual editor to add states (drag on the canvas)
4. **Define Transitions** — Draw arrows between states and specify transition rules
5. **Save It** — Save the machine so you can rename, reopen, or delete it later ([Machine Management](Features/machine-management.md))
6. **Run It** — Type an input in the simulation pane and press Run; pause, step, and reset stay available, and a long run pauses itself at a checkpoint ([Simulation & Playback](Features/simulation.md))
7. **Prove It** — Add test cases with expected outcomes and run the suite, then plot time and space against input length ([Test Cases](Features/test-cases.md))
8. **Share the Result** — Export the diagram as an image or the instructions as a table ([Export](Features/export.md))

## Key Concepts

### Visual Editor vs. Code Editor

The Automata Editor provides two synchronized views:

- **Visual Editor**: Drag-and-drop state diagram editor (left panel)
- **Code Editor**: Text-based machine definition (right panel)

Both views represent the same machine. Changes in one automatically update the other.

### Computation Visualization

Run your machine in the **simulation pane** to:

- Watch the tape update and scroll as the head moves
- Track the current state and the active transition on the diagram
- See the step counter and the count of distinct tape cells visited
- Distinguish *accepted*, *rejected*, and *stuck* outcomes (see [Simulation & Playback](Features/simulation.md))

## Next Steps

- **Features:** See what the editor can do in the [Features Overview](Features/index.md)
- **Architecture:** Explore the [Technical Architecture](Technical%20Architecture/index.md)
- **API reference:** See [API Documentation](API%20Documentation/index.md)
- **Contributing:** Read the [Development Guide](Development%20Guide/index.md)

## Need Help?

- **Terminology?** See the [Glossary](glossary.md)
- **Want to contribute?** Read the [Contribution Guidelines](Development%20Guide/contribution-guidelines.md)
- **Looking for API details?** Visit the [API Documentation](API%20Documentation/index.md)

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder IDE [auto].
