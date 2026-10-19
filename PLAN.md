# REST API Endpoints Adventure — Plan

## 1. Goal
Help a student learn to choose correct REST endpoints given a frontend/backend need.
Player acts as junior dev at fictional `ShipFast`, fixing API calls ticket-by-ticket.

Stack: **single self-contained `index.html`** (vanilla HTML/CSS/JS, no build, no deps).
Progress persisted in `localStorage`.

## 2. Core Loop
```
Ticket (need) → Build request [Method + Path + Query/Body] → Send →
Mock API response (status + JSON) → Pass/Fail + teaching explanation → Next ticket
```

## 3. Request Builder UI
Three panels in one screen:
- **Left: Ticket card** — from (frontend/backend), story, concrete task, live `fetch()` snippet, Hints button (costs 10 XP).
- **Center: Builder**
  - Method buttons: GET / POST / PUT / PATCH / DELETE
  - URL bar: fixed `https://api.shipfast.dev` + editable path input
  - Query-param dynamic rows (`+ Add param`), disabled hint when not needed
  - JSON body textarea (enabled mainly for POST/PUT/PATCH)
  - Live `fetch` preview + Send / Reset buttons
- **Right: Console** — faked latency spinner, then colored status + JSON response
- **Feedback bar** — success/fail, why, REST rule, Next button

Validation normalizes: uppercase method, trim, collapse `//`, strip trailing `/`, case-insensitive path compare. Multiple accepted paths allowed.

## 4. Scope: Basics + Intermediate
- Resources: plural nouns (`/users` not `/getUsers`, no verbs in URL)
- Methods: GET collection, GET one, POST create, PUT full-replace, PATCH partial, DELETE
- Nesting: `/users/42/orders`, `/users/42/orders/7`
- Query: filtering `?status=shipped`, pagination `?limit=10&page=2`
- Idempotency: PUT retry-safe vs POST
- Status codes: 200 OK, 201 Created, 204 No Content, 400 Bad Request, 404 Not Found

## 5. Content: 5 Chapters, 20 Scenarios
1. **Resources & Methods** — list users, get one, create user, fix `/getUsers` → `GET /users`
2. **Nesting + Query** — nested orders, single nested order, `?status=shipped`, `?limit=10&page=2`
3. **Updates** — PUT full replace, PATCH partial, DELETE → 204, idempotent PUT retry
4. **Status Codes & Errors** — POST → 201, DELETE → 204, fix singular `/user/42` 404, fix 400 missing body
5. **Boss Run: Todos CRUD** — GET /todos → POST /todos → PATCH /todos/5 → DELETE /todos/5

Scenario schema:
```js
{id, chapter, title, from, story, task, snippet,
 method: "GET", paths: ["/users/42"], query: {status:"shipped"},
 bodyKeys: ["name","email"], bodyExample: {...},
 successStatus: 200, successBody: {...},
 explanation, hints: [...]}
```

## 6. Adventure / Progression (mastery through repetition)
- Linear unlock: master ticket N (solve it **MASTERY=2 times**) to unlock N+1. Chapter map sidebar shows ★★/★☆/○ per ticket.
- Attempt 1 uses authored IDs; attempt 2 is a **new requirement**: tickets with IDs get fresh random IDs (e.g. `/users/42` → `/users/17`, text rewritten to match); the 8 ID-less tickets have hand-written alternates (new resource / values / body fields, e.g. list products instead of users, `limit=5&page=3` instead of `10&2`). Ticket, snippet, hints, and mock responses all update.
- "🔁 Drill again" button on mastered tickets re-runs round 2 with fresh IDs for extra reps.
- XP: +100 per successful round (+25 streak bonus at 3+ streak), −5 wrong, −10 hint. Replays after mastery worth +10.
- Streak counter, star progress bar (total ★ / 40), completion = 20/20 mastered.
- Every verdict auto-scrolls into view. A **Next Task** button appears both in the feedback panel and inside the Mock API Console on success (round pass → next round, mastered → next ticket, last → finish); every navigation hard-refreshes the workspace.
- Old saves auto-migrate: previously completed tickets count as fully mastered.
- Mock server: 600ms delay, returns scenario `successBody` on pass, realistic error JSON on fail

## 7. Implementation
- Single file: `<style>` dark game theme, responsive + mobile stacking, `<script>` with:
  - `SCENARIOS[]`, `loadState/saveState`, `renderMap/renderScenario/renderProgress`
  - `normalizePath()`, `collectInput()`, `validate()` (method → path → query → body checks in order for targeted hints)
  - `mockSend()`, `showHint()`, keyboard: `Ctrl+Enter` = Send
- Accessibility: labels, buttons, focus states, sufficient contrast, resizable text

## 8. Verification
- `python3 -m http.server` + open `index.html`
- Play through all 20: correct input passes, wrong method/path/query/body gives targeted error
- Check: trailing slash tolerance, verb-in-URL rejection, JSON body errors, localStorage persist, reset works
- MVP scope: 20 scenarios OK; can trim to 10 by taking first 2 per chapter without code changes.
