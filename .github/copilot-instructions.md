# Copilot instructions: Q&A Wall

## House rules (non-negotiable)
1. **EU only.** Everything runs in Azure Sweden Central. The browser loads nothing from other origins: no CDNs, web fonts, analytics or third-party scripts.
2. **No personal data.** Never ask for, store or log names, e-mail addresses, IP addresses or user agents. Participants are anonymous; a device is identified only by a random ID generated in the browser. All data lives in memory and is gone when the app restarts. Nothing is exported.
3. **Tests required.** Every requirement in the spec has at least one automated test (vitest + supertest). CI must be green. Code reaches `main` only through a pull request that a human merges. (Specs and plans in `docs/` may be pushed directly by the product owner.)
4. **Simple.** Express is the only runtime dependency. Vanilla HTML/CSS/JS: no frontend framework, no browser build step, no database.
5. **Accessible.** The light and dark themes meet WCAG 2.1 AA contrast. Everything works with keyboard and screen reader. With `prefers-reduced-motion`, nothing moves, scrolls or blinks.

## How we work
- Specs live in `docs/superpowers/specs/` and implementation plans in `docs/superpowers/plans/`.
- Implement the plan's tasks in order and tick each checkbox (`- [x]`) when the task is done. Build exactly what the spec says, nothing more.
- If the plan refers to skills you don't have (for example `superpowers:subagent-driven-development`), just execute the tasks yourself, in order.
- Write the test first, then the code.
- Do not change `.github/`. It is owned by the platform team.

## Platform contract (CI and deployment depend on it)
| What | Contract |
|---|---|
| Runtime | Node 22, TypeScript compiled with `tsc` to `dist/` |
| Scripts | `npm run build` compiles · `npm test` runs `vitest run` · `npm start` runs `node dist/server.js` |
| Port | Listen on `process.env.PORT`, default `8080` |
| Health | `GET /healthz` returns `200` with body `ok` |
| Config | `PRESENTER_KEY` (secret for presenter actions) · `EVENT_TITLE` (shown in the header) |
| Container | `Dockerfile` in the repo root: `node:22-alpine`, multi-stage, runs as user `node`, `EXPOSE 8080` |
| Static files | Served by Express from `public/` |
| Scale | Exactly one replica. State lives in memory. |

Merging to `main` deploys automatically to Azure Container Apps (Sweden Central).

## API conventions
- JSON over HTTP under `/api`.
- `POST /api/questions` with body `{ "text": string }` creates a question and returns `201` with the question as JSON.
- `GET /api/events` is a Server-Sent Events stream:
  - Send `Content-Type: text/event-stream` and `Cache-Control: no-cache`, and call `res.flushHeaders()` immediately.
  - Every event carries the changed data as JSON, so clients never need a follow-up request. The first event after connecting contains the full current state.
  - Send a comment line `: ping` every 25 seconds.
  - The platform ingress closes every HTTP request after 240 seconds. Browsers rely on `EventSource` auto-reconnect and rebuild their state from the first event.

## Layout
`src/` server · `public/` browser · `tests/` vitest + supertest
