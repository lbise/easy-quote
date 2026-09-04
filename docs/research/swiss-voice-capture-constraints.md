# Swiss voice-capture constraints for Easy Quote

Status: research for issue [#24](https://github.com/lbise/easy-quote/issues/24), 4 September 2026. This note is a product and engineering recommendation, not Swiss legal advice. It assumes the MVP described in [#11](https://github.com/lbise/easy-quote/issues/11): an Artisan deliberately holds a phone push-to-talk control, recording stops before processing, a transcript is shown briefly, successful capture changes the working Quote directly, and failure does not change it.

## Bottom line

Easy Quote should treat an on-site voice capture as a narrow, high-risk exception, not as ambient dictation. Swiss criminal law can make recording a private conversation without the required participants' consent an offence. [Criminal Code, Arts. 179bis-179ter](https://www.fedlex.admin.ch/eli/cc/54/757_781_799/en) A visual recording state on the Artisan's phone and a privacy-policy link do not solve that problem. The safe MVP rule is simple: record only the Artisan's own dictation in a place where no other person can be heard, or after the Artisan has told every person who may be recorded what is happening and obtained their agreement. If the Artisan cannot make that confirmation, the app must not start capture. This is deliberately stricter than the minimum legal conclusion because whether an incidental voice is part of a protected private conversation is fact-sensitive.

The resulting product should never listen in the background, should show an unmissable active-recording state, should send audio only after Stop, should retain neither raw audio nor a transcript after the immediate result, and should require speech and AI providers to act under written processor terms with no training or secondary use. The Quote text that the Artisan accepts is a different record. It may remain in the Quote under the product's normal retention rules, but it must not be labelled or retained as a voice transcript.

## What Swiss law says

### Secret recordings and personality rights

- Swiss Criminal Code Article 179bis criminalises recording a private conversation with a sound-recording device without the consent of **all** participants. It also criminalises making such a recording available to a third party or using it. Article 179ter separately criminalises recording a conversation between other people without the consent of its participants, and passing that recording on or using it. The statutory words, and the question whether a given on-site exchange is "private" or a background speaker is a participant, need fact-specific legal analysis. Easy Quote should not rely on a narrow reading for customer homes, work sites, or nearby staff. [Criminal Code, Arts. 179bis-179ter](https://www.fedlex.admin.ch/eli/cc/54/757_781_799/en)
- Independently, a person whose personality rights are unlawfully infringed may seek judicial protection. The Civil Code identifies consent, an overriding private or public interest, or law as possible justifications. [Civil Code, Arts. 28 and 28a](https://www.fedlex.admin.ch/eli/cc/24/233_245_233/en) A criminal-law exception should therefore not be treated as permission to keep or reuse an incidental recording.

### Federal Data Protection Act

- The Federal Act on Data Protection applies to private persons processing personal data other than exclusively personal use. It defines personal data broadly as information relating to an identified or identifiable natural person, and processing as any handling of that data. An identifiable speaker's audio and the words attributed to them therefore need to be handled as personal data. [FADP, Art. 2 and Art. 5(a), (d)](https://www.fedlex.admin.ch/eli/cc/2022/491/en)
- A voice recording is not automatically "sensitive personal data". It can contain sensitive content, such as health or trade-union information, and voice data becomes biometric data when it is processed by a specific technical method to uniquely identify a natural person. Easy Quote must not create voiceprints, identify speakers, or infer characteristics from voices. [FADP, Art. 5(c)](https://www.fedlex.admin.ch/eli/cc/2022/491/en)
- Processing must be lawful, in good faith, and proportionate. Collection must have a specified and recognisable purpose. The controller must destroy or anonymise personal data as soon as it is no longer necessary for that purpose. These are the statutory basis for a one-purpose capture flow and for deleting audio and transient transcripts. [FADP, Art. 6](https://www.fedlex.admin.ch/eli/cc/2022/491/en)
- A private controller must inform the data subject adequately when it collects personal data, including at least the controller's identity, the processing purpose, and, when data is disclosed abroad, the recipient state or safeguards. The FADP also says a personality infringement is unlawful unless consent, an overriding interest, or law justifies it. That makes a clear notice necessary, but it does not turn a hidden or criminally prohibited recording into an acceptable one. [FADP, Arts. 19, 20, 30 and 31](https://www.fedlex.admin.ch/eli/cc/2022/491/en)
- Controllers must use privacy by design and privacy by default, and must apply technical and organisational measures appropriate to the risk. [FADP, Arts. 7 and 8](https://www.fedlex.admin.ch/eli/cc/2022/491/en) For this feature, defaulting to no microphone access and no retention is more defensible than trying to secure a broad audio archive.
- An Artisan Business that decides why and how it captures a site conversation will normally be the controller for that capture. Easy Quote may be a processor when it handles tenant content solely on the Business's instructions. The actual roles depend on the product terms and real decisions, so Swiss counsel should confirm them. A controller may use a processor only where the processor handles data as the controller may, confidentiality does not prohibit delegation, and the controller ensures data security. A processor needs prior authorisation for a sub-processor. [FADP, Art. 5(j)-(k) and Art. 9](https://www.fedlex.admin.ch/eli/cc/2022/491/en)
- Sending audio or transcript data abroad needs the FADP's conditions for disclosure abroad. The product must know each processor's processing locations and onward sub-processors before it can make the Article 19 notice or assess the Article 16 safeguard. [FADP, Arts. 16-18 and 19](https://www.fedlex.admin.ch/eli/cc/2022/491/en)

### Telecommunications

- The Telecommunications Act requires providers of telecommunications services to keep information relating to their subscribers' communications secret. A telecommunications service is transmission of information for third parties by telecommunications techniques. [TCA, Art. 3(b)-(c) and Art. 43](https://www.fedlex.admin.ch/eli/cc/1992/95/en) Easy Quote is likely an application using a telecoms provider rather than a telecoms provider itself, but that classification should not be assumed without counsel. In either case, the TCA does not displace the Criminal Code or FADP rules above. Encrypting transport and refusing provider secondary use are product requirements under the FADP risk standard, not a claim that Article 43 directly governs the app.

### Employment and sites with workers

- An employer must protect workers' personality and health, and may process worker data only to the extent it concerns the worker's suitability for employment or is necessary for performance of the employment contract. [Code of Obligations, Arts. 328 and 328b](https://www.fedlex.admin.ch/eli/cc/27/317_321_377/en) This matters when an Artisan Business employs the phone user or when a Customer's staff may be recorded. The relevant employer has its own duties. An individual Artisan cannot waive them for the Customer's staff.
- The Employment Act requires employers to take measures necessary to protect workers' health. Ordinance 3 forbids surveillance or control systems intended to monitor workers' behaviour at work. Where a system is needed for another reason, it must be designed and arranged so it does not impair workers' health or freedom of movement. [Employment Act, Art. 6](https://www.fedlex.admin.ch/eli/cc/1966/57/en); [Ordinance 3, Art. 26](https://www.fedlex.admin.ch/eli/cc/1993/18/en) SECO states the same prohibition in its official guidance on [technical workplace surveillance](https://www.seco.admin.ch/fr/surveillance-technique-au-poste-de-travail). A capture feature that records a coworker or Customer employee must never be sold, configured, or repurposed for attendance, performance, or behavioural monitoring.

## MVP constraints

These are conservative product requirements, not assertions that each is expressly dictated by a statute.

### Before recording

1. Make microphone permission opt-in. The initial voice-capture screen must say that recording is only for the Artisan's own quote instruction and that other conversations must not be captured.
2. Put a just-in-time gate in front of every recording. The Artisan must affirm one of these facts: "I am alone or no other voice can be heard" or "I have told every person who may be heard that Easy Quote will record their voice to transcribe my quote instruction, and they agreed." Do not offer a "continue anyway" path.
3. Provide a short, localisable notice that the Artisan can show or say: "I am recording a short quote instruction in Easy Quote. It will be transcribed to prepare this quote and then the audio will be deleted. Please tell me now if you do not agree." This is a practical notice aid, not proof of legally valid consent. The Artisan must move to a private place or use manual entry if anyone objects, cannot understand the notice, or is not in a position to agree.
4. Link the full privacy notice from that gate. It must identify the Artisan Business as controller where that is the agreed role, describe the narrow purpose, name Easy Quote and the speech/AI recipients, state any foreign processing location and safeguard, and give a contact route. Use the agreed roles in the product terms, not a generic claim that Easy Quote is always only a processor.
5. Do not make a Customer's contractual acceptance of a quote, a site sign, or the presence of a workplace policy stand in for a person's recording agreement. Ask Swiss counsel for the exact consent and notice wording, especially for homes, shared sites, minors, or vulnerable people.

### Recording state and transport

1. Keep the microphone off in idle, after Stop, when the app backgrounds, and after an interruption. No pre-roll buffer, wake word, automatic restart, always-listening mode, or spoken assistant interview.
2. While the button is held, show a full-width, high-contrast "Recording" state with elapsed time and an obvious Stop control. Keep it on screen until audio capture has ended. The OS microphone indicator is useful but is not the product's bystander notice.
3. Stop must terminate capture immediately. Process only after Stop. Do not stream audio to a provider while the button is held.
4. Let the Artisan cancel before processing. Cancellation deletes the local temporary audio and makes no Quote change.
5. If capture occurs around workers, the app must not offer speaker labels, employee analytics, monitoring dashboards, or any retention setting that could turn the feature into workplace surveillance.

### Audio, transcript, and Quote data

1. Keep raw audio in encrypted, app-private temporary storage only until the stopped capture is sent and acknowledged, cancelled, or fails. Delete it immediately in each case. A retry after a failure requires a new recording, not reuse of retained audio.
2. Contractually require the speech and AI providers to delete request audio and intermediate audio as soon as they return the result. Easy Quote must not store raw audio in application databases, backups, logs, crash reports, support attachments, analytics, or observability traces.
3. Show the transcript only in the immediate post-stop review. Clear it when the Artisan dismisses it, starts another capture, backgrounds the app, or after a short hard limit such as 60 seconds. Do not persist the transcript, token timings, confidence scores, speaker labels, or a transcript-to-Quote provenance record.
4. Apply accepted instructions directly to the working Quote as #11 requires. A Quote line may contain words the Artisan chose to use, but it is a Quote record, not a retained transcript. Processing failure, cancellation, ambiguity, or rejection must leave the Quote unchanged.
5. Do not use audio or transcript data to train models, evaluate product quality, develop prompts, create voice profiles, or market to the Artisan. Any future quality-review programme needs a separate design and legal review.

### Processor and security boundary

1. Before enabling a provider, record its legal entity, controller/processor role, sub-processors, processing and support locations, retention, deletion mechanism, encryption, access controls, incident notification, and whether it uses requests for model improvement. A provider that cannot answer these questions is not suitable for the MVP.
2. Put a data-processing agreement in place. It must limit each provider and approved sub-processor to transcription or instruction extraction for the request, prohibit training and secondary use, require confidentiality and appropriate security, require prompt incident notice, and require deletion. Obtain the controller's required authorisation for sub-processors.
3. Treat a provider's "zero retention" marketing statement as insufficient until its contract, API configuration, logs, abuse-monitoring path, and sub-processors confirm the actual lifecycle.
4. Use authenticated, encrypted transport, per-tenant access controls, and secret management. Restrict staff support access so support cannot retrieve live or historical raw audio or transient transcripts.

## Questions that block a final implementation decision

### For issue #16, security, privacy, and lifecycle

- Who is controller, processor, or independent controller for Quote data, microphone audio, transcript, security logs, and support requests? Put the answer in the customer terms, privacy notice, and data-processing agreement.
- What exact deletion SLA applies at the app, API, provider, backup, log, retry-queue, and disaster-recovery layers? The required MVP answer should be no raw-audio and no-transcript persistence, not merely a short retention period.
- Which access, encryption, authentication, tenant-isolation, incident-response, data-subject request, and foreign-disclosure controls demonstrate FADP Articles 7, 8, 16-19 compliance? Does the remaining processing require a data-protection impact assessment under [FADP Art. 22](https://www.fedlex.admin.ch/eli/cc/2022/491/en)?
- What is the customer-facing consent and bystander-notice record, if any? Avoid storing a list of bystanders by default, as that would create more personal data. Counsel should decide whether the per-capture Artisan affirmation is enough for the intended settings.
- What are the deletion and correction rules for Quote text after a person asks about an incidental mention? A brief transcript can disappear, yet its words may already appear in a Quote line.

### For issue #18, stack and integrations

- Which speech-to-text and AI providers can meet the no-training, request-only retention, deletion, no speaker identification, DPA, authorised sub-processing, and location requirements in writing?
- Can the mobile stack record to encrypted app-private temporary storage, guarantee deletion on Stop, cancel, error, backgrounding, and uninstall, and prevent crash/diagnostic tooling from uploading audio or transcript payloads?
- Can the backend defer every provider request until Stop and enforce an allowlist of approved processors and regions? Can it prove deletion and reject payload logging at gateways, queues, traces, and support tools?
- If an EU-established provider processes the data and the arrangement falls within the GDPR, its processor contract also needs the requirements in [GDPR Art. 28](https://eur-lex.europa.eu/eli/reg/2016/679/oj). That is conditional, not a claim that the Swiss-only MVP is automatically subject to the GDPR. Swiss FADP duties remain the baseline.

## Counsel review requested

Obtain Swiss privacy and criminal-law advice before launch on the Article 179bis and 179ter application to incidental nearby speech, valid agreement in customer homes and workplaces, the proposed notice wording, controller allocation, foreign providers, and whether the selected processing creates a high risk that requires a FADP impact assessment. The product should keep the conservative rule until that advice says a narrower rule is safe.

## Primary sources

- [Swiss Criminal Code, Arts. 179bis-179ter](https://www.fedlex.admin.ch/eli/cc/54/757_781_799/en)
- [Swiss Civil Code, Arts. 28 and 28a](https://www.fedlex.admin.ch/eli/cc/24/233_245_233/en)
- [Federal Act on Data Protection](https://www.fedlex.admin.ch/eli/cc/2022/491/en)
- [Telecommunications Act](https://www.fedlex.admin.ch/eli/cc/1992/95/en)
- [Code of Obligations, Arts. 328 and 328b](https://www.fedlex.admin.ch/eli/cc/27/317_321_377/en)
- [Employment Act, Art. 6](https://www.fedlex.admin.ch/eli/cc/1966/57/en) and [Ordinance 3 to the Employment Act, Art. 26](https://www.fedlex.admin.ch/eli/cc/1993/18/en)
- [SECO: Surveillance technique au poste de travail](https://www.seco.admin.ch/fr/surveillance-technique-au-poste-de-travail)
- [Federal Data Protection and Information Commissioner: Data protection basics](https://www.edoeb.admin.ch/edoeb/en/home/datenschutz/grundlagen.html)
- [EU GDPR, Art. 28](https://eur-lex.europa.eu/eli/reg/2016/679/oj), only for the conditional EU-processor point above.
