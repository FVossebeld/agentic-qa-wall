# Agentic Q&A wall

A live, anonymous Q&A wall for a conference session, built with agentic engineering:

1. **Rules first.** The house rules in `.github/copilot-instructions.md`: EU only, no personal data, tests required, accessible.
2. **Context and brainstorm.** Ideas come from the meeting invite (Microsoft 365 via WorkIQ) and are shaped together with Copilot using the [superpowers](https://github.com/obra/superpowers) brainstorming skill.
3. **Spec and plan before code.** The design goes to `docs/superpowers/specs/` and the implementation plan to `docs/superpowers/plans/`.
4. **Agents implement, humans decide.** The GitHub Copilot coding agent implements the plan in a pull request. CI and Copilot code review run on every PR, and a human merges.
5. **Paved road.** Merging to `main` deploys to Azure Container Apps in Sweden Central.
