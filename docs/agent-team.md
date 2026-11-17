# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate the custom agent team building Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the Planner, Coder, and Designer; assigns file scopes and phases, manages dependencies and parallel work, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and relevant documentation, then produces an implementation plan with steps, file assignments, dependencies, edge cases, and validation expectations. Does not write code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements assigned application logic and runnable-app support, following repository patterns and validating changes. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Shapes the dashboard's user experience, visual design, accessibility, information hierarchy, interaction flow, and responsive behavior. | `.github/agents/designer.agent.md` |
