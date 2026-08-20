<!-- gitnexus:start -->
# GitNexus — Code Intelligence

This project is indexed by GitNexus as **modly** (2792 symbols, 6065 relationships, 213 execution flows). Use the GitNexus MCP tools to understand code, assess impact, and navigate safely.

> Index stale? Run `node .gitnexus/run.cjs analyze` from the project root — it auto-selects an available runner. No `.gitnexus/run.cjs` yet? `npx gitnexus analyze` (npm 11 crash → `npm i -g gitnexus`; #1939).

## Always Do

- **MUST run impact analysis before editing any symbol.** Before modifying a function, class, or method, run `impact({target: "symbolName", direction: "upstream"})` and report the blast radius (direct callers, affected processes, risk level) to the user.
- **MUST run `detect_changes()` before committing** to verify your changes only affect expected symbols and execution flows. For regression review, compare against the default branch: `detect_changes({scope: "compare", base_ref: "main"})`.
- **MUST warn the user** if impact analysis returns HIGH or CRITICAL risk before proceeding with edits.
- When exploring unfamiliar code, use `query({search_query: "concept"})` to find execution flows instead of grepping. It returns process-grouped results ranked by relevance.
- When you need full context on a specific symbol — callers, callees, which execution flows it participates in — use `context({name: "symbolName"})`.
- For security review, `explain({target: "fileOrSymbol"})` lists taint findings (source→sink flows; needs `analyze --pdg`).

## Never Do

- NEVER edit a function, class, or method without first running `impact` on it.
- NEVER ignore HIGH or CRITICAL risk warnings from impact analysis.
- NEVER rename symbols with find-and-replace — use `rename` which understands the call graph.
- NEVER commit changes without running `detect_changes()` to check affected scope.

## Resources

| Resource | Use for |
|----------|---------|
| `gitnexus://repo/modly/context` | Codebase overview, check index freshness |
| `gitnexus://repo/modly/clusters` | All functional areas |
| `gitnexus://repo/modly/processes` | All execution flows |
| `gitnexus://repo/modly/process/{name}` | Step-by-step execution trace |

## CLI

| Task | Read this skill file |
|------|---------------------|
| Understand architecture / "How does X work?" | `.claude/skills/gitnexus/gitnexus-exploring/SKILL.md` |
| Blast radius / "What breaks if I change X?" | `.claude/skills/gitnexus/gitnexus-impact-analysis/SKILL.md` |
| Trace bugs / "Why is X failing?" | `.claude/skills/gitnexus/gitnexus-debugging/SKILL.md` |
| Rename / extract / split / refactor | `.claude/skills/gitnexus/gitnexus-refactoring/SKILL.md` |
| Tools, resources, schema reference | `.claude/skills/gitnexus/gitnexus-guide/SKILL.md` |
| Index, status, clean, wiki CLI commands | `.claude/skills/gitnexus/gitnexus-cli/SKILL.md` |

<!-- gitnexus:end -->

# Behavioral Guidelines (Karpathy)

## 1. Think Before Coding
- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First
- Minimum code that solves the problem. Nothing speculative.
- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

## 3. Surgical Changes
- Touch only what you must. Clean up only your own mess.
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

## 4. Goal-Driven Execution
- Transform tasks into verifiable goals: "Add validation" → "Write tests for invalid inputs, then make them pass"
- For multi-step tasks, state a brief plan with verification steps.
- Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

# Pre-Task Workflow (adapted from `.agent/rules/AUTO_AGENT_LOADING.md`)

## Pre-Task Checklist

At the start of every task — **all steps are MANDATORY, in order, before writing any code**:
1. **Re-read AGENTS.md** — refresh all rules for this session
2. **Check GitNexus index freshness** — `read_mcp_resource` `gitnexus://repo/modly/context` (re-index with `node .gitnexus/run.cjs analyze` if stale)
3. **Load the relevant skill(s)** from `.claude/skills/` based on task type (table below) via the `skill` tool — do this BEFORE starting the work, not after
4. **Before EVERY edit to a function/class/method/variable** — run `impact({target, direction})` (and `context`/`query` when needed) on the target symbol; report blast radius to the user. NEVER skip this. Do not begin editing until impact is run and (if HIGH/CRITICAL) the user is warned.
5. **If task is large (>=3 files, >=5 changes, or spans frontend+backend)** — create a hierarchical plan before coding
6. **Before committing** — run `detect_changes()` to verify only expected symbols/flows changed

## Skill Auto-Selection by Task Type

| Task Type | Auto-load `.claude/skills/` |
|-----------|---------------------------|
| React / UI / Frontend | `react-specialist`, `typescript-pro`, `javascript-pro` |
| Vue | `vue-expert`, `typescript-pro`, `javascript-pro` |
| Angular | `angular-architect`, `typescript-pro`, `javascript-pro` |
| Next.js | `nextjs-developer`, `react-specialist`, `typescript-pro` |
| Node.js / Express | `nodejs-expert`, `typescript-pro`, `javascript-pro` |
| ASP.NET Core / C# | `dotnet-core-expert`, `csharp-developer` |
| Spring Boot / Java | `spring-boot-engineer`, `java-architect` |
| Django / Python | `django-developer`, `python-pro` |
| FastAPI / Python | `fastapi-developer`, `python-pro` |
| Flask / Python | `flask-developer`, `python-pro` |
| Rails / Ruby | `rails-expert` |
| Laravel / PHP | `laravel-specialist` |
| Go | `golang-pro` |
| Rust | `rust-engineer` |
| Kotlin | `kotlin-specialist` |
| Flutter / Dart | `flutter-expert` |
| Swift / iOS | `swift-expert` |
| Database / SQL | `sql-pro`, `database-architect` |
| API Design | `api-designer` |
| Docker / K8s / Deploy | `docker-expert`, `kubernetes-specialist`, `terraform-engineer` |
| Testing | `test-generator` |
| Performance | `performance-optimizer` |
| Security | `security-reviewer` |
| RAG / Vector DB | `rag-engineer`, `vector-search-engineer` |
| Git / Workflow | `git-workflow` |
| Architecture / Docs | `documentation-writer` |
| Refactoring | `refactoring-specialist` |
| Code Review | `code-reviewer` |
| GitNexus / Exploring | `gitnexus/gitnexus-exploring` |
| GitNexus / Impact | `gitnexus/gitnexus-impact-analysis` |
| GitNexus / Debugging | `gitnexus/gitnexus-debugging` |
| GitNexus / Refactoring | `gitnexus/gitnexus-refactoring` |
| GitNexus / Guide | `gitnexus/gitnexus-guide` |
| GitNexus / CLI | `gitnexus/gitnexus-cli` |

## Hierarchical Plan for Large Tasks

When a task involves **>=3 files**, **>=5 distinct changes**, or **spans multiple components**, create a hierarchical plan:

```
1. [Component/Area]
   1.1. [Sub-task]
      1.1.1. [Specific action] — file: `path/to/file`
      1.1.2. [Specific action] — file: `path/to/file`
   1.2. [Sub-task]
2. [Component/Area]
   ...
```

## Plan & Task Documents Workflow

Whenever a task requires creating `implementation_plan.md` and/or `task.md` to track progress:

1. Create `drafts/plans/` if it does not exist.
2. Create a timeline folder inside `drafts/plans/`:
   - Name format: `<MMDDYY>_<number>_<status>`
     - `<number>`: incrementing sequence starting at `01`
     - `<status>`: folder status (e.g. `in_progress`, `done`, `cancelled`) — keep it updated until all tasks in the implementation plan are marked complete
   - Example: `drafts/plans/080826_01_in_progress/`
3. Place **both** `implementation_plan.md` and `task.md` inside that folder.
4. Never store plan/task files at the repository root — always inside `drafts/plans/<timeline>/`.

## Commit Message Convention

At the end of every completed task, suggest a commit message following Conventional Commits:
- Prefixes: `feat:`, `fix:`, `refactor:`, `chore:`, `style:`, `docs:`, `test:`, `perf:`
- Add scope when applicable: `fix(ci):`, `feat(auth):`
- Keep subject line under 72 characters

## Language
- Respond in Vietnamese when chatting with the user
- Code, variable names, and technical documentation remain in English

---

# Standing Rules (auto-applied every session — no need to repeat in prompts)

1. Read `drafts/plans/WORKSPACE.md` for the familiar scope folders (read-only, do not modify/delete).
2. Detailed rules: `.agent/rules/AUTO_AGENT_LOADING.md` + `.agent/rules/GEMINI.md` — read only when the task explicitly needs them.
3. Plan/task docs go under `drafts/plans/<MMDDYY>_<n>_<status>/` (see Plan & Task Documents Workflow above).
4. End of every task: report status (success/failure) + propose a Conventional Commits message.
5. For a signature-placement task on Vietnamese reports: verify each signer's rect against name/role line bboxes and print raw zone values instead of deriving coordinates by hand.
