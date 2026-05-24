---
name: overcome-false-limitations
description: "Analyze previous conversations, logs, command output, browser/tool traces, and claimed blockers to detect false limitations, misread errors, wrong tool schemas, wrong browser/profile/auth assumptions, and recoverable failures; then exhaust realistic alternate paths before declaring anything blocked."
---

# Overcome False Limitations Skill

Use this skill when the user says an agent stopped early, claimed something was impossible, blamed auth/browser/tool limits, or treated an error as a blocker without reading the real output.

The user's request is:

`$ARGUMENTS`

## Core Rule

A limitation is not real until the agent has read the primary output, identified the failing layer, checked prior successful patterns, and tried the fastest realistic alternate path.

Do not accept old agent conclusions like "blocked", "not possible", "requires auth", "URL is blocked", "tool unavailable", "browser cannot access it", or "command failed" as truth. Treat them as hypotheses to re-test.

## Anti-Gate Rule

Do not add or recommend code that blocks, disables, hides, removes, gates, or marks a feature/account/platform/tool as unavailable from a single failed attempt.

Before any temporary gate is justified:

- try at least 3 distinct approaches to make the intended path work;
- collect evidence from at least 2 different source layers, not the same endpoint twice;
- check the user's documented spec or durable position first;
- if a temporary gate is still necessary, require a code comment like `// TEMP-GATE: YYYY-MM-DD - tried: ... - not tried: ... - removal condition: ...`.

External API errors such as 403, 429, 500, empty results, selector misses, and timeouts are symptoms. Investigate alternate headers, sessions, user agents, browser profiles, existing state, rate windows, provider state, and prior successful paths before encoding a negative capability claim.

## Success Standard

Finish with one of these:

- `overcome`: the claimed limitation was bypassed, fixed, or converted into a working path.
- `real-blocker`: the blocker remains after primary evidence and at least three realistic alternatives were exhausted.
- `deferred`: the blocker is outside the current scope, parked in the right tracker with exact missing proof.

Never finish at "looks blocked" or "probably needs manual action" unless the evidence proves that exact boundary.

## Evidence Order

Use the fastest source that can disprove the claimed limitation:

1. Current thread wording and the user's corrections.
2. The exact command/tool output, including stderr, exit code, generated files, and logs.
3. Repo instructions and local skill guidance.
4. Prior conversations and session stores for the same failure class.
5. Current local machine state: PATH, profiles, cookies, config files, env, credentials already present, running services.
6. Official docs or provider/API truth when the error is tool/vendor-specific.
7. Prior agent claims only as leads.

## Triage Workflow

1. Normalize the user's complaint into a claimed-limitation statement:
   - "Agent said X was blocked because Y."
   - "Actual recoverable path may be Z."
2. Locate the primary artifact:
   - command output, stack trace, HTTP status, browser screenshot, tool-call schema error, local log, generated file, or session transcript.
3. Read the output before reasoning:
   - capture exit code and stderr;
   - inspect any output file with `file`, `head`, `tail`, or the relevant viewer;
   - check logs named in the error;
   - if output is truncated, rerun narrower or read the artifact directly.
4. Classify the failure layer:
   - wrong command syntax;
   - wrong API endpoint or HTTP method;
   - wrong tool schema;
   - wrong browser/profile/session;
   - wrong headers or user agent;
   - PATH/login shell mismatch;
   - missing local dependency that has an installed alternative;
   - remote provider state;
   - real permission/account/billing/legal hold boundary.
5. Try the recovery ladder for that class.
6. Verify with the smallest proof that matches the intended user surface.
7. Sweep sibling instances: same tool, same browser route, same command family, same deploy path, same profile/auth assumption.
8. Only then report `overcome`, `real-blocker`, or `deferred`.

## Recovery Ladders

### Command Or CLI Failure

- Rerun through login shell when PATH may be stripped: `zsh -lic '<command>'`.
- Preserve and read full stderr; do not summarize from memory.
- Check version/help output separately from real startup.
- Inspect logs/config mentioned by the error.
- Search local caches/configs before reinstalling.
- If `timeout` is missing, use an available bounded fallback such as Perl `alarm`.
- If one subcommand fails, test the underlying binary/API directly.

### Browser, URL, Or Auth Failure

- Decide which browser context is authoritative:
  - local app preview/browser tool for localhost;
  - existing logged-in Chrome/Comet profile when auth matters;
  - direct HTTP/curl only when it matches the intended surface.
- Do not launch a fresh unauthenticated browser and conclude the site needs login.
- If a URL looks blocked, compare:
  - browser navigation;
  - direct curl with the same method and relevant headers;
  - provider/API endpoint versus marketing host;
  - `Accept: text/html` versus default curl headers;
  - screenshot output file validity.
- If a tool call fails, verify the schema and endpoint before blaming the website.
- Every screenshot used as proof must be visually inspected.

### API, Provider, Or Deploy Failure

- Check the exact provider truth surface early: balance, deployment status, revision/SHA, health, job logs, webhook status, or account state.
- Separate backend/API proof from user-visible proof; do not upgrade one into the other.
- Do not treat `git push`, process exists, container healthy, or HTTP 200 on a fallback page as final proof unless that is the requested end state.
- Wait for deploy propagation when the user surface depends on production.

### Data, DB, Or Runtime Failure

- `SELECT` before any write.
- Inspect live schema before assuming table/column names.
- If local DB hostname is internal-only, switch to public API, admin UI, logs, or tunnel paths instead of softening the verdict.
- Keep unreachable local infrastructure as a blocker note, not as evidence that product behavior is fixed.

## Common False-Limitation Patterns

Load [references/failure-patterns.md](references/failure-patterns.md) when you need concrete reminders from prior conversations. Use it as a pattern library, not as current truth.

## Blocker Declaration Format

If a blocker remains, state:

- exact primary evidence;
- alternatives tried;
- why each failed;
- what single missing fact, permission, account state, or external action would unblock it;
- best available fallback proof.

Do not use vague blocker language.

## Final Answer Shape

Keep the closeout compact:

`SHIPPED:` what was overcome or created.

`EVIDENCE:` command/tool/browser/provider proof.

`LIMITS:` only real blockers that survived the recovery ladder.
