# Design, Develop, and Deploy Multi-Agent Systems with CrewAI

Assignments completed for the [**Design, Develop, and Deploy Multi-Agent Systems with CrewAI**](https://www.deeplearning.ai/courses/design-develop-and-deploy-multi-agent-systems-with-crewai/) course by DeepLearning.AI, taught by **João Moura** — Co-founder and CEO of CrewAI.

This course covers the full lifecycle of production-ready multi-agent systems: from designing agent roles and task pipelines, to adding memory, guardrails, and observability, to orchestrating agents with CrewAI Flows.

---

## Assignments Overview

### A1 — Multi-Agent Automatic Code Review

**Objective:** Build a multi-agent crew that automatically reviews pull requests for code quality and security issues.

**What I built:**
- A three-agent crew composed of a **Senior Developer**, a **Security Engineer**, and a **Tech Lead**
- Each agent is assigned a specialized task: quality analysis, security review, and final review decision
- The Security Engineer is equipped with **SerperDevTool** (searching OWASP) and **ScrapeWebsiteTool** to retrieve real-time security best practices
- Agent outputs are structured as JSON with well-defined schemas (`critical_issues`, `security_vulnerabilities`, etc.)
- The Tech Lead synthesizes the findings from both agents using task **context chaining** and produces a final recommendation: approve, request changes, or escalate

**Skills demonstrated:** Agent design, task definition, tool integration, context chaining, crew orchestration

---

### A2 — Adding Functionalities to the Automatic Code Review Flow

**Objective:** Extend the crew from A1 with production-grade reliability features.

**What I built:**
- **Pydantic-based structured outputs** (`output_json`) to enforce consistent, parseable JSON from each agent
- Two custom **guardrails**:
  - `security_review_output_guardrail` — validates that risk levels are within accepted categories (`low`, `medium`, `high`) and that `highest_risk` correctly reflects the most severe finding
  - `review_decision_guardrail` — ensures the final decision includes an actionable keyword (`approve`, `request changes`, or `escalate`)
- A **before-kickoff execution hook** (`read_file_hook`) that reads the PR diff file and injects its contents into the crew's inputs before any agent begins
- **Crew memory** to allow agents to retain context across multiple pull request reviews, improving consistency over time
- YAML-based agent and task configuration for clean separation of prompts from code

**Skills demonstrated:** Output schemas, guardrails, execution hooks, crew memory, YAML configuration, structured validation

---

### A3 — Building an Automatic Code Review Flow

**Objective:** Integrate the crew into a **CrewAI Flow** that intelligently routes pull requests based on their complexity, adding parallelism and state management.

**What I built:**
- A `PRCodeReviewFlow` using CrewAI's `Flow` API with a typed `ReviewState` (Pydantic model) tracking PR content, errors, review results, token usage, and the final answer
- A **router** (`analyze_changes`) that uses an LLM call to classify the PR as `SIMPLE` or `COMPLEX`, avoiding unnecessary agent invocations for minor changes
- Two review paths:
  - **Simple path**: a direct LLM call producing a confidence score, findings, and recommendations
  - **Complex path**: the full `CodeReviewCrew` is deployed, with the `analyze_code_quality` and `review_security` tasks running **in parallel** (`async_execution=True`) for improved performance
- A `make_final_decision` step that consolidates the result of either path using `or_()` conditional logic
- **Flow persistence** (`@persist`) and **tracing** (`tracing=True`) for full observability, with flow state saved to `flow_state.json` for token usage analysis
- A new `summarize_findings` task replacing the previous `make_review_decision` task, with a structured Pydantic output including confidence score, key findings, required fixes, and recommendations
- The project is structured as a proper **CrewAI CLI project** (`crewai run`, `crewai flow plot`), generating an interactive HTML flow diagram

**Skills demonstrated:** CrewAI Flows, routing logic, parallel task execution, flow state management, persistence, tracing, CLI project structure, cost analysis

---

## Tech Stack

| Tool / Library | Purpose |
|---|---|
| [CrewAI](https://www.crewai.com/) | Multi-agent orchestration framework |
| [OpenAI GPT-4o-mini](https://openai.com/) | LLM powering all agents and direct LLM calls |
| [SerperDevTool](https://docs.crewai.com/en/tools/search-research/serperdevtool) | Web search scoped to OWASP |
| [ScrapeWebsiteTool](https://docs.crewai.com/en/tools/web-scraping/scrapewebsitetool) | Scraping security best-practice pages |
| [Pydantic](https://docs.pydantic.dev/) | Structured output validation |
| Python / Jupyter Notebooks | Development environment |

----

## Course

- **Course**: [Design, Develop, and Deploy Multi-Agent Systems with CrewAI](https://www.deeplearning.ai/courses/design-develop-and-deploy-multi-agent-systems-with-crewai/)
- **Platform**: DeepLearning.AI
- **Instructor**: João Moura (Co-founder & CEO, CrewAI)
