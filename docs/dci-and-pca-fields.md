# Kane [Campaign Concierge] — DCI fields and Post Call Analysis fields

Every description below is character-checked against the dashboard's silent 200-character
truncation limit. Field names are lowercase snake_case and match exactly what the main
prompt's `<event_campaign_context>` block and the site's `dciEnvelope()` function use — if
you rename one, rename it in all three places.

## Dynamic Context Ingestion (Agent tab → Dynamic Context Ingestion → on)

| # | Field name | Chars | Context for Agent |
|---|---|---|---|
| 1 | `event_program` | 190 | Which TestMu event motion the visitor came from: testmuconf-2027, testmuconf-2026, testmu-learning or explore. Sets which approved FAQ and resource set you may answer from. Never mention it. |
| 2 | `event_phase` | 151 | Where the visitor is in that motion: registration, archive, learning or explore. Shapes what a useful next step is. Background only — never restate it. |
| 3 | `event_topic` | 165 | The theme the campaign was built around, e.g. agentic-engineering. Use it to choose examples and session recommendations. May be empty. Never name it to the visitor. |
| 4 | `event_faq_set` | 150 | Version of the approved answer set live on this page, e.g. conf-2027-v1. Answer only from this set; if something is not in it, say you do not have it. |
| 5 | `campaign_source` | 159 | Broad channel the visitor arrived through, e.g. event-email, paid, community, direct. Background only. Never mention the channel, the campaign or any tracking. |
| 6 | `campaign_cta` | 156 | The primary action this page is asking for, e.g. boarding-pass. Point to the on-page button; never collect registration details or describe the form fields. |
| 7 | `visitor_intent` | 169 | Coarse hint only: learn, try, evaluate or unknown. If what the visitor says contradicts it, follow the visitor. It never on its own qualifies anyone for a human handoff. |

**Deliberately NOT passed to the agent:** raw `utm_*` values, the opaque `ctx` token,
email addresses, company or account identity, CRM notes, account-owner names, lead scores,
opportunity data, IP-derived location. The site derives `campaign_source` from
`utm_source` and passes nothing else — see `dciEnvelope()` in `index.html`.

Kane (production) has DCI **off** with zero variables. The existing ABM agent
Kane (Clone) has three (`company_name`, `industry`, `key_pain`) — unrelated, and untouched.

## Post Call Analysis (Analysis and Actions tab)

Kane's three existing rules are replicated verbatim, then four campaign rules are added.
All are `replace_always`, matching Kane.

### Replicated from Kane (do not reword)
1. **Business Email** — string — "Review the conversation and output any email addresses shared by the user that ARE NOT personal email addresses (e.g. using free email providers like gmail.com, yahoo.com, etc.)"
2. **Existing User/Customer** — boolean — "Review the transcript to see if the user mentions already being a TestMu customer or user. This can also potentially be inferred if they're asking detailed setup or in-product questions. Return "True" if they are likely a customer/user, "False" if they are not."
3. **Topic of Interest** — string, permitted values: Agent to Agent Testing · Online Browser Testing · Native App Testing · Real Device Cloud · Selenium Testing · Cypress Testing · Appium · HyperExecute · Visual Regression Cloud · Accessibility Testing · Test Manager · Enterprise and/or On-premise Selenium Grid · Professional Services · Other

### New — campaign-specific
4. **Event Program** — string, permitted values: `testmuconf-2027` · `testmuconf-2026` · `testmu-learning` · `explore` · `Unknown`
   > Output the TestMu event motion this conversation belonged to, based on the campaign context the agent was given and what the visitor discussed. Use Unknown if it cannot be determined.

5. **Event Outcome** — string, permitted values: `Registered` · `Registration Opened Not Completed` · `Found Content` · `Learning Path Chosen` · `Browsed Only` · `Unresolved Question`
   > Output what the visitor actually achieved in this conversation. Registered only if the visitor states they completed registration. Browsed Only if they asked nothing that was resolved.

6. **Qualified Handoff** — boolean
   > Return True only if the visitor explicitly said they are evaluating TestMu AND raised at least one of: an active comparison, an integration requirement, a team rollout, a governance, security or scale constraint, or a request to speak to an expert. Event interest alone is False.

7. **Stated Need** — string
   > Output, in the visitor's own words where possible, the concrete problem or requirement they described — the flaky suite, the slow pipeline, the compliance deadline, the team they are enabling. Leave empty if they never stated one.

**Character check:** instruction text on Post Call Analysis rules is not subject to the
200-char DCI limit, but each is kept short deliberately — long instructions here produce
noisier extractions.

## Analysis and Actions — what is NOT being turned on
Salesforce is the only CRM connected on the TestMu tenant. Per the plan, only the
consented summary, stated need, campaign attribution and follow-up request should ever
reach the CRM. That mapping is a RevOps configuration action, not something the prompt or
these fields can do on their own — flagging it rather than enabling a sync and hoping.
