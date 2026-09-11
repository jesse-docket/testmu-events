# TestMu AI — Event Concierge campaign site

One campaign-aware page, served from GitHub Pages, that changes its hero, CTA, resources,
FAQ set and concierge opening from the URL it was opened with.

**Live:** `https://jesse-docket.github.io/testmu-events/` (custom domain target:
`events.testmuai.com` — see `CNAME`; the DNS record is not set up by this repo).

## Campaign URLs

| Motion | URL |
|---|---|
| Conf '27 registration | `/?utm_source=event-email&utm_medium=event-concierge&utm_campaign=testmuconf-2027&utm_content=registration&event_program=testmuconf-2027&event_phase=registration&event_topic=agentic-engineering` |
| Conf '26 archive | `/?event_program=testmuconf-2026&event_phase=archive` |
| Learning / certification | `/?event_program=testmu-learning&event_phase=learning` |
| Fallback | `/` (or any unknown `event_program`) |

Add `&debug=1` to log every analytics event to the console.

## Parameters read

`utm_source`, `utm_medium`, `utm_campaign`, `utm_content`, `utm_term`, `event_program`,
`event_phase`, `event_topic`, `event_content`, `event_cta`, `intent`, `ctx`.

`event_program` wins; `utm_campaign` is the fallback. Unknown or missing values resolve to the
"Explore TestMu events" state and never break the page. Values are length-capped and
character-filtered on read, and are only ever used as data — never written to the DOM as HTML.

## What the concierge receives

The page derives a small envelope and passes it via `AISeller.setContext()`:
`event_program`, `event_phase`, `event_topic`, `event_faq_set`, `campaign_source`,
`campaign_cta`, `visitor_intent`. These are the seven Dynamic Context Ingestion fields on the
agent — see `docs/dci-and-pca-fields.md`.

Raw UTM values, the opaque `ctx` token, and anything identifying are **not** passed to the
agent and are never rendered on the page.

## Registration (Conf '27)

The primary CTA opens `https://www.testmuai.com/testmuconf-2027/` in an iframe modal with every
campaign parameter preserved. If the frame does not report a load within 4.5s, or a
`frame-ancestors` CSP violation fires, the modal swaps itself for a "Continue to registration"
link to the same parameterised URL. An "Open in new tab" link is present from the start.

**Known limitation:** Chrome fires `load` even when it renders its own "refused to connect"
page, so timeout detection is the reliable signal and CSP detection is best-effort. The
always-visible new-tab link is the guaranteed escape hatch.

A `postMessage` from `https://www.testmuai.com` with `type: "testmu:registration:complete"` (or
`"registration_complete"`) fires the `registration_completed` analytics event. TestMu has not
implemented that message yet — until they do, completion is not tracked here.

## Analytics events

`page_loaded`, `campaign_resolved`, `agent_opened`, `agent_starter_selected`, `faq_opened`,
`resource_clicked`, `secondary_cta_clicked`, `registration_opened`,
`registration_frame_blocked`, `registration_completed`, `archive_opened`, `learning_opened`,
`explore_cta`.

Each pushes to `window.dataLayer` and to PostHog if present. No PII, and never the `ctx` token.
`window.testmuTrack(name, props)` is exposed for ad-hoc events.

## Editing campaigns

Everything a marketer needs is in the `CAMPAIGNS` object at the top of the script block in
`index.html`: hero copy, facts, CTA, resources and the versioned FAQ set. Bump `faqSet` when the
approved answers change so the agent's `event_faq_set` context stays truthful.

## Not built here (deliberately)

GitHub Pages is static, so none of the following exists yet and none of it is faked:

- Validation of the signed, short-lived `ctx` token
- The secure context service that resolves `ctx` to a known recipient
- CRM writes and account enrichment
- Server-side consent handling

The page reads `ctx` only to preserve it on outbound TestMu links. Until a backend exists,
every visitor is treated as unknown. See `docs/backend-requirements.md`.

## Repo contents

```
index.html                                    the whole site (self-contained)
CNAME                                         events.testmuai.com
prompts/kane-campaign-concierge-MAIN-PROMPT.txt
prompts/kane-campaign-concierge-PROMPT-PARTIAL.txt
docs/kane-source-config.md                    what Kane was, captured read-only
docs/dci-and-pca-fields.md                    field names + char-checked descriptions
docs/backend-requirements.md                  what still needs a server
```
