# Google Gemini free inference for synthetic development

**Status:** a narrow synthetic-text smoke test is plausible only after the owner confirms the applicable account and regional terms. It is not a production route or a provider approval.

**Research date:** 8 September 2026

**Scope:** Gemini Developer API and Google AI Studio first. This report does not identify the Google service used in the earlier prototype. It distinguishes that service from Google Cloud and Vertex AI trial credits, and does not create an account, enable billing, make an API call, or run a benchmark.

## Decision

Use no real Easy Quote content on an unpaid Google service. A later, developer-operated smoke test may use only wholly fictional English/French text and the two stable Developer API models below. It should be a synchronous, stateless request with a provider-neutral action-batch fixture. Do not expose that API client to an Artisan or Customer. Do not test audio in this first step.

This is a proposed synthetic-development exception to the production policy, not an accepted change to it. The existing policy still requires transient content, no secondary use, deletion on every completion/failure/cancellation path, and Switzerland/EEA treatment of processing, logs, support, and subprocessors before real Customer, Quote, or voice content can go to a provider. Public Gemini terms do not establish those conditions.

## What "free Google" can mean

These routes have different billing and data terms. The earlier prototype gives no evidence of which one was used.

| Route | What is free | What it does not establish |
| --- | --- | --- |
| Gemini Developer API / AI Studio | AI Studio interaction and unpaid Gemini API quota are "Unpaid Services." The pricing page currently lists zero token price for the free tier on the 2.5 models considered below. | It is not Vertex AI. Outside the EEA, Switzerland, and UK, Google may use submitted content and responses to develop its products, and human reviewers may read, annotate, and process API input and output. Google's own instruction is not to submit sensitive, confidential, or personal information. |
| Developer API with active Cloud Billing | A Gemini API request is a "Paid Service" only through a Cloud Project with an active billing account. AI Studio is also treated as a Paid Service when its account has access to a Cloud Project with active Cloud Billing or is a Workspace enterprise account. Paid pricing then applies after any listed free allowance. | No-training is not zero retention or a Swiss/EEA-only commitment. Google says it logs paid prompts and responses for a limited safety/security period and may transiently store or cache them in any country where Google or its agents have facilities. |
| Google Cloud Free Trial / Vertex AI | Google Cloud offers eligible new users a separate 90-day, USD 300 Welcome credit. It requires a valid payment method and is usable only for products covered by that trial. | A credit is not unpaid Developer API quota, nor a data-processing promise. This report did not verify whether any particular Vertex AI model, endpoint, region, or SKU is covered, or its terms and retention. Treat Vertex AI as a separate provider route requiring its own assessment. |

For a Swiss, EEA, or UK account, the Developer API Additional Terms say the paid-service data-use terms apply to *all* services, including AI Studio and unpaid Gemini API quota. That narrows the usual unpaid-service training/review warning. It does not override the same terms' rule that API clients made available to users in Switzerland, the EEA, or UK may use only Paid Services. It also does not turn the paid-service retention and any-country caching language into the strict production rule.

The term page does not explain enough to determine the account or user location that controls those clauses, or whether a particular internal developer test receives the Swiss/EEA rule. Confirm that before relying on it. Until then, fictional inputs remain the safe boundary.

## Access, price, and quota limits

The current public rate-limit page defines the Free tier as an active project or free trial. Limits apply per project, not per API key; daily requests reset at midnight Pacific time. It says that model and account limits vary, active limits are shown in AI Studio, specified limits are not guaranteed, and rate limits can change with account tier and standing. It does not publish a fixed current Free-tier RPM, TPM, or RPD number for either candidate. This report therefore makes no numeric quota promise.

Current published paid Standard prices are a useful later budget baseline, not a quote. `gemini-2.5-flash` costs USD 0.30 per million text/image/video input tokens, USD 1.00 per million audio input tokens, and USD 2.50 per million output tokens. `gemini-2.5-flash-lite` costs USD 0.10, USD 0.30, and USD 0.40 respectively. Both list free-tier token prices as free of charge. Google can change pricing and availability, so save the dated pricing and actual displayed quota before any test.

## Narrow model candidates

| Model | Why it is a candidate for fictional text smoke tests | Limits that matter |
| --- | --- | --- |
| `gemini-2.5-flash` | The model page calls this stable. It accepts text, image, video, and audio; returns text; and lists structured outputs and function calling as supported. | Its Stable endpoint is `gemini-2.5-flash`; the page lists a 1,048,576-token input and 65,536-token output limit. Audio generation and Live API are not supported. |
| `gemini-2.5-flash-lite` | Also stable. It accepts text, image, video, audio, and PDF; returns text; and lists structured outputs and function calling as supported. It is the lower paid-price comparator. | Its Stable endpoint is `gemini-2.5-flash-lite`; it has the same published input/output limits. Audio generation and Live API are not supported. |

Google describes a stable model string as a specific stable model that usually does not change. It says `latest` can be hot-swapped, preview models have tighter limits and at least two weeks' deprecation notice, and experimental endpoints can change. Use the two stable strings above for a repeatable smoke test. Record the string, source-page date, SDK/API version, and test date. Do not use a `latest`, preview, or experimental alias for the baseline.

The model pages establish text and audio input support. They do not separately list English/French support, publish an English/French Quote-extraction accuracy guarantee, or make a French transcription-quality claim. Structured output supplies a JSON Schema subset and predictable syntax, not correct facts. The application must still reject unsupported identifiers, validate batch preconditions, and leave missing commercial facts blank. That is consistent with the settled non-invention and atomic batch rules; it does not decide the still-open Quote contract.

## Keep audio out of the first test

Both candidates accept audio, and Gemini's audio-understanding guide permits audio input and JSON-shaped results. That is not proof of a production-ready transcription route. The guide combines channels to mono and describes audio understanding, not the approved immediate-deletion, failure/cancellation, retention, location, support, or bystander controls. Google now also lists dedicated `gemini-3.5-transcribe` and `gemini-3.5-transcribe-live` models, which are a separate model family from the two candidate text models.

Do not infer that free text evaluation approves free audio processing. If audio is evaluated at all, use only wholly synthetic, non-identifying audio in an isolated later experiment. It needs the same separate provider-policy and transcription-quality review that real voice capture needs. No actual Artisan voice, Customer voice, site recording, business recording, or "sanitized" historical audio belongs in the free-service test.

## Bounded evaluation method

### 1. Synthetic text smoke test

After the owner confirms the account/term boundary, send a small set of developer-authored fictional requests directly to one stable model at a time. Do not wire a browser, mobile app, or production service to it. Do not use Files API, cached context, tuning, grounding, batch processing, or a durable queue.

Each fixture should have:

- an invented Quote state with made-up names, addresses, telephone numbers, materials, and amounts that cannot identify or describe a real person or Artisan Business;
- a short English or French instruction and expected provider-neutral action batch, including stable fictional Quote Line identifiers;
- explicit expected blanks for an omitted quantity, unit, price, material, Customer, VAT treatment, validity, or terms;
- cases for one add, update, removal, multiple actions, ambiguous target, invented fact, invalid identifier, and malformed structured output.

Measure only whether the response parses, validates as one complete batch, preserves stated facts, leaves required unknowns blank, and makes no mutation for ambiguity. Retain test fixtures and results only if they remain wholly fictional. Keep credentials out of source control and log only synthetic test metadata. This is a smoke test, not a production integration.

### 2. Later provider comparison

Only after the owner chooses candidates for comparison, run the same frozen fixtures through small provider-specific callers. A common fixture format and a normaliser for each provider are enough. Do not build a general multi-provider framework or live production route.

For every run, record the pinned model/version, region and tier shown by the provider, timestamp, request and response token counts where available, wall-clock latency, failures/refusals, and the published plus observed cost. Score bilingual meaning, non-invention, missing-fact handling, multi-action atomicity, identifier/precondition correctness, and cost per usable result. The later validation ticket owns thresholds and acceptance gates. This report does not select a provider.

### 3. Separate production approval

Do not reuse the smoke-test result as privacy or production evidence. Before real content can leave Easy Quote, a candidate must separately show an approved contract and configuration for no secondary use, provider-side retention/deletion after success, failure, cancellation, and timeout, no content-bearing logs or backups, applicable Switzerland/EEA processing and support/subprocessor treatment, and cost and quality at the agreed workload. The prior hosted-AI research found no public-evidence route that meets the present strict transient-content policy.

The repository's sanitized reference corpus is not automatically fit for an unpaid service. Sanitization does not prove that it lacks confidential, personal, or business-derived content, nor does it accept Google's terms on behalf of a source. Keep it out of this experiment. Use newly written fictional text and synthetic audio only.

## Open facts and ownership

- The user has not identified the earlier Google product, account country, billing state, or model. This research does not guess.
- No public source read here commits to a numeric free quota, English/French Quote quality, or strict provider deletion and regional handling for this route.
- The user has not accepted a synthetic-development exception. This report proposes one narrow exception only.
- Issue [#18](https://github.com/lbise/easy-quote/issues/18) chooses a provider. Issue [#15](https://github.com/lbise/easy-quote/issues/15) chooses benchmark thresholds. The production transient-content policy remains owned by [#16](https://github.com/lbise/easy-quote/issues/16#issuecomment-5580585487).

## Primary sources

All sources were accessed on 8 September 2026. Product pages, quota displays, pricing, and terms change.

- [Gemini API Additional Terms of Service, effective 23 March 2026](https://ai.google.dev/gemini-api/terms): Unpaid/Paid Services; human review; EEA, Switzerland, and UK clauses; billing; paid-service logging and caching.
- [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing): current free/paid token prices and "Used to improve our products" rows.
- [Gemini API rate limits](https://ai.google.dev/gemini-api/docs/rate-limits): project scope, daily reset, account-specific limits, Free tier qualification, and non-guarantee.
- [Available regions for Google AI Studio and Gemini API](https://ai.google.dev/gemini-api/docs/available-regions): Switzerland is listed as an available region. Availability does not establish data residency.
- [Gemini 2.5 Flash](https://ai.google.dev/gemini-api/docs/models/gemini-2.5-flash) and [Gemini 2.5 Flash-Lite](https://ai.google.dev/gemini-api/docs/models/gemini-2.5-flash-lite): model codes, stable status, input types, capabilities, and limits.
- [Gemini models and version patterns](https://ai.google.dev/gemini-api/docs/models/gemini): stable, preview, latest, and experimental behavior; dedicated transcription models.
- [Structured output](https://ai.google.dev/gemini-api/docs/structured-output) and [audio understanding](https://ai.google.dev/gemini-api/docs/audio): JSON Schema subset and generic audio-input behavior.
- [Google Cloud Free Program](https://cloud.google.com/free/docs/free-cloud-features): distinct 90-day USD 300 free-trial credit, eligibility, payment method, and covered-products limits.
- [Issue #16 security and data-lifecycle resolution](https://github.com/lbise/easy-quote/issues/16#issuecomment-5580585487), [issue #17 architecture resolution](https://github.com/lbise/easy-quote/issues/17#issuecomment-5581781011), and [issue #27 hosted AI/speech research](https://github.com/lbise/easy-quote/issues/27#issuecomment-5581984638): Easy Quote's current policy and action-batch constraints.
