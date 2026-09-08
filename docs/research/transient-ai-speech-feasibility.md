# Hosted AI and speech against the transient-content policy

**Status:** no hosted route is established as policy-compliant

**Research date:** 2026-09-08

**Scope:** a narrow recheck of OpenAI, Azure, and AWS for English/French speech-to-Quote assistance. This report applies the security resolution in [issue #16](https://github.com/lbise/easy-quote/issues/16#issuecomment-5580585487), not a weaker "no training" rule. It does not choose a provider, create an account, or test with customer data.

## The rule being tested

A usable hosted route must let Easy Quote enforce all of these without putting content in a provider's ordinary logs, durable job system, or backup:

- delete audio after transcription, including failure;
- delete the transcript and AI input/output after completion, failure, or cancellation;
- expire all server-side temporary content within 15 minutes, including stalled work;
- prohibit training and unrelated reuse;
- keep default processing, logs, support, and subprocessors in Switzerland or the EEA.

"No training" is one small part of that list. It says nothing about an abuse log. A regional endpoint is also not proof that support and every subprocessor stay in-region.

## Result at a glance

| Route | English/French and region | Content-retention result | Decision status |
| --- | --- | --- | --- |
| OpenAI API | The `eu.api.openai.com` Europe region covers Switzerland and the EEA and lists `/v1/audio/transcriptions` and `/v1/chat/completions` with regional processing. | It needs approved ZDR or MAM plus a modified-retention amendment. ZDR avoids the named endpoint state, but OpenAI reserves safety-retention exceptions and excludes system data, including support requests, from residency. | Not established. The entitlement and contract must close the exception and country gap. |
| Azure Speech plus Azure OpenAI | Azure Speech has real-time transcription in `switzerlandnorth`; its language table includes `fr-CH`, `fr-FR`, `en-US`, and other English/French locales. | Real-time Speech says audio is memory-only and that it retains no customer data. Azure OpenAI requires approved modified abuse monitoring to stop stored human-review samples. Microsoft still documents limited EU Data Boundary transfers outside the boundary. | Closest technical candidate, but not established until entitlement, exact model availability, cancellation handling, support, and subprocessor terms are verified. |
| Amazon Transcribe plus Bedrock | Transcribe has batch and streaming endpoints in `eu-central-2` Zurich; Bedrock also has a Zurich runtime endpoint. English and French have batch/streaming language entries. | Transcribe says it may temporarily store content to improve analysis models and directs deletion requests to Support. An Organizations opt-out only removes content not required to provide the service. This cannot prove deletion on completion, failure, or cancellation. | Fails the strict speech test on public evidence. Bedrock ZDR does not repair Transcribe. |

None of these is a green light. The Azure route is worth a procurement check because the public technical controls line up better than the others. It still has real contractual holes.

## Concrete configurations and gaps

### OpenAI API

**Proposed narrow configuration, conditional on entitlement.** Use a Europe-residency project and send only synchronous requests to `https://eu.api.openai.com/v1`:

1. `POST /audio/transcriptions`, model `gpt-4o-mini-transcribe-2025-12-15`, for recorded English or French audio.
2. `POST /chat/completions`, pinned `gpt-4o-mini-2024-07-18`, with Structured Outputs and `store: false`, for the transcript-to-draft step.
3. Do not use Assistants, Threads, Files, Batches, background mode, audio-output chat, or any tool that persists application state. Keep the uploaded audio and returned transcript only in process memory. A 15-minute watchdog must abort the request and discard the operation locally.

The data-controls table lists `/v1/audio/transcriptions` with no default abuse-monitoring or application-state retention and marks it ZDR-eligible. It lists Chat Completions as ZDR-eligible, but default abuse-monitoring logs retain content for up to 30 days. OpenAI says ZDR excludes customer content from those logs and forces `store` false for Chat Completions. MAM excludes customer content from abuse logs, but does not impose `store: false`; ZDR is the less error-prone choice for this route. [OpenAI data controls](https://developers.openai.com/api/docs/guides/your-data)

The Europe residency table names `eu.api.openai.com`, includes Switzerland and the EEA, and explicitly lists both required endpoints as regionally processed. Non-US residency requires approval for abuse-monitoring controls and a Modified Retention amendment. It is not a self-service promise. [OpenAI data residency](https://developers.openai.com/api/docs/guides/your-data#data-residency)

**Why this does not pass yet.** OpenAI says it may make a model ineligible for ZDR or MAM and retain content under its Safety Retention policy when it identifies severe risk. Its residency page says system data can be processed and stored outside the selected region. That includes support requests, analytics, billing, usage data, and the structured-output schema. The public pages do not prove a maximum in-memory lifetime, deletion on failed or cancelled requests, or that every support/subprocessor path stays in Switzerland or the EEA. Those are policy requirements, not implementation details to wave away.

**What must be obtained before using customer data.** Written confirmation in the executed agreement that the named audio and Chat Completions models are ZDR-eligible in Europe; the project is approved and configured as ZDR; a Safety Retention exception does not conflict with the agreed policy or has an agreed handling rule; and content-bearing logs, support access, and subprocessors stay in Switzerland/EEA. Also capture the project configuration and issue a non-customer-data cancellation/failure test that shows no retrievable content after the 15-minute deadline.

**Price signal, not a budget guarantee.** OpenAI currently publishes `gpt-4o-mini-transcribe` at an estimated USD 0.003/minute, and `gpt-4o-mini` at USD 0.15 per million input tokens and USD 0.60 per million output tokens. [OpenAI pricing](https://developers.openai.com/api/docs/pricing) [model page](https://developers.openai.com/api/docs/models/gpt-4o-mini)

For an illustration only, 100 voice operations per month, three minutes each, plus 4,000 input and 1,000 output tokens per operation is 300 minutes, 400,000 input tokens, and 100,000 output tokens. At the published USD units this is USD 1.02 before any residency uplift, taxes, platform charges, retries, or the rest of the application. If the non-AI infrastructure were CHF 90 and USD 1 were conservatively treated as CHF 1, the example totals CHF 91.02. It does not prove either entitlement affordability or a CHF 100 total monthly bill.

### Azure Speech plus Azure OpenAI

**Proposed narrow configuration, conditional on entitlement.** Create both resources in `switzerlandnorth` and use:

1. Azure Speech real-time speech-to-text with an explicit recognition locale. Use `fr-CH` for Swiss French and one selected English locale such as `en-US` or `en-GB`. Do not use batch transcription, diarization, custom speech training, or Microsoft-hosted transcription output.
2. A *regional Standard* Azure OpenAI deployment, not Global or Data Zone, and its stateless Chat Completions API with Structured Outputs. `gpt-4.1-mini` is a candidate because the model documentation lists Chat Completions and Structured Outputs. The exact model version must be checked in the `switzerlandnorth` deployment picker before selection. The public material read for this report establishes the features, not that this precise regional deployment is presently available.
3. Obtain approved modified abuse monitoring and verify the resource capability `ContentLogging: false` through the portal or management API. Do not use Responses, Threads, Assistants, stored completions, Files, batch, fine-tuning, or "on your data" features.

Azure Speech documents real-time STT as server-memory processing with no data at rest. Its retention section says Microsoft does not retain data for real-time STT. `switzerlandnorth` supports real-time transcription, and the language table includes Swiss French, French, and English locales. [Speech data privacy](https://learn.microsoft.com/en-us/legal/cognitive-services/speech-service/speech-to-text/data-privacy-security) [Speech regions](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/regions) [Speech languages](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-support?tabs=stt)

For Azure OpenAI, Microsoft says a Standard deployment processes prompts and responses within the customer-specified geography, while Global and Data Zone can process more broadly. The models are stateless and do not train the base model. Those facts alone do not solve retention: by default, flagged prompts and completions can be retained for human review. Modified abuse monitoring is an application for customers who meet additional Limited Access criteria. After approval, the resource can show `ContentLogging: false`. [Azure OpenAI data privacy](https://learn.microsoft.com/en-us/azure/ai-foundry/responsible-ai/openai/data-privacy) [abuse monitoring](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/concepts/abuse-monitoring) [Azure model capabilities](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models)

**Why this does not pass yet.** Modified abuse monitoring is eligibility-based, not a default control. The public documentation does not give a binding deletion deadline for a request that fails or is cancelled, even though stateless processing and disabled content logging are encouraging. The Microsoft EU Data Boundary states that some Customer Data, personal data, and Professional Services Data can still transfer outside the boundary in limited circumstances. That is directly at odds with an unqualified Switzerland/EEA-only rule for support and subprocessors. It also needs confirmation that the selected Azure services, resource-manager configuration, subscription, and support arrangement are in scope.

**What must be obtained before using customer data.** Microsoft must approve modified abuse monitoring for the actual subscription and the deployment must evidence `ContentLogging: false`. Confirm the selected regional Standard model and version in `switzerlandnorth`, the applicable DPA and subprocessor list, EU Data Boundary scope, support-access countries, and each stated exception. Obtain a written answer for content removal on completion, failure, cancellation, and a stalled request beyond 15 minutes. The application still needs an in-memory operation design, an abort path, content-free logs, and no durable retry queue.

**Budget.** Azure bills Standard Azure OpenAI by input/output tokens and Speech by usage, but the public pricing page says prices vary by agreement, date, and currency. This research did not obtain a dated Switzerland North calculator export for both services or a price for the modified-abuse-monitoring entitlement. The CHF 100 target is therefore unproven for this route, even if low pilot usage may be cheap. [Azure OpenAI pricing](https://azure.microsoft.com/en-us/pricing/details/azure-openai/)

### AWS Transcribe plus Amazon Bedrock

**The superficially plausible configuration.** The regional endpoints exist: streaming Transcribe at `transcribestreaming.eu-central-2.amazonaws.com` and Bedrock Runtime at `bedrock-runtime.eu-central-2.amazonaws.com`. For generation, Bedrock documents an account or project `data_retention_mode: none`; under that mode it says AWS writes no request or response data to durable storage and does not share it with the model provider. It blocks models that require retention. [Transcribe endpoints](https://docs.aws.amazon.com/general/latest/gr/transcribe.html) [Bedrock retention](https://docs.aws.amazon.com/bedrock/latest/userguide/data-retention.html)

That generation control is meaningful. It is not evidence for the speech half. Amazon Transcribe says it may temporarily store content to improve analysis models and directs a customer who wants deletion to open a Support case. Batch is plainly incompatible because it requires S3 input and puts all transcripts in S3. Streaming avoids the application S3 objects, but the same temporary-storage statement remains. [Transcribe input and output](https://docs.aws.amazon.com/transcribe/latest/dg/how-input.html)

AWS Organizations can apply an AI-services opt-out policy. But AWS says the opt-out deletes historical content only when it is not required to provide service functions. It does not prove immediate deletion, a 15-minute cap, failed/cancelled request handling, or elimination of content needed by Transcribe. [AI services opt-out policy](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_ai-opt-out.html)

**Result.** Do not send Easy Quote audio to Transcribe under this policy on the current public evidence. A Bedrock ZDR model cannot make a Transcribe retention promise disappear. The exact Bedrock model that supports `none` in Zurich, its structured-output capability, English/French quality, and Switzerland/EEA-only support/subprocessor treatment are also unverified. Those are procurement and test questions, not a basis for selecting AWS.

## Controls Easy Quote would still own

A provider setting does not implement the security resolution. Any conditional pilot must reject work unless these are in place:

- browser audio stays in memory and is streamed or uploaded directly to the chosen request. On success, error, abort, timeout, and disconnect, clear the buffer and discard its key or reference;
- make the speech and model calls synchronous. Do not put audio, transcript, prompt, response, or a serialised request in a queue, cache, tracing system, analytics product, crash report, or backup;
- cap the operation with a server-side 15-minute timer. On expiry, abort upstream work where the API supports it, clear local memory, and record only content-free outcome metadata;
- treat retries as a new user operation with new input. A retry must not retrieve prior content;
- send only a minimum transcript and operation-relevant Quote context. Keep the accepted Quote edits, never a copy of the transient provider exchange;
- before production, run a redacted synthetic test for success, provider failure, caller cancellation, timeout, and a stalled request. Preserve configuration evidence and content-free test results.

These controls reduce Easy Quote's own exposure. They cannot fill a provider's missing deletion, support-country, or subprocessor promise.

## Narrow next verification

The useful next step is one short procurement questionnaire, not another broad vendor search. Ask the provider that can answer Azure's open points first for an executed agreement or written addendum covering the named region, services, and model version:

1. Is modified abuse monitoring approved for this subscription, and can `ContentLogging: false` be evidenced for the exact resource?
2. Does the named regional Standard model exist in `switzerlandnorth` and support Structured Outputs on the chosen API version?
3. For audio, transcript, prompt, and completion, what is the maximum provider-side lifetime after success, failure, caller cancellation, and a hung request? Are any copies in backups, queues, diagnostic logs, or support systems?
4. Which processing, log, support, and subprocessor countries apply to this configuration? List every exception to Switzerland/EEA processing.
5. What exact Switzerland North price applies to Speech, the model, network, logging, and the required entitlement? Provide a dated CHF estimate at agreed pilot volumes.

If that answer cannot give deletion and country commitments without exceptions that the policy accepts, the honest outcome is no hosted voice route under the present policy. Manual text remains available by the security resolution; it does not need a privacy exception.

## Source notes

All cited sources are provider-owned documentation or pricing pages, accessed 2026-09-08. Public documentation changes. Pricing, regional model availability, entitlement decisions, DPA terms, support arrangements, and subprocessors must be captured again at procurement. The earlier report on `research/ai-speech-pdf-options` was used as a shortlist only; this report rechecked the provider claims above against current first-party material.
