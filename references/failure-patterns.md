# Failure Patterns

Use these as reminders when auditing a claimed blocker. Re-check current evidence before applying any pattern.

## Misread Command Output

- A CLI version check can pass while real startup fails. Run the real command and read logs/config named in the error.
- PATH can be stripped in Codex shells. Retry `zsh -lic '<command>'` before saying `gh`, `pnpm`, `gcloud`, `op`, `fastlane`, `pod`, or similar tools are unavailable.
- A running process is not proof of health. Check the service-specific health command, RPC probe, or expected output.
- Missing `timeout` is not a blocker. Use an available bounded execution fallback.

## Wrong Endpoint Or Tool Schema

- A marketing host is often not the API host. Verify official endpoint, HTTP method, body shape, and timeout before saying the URL is blocked.
- A tool schema error is not website/auth failure. Fix the call shape first.
- File-producing APIs require file proof: save output, run `file`, inspect dimensions/content when relevant.

## Browser And Auth Context Drift

- A fresh automation browser is often not the logged-in browser. Use the current in-app browser, existing Chrome/Comet profile, or Computer Use when authenticated state is the point.
- Do not conclude "needs auth" from an unauthenticated profile when the user already has an authenticated profile open.
- Browser fallback routes can look broken under raw curl. Match `Accept` headers and browser navigation behavior before calling it a product issue.
- Screenshots are proof only after visual inspection.

## Deploy And Production Truth Drift

- `git push` is not production. Confirm the deploy system consumed the pushed SHA and the live route/API shows the new behavior.
- Container healthy is not the same as product fixed. Check the actual user route, API response, or browser surface.
- A backend provider check does not prove the browser flow. Keep the verdict scoped to the proof actually collected.

## Data And Provider State

- When the complaint is provider-related, check provider truth early: balance, account state, webhook logs, profile lookup, or status endpoint.
- Do not stop at route code when a live account balance, billing limit, quota, or provider rejection explains the symptom.
- If workstation DB access is blocked by internal hostnames, use public API/admin/logs/tunnel paths before declaring the investigation blocked.

## Prior-Context Contradiction Handling

- Prior "done", "fixed", "blocked", and "not possible" claims are suspect until reverified.
- User corrections outrank agent reports.
- If a previous conversation already found a working path, do not restart from generic search. Reuse and verify the proven path first.
- If a previous fix only proved a narrower backend/static surface, do not claim broader browser/user-visible completion.

