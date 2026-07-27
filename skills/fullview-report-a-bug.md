---
name: Record a Fullview bug report for a user
description: >-
  Start and stop a Fullview session-replay bug recording for an identified end
  user via the Bug Report integration API, so a support agent can review the
  reproduction against a help-desk case.
api: openapi/fullview-bug-report-openapi.yml
operations:
  - startBugReport
  - stopBugReport
---

# Record a Fullview bug report

Use this skill to capture a Fullview session replay for a specific end user
during a support/bug workflow, then stop it when the reproduction is complete.

## Prerequisites

- The end user must already be **identified** in Fullview client-side via
  `window.$fvIdentity` (see `conventions/fullview-conventions.yml`). You need
  their `externalId` (your system's user id) and `email`.
- Your Fullview `organisationId`.
- A valid platform access token from the Keycloak OIDC provider
  (`authentication/fullview-authentication.yml`), sent as a bearer token.
- Base host: `https://api.eu1.fullview.io` (use the host for your region).

## Steps

1. **Start the recording** — call `startBugReport`
   (`POST /access/api/integrations/bug-report/start`) with a JSON body of
   `organisationId`, `externalId`, and `email`. Optionally include
   `integrationData` (`platform`, `workspaceId`, `caseId`) to link the
   recording to a help-desk case (Zendesk / Intercom / Salesforce / HubSpot).

2. **Let the user reproduce the issue.** The Web SDK captures the session
   replay. You can observe cobrowse lifecycle in the browser via
   `fullview:call*` DOM events if a live call is involved.

3. **Stop the recording** — call `stopBugReport`
   (`POST /access/api/integrations/bug-report/stop`) with the same
   `organisationId`, `externalId`, and `email`.

## Rules

- Always pass a stable `externalId`; it is how the recording is attributed to
  the user and joined to the help-desk case.
- Respect privacy controls: PII redaction / field masking and
  `disableReplaysForUser` may suppress capture — do not assume every session is
  recorded.
- No idempotency key is documented; do not rely on retry-safe semantics for
  start/stop — check state before re-issuing.
