# Project Pulse dashboard implementation plan

**Target document:** `docs/project-pulse-plan.md`  
**Scope:** Plan only; no application files are modified by this plan.

## Summary

Build Mona's lightweight, static Project Pulse dashboard so contributors can scan projects, owners, status, recent activity, priority or risk, and a short summary. Use only the requested app files, with no build system or added dependencies. The page should load project data from JSON and display accessible, responsive project cards.

The launch configuration must run `python3 -m http.server 5500` with `app/` as its working directory and open `index.html`, not the server's directory listing.

## Ordered phases and file ownership

### 1. Confirm design and implementation contracts

- **Orchestrator:** Assign the file scopes below and make sure Designer and Coder agree on the CSS class hooks and JSON fields before implementation.
- **Designer:** Define the card layout, hierarchy, responsive behavior, accessibility approach, and styling hooks. No file edits in this phase.
- **Coder:** Confirm the page will load JSON from the local server; keep rendering logic inline in `app/index.html` because no JavaScript file is in scope.

**Dependency:** This contract precedes implementation so the markup, CSS, data, and launch configuration fit together.

### 2. Create independent assets in parallel

- **Designer — `app/styles.css` only:** Implement the visual design, including `.dashboard` and `.project-card`, status badges, clear priority treatment, readable spacing, contrast, rounded corners, and shadows. Include responsive layout and visible keyboard focus.
- **Coder — `app/project-data.json` only:** Create a top-level `projects` array. Every project must include `name`, `owner`, `status`, `recentActivity`, and `priority`. Add a short `summary` field as well to satisfy the brief's contributor-friendly summary goal; retain the five required fields.
- **Coder — `.vscode/launch.json` only:** Create strict JSON with a **Run Project Pulse Dashboard** configuration. Run `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`. Configure `serverReadyAction` to open `http://localhost:%s/index.html`; match the Python server's readiness output and use an external browser action.

**Parallel decision:** These files have separate owners and their core contracts are already known, so CSS, data, and launch setup can proceed in parallel. Designer owns the stylesheet; Coder must not edit it.

### 3. Implement the page after the contracts are ready

- **Coder — `app/index.html` only:** Create an accessible page titled exactly **Project Pulse**. Reference `styles.css` and `project-data.json`, provide the dashboard and card hooks agreed with Designer, and render each project's name, owner, status, recent activity, priority, and summary from the JSON.
- Load data with `fetch` from the served page. Use safe DOM text insertion rather than treating data as HTML. Include a helpful loading state and a visible error state if data cannot be loaded or parsed.

**Dependency:** The page depends on the JSON field contract and Designer's CSS hooks. It follows Phase 2 to avoid mismatches. Coder owns only the HTML at this stage.

### 4. Integrate and validate

- **Orchestrator:** Review all four implementation files together, check that ownership boundaries were respected, and report any remaining limitations.
- **Designer:** Confirm the rendered layout, responsiveness, contrast, and accessibility behavior.
- **Coder:** Run the launch configuration and fix implementation issues within the assigned files.

**Dependency:** Integration validation follows implementation; any fixes should stay with the owner of the affected file.

## Parallel and sequential work

- **Parallel:** In Phase 2, Designer can implement `app/styles.css` while Coder creates `app/project-data.json` and `.vscode/launch.json`.
- **Sequential:** Phase 1's design and data contracts come first. Coder implements `app/index.html` after those contracts exist. Integrated and runtime validation happens only after all four files are ready.
- **No overlapping ownership:** Designer owns `app/styles.css`; Coder owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. Orchestrator coordinates and reviews, but does not implement these files.

## Edge cases and risks

- A page opened directly with `file://` may fail to fetch JSON; use the configured local server.
- Handle missing, invalid, or empty project data without leaving a blank dashboard.
- Use consistent status and priority labels, but ensure unknown values remain readable and are not represented by color alone.
- Long project names, summaries, or activity text must wrap without breaking the card layout on narrow screens.
- Avoid inserting JSON values as HTML.
- Port `5500` may already be occupied; diagnose a failed launch rather than mistaking it for a page error.
- Python's readiness message can vary by version or address family. Verify the configured pattern triggers the browser, and adjust it if needed while retaining the required URL and `app/` working directory.
- The brief requires five JSON fields and also asks for a short summary; adding `summary` is an intentional additive field.

## Validation expectations

1. Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`.
2. Check that the HTML has the exact title, stylesheet and data references, `.dashboard` structure, `.project-card` elements, and visible project fields.
3. Check that CSS contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`; review keyboard focus, contrast, and narrow-screen layout.
4. In VS Code, run **Run Project Pulse Dashboard**. Confirm the browser opens `http://localhost:<port>/index.html`, shows dashboard content rather than a directory listing, and displays multiple cards from JSON. Check the browser console and exercise the JSON-load error state if practical; stop the server afterward.
5. The Step 3 workflow checks the required output files, key phrases, and JSON parsing. Treat those checks as structural validation, not a substitute for the live launch test.
6. `scripts/validate-exercise.sh` is primarily a starter-repository check, including checks that learner output files are not tracked in the template. Do not treat it as the app's runtime test.

## Open questions

None block implementation. The brief does not prescribe project examples, status or priority vocabularies, or a specific visual theme; choose a small, realistic, consistently labeled sample set and document any assumptions in the handoff.

**Reference:** [VS Code launch configuration and `serverReadyAction`](https://code.visualstudio.com/docs/debugtest/debugging-configuration).
