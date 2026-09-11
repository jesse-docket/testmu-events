# What still needs a server

The plan specifies a secure context service, signed-token validation, CRM writes and account
enrichment, all server-side. GitHub Pages serves static files only, so none of that can live in
this repo. This file states what is missing rather than pretending it exists.

## 1. Signed context token (`ctx`)

**Plan:** campaign sends add an opaque, signed, short-lived `ctx` token identifying a known
recipient without putting PII in the URL.

**Today:** the page reads `ctx`, never decodes it, never sends it to the agent or to analytics,
and only preserves it on outbound `www.testmuai.com` links so the destination can use it.

**Needed:** an endpoint that takes `ctx`, verifies the signature and expiry, and returns the
`account` and `visitor` halves of the DCI envelope. Until then `account.segment` is always
unknown and `visitor.consent` is always unknown.

## 2. The account half of the DCI envelope

The plan's envelope has `account.segment`, `account.roleFamily`, `account.productInterest`.
None of these can be derived client-side without either identifying the visitor or shipping an
enrichment key to the browser. They are omitted. The seven DCI fields that are wired are all
campaign-derived and non-identifying.

## 3. CRM handoff

**Plan:** send the existing TestMu CRM only the consented summary, stated need, campaign
attribution and follow-up request, and only after qualification.

**Today:** the agent's Post Call Analysis extracts `Qualified Handoff`, `Stated Need`,
`Event Program` and `Event Outcome`. Nothing is written to a CRM — all CRM sync toggles on the
agent are off, and Salesforce is the only connected CRM on the tenant.

**Needed:** a RevOps decision on the Salesforce field map, then the sync enabled deliberately.
Prompt text cannot do this and no prompt text pretends it can.

## 4. Consent

`visitor.consent` is `unknown` for everyone. The agent's consent config is `mode: existing`,
inherited from Kane. If the events site needs its own consent surface, that is a Docket agent
configuration change plus a site change, not a prompt change.

## 5. Registration completion

Tracking `registration_completed` depends on TestMu's registration success page posting a
non-PII `postMessage` to the parent frame. The listener is implemented and scoped to
`https://www.testmuai.com`. TestMu has to add the `postMessage`.
