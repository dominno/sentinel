

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

## Setup

1.  **Install Your AI Tool**: Sentinel works with tools like Cursor, Winsurf, etc. (Install your preferred tool).
2.  **Clone Sentinel (or Copy Rules)**:
    ```bash    
    git clone git@github.com:dominno/sentinel.git
    cp sentinel/RULES.md YOUR_PROJECT/.cursorrules # Or .winsurfrules, etc.
    ```
3.  **Create Required Project Files & Directories**: Sentinel relies on a specific structure.
    ```bash
    mkdir -p docs tasks src # Add other source dirs as needed
    touch \
      docs/PRD.md \
      docs/technical.md \
      docs/architecture.mermaid \
      docs/unit_testing_guideline.md \
      docs/status.md \
      docs/log.md \
      tasks/tasks.md
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
| `tasks/tasks.md`                 | All implementation plans (checklists)                                                 |
| `.cursorrules`/`.winsurfrules`    | The core Sentinel ruleset                                                             |

*(Note: Decision History in status.md is strictly for major decisions; log.md captures all other events.)*

---

## Workflow & Usage

Interact with your AI assistant using specific prompts that trigger Sentinel's modes and enforcement.

**Mandatory Steps Before Starting Any Task:**
1.  **Context Restore**: Load all required docs (`status.md`, `tasks.md`, etc.).
2.  **Git Flow**: Checkout `develop` and create/switch to a new feature branch (`feature/[task-id]-[slug]`).
3.  **Confirm Test/Lint Commands**: Ensure they are defined.
4.  **No Work Before Branch**: Absolutely no code, directories, or artifacts are created before the correct branch exists.

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
    *   It may ask clarifying questions to ensure the generated tasks are well-defined.
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

-   **Proceed Without Asking**: For clear checklist items, writing tests for known behavior, refactoring passing code, updating logs/status.
-   **STOP and ASK**: If instructions are unclear, multiple interpretations exist, the PRD/plan is missing, architecture might be violated, or existing logic is being deleted/replaced.
-   **Default to Caution**: Act with speed when clear, ask when uncertain.
-   **Fail-Safe**: Enter `FAIL_SAFE_MODE` if context cannot be restored or ambiguity cannot be resolved.

---

## Continuous Improvement

Sentinel includes mechanisms for learning and process refinement:

-   **Retrospectives**: After each completed task, a brief retrospective (`What went well? What broke? What to change?`) is appended to `docs/log.md`.
-   **Root Cause Analysis**: If the same process violation occurs repeatedly, Sentinel mandates a root cause analysis to identify and implement a permanent fix.
-   **Challenge Comfort Zone**: If a task or test seems too simple, Sentinel requires proposing at least one additional edge case, security scenario, or refactor to ensure thoroughness.

---

## Modes Explained

Sentinel operates in strict modes. The current mode is always announced.

| Mode                 | Description                                                           | Trigger                                                              |
| :------------------- | :-------------------------------------------------------------------- | :------------------------------------------------------------------- |
| `TDD_ENFORCEMENT`    | Implements feature using strict test-first flow (Test-Code-Refactor)  | Feature prompt for existing task                                     |
| `PLANNER_MODE`       | Scaffolds new tasks from idea or PRD                                  | Feature prompt for non-existing task                                 |
| `DEBUGGER_MODE`      | Investigates and resolves bugs/regressions/repeated errors            | Test failures, unexpected errors                                     |
| `ARCHITECTURE_MODE`  | Designs or validates new systems/modules                              | Request for new system/module design or architectural change         |
| `CODE_REVIEWER_MODE` | Reviews code against quality standards with constructive feedback     | Request for review, post-task completion                             |
| `TECH_DEBT_REFACTOR` | Identifies, logs, and prioritizes technical debt                      | Post-task completion, identification of refactor opportunity       |
| `CONTEXT_RESTORE`    | Loads required documentation before starting any major action         | Start of any task/mode, after context loss                           |
| `FAIL_SAFE_MODE`     | Stops execution if anything is unclear, ambiguous, or context missing | Missing context, unclear instructions, rule ambiguity                |

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



