# Sentinel: The Disciplined AI Development System

**Sentinel** is a ruleset and execution system that transforms AI coding tools into disciplined, test-driven collaborators. It acts as a programmable operating system for development, enforcing structure, context, and test-first implementation through a set of non-negotiable engineering principles defined in `.cursorrules`.

> Sentinel doesn't just assist you. It governs your workflow, ensuring quality and consistency.

---

## Why Sentinel?

Modern AI tools are powerful code generators, but often lack context, violate architectural boundaries, and bypass essential engineering practices. This leads to technical debt, rework, and systems that are hard to maintain.

**Sentinel fixes this.** It makes AI *obey your engineering system*—not undermine it. Key benefits include:

-   **Strict TDD Enforcement**: No code is written before a failing test exists and passes. The Red-Green-Refactor cycle is mandatory.
-   **Git Flow Discipline**: Enforces proper branch creation, merging, and commit hygiene for every task.
-   **Architectural Compliance**: Code must align with defined boundaries (`docs/architecture.mermaid`) and quality standards (`CODE_QUALITY`).
-   **Task-Driven Flow**: Every action is linked to a clearly defined task in `tasks/tasks.md`.
-   **Explicit Modes**: Operates in distinct modes (`PLANNER_MODE`, `TDD_ENFORCEMENT`, `ARCHITECTURE_MODE`, etc.) for clarity.
-   **Process Violation Logging**: Deviations aren't ignored—they're logged with timestamps (`docs/log.md`, `docs/status.md`) and require resolution.
-   **Context Awareness**: Leverages project documentation (`docs/*`) to maintain context and ensure alignment.
-   **Automated Quality Gates**: Enforces checks like passing tests, linting, code review, and tech debt analysis before task completion.

---

[![Watch the video](https://img.youtube.com/vi/Pu5OOsZK-D0/0.jpg)](https://www.youtube.com/watch?v=Pu5OOsZK-D0)

# Quick Start (Cursor)

Want to start a new project with Sentinel in Cursor? Just do this:

1. **Copy the rules file:**
   - Download or copy `RULES.md` from this repo into your new project directory as `.cursorrules`.

2. **Open Cursor in your project directory.**

3. **Trigger onboarding:**
   - Type: "I want to build [YOUR PROJECT IDEA]" in the Cursor chat.
   - Sentinel will enter onboarding mode, ask clarifying questions, and generate all required docs (`docs/PRD.md`, `docs/technical.md`, `docs/architecture.mermaid`, `docs/unit_testing_guideline.md`, `tasks/tasks.md`).

4. **Review and approve the generated docs.**

5. **You're ready to plan and build—Sentinel will guide you step by step!**

## Setup

> **Already using Cursor? See the Quick Start above for the fastest way to begin!**

1.  **Install Your AI Tool**: Sentinel works with tools like Cursor, Winsurf, etc. (Install your preferred tool).
2.  **Clone Sentinel (or Copy Rules)**:
    ```bash    
    git clone git@github.com:dominno/sentinel.git
    cp sentinel/RULES.md YOUR_PROJECT/.cursorrules # Or .winsurfrules, etc.
    ```
3.  **Create Required Project Files & Directories**: Sentinel relies on a specific structure.
    ```bash
    mkdir -p docs tasks src docs/research # Add other source dirs as needed
    touch \
      docs/PRD.md \
      docs/technical.md \
      docs/architecture.mermaid \
      docs/unit_testing_guideline.md      
    ```
4.  **Define Test + Lint Commands**: Add your project's specific test and lint commands to `docs/unit_testing_guideline.md`. Sentinel will not run without these commands defined.
5.  **Install and Configure MCP Time Server**: Sentinel uses a Model Context Protocol (MCP) time server for consistent timestamps in logs.
    *   Install the server: Follow instructions at [https://github.com/modelcontextprotocol/servers/tree/main/src/time](https://github.com/modelcontextprotocol/servers/tree/main/src/time)
    *   Configure your AI tool (Cursor, Winsurf, etc.) to use it. For Cursor, you would update `~/.cursor/mcp.json` like this:
        ```json
        {
          "mcpServers": {
            "time": {
              "command": "uvx",
              "args": ["mcp-server-time", "--local-timezone=Europe/Warsaw"]
            }
          }
        }
        ```
6.  **Populate Initial Docs**: Fill in `docs/PRD.md`, `docs/technical.md`, `docs/architecture.mermaid`, and `docs/unit_testing_guideline.md` with your project's specifics.

# What should each file contain?

- **docs/PRD.md**: Product Requirements Document. Should include Product Vision, Goals & Success Criteria, User Personas/Stakeholders, User Flow (step-by-step or diagram of what the user sees and can do), User Stories/Use Cases, Features & Requirements, Out of Scope, Constraints & Assumptions, Acceptance Criteria, and Metrics/KPIs.
- **docs/technical.md**: Technical guidelines and engineering standards. Should specify design patterns, architectural constraints, code quality principles (SOLID, DRY, KISS), and any technology-specific requirements or recommendations.
- **docs/architecture.mermaid**: System boundaries and data flows, expressed as a Mermaid diagram. Should show modules, their relationships, and how data moves through the system.
- **docs/unit_testing_guideline.md**: How to run tests, what test/lint commands to use, and expectations for test coverage and style (e.g., TDD, edge cases, error handling).

> **Not sure how to create these files?**
> Use the Quick Start onboarding above! Just copy the rules file, open Cursor, and describe your project idea. Sentinel will guide you through generating all required docs interactively.

---

## File Structure

Sentinel relies on these key files for context and governance:

| File                             | Role                                                                                  |
| :------------------------------- | :------------------------------------------------------------------------------------ |
| `docs/PRD.md`                    | Product reasoning — the "why"                                                         |
| `docs/technical.md`              | Constraints and engineering standards                                                 |
| `docs/architecture.mermaid`      | System boundaries and data flows                                                      |
| `docs/unit_testing_guideline.md` | TDD style and expectations                                                            |
| `docs/status.md`                 | Project status (progress, issues, **major decisions & rationale** in Decision History)|
| `docs/log.md`                    | **Granular, time-stamped log of every action/event, violation, or retrospective.**    |
| `tasks/tasks.md`                 | All implementation plans (checklists). Task entries include: Task ID, title, description, explicit statuses (`Planned | In Progress | Blocked | Done`), priority, PRD ref, dependencies, acceptance criteria, edge cases, and optional complexity estimate (1-10). |
| `tasks/[TASK-ID].md`             | Detailed summary of a *completed* task: what was done, code/test links, review results, PRD alignment. |
| `docs/research/[TASK-ID].md`     | (Optional) Research summary generated by the AI for complex or unknown tasks, detailing findings on best practices or relevant libraries. |
| `.cursorrules`/`.winsurfrules`    | The core Sentinel ruleset                                                             |

*(Note: Decision History in status.md is strictly for major decisions; log.md captures all other events.)*

---

## Workflow & Usage

Interact with your AI assistant using specific prompts that trigger Sentinel's modes and enforcement.

**Mandatory Steps Before Starting Any Task:**
1.  **Context Restore**: Load all required docs (`status.md`, `tasks.md`, etc.).
2.  **Dependency Violation Check**: Before any other setup, Sentinel checks if all listed dependencies for the task in `tasks/tasks.md` have a status of `Done`. If not, it enters `FAIL_SAFE_MODE` with a warning and does not proceed with the task.
3.  **Git Flow**: Checkout `develop` and create/switch to a new feature branch (`feature/[task-id]-[slug]`).
4.  **Confirm Test/Lint Commands**: Ensure they are defined.
5.  **No Work Before Branch & Checks**: Absolutely no code, directories, or artifacts are created before the correct branch exists and dependency checks have passed.

**Starting a New Feature (Defined in `tasks/tasks.md`)**:

Use the `FEATURE_WORK_PROMPT_TEMPLATE` defined in `.cursorrules`. Expand it fully:

```text
Work on feature [FEATURE NAME or TASK-ID] following these non-negotiable rules:

**Context & Setup:**
- Restore full context before any work (status.md, tasks.md, technical.md, architecture.mermaid, unit_testing_guideline.md)
- If context cannot be restored, STOP and enter FAIL_SAFE_MODE immediately
- Create and switch to a new feature branch from develop before any code creation
- Confirm test command and linting rules are defined

**TDD Workflow - Strict Test-First:**
- For EACH checklist item, follow the complete TDD loop:
  1. Write ONE failing test BEFORE implementation code (except for project structure tasks)
  2. Run the test IMMEDIATELY to verify it fails for the expected reason
  3. Write MINIMAL code to pass the test
  4. Run the test again to verify it passes
  5. Refactor while keeping tests passing
  6. Run all tests before moving to next item
- NEVER create multiple test files in sequence without running tests between them
- If TDD order is violated, STOP immediately and log as process violation

**Code Quality Standards:**
- Follow SOLID, KISS, DRY principles
- Avoid magic numbers, use constants/configs
- Extract shared logic, keep functions < 30 LOC
- Maintain strict module boundaries per architecture.mermaid
- Document public methods with JSDoc

**Autonomous Decision Making:**
- Proceed WITHOUT asking when implementing clear checklist items, writing tests, or making small refactors
- STOP and ASK when instructions are unclear, multiple interpretations exist, or architectural boundaries might be crossed
- Act with speed when clear, ask when uncertain

**Task Completion:**
- Run full test suite before marking complete
- Explicitly review all DEFINITION_OF_DONE criteria
- Perform tech debt check and log any findings
- Update status.md and tasks.md with canonical timestamps
- Merge feature branch to develop after all checks pass

Log all work meticulously and STOP immediately if any rule is violated.
```

Just use 

```
Work on [FEATURE NAME or TASK-ID] use FEATURE_WORK_PROMPT_TEMPLATE
```

**Requesting a New Feature (Not in `tasks/tasks.md`)**:

```text
"I have an idea: users should reset passwords via email"
```

Sentinel will:
1. Enter `PLANNER_MODE`.
2. Load relevant PRD/architecture.
3. Ask clarifying questions if needed.
4. Generate a task shell in `tasks/tasks.md` following the template and `CODE_QUALITY` principles.
5. **Ask for your approval** before proceeding.
6. Once approved, use the `FEATURE_WORK_PROMPT_TEMPLATE` to start implementation.

**Mandatory Steps Before Marking Any Task Complete:**
1.  **All Tests Pass**: The full test suite runs successfully.
2.  **Checklist/Criteria Met**: All items in `tasks/tasks.md` are complete.
3.  **Code Quality Checks**: Code is linted, formatted, documented.
4.  **Reviews Performed**: `CODE_REVIEWER_MODE` and `TECH_DEBT_REFACTOR` completed, results logged.
5.  **Git Flow**: Feature branch is merged into `develop`.
6.  **Logs/Status Updated**: `status.md`, `log.md`, and `tasks/tasks.md` reflect completion with canonical timestamps.
7.  **Branch Discipline Verified**: Task was started and completed on its dedicated feature branch.

---

**After Task Completion:**

Once a task fully meets its `DEFINITION_OF_DONE` (including merge to develop and final log/status updates):

1.  **Task Completion Summary Generation**:
    *   Sentinel automatically generates a detailed Markdown summary for the completed task.
    *   This summary is saved to `tasks/[COMPLETED_TASK_ID].md`.
    *   Contents include: What was done, PRD alignment, links/paths to implemented code and written tests, final review results (from `CODE_REVIEWER_MODE` and `TECH_DEBT_REFACTOR`), and a link to the main log entry.
    *   AI confirms: "Task summary created at `tasks/[COMPLETED_TASK_ID].md`."

2.  **Next Task Suggestion Logic**:
    *   After summary generation, Sentinel parses `tasks/tasks.md` to identify the next high-priority, unblocked task (Status: `Planned`, all dependencies `Done`).
    *   It prompts: "✅ Task [Previous TASK-ID] completed. Next up is [TASK-ID]: [Title] (Priority: [Priority]). Proceed?"
    *   If confirmed, Sentinel initiates the new task (Git Flow, TDD loop).
    *   If declined or no suitable task is found, Sentinel awaits further user instruction.

---

## Creating Tasks from Documentation

Sentinel can help you break down high-level ideas from your `docs/PRD.md`, `docs/technical.md`, and `docs/architecture.mermaid` into actionable tasks in `tasks/tasks.md`. This is typically a two-stage process involving `ARCHITECTURE_MODE` (for larger features) and `PLANNER_MODE`.

**1. High-Level Design with `ARCHITECTURE_MODE` (Optional, for complex features)**

If you're introducing a significant new feature or module that might impact your system's architecture:

*   **Invoke `ARCHITECTURE_MODE`**:
    ```text
    "Design the new [Feature/Module Name] based on docs/PRD.md and docs/technical.md"
    ```
*   **Process**:
    *   Sentinel will load `docs/architecture.mermaid` and other relevant documents.
    *   It will ask clarifying questions about scale, data flow, and constraints.
    *   It will propose design options and, upon approval, update `docs/architecture.mermaid` and outline major components.
*   **Outcome**: A clearer architectural vision and potential high-level epics or areas of work. This output then serves as a more refined input for `PLANNER_MODE`.

**2. Detailed Task Scaffolding with `PLANNER_MODE`**

Once you have a clear idea (either directly from your PRD or refined through `ARCHITECTURE_MODE`), use `PLANNER_MODE` to generate detailed, actionable tasks:

*   **Invoke `PLANNER_MODE`**:
    ```text
    "Create tasks for [Feature/User Story description] based on docs/PRD.md, docs/technical.md, and the updated docs/architecture.mermaid. Ensure tasks align with docs/unit_testing_guideline.md."
    ```
    Or, if a task doesn't exist when you try to work on it:
    ```text
    "Work on [Non-existent Task Name/ID]" // Sentinel will automatically switch to PLANNER_MODE
    ```
*   **Process**:
    *   Sentinel enters `PLANNER_MODE`.
    *   It loads the specified documents (`docs/PRD.md`, `docs/technical.md`, `docs/architecture.mermaid`, `docs/unit_testing_guideline.md`).
    *   (AI) **Complexity & Unknowns Analysis**: The AI estimates the complexity of the requested feature/task on a 1-10 scale. If complexity is high (>7) or if the task involves significant "unknowns" (either AI-inferred or user-flagged), Sentinel will:
        *   Prompt: "Should I research best practices or relevant libraries first?"
        *   If research is approved, the AI uses web search, synthesizes findings, and saves a summary to `docs/research/[TASK-ID].md`. It then asks for user direction on how to proceed with planning based on this research.
    *   (AI) **Task Structuring & Expansion**: Based on the initial request and any research, the AI generates a task shell in `tasks/tasks.md`. 
        *   If a task's complexity remains high (>7) even after potential research, the AI will suggest invoking the `TASK_EXPANSION_PROTOCOL`. If the user agrees, the complex task is broken down into smaller, numbered sub-tasks (e.g., `[PARENT_TASK_ID].1`, `[PARENT_TASK_ID].2`), and the original parent task is updated in `tasks/tasks.md` to `Status: Epic`, with its implementation plan listing the new sub-task IDs.
    *   It may ask further clarifying questions to ensure the generated tasks are well-defined.
    *   It generates a task shell in `tasks/tasks.md` for each identified sub-task, including:
        *   Task ID, title, status, priority.
        *   PRD reference and architectural module.
        *   A step-by-step TDD-based checklist.
        *   Acceptance criteria.
        *   Potential edge cases.
    *   Sentinel will then **ask for your approval** for the generated task(s).
*   **Outcome**: Ready-to-implement tasks in `tasks/tasks.md`, complete with checklists and acceptance criteria.

Once tasks are approved and exist in `tasks/tasks.md`, you can begin implementation using the strict `TDD_ENFORCEMENT` mode by prompting:
```text
"Work on [TASK-ID] use FEATURE_WORK_PROMPT_TEMPLATE"
```

---

## AI Autonomy & Guardrails

Sentinel trusts the AI assistant to act autonomously **only when instructions are clear and unambiguous**. The AI should:

-   **Proceed Without Asking**: For clear checklist items, writing tests for known behavior, refactoring passing code (within the immediate scope of current work), updating logs/status.
-   **STOP and ASK**: 
    - If instructions are unclear, multiple interpretations exist, or the PRD/plan is missing.
    - If a proposed change **might violate architecture** (AI will state: "This proposed change might violate [specific architectural principle/boundary in @{docs/architecture.mermaid}]. How should I proceed?").
    - If a proposed change is a **structural refactor affecting multiple modules/files** (AI will state potential impact, e.g., "This proposed change is a structural refactor that is estimated to affect [N] modules and/or approximately [M] files... This type of refactor should typically be its own dedicated task or part of a `TECH_DEBT_REFACTOR` cycle. Shall I create a new task proposal for this refactor, or how should I proceed?").
    - If **deleting or replacing significant existing logic** (AI will confirm: "This will delete/replace substantial existing logic for [feature/module]. Please confirm.").
-   **Default to Caution**: Act with speed when clear, ask when uncertain.
-   **Fail-Safe**: Enter `FAIL_SAFE_MODE` if context cannot be restored or ambiguity cannot be resolved.

---

## Continuous Improvement

Sentinel includes mechanisms for learning and process refinement:

-   **Retrospectives**: After each completed task, a brief retrospective (`What went well? What broke? What to change?`) is appended to `docs/log.md`.
-   **Root Cause Analysis**: If the same process violation occurs repeatedly, Sentinel mandates a root cause analysis to identify and implement a permanent fix.
-   **Challenge Comfort Zone**: If a task or test seems too simple, Sentinel requires proposing at least one additional edge case, security scenario, or refactor to ensure thoroughness.

### Rule Evolution with `RULE_MAINTENANCE_MODE`

To ensure the ruleset itself remains effective and adapts to the project's needs, Sentinel includes `RULE_MAINTENANCE_MODE`:

*   **Purpose**: Provides a structured feedback loop for iteratively refining `RULES.md` and any associated `.cursor/rules/*.mdc` files.
*   **Triggers**: This mode can be:
    *   Manually invoked by the user.
    *   Automatically proposed by the AI after a set number of completed tasks.
    *   Automatically proposed after a threshold of identical process violations is logged.
    *   Automatically proposed if a `TECH_DEBT_REFACTOR` cycle identifies inefficiencies linked to rule ambiguity or impracticality.
*   **Workflow**:
    1.  **AI Data Collection & Analysis**: The AI parses `docs/log.md`, `docs/status.md`, and `tasks/tasks.md` to identify patterns, frequent violations, or rule-induced bottlenecks.
    2.  **AI Hypothesis & Proposal**: Based on its analysis, the AI generates 1-3 targeted proposals for rule changes (modifications, additions, clarifications, or deletions), including the evidence and expected positive impact.
    3.  **User Review & Decision**: The user reviews the AI's proposals and can approve, modify, or reject them with reasoning.
    4.  **Implementation & Logging**: Approved changes are implemented in the relevant rule files (e.g., `RULES.md` or `.cursor/rules/custom.mdc`). All changes are meticulously logged in `docs/log.md` and a dedicated rule changelog.
*   **Goal**: To enhance rule clarity, effectiveness, and efficiency, while actively preventing ruleset bloat and ensuring the rules facilitate, rather than hinder, high-quality development.

---

## Modes Explained

Sentinel operates in strict modes. The current mode is always announced.

| Mode                 | Description                                                           | Trigger                                                              |
| :------------------- | :-------------------------------------------------------------------- | :------------------------------------------------------------------- |
| `TDD_ENFORCEMENT`    | Implements feature using strict test-first flow (Test-Code-Refactor)  | Feature prompt for existing task                                     |
| `PLANNER_MODE`       | Scaffolds new tasks from idea or PRD. Includes AI-driven complexity estimation (1-10), prompts for research on complex/unknown tasks (output to `docs/research/`), and can invoke `TASK_EXPANSION_PROTOCOL` to break down large tasks into sub-tasks, with parent becoming an `Epic`. | Feature prompt for non-existing task                                 |
| `DEBUGGER_MODE`      | Investigates and resolves bugs/regressions/repeated errors            | Test failures, unexpected errors                                     |
| `ARCHITECTURE_MODE`  | Designs or validates new systems/modules                              | Request for new system/module design or architectural change         |
| `CODE_REVIEWER_MODE` | Reviews code against quality standards with constructive feedback     | Request for review, post-task completion                             |
| `TECH_DEBT_REFACTOR` | Identifies, logs, and prioritizes technical debt                      | Post-task completion, identification of refactor opportunity       |
| `CONTEXT_RESTORE`    | Loads required documentation before starting any major action         | Start of any task/mode, after context loss                           |
| `FAIL_SAFE_MODE`     | Stops execution if anything is unclear, ambiguous, or context missing | Missing context, unclear instructions, rule ambiguity                |
| `RULE_MAINTENANCE_MODE` | Analyzes system performance against rules and proposes improvements to the ruleset itself. | Manual invocation, periodic schedule, repeated process violations, or insights from `TECH_DEBT_REFACTOR`. |
| `RESEARCH_MODE`      | Allows user-initiated research for any task. AI uses web search, synthesizes findings, and saves a summary to `docs/research/[TASK-ID].md`. | Explicit user command, e.g., "Enter `RESEARCH_MODE` for [TASK-ID]" or "Research [topic] for [TASK-ID]". |

---

## Definition of Done (Non-Negotiable)

A task is only complete when **ALL** these criteria are met and verified:

-   [ ] All tests have been run and pass after the final implementation step.
-   [ ] All acceptance criteria from `tasks/tasks.md` are explicitly met.
-   [ ] All documented edge cases are handled.
-   [ ] Relevant documentation and code comments are up to date.
-   [ ] Code is linted, formatted, and passes all static analysis checks.
-   [ ] Peer review or self-review completed (via `CODE_REVIEWER_MODE`).
-   [ ] Tech debt check completed and results logged (via `TECH_DEBT_REFACTOR`).
-   [ ] Feature branch has been merged into `develop`.
-   [ ] Task was started and completed on its dedicated feature branch.
-   [ ] `status.md` and `log.md` updated with completion status and canonical timestamp.

*(Sentinel may use a checklist format like ✅ / ❌ / STILL NEED TO CHECK during this verification)*


## Who Should Use Sentinel?

Sentinel is designed for:

*   Engineering teams scaling AI-driven development while maintaining quality.
*   Founders and leads enforcing consistent engineering practices.
*   Developers tired of context-free, low-quality AI code generation.
*   Anyone aiming to replace chaotic "code generation" with disciplined "code governance".

It is **not** ideal for:

*   Rapid prototyping where process is secondary.
*   Exploratory coding without clear goals.
*   Teams unwilling to adhere to strict process discipline.

## Examples in Action

See Sentinel used in real projects:

*   **[dominno/task-timer](https://github.com/dominno/task-timer)**: A simple CLI tool for time tracking built entirely using the Sentinel process with an AI assistant. 


## License

MIT — use, adapt, and enforce Sentinel in your own projects. Sentinel values discipline and quality. 

---

## Inspiration

We drew inspiration from the following resources:

-   [The Ultimate Guide to AI-Powered Development with Cursor: From Chaos to Clean Code](https://medium.com/@vrknetha/the-ultimate-guide-to-ai-powered-development-with-cursor-from-chaos-to-clean-code-fc679973bbc4)
-   [AgentDesk - Cursor Rules Prompt](https://www.agentdesk.ai/prompts/cursor-rules)



