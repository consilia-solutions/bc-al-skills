---
name: harden-al-test-execution
description: 'Run AL test authoring and execution with fail-fast guards, blocked-state detection, and deterministic stop conditions. Use when AL Test Writer or AL Test Runner appears to hang, returns incomplete output, or cannot run due to environment/setup issues.'
argument-hint: 'Optional: test scope (all tests, codeunit, or method) and app/test project paths'
user-invocable: true
---

# Harden AL Test Execution

Prevent freeze-like behavior when tests cannot run by enforcing preflight checks, bounded retries, and explicit blocked outcomes.

## Outcome

Always end in one of three states:
- Passed: tests executed and results were collected.
- Failed: tests executed and assertions/build checks failed.
- Blocked: tests could not run (environment/setup/tooling issue), with a clear reason and next action.

Never remain in an implicit waiting state.

## Inputs

Optional:
- Test scope: all tests, codeunit, method.
- App path and test app path.
- Target BC version/runtime.

## Procedure

1. Run preflight checks before writing or running tests.
- Validate that app and test projects exist.
- Validate `app.json` can be read for both projects.
- Validate dependency apps are present in `.alpackages`.
- Validate at least one test object exists in test source.
- If any check fails, return `Blocked` immediately with the failing check.

2. Build gate before test execution.
- Run AL diagnostics/build for app and test app first.
- If build fails, do not run tests.
- Return `Blocked` with the first 10 diagnostics and exact file paths/lines.

3. Write or update tests only after preflight/build gate passes.
- Keep test scope minimal to requested behavior.
- Avoid broad refactors while fixing a failing test path.

4. Execute tests with bounded attempts.
- Attempt up to 3 runs max.
- After each failed run, classify failure type:
  - Product failure (assertion/behavior mismatch).
  - Build failure (compile/package errors).
  - Environment failure (server/container/session/dependency issues).
- Retry only product/build failures that were modified in the previous attempt.
- Do not retry environment failures automatically.

5. Detect and stop on freeze indicators.
- If command output is idle without progress and no prompt is pending, stop and mark `Blocked`.
- If tool reports input needed, ask once and continue; if unresolved, mark `Blocked`.
- If repeated identical error appears twice with no code delta, stop retry loop and report `Blocked`.

6. Emit a deterministic end report.
- Include:
  - Final state: Passed, Failed, or Blocked.
  - Attempts used.
  - First actionable root cause.
  - Files touched.
  - Exact next action for user/agent.

## Guardrails

- Never loop indefinitely.
- Never continue to review/translation/merge steps if test execution is `Blocked`.
- Never claim success without test output evidence.
- Prefer failing fast with clear diagnostics over speculative retries.

## Output Template

Use this final structure:

```text
Test Workflow State: <Passed|Failed|Blocked>
Attempts: <n>/3
Root Cause: <one-line cause>
Evidence:
- <tool/command output summary>
- <file:line diagnostics>
Files Touched:
- <path>
Next Action:
- <single highest-value next step>
```

## Example Blocked Cases

- Missing symbol packages in `.alpackages`.
- App/test build diagnostics prevent test publish.
- BC test runtime or container unavailable.
- Dependency version mismatch between app and test app.
- Interactive prompt unresolved during test command.