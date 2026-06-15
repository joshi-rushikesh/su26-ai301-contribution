# AI301 Contribution README

Student: Rushikesh Joshi
GitHub: @joshi-rushikesh
Course: CodePath AI301 — Summer 2026
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

This issue asks for Nightscout to support custom webhook URLs/events in addition to its existing IFTTT webhook integration. From my understanding, the current problem is that IFTTT webhook notifications can be throttled, which may delay when requests are actioned, while a custom webhook URL could give users more direct control over where event notifications are sent. A successful contribution would add or plan a configurable way for Nightscout to send selected events to a custom endpoint while following the project’s existing configuration and notification patterns.

### Why I Chose This Issue

I chose this issue because it is open, unassigned, labeled `help wanted`, and does not appear to have an existing linked pull request. It also matches my background because I have experience with JavaScript, APIs, configuration-driven features, and reading unfamiliar codebases.

This issue is a good learning opportunity because it requires me to understand an existing open source project’s notification flow before making changes. My goal is to practice tracing a real code path, identifying the right module boundaries, and proposing a clean implementation that fits the existing Nightscout patterns. I also like that the issue has a clear user-facing motivation: allowing configurable webhook destinations instead of relying only on IFTTT.

### Issue Selection Checklist

* I can explain the problem in one sentence: Yes — Nightscout needs configurable custom webhook support so events can be sent to user-defined endpoints instead of only through IFTTT.
* Scope looks realistic for 3–4 weeks: Yes, as long as I focus on the existing notification/webhook path and keep the first implementation small.
* Matches my skills or things I can learn quickly: Yes — this involves JavaScript/Node.js, environment configuration, HTTP requests, and API-style integration work.
* Issue is active and claimable: Mostly yes — it is open, unassigned, labeled `help wanted`, and has no linked PR, though it is an older issue so I asked maintainers for confirmation.
* There is helpful context: Yes — the issue explains the current IFTTT throttling problem and gives example configuration variables.
* The project has setup/contribution docs: Yes — the repository includes README and contribution documentation to review.

### Initial Codebase Areas to Investigate

* Existing IFTTT Maker / webhook notification code
* Existing alarm and notification flow
* Existing environment/configuration variable patterns
* `lib/`
* `server.js`
* `docs/`
* `README.md`
* `CONTRIBUTING.md`
* Tests related to alarms, plugins, or notifications

### Initial Acceptance Criteria

A successful contribution should preserve the existing IFTTT behavior while adding a configurable custom webhook path. The feature should likely allow users to define a custom webhook URL and event mapping through environment/configuration variables, send the relevant event data to that endpoint, and include tests or documentation explaining how the new configuration works. Because this is related to notifications, I will test in a development environment and avoid making assumptions about production medical alert reliability.

### Communication Log

* Forked the repository to my GitHub account.
* Selected the Nightscout custom webhook issue for Phase I.
* Commented on the GitHub issue introducing myself, explaining my understanding of the issue, and asking for maintainer pointers.
* Attempted to claim the issue in the CodePath issue sheet, but the sheet was not editable on my end.
* Posted in Slack asking staff/TFMs to confirm or mark the issue as claimed because I could not edit the sheet.

---

## Phase II: Reproduce & Plan

### Status

Not Started

### Understanding the Issue

*To be completed in Phase II.*

### Local Development Setup

*To be completed in Phase II.*

### Reproduction Process

*To be completed in Phase II.*

### Findings From Codebase Investigation

*To be completed in Phase II.*

### Solution Approach

*To be completed in Phase II.*

### Risks / Open Questions

*To be completed in Phase II.*

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
