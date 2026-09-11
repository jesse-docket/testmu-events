# Kane (source agent) — captured configuration

Captured 2026-09-11 from `GET /api/v2/web/agents/info/<id>` and
`GET /api/v2/web/post-call-rules?agent_id=<id>`. Read-only. No UI controls were
clicked on the source agent; no PUT/POST/PATCH was issued against it.

- Source agent: **Kane** — `5a13adeb-10f9-4938-9372-a60cc486ab71` (Active)
- Existing ABM clone (NOT touched): **Kane (Clone)** — `4c492746-8330-4f2f-8f8c-aa72a384cd7c`

## Agent tab
| Setting | Kane |
|---|---|
| Description | serve as the primary point of contact for Testmu prospects and clients seeking greater clarity on their products and services. |
| Agent type | Voice |
| Interaction mode | `audio_to_audio` (Voice & Text) |
| Widget interface mode | `floating` |
| Persona name / greeting / avatar | null / null / null |
| Company avatar enabled | true |
| Work hours | off |
| Agent-to-human handoff (calendar) | off |
| Meeting booking | off |
| Callout config | enabled, no timeout |
| Consent config | mode `existing`, event consent not required |
| FAQ | disabled |

### Agent CTAs (`cta_config.is_active = true`)
1. **default** — "Book a Demo" · behaviour `iframe` · `https://www.testmuai.com/docket-form/` · instructions: "Default CTA" · auto-trigger false
2. **cta-1778199731098** — "Start Chat Session" · behaviour `new_tab` · `https://www.testmuai.com/chat-session/` · auto-trigger false · full human-handoff trigger ruleset (see prompts/ for verbatim copy)

### Email Validation
`allow_free: true`, `allow_role: true`, `allow_disposable: true`, `check_deliverability: false`

### Dynamic Context Ingestion
`is_active: false`, `variables: []` — Kane has **no** DCI fields. (The ABM clone has 3: `company_name`, `industry`, `key_pain`; schema is `{name, description}`.)

## Knowledge tab
- Knowledge Base sources: configured (Customize Sources)
- Slides: **on** — `TestMu AI Introduction (ENT) .pptx.pdf` (enabled), `TestMu Pricing Details.pdf` (disabled)
- Videos: null (off)

## Analysis and Actions
CRM connection state: Salesforce **connected**; HubSpot / Marketo / Dynamics / Chili Piper / Zoom / Google Calendar / Outlook not connected.

Notifications: `is_active: false`; Slack platform entry active on channel `C0AQH6V5YCA`; new-lead alert on; custom instruction on — "Trigger an alert when anyone asks to talk to a human or actual person".

### Post Call Analysis rules (3, all active, merge `replace_always`)
1. **Business Email** — string — "Review the conversation and output any email addresses shared by the user that ARE NOT personal email addresses (e.g. using free email providers like gmail.com, yahoo.com, etc.)"
2. **Existing User/Customer** — boolean — "Review the transcript to see if the user mentions already being a TestMu customer or user. This can also potentially be inferred if they're asking detailed setup or in-product questions. Return "True" if they are likely a customer/user, "False" if they are not."
3. **Topic of Interest** — string with permitted values: Agent to Agent Testing, Online Browser Testing, Native App Testing, Real Device Cloud, Selenium Testing, Cypress Testing, Appium, HyperExecute, Visual Regression Cloud, Accessibility Testing, Test Manager, Enterprise and/or On-premise Selenium Grid, Professional Services, Other

## Widget tab
| Setting | Kane |
|---|---|
| Theme | dark |
| Experience | modal |
| Button text | Ask Me Anything |
| Welcome text | (empty) |
| Sounds | off |
| Position | bottom_center |
| Halo | on, brightness 100 |
| Privacy policy | off |
| Progressive unmute | off |
| Colors | primary `#f3f2ee` · middle `#d6d5d0` · secondary `#b8b7b2` |
| Whitelist domains | **none** |
