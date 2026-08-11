# AI301 Contribution README

Student: Rushikesh Joshi  
GitHub: @joshi-rushikesh  
Course: CodePath AI301 - Summer 2026  
Selected Repository: nightscout/cgm-remote-monitor  
Selected Issue: Custom WebHook Support #5742  
Fork: https://github.com/joshi-rushikesh/cgm-remote-monitor

---

## Phase I: Issue Selection

### Status

Phase I Complete

### Chosen Issue

**Issue Title:** Custom WebHook Support  
**Issue Number:** nightscout/cgm-remote-monitor#5742  
**Issue Link:** https://github.com/nightscout/cgm-remote-monitor/issues/5742  
**Repository:** https://github.com/nightscout/cgm-remote-monitor  
**Fork:** https://github.com/joshi-rushikesh/cgm-remote-monitor  
**Labels:** feature request, help wanted

### Problem Summary

This issue asks for Nightscout to support custom webhook URLs/events in addition to its existing IFTTT webhook integration. From my understanding, the current problem is that IFTTT webhook notifications can be throttled, which may delay when requests are actioned, while a custom webhook URL could give users more direct control over where event notifications are sent. A successful contribution would add or plan a configurable way for Nightscout to send selected events to a custom endpoint while following the project's existing configuration and notification patterns.

### Why I Chose This Issue

I chose this issue because it is open, unassigned, labeled `help wanted`, and does not appear to have an existing linked pull request. It also matches my background because I have experience with JavaScript, APIs, configuration-driven features, and reading unfamiliar codebases.

This issue is a good learning opportunity because it requires me to understand an existing open source project's notification flow before making changes. My goal is to practice tracing a real code path, identifying the right module boundaries, and proposing a clean implementation that fits the existing Nightscout patterns. I also like that the issue has a clear user-facing motivation: allowing configurable webhook destinations instead of relying only on IFTTT.

### Issue Selection Checklist

- I can explain the problem in one sentence: Yes - Nightscout needs configurable custom webhook support so events can be sent to user-defined endpoints instead of only through IFTTT.
- Scope looks realistic for 3-4 weeks: Yes, as long as I focus on the existing notification/webhook path and keep the first implementation small.
- Matches my skills or things I can learn quickly: Yes - this involves JavaScript/Node.js, environment configuration, HTTP requests, and API-style integration work.
- Issue is active and claimable: Mostly yes - it is open, unassigned, labeled `help wanted`, and has no linked PR, though it is an older issue so I asked maintainers for confirmation.
- There is helpful context: Yes - the issue explains the current IFTTT throttling problem and gives example configuration variables.
- The project has setup/contribution docs: Yes - the repository includes README and contribution documentation to review.

### Initial Codebase Areas to Investigate

- Existing IFTTT Maker / webhook notification code
- Existing alarm and notification flow
- Existing environment/configuration variable patterns
- `lib/`
- `server.js`
- `docs/`
- `README.md`
- `CONTRIBUTING.md`
- Tests related to alarms, plugins, or notifications

### Initial Acceptance Criteria

A successful contribution should preserve the existing IFTTT behavior while adding a configurable custom webhook path. The feature should likely allow users to define a custom webhook URL and event mapping through environment/configuration variables, send the relevant event data to that endpoint, and include tests or documentation explaining how the new configuration works. Because this is related to notifications, I will test in a development environment and avoid making assumptions about production medical alert reliability.

### Communication Log

- Forked the repository to my GitHub account.
- Selected the Nightscout custom webhook issue for Phase I.
- Commented on the GitHub issue introducing myself, explaining my understanding of the issue, and asking for maintainer pointers.
- Posted in Slack because the CodePath issue sheet was not editable on my end.
- A CodePath staff member marked the issue as claimed for me in the issue sheet.
- Posted my Phase I completion milestone in the CodePath celebration channel.

---

## Phase II: Reproduce & Plan

### Status

Phase II Complete

### Understanding the Issue

Nightscout currently supports notification delivery through its IFTTT Maker integration, but the destination is tightly coupled to IFTTT. Issue #5742 requests the ability to send Nightscout notification events directly to configurable webhook destinations so operators are not dependent only on IFTTT and its throttling behavior.

My investigation showed that this is not simply a missing environment variable. The existing Maker notification path combines event routing, IFTTT-specific event fan-out, the IFTTT destination URL, and the requirement for a `MAKER_KEY`. A webhook-only configuration therefore cannot currently reuse this path without architectural changes.

The existing `lib/plugins/webhook.js` plugin does not fully solve #5742. It sends new SGV readings to one configured destination, while #5742 is about routing Nightscout notification events to configurable destinations based on event names.

### Local Development Setup

**Working Branch:**  
https://github.com/joshi-rushikesh/cgm-remote-monitor/tree/wip/custom-webhook-5742

**Upstream Base:** `dev`

Nightscout's `CONTRIBUTING.md` specifies that development happens against the `dev` branch and that new pull requests should target `dev`.

Before implementation, I synchronized my working branch with the current upstream `dev` branch. The branch had zero unique commits at that point, so it could be safely fast-forwarded without rewriting or losing work.

**Environment:**

- Windows
- Node.js v21.7.3
- npm 10.5.0
- `package.json` requires Node `>=20.x` and npm `>=10.x`
- `.nvmrc` specifies Node 22
- Dependencies were already installed and usable

#### Setup Challenges

**1. `npm run test-single` did not work correctly on Windows.**

The script relies on POSIX-style `$TEST` expansion:

```text
./tests/$TEST.test.js
```

On Windows, npm executes the script through `cmd.exe`, which did not expand `$TEST`. The result was:

```text
Error: No test files found: "./tests/$TEST.test.js"
```

I resolved this by calling the underlying test tooling directly:

```bash
npx env-cmd -f ./my.test.env npx mocha --timeout 5000 --require ./tests/hooks.js --exit ./tests/maker.test.js
```

**2. Tests require a local `my.test.env` file and `NODE_ENV=test`.**

The project test scripts expect a gitignored `my.test.env`. I verified that my local test environment contains `NODE_ENV=test`. For a fresh checkout, the test environment can be initialized from the project's test configuration before running the suites.

**3. Local Node version differs from `.nvmrc`.**

`.nvmrc` specifies Node 22, while my machine currently has Node 21.7.3. This did not block the investigated test suites because the project's `package.json` engine requirement is Node `>=20.x`.

I documented the difference rather than making an unrelated environment change during Phase II.

**4. Dependency drift appeared after synchronizing with `dev`.**

The updated branch references a newer `nightscout-connect` version than the currently installed local package. It did not affect the Maker, webhook, settings, or environment tests used for Phase II, so I left the dependency files unchanged rather than introducing unrelated lockfile changes.

### Reproduction Process

#### Steps to Reproduce

1. Clone my fork:

   ```bash
   git clone https://github.com/joshi-rushikesh/cgm-remote-monitor.git
   cd cgm-remote-monitor
   ```

2. Add the Nightscout repository as the upstream remote:

   ```bash
   git remote add upstream https://github.com/nightscout/cgm-remote-monitor.git
   git fetch upstream
   ```

3. Create or check out the working branch from Nightscout's `dev` branch:

   ```bash
   git checkout -b wip/custom-webhook-5742 upstream/dev
   ```

4. Install dependencies if needed:

   ```bash
   npm install
   ```

5. Prepare the project's test environment and confirm `NODE_ENV=test`.

6. Search the repository for a custom notification-webhook configuration surface:

   ```bash
   git grep "CUSTOM_WEBHOOK"
   ```

   **Actual result:** No matching implementation or configuration exists.

7. Inspect `lib/plugins/maker.js`.

   The outbound Maker request is constructed using a hardcoded IFTTT destination:

   ```text
   https://maker.ifttt.com/trigger/...
   ```

   There is no configurable alternate destination.

8. Inspect `lib/server/env.js`.

   The extended-settings parser only consumes variables prefixed by the name of an enabled plugin. The issue's illustrative `CUSTOM_WEBHOOK_1_URL` naming therefore cannot simply flow through the current Maker extended-settings configuration.

9. Run the existing Maker tests to establish the baseline:

   ```bash
   npx env-cmd -f ./my.test.env npx mocha --timeout 5000 --require ./tests/hooks.js --exit ./tests/maker.test.js
   ```

   **Result:**

   ```text
   6 passing
   0 failing
   ```

10. Probe the actual Maker notification path using custom webhook-like configuration and intercept the outbound request function rather than making live network calls.

11. Trigger a realistic urgent-low notification.

12. Inspect the generated destinations.

   **Actual result:** Three requests were generated and all targeted `maker.ifttt.com`. Zero requests targeted the configured custom endpoint.

13. Repeat the investigation after synchronizing with the current `dev` branch to confirm the behavior still exists.

#### Expected Behavior

A Nightscout operator should be able to configure one or more custom webhook URL/event pairs. When a matching Nightscout notification event occurs, Nightscout should send that event directly to the configured endpoint.

Existing `MAKER_KEY` and `MAKER_ANNOUNCEMENT_KEY` behavior should continue working unchanged.

#### Actual Behavior

- No `CUSTOM_WEBHOOK*` notification configuration currently exists.
- Maker notification delivery is hardcoded to `maker.ifttt.com`.
- The Maker plugin requires an IFTTT Maker key before the notification path is available.
- Custom webhook configuration supplied during the reproduction is ignored.
- A reproduced urgent-low event generated three IFTTT requests and zero requests to the configured custom endpoint.
- The existing `webhook` plugin is for SGV updates rather than configurable notification-event routing.

### Reproduction Evidence

**Working Branch:**  
https://github.com/joshi-rushikesh/cgm-remote-monitor/tree/wip/custom-webhook-5742

**Maker Baseline:**

```text
maker
  √ turn values to a query
  √ send a request
  √ not send a request without a name
  √ not send a request without a level
  √ send a allclear, but only once

multi announcement maker
  √ send 2 requests for the 2 keys

6 passing
```

Other relevant baseline suites also passed during investigation:

- `tests/maker.test.js` - 6 passing
- `tests/webhook.test.js` - 10 passing
- `tests/settings.test.js` - 14 passing
- Environment/settings/webhook investigation - no relevant failures

No live IFTTT or external webhook requests were made during reproduction. The outbound request layer was intercepted for testing.

### Findings From Codebase Investigation

#### `lib/plugins/maker.js`

This is the existing IFTTT notification sender.

Important findings:

- `init(env)` reads Maker keys.
- `sendEvent()` is the public event entry point.
- `makeRequests()` fans one notification into three IFTTT events:
  - `ns-event`
  - `ns-<level>`
  - `ns-<level>-<name>`
- `makeKeyRequest()` constructs the hardcoded `maker.ifttt.com` URL.
- The plugin returns `null` when no Maker key exists.

This means custom-webhook delivery should not simply be inserted into `makeKeyRequest`, because doing so would inherit both the IFTTT key requirement and the IFTTT-specific three-event fan-out.

#### `lib/server/pushnotify.js`

This module dispatches Nightscout notifications.

It constructs the canonical notification information used by Maker, including the event name, severity level, title, message, and announcement state.

This appears to be a better architectural level for introducing an independent custom-webhook dispatch path because it sits above the IFTTT-specific sender.

#### `lib/server/env.js`

`findExtendedSettings()` only exposes environment variables whose prefixes correspond to enabled plugins.

This creates a configuration constraint for the example variable naming from #5742.

#### `lib/settings.js`

The existing `frameUrl1..8` / `frameName1..8` settings provide a strong pattern for numbered configuration pairs.

The repository also recently added support for mapping numbered camel-case settings back to numbered environment variable names. This makes a structure such as:

```text
CUSTOM_WEBHOOK_URL_1
CUSTOM_WEBHOOK_EVENT_1
CUSTOM_WEBHOOK_URL_2
CUSTOM_WEBHOOK_EVENT_2
```

consistent with existing Nightscout configuration conventions.

#### `lib/plugins/pushover.js`

The Pushover implementation already demonstrates Nightscout's approach to selecting destination sets based on notification properties and applying fallback behavior.

This is useful as an analogous routing pattern.

#### `lib/plugins/webhook.js`

Nightscout now contains a separate webhook plugin, added after #5742 was originally opened.

However, it:

- triggers on new SGV readings
- uses one configured destination
- does not map notification event names to destinations
- does not replace the Maker notification flow requested by #5742

Its reviewed HTTP behavior is still useful as a pattern for network handling, including protocol selection, timeouts, and success/error handling.

### Git History Investigation

I used `git log` and `git blame` to understand why the Maker path is structured this way rather than assuming the current implementation represented a general notification design.

The Maker event fan-out and its explanatory comment date back to the original 2015 implementation. The code explicitly explains that multiple events are sent because Maker/IFTTT filters events by name.

This indicates that the three-event fan-out is an IFTTT-specific compatibility mechanism rather than a general requirement for every future notification transport.

The hardcoded Maker URL is also part of the original design and has never been generalized into a destination abstraction.

I also reviewed the history of `lib/plugins/webhook.js`. That implementation provides prior art for sending webhooks but addresses SGV updates rather than notification-event routing.

### Root Cause

The root cause is architectural rather than a single missing variable.

1. The outbound destination and IFTTT transport are hardcoded together in `lib/plugins/maker.js`.
2. The Maker notification path only exists when an IFTTT key is configured.
3. The three-event fan-out is specifically an IFTTT filtering workaround.
4. The existing extended-settings parser cannot directly represent the issue's proposed custom-webhook variable naming without additional configuration work.
5. The newer SGV webhook plugin solves a different use case and therefore does not close the notification-routing gap.

### Solution Approach

#### Understand

Nightscout notification events can currently be delivered through the IFTTT Maker integration, but that transport has a hardcoded destination and requires a Maker key. The requested feature is an independent way to route selected Nightscout notification events directly to configurable HTTP/HTTPS endpoints.

Existing Maker behavior must remain backward compatible.

#### Match

I found several existing Nightscout patterns that can guide the implementation:

- Numbered configuration pairs: `frameUrl1..8` / `frameName1..8`
- Notification destination selection: Pushover's routing helpers
- HTTP/HTTPS webhook delivery semantics: `lib/plugins/webhook.js`
- Existing outbound-request test seams in Maker and webhook tests

#### Implementation Plan

1. Add numbered custom webhook URL/event configuration using the project's existing numbered-setting conventions, likely:

   ```text
   CUSTOM_WEBHOOK_URL_1
   CUSTOM_WEBHOOK_EVENT_1
   CUSTOM_WEBHOOK_URL_2
   CUSTOM_WEBHOOK_EVENT_2
   ```

2. Normalize configured URL/event pairs into validated webhook destinations.

3. Skip incomplete or malformed configuration safely without crashing Nightscout.

4. Allow only `http:` and `https:` destinations.

5. Add a dedicated notification-webhook sender rather than modifying the existing SGV webhook plugin.

6. Wire custom-webhook dispatch into the notification layer independently of `MAKER_KEY`.

7. Preserve the existing Maker/IFTTT three-event behavior exactly.

8. Match configured custom-webhook events without causing accidental triple delivery.

9. Reuse the existing project's HTTP/HTTPS timeout and error-handling patterns where appropriate.

10. Add focused automated tests for configuration parsing, event matching, multiple destinations, invalid configuration, network errors, and backward compatibility.

11. Update the README and example environment configuration with the new settings and behavior.

#### Implement

Implementation will be completed in Phase III on:

**Branch:** `wip/custom-webhook-5742`  
**Branch Link:** https://github.com/joshi-rushikesh/cgm-remote-monitor/tree/wip/custom-webhook-5742  
**PR Target:** `nightscout/cgm-remote-monitor:dev`

#### Review

Before submitting a pull request, I will:

- Review the diff against `CONTRIBUTING.md`
- Keep changes scoped only to #5742
- Verify no local environment files or secrets are committed
- Preserve existing Maker behavior
- Preserve the existing SGV webhook behavior
- Run the relevant tests
- Run the project's linting command
- Review the PR against Nightscout's expected `dev` target branch
- Document new configuration as required by the contributing guide

#### Evaluate

Planned tests include:

1. No custom configuration produces no request.
2. A matching event sends exactly one request to the configured endpoint.
3. A non-matching event sends no request.
4. Incomplete URL/event pairs are ignored safely.
5. Malformed URLs do not crash initialization.
6. Non-HTTP/HTTPS schemes are rejected.
7. Multiple configured webhook pairs work independently.
8. Sparse numbered configuration is handled safely.
9. Network errors are handled without crashing the notification path.
10. HTTP and HTTPS destinations use the appropriate transport.
11. Existing Maker tests remain green.
12. Existing SGV webhook tests remain green.
13. Numbered environment-variable mapping is covered by tests.
14. No live external requests are made during automated testing.

### Risks / Open Questions

- Existing IFTTT users must see no change to request count, event naming, timing, or Maker-key fallback behavior.
- Custom webhooks must work without requiring `MAKER_KEY`.
- Custom event matching should not accidentally inherit Maker's three-request fan-out.
- Incomplete or mistyped event configuration should fail safely.
- Sparse numbered configuration should be supported without requiring every slot.
- Operator-configured URLs introduce SSRF considerations and must be handled carefully.
- Full webhook URLs should not be logged because they may contain tokens or other sensitive values.
- Malformed URLs and unsupported schemes must not crash startup.
- Network errors and timeouts must not block or crash Nightscout's alarm-processing path.
- The existing SGV-oriented `webhook` plugin must not regress.
- Final configuration naming may be adjusted if maintainer feedback indicates a different convention is preferred.

---

## Phase III: Build

### Status

Not Started

### Implementation Notes

*To be completed in Phase III.*

### Files Changed

*To be completed in Phase III.*

### Testing Strategy

*To be completed in Phase III.*

### Progress Log

*To be completed in Phase III.*

---

## Phase IV: Submit & Iterate

### Status

Not Started

### Pull Request

*To be completed in Phase IV.*

### PR Summary

*To be completed in Phase IV.*

### Maintainer Feedback Log

*To be completed in Phase IV.*

### Revisions Made

*To be completed in Phase IV.*

### Final Reflection

*To be completed in Phase IV.*
