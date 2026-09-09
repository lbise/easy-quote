# Swiss pilot deployment and recovery cost research

Verified: 2026-09-10

Issue: [Verify the concrete Swiss pilot deployment and recovery costs](https://github.com/lbise/easy-quote/issues/34)

Decision owner: [Decide deployment, operations, and observability](https://github.com/lbise/easy-quote/issues/19)

## Scope and bounded result

This report verifies components for the selected Docker Compose / Better Auth stack; it **does not select, buy, configure, or approve** a provider or real-data processing. It builds on [Swiss hosting feasibility](https://github.com/lbise/easy-quote/blob/research/swiss-hosting-feasibility/docs/research/swiss-hosting-feasibility.md) and [transactional email feasibility](https://github.com/lbise/easy-quote/blob/research/transactional-email-feasibility/docs/research/transactional-email-feasibility.md). The earlier reports establish the VPS, SMTP and policy context; this report narrows the live PDF store, encrypted backup route, recovery shape, and complete illustrative budget.

A concrete *candidate composition* is technically plausible:

- production: an Infomaniak VPS Lite with **4 vCPU, 8 GB RAM and 160 GB** at CHF 18.00/month, running the application, PostgreSQL, and the separate durable Chromium worker;
- live PDFs: Infomaniak Public Cloud Object Storage, accessed through its S3-compatible endpoint, with no public container ACL and application-mediated downloads;
- recovery copy: a separate Swiss Backup cloud-backup space, written by Restic with its own encryption key; and
- isolated staging/recovery: a second, temporary Swiss VPS Lite of the same size for fictional staging and the monthly restore drill.

The primary documentation supports the building blocks and Swiss placement. It does **not** prove VPS/database or Public Cloud Object Storage at-rest encryption, the Public Cloud storage tariff, S3-credential scope, Swiss Backup append-only protection, a separate-account failure domain, one-hour RPO, one-business-day recovery, or Chromium capacity. Therefore this is a conditional delivery shape, not a deployment approval or recovery guarantee.

## Requirements carried forward

The governing decisions require Swiss hosting for the application, PostgreSQL, PDFs, backups, staging with fictional data, and any recovery environment handling real data. PDFs are private and normal downloads return preserved original bytes. Backups are encrypted, unavailable for ordinary access, retained for **at most 30 days**, and must not resurrect deleted data; recovery targets are at most one hour of lost saved work and restoration within one business day. Temporary AI/speech content never enters backups. See the [security resolution](https://github.com/lbise/easy-quote/issues/16#issuecomment-5580585487), [architecture resolution](https://github.com/lbise/easy-quote/issues/17#issuecomment-5581781011), [stack resolution](https://github.com/lbise/easy-quote/issues/18#issuecomment-5591360674), and [operations checkpoint](https://github.com/lbise/easy-quote/issues/19#issuecomment-5596432283).

The plan is one invited Artisan Business initially, expanding to no more than ten. It is not an HA design or a 24/7 support commitment. Léonard remains responsible for updates, backup review, and recovery.

**Later user decisions, not independent research findings:** the CHF 100 ceiling is VAT-inclusive and is provisionally allocated as CHF 60 infrastructure, CHF 25 AI/speech, and CHF 15 contingency. The one-business-day recovery target applies to a failed host or damaged deployment, not a provider-wide Infomaniak outage; the common-provider risk is accepted for this pilot. These decisions do not turn the provider facts or recovery proof gaps below into established capabilities.

## Candidate components and documented boundaries

### Compute: VPS Lite 4 vCPU / 8 GB / 160 GB

Infomaniak currently lists that VPS Lite configuration at CHF 18.00 monthly and says its VPS Lite is hosted in Switzerland. It supplies root access, so Compose can supervise separate application, worker, and PostgreSQL processes, and the operator can install the Playwright/Chromium dependencies. This is a capability inference from an unmanaged Linux VM, not a vendor statement that the application will fit or that Chromium will render reliably under pilot load. [VPS Lite product page](https://www.infomaniak.com/en/hosting/vps-lite).

The same page gives neither a database backup policy nor an application availability SLA. The prior hosting research also records that VPS Lite has no snapshots or availability SLA. A snapshot would not be a PostgreSQL- and PDF-consistent restore anyway.

**At-rest encryption is not verified for this VPS/database route.** The VPS Lite page does not state that its NVMe disk is encrypted at rest, name an encryption/key-management model, or say that the claim applies to PostgreSQL data, WAL, temporary files, or deleted blocks. Infomaniak's general Trust Centre says it uses “systematic encryption of stored data”, but does not bind that statement to VPS Lite or state the relevant key/deletion behaviour. PostgreSQL itself documents that storage encryption is a filesystem- or block-level concern (for Linux, for example `dm-crypt` + LUKS), not an automatic database property. Written VPS-specific confirmation or a designed and tested operator-managed disk-encryption arrangement is required before claiming stored-data encryption; the latter has boot, key-custody, unattended-restart, and recovery implications. [Infomaniak Trust Centre](https://www.infomaniak.com/en/trust-center) and [PostgreSQL encryption options](https://www.postgresql.org/docs/current/encryption-options.html).

### Managed live PDF store: Infomaniak Public Cloud Object Storage

Infomaniak's official Public Cloud documentation says Object Storage is built on Swift, can be used through HTTP and S3, and is intended for unstructured objects such as documents. Its S3 page provides `s3.pub1.infomaniak.cloud` (and `pub2`), says all data is in its Swiss data centres, and requires S3 clients that need it to use `forcePathStyle=true`. The apparent `us-east-1` region is explicitly only a compatibility value, not a location assertion. [Object Storage overview](https://docs.infomaniak.cloud/object_storage/) and [S3 compatibility](https://docs.infomaniak.cloud/object_storage/s3/).

This is a live object store, not the Swiss Backup product. It can meet the application-facing S3 interface, subject to the following controls and gaps:

| Area | Documented fact | Required interpretation or unresolved fact |
| --- | --- | --- |
| Swiss location | The S3 guide states all data is in Infomaniak Swiss data centres. | Obtain the exact service/DPA/subprocessor/support-access terms before real data; product-page location is not a complete processor assessment. |
| Access | Containers are private absent a read ACL. Public access is deliberately enabled by a `.r:*` read ACL. S3 bucket policies are unsupported. | Never set public ACLs or public links. Keep the S3 key server-side; browser downloads go through the application after Business authorization. The public docs do not prove a bucket/prefix-scoped S3 credential suitable for this application. |
| Stored bytes | Swift objects are not modified in place; replacing content requires a complete re-upload. The service checks uploaded objects against an MD5 checksum. | Use a write-once key for each published PDF, record a cryptographic digest and length with the Revision, and stream that stored object on download. Delivery must prove a byte-for-byte digest match; MD5 transport/integrity behaviour is not sufficient proof of the product requirement. |
| Encryption | HTTPS is shown in the S3 client configuration. Infomaniak's generic Trust Centre claims systematic encryption of stored data. | No reviewed Infomaniak Public Cloud Object Storage page establishes that the generic claim applies to this service, or specifies provider-managed encryption at rest, customer-managed keys, or key-deletion behaviour. Confirm it or design and test application-layer encryption that decrypts to the exact published PDF bytes. Do not claim this requirement is already met. |
| Cost | Public Cloud billing uses ICU credits (CHF 1 = 50 ICU). | The reviewed official documentation does not publish the Object Storage per-GiB, request, or egress rate. A live account's meter/rate card must be checked before procurement; the budget below uses a capped planning allowance, not a vendor quote. |

Sources: [ACL documentation](https://docs.infomaniak.cloud/object_storage/acls/), [Swift object-storage documentation](https://docs.infomaniak.cloud/object_storage/swift_object_storage/), [application credentials](https://docs.infomaniak.cloud/identity/applications_credentials/), and [Public Cloud billing](https://docs.infomaniak.cloud/metering/billing/).

### Self-hosted check: MinIO fails the maintained criterion

The one self-hosted implementation examined was [MinIO](https://github.com/minio/minio). The upstream GitHub repository API currently reports `archived: true`; its latest release remains a 2025 security release. The repository README also says the community edition is source-code-only and offers best-effort community support. A 2025 release does **not** establish maintenance in September 2026, and the archived status is contrary evidence. MinIO therefore fails this ticket's maintained-self-hosted criterion and is not a viable alternative in this report. [Upstream repository metadata](https://api.github.com/repos/minio/minio), [latest release](https://api.github.com/repos/minio/minio/releases/latest), and [upstream README](https://github.com/minio/minio/blob/master/README.md).

No replacement self-hosted implementation was added: the ticket permits at most one maintained comparison, not a forced comparison. The self-hosted alternative consequently remains unverified. In any later comparison, confirm current upstream maintenance, licence/support posture, private access controls, at-rest encryption, deletion/version behaviour, backup isolation, and pilot capacity before treating it as a candidate.

## Retention and deletion pitfalls

Object versioning is not a substitute for the independent backup and can defeat deletion. In Infomaniak Swift, deleting a versioned object removes only the latest version; older versions remain available until each is deleted. The S3 compatibility page also warns that multipart overwrite leaves old chunks in a `+segments` bucket for manual deletion. The reviewed Public Cloud documentation does not establish an automatic lifecycle-expiry control for these remnants. For a published PDF, either leave live bucket versioning off and use an immutable, never-overwritten object key, or inventory and delete every version/segment on an approved erasure or Business deletion. Verify absence with a privileged inventory before declaring deletion complete.

Conversely, an accidental lifecycle rule or broad prefix deletion could erase a published PDF while its Revision remains. Do not put an expiry rule on live published PDFs. Any later self-hosted comparison must separately prove its version and lifecycle semantics; MinIO no longer supplies that comparison because it fails the maintained criterion.

The deletion ledger must outlive every recovery point that can contain the deleted record. It is not safe to remove the ledger merely because the live object is gone. On restoration, apply the ledger to the restored database and delete the associated live/recovered PDF objects before access resumes.

## Encrypted independent backup route

Swiss Backup is explicitly a **backup** space, not the live PDF store. Its product page says cloud-backup space can be accessed through Swift, S3, or SFTP and that it keeps three copies across two Swiss data centres. The capacity price page lists 1 TB at CHF 4.18/month excluding VAT. [Swiss Backup cloud storage](https://www.infomaniak.com/en/swiss-backup/cloud-storage) and [pricing](https://www.infomaniak.com/en/swiss-backup/prices).

Infomaniak documents a Restic-to-Swiss-Backup route. Its guide requires a separately created encryption key and warns that loss of that key makes data unreadable; it shows backup and restore through a Swift repository. Restic's own documentation says a repository contains encrypted backup data and metadata and requires a repository password/key. [Infomaniak Restic guide](https://docs.infomaniak.cloud/block_storage/swissbackup/) and [Restic repository documentation](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html).

A delivery design can use a dedicated Swiss Backup space/credential, a Restic repository key held in the recovery password manager, and a restricted runtime copy only for the backup job. This separates application S3 credentials from backup credentials and lets a recovery environment obtain only the credentials it needs. It does **not** prove the required failure isolation:

- the reviewed Swiss Backup sources do not establish write-only/append-only credentials, object lock, immutability, or least-privilege path scoping;
- a backup credential and Restic key left on the production VM can be used by an attacker who compromises that VM; and
- production VPS, Public Cloud Object Storage, and Swiss Backup are all Infomaniak services, so provider/account-wide outage or compromise remains a common failure domain.

Treat a separate Infomaniak account, credential scope, recovery-key custody, provider-wide failure scenario, and any immutable-backup entitlement as **unresolved vendor facts**. Do not call this route independent beyond its separate service, data copy, and credential design until those facts are answered. A different-provider Swiss backup would improve vendor failure isolation, but none was selected or researched because this ticket is bounded to the existing Infomaniak route. The user's accepted one-business-day recovery scope excludes a provider-wide Infomaniak outage; it does not make that outage recoverable or remove the need to state the common-provider risk.

## Database/PDF-consistent recovery and one-hour RPO path

PostgreSQL supports point-in-time recovery by restoring a base backup and replaying archived WAL. `pg_basebackup` can take a base backup from a running cluster. [PostgreSQL continuous archiving](https://www.postgresql.org/docs/current/continuous-archiving.html) and [`pg_basebackup`](https://www.postgresql.org/docs/current/app-pgbasebackup.html). This establishes compatibility, not a configured schedule or a recovery guarantee.

A feasible delivery procedure is:

1. Keep temporary AI/speech directories, prompts, transcripts, and raw recordings outside every backup input path. Test the exclusion.
2. At least hourly, preserve a PostgreSQL WAL/PDF recovery increment and an immutable manifest of the PDF keys, byte lengths, and digests referenced at that database recovery point. Copy every manifest object by exact key/version to a local backup staging area, then commit that set to the encrypted Restic repository. The database recovery point must not be marked successful until every referenced PDF is present and digest-checked in that backup set.
3. Run the backup frequently enough that a *completed* set is never older than one hour. Alert on a missed, failed, late, or unverifiable set. A daily base backup plus archived WAL and hourly PDF/manifest sets may meet the target; the actual cadence, copy time, and WAL volume need measurement.
4. Make the retention boundary **21 days**, not 30: create a fresh base backup at least daily; retain only base backups, the WAL needed by each retained base, PDF manifests/objects, and Restic snapshots whose recovery point is within that 21-day window; and remove the whole dependency set for an expired base. This leaves nine days to detect and repair expiry failure before the 30-day maximum. Do not retain a weekly base for a 21-day recovery point if restoring it requires a base or WAL older than 21 days.
5. Restic removal is two phases. `forget` removes snapshot references; the underlying chunks remain until `prune` completes. Its own documentation says `forget --prune` can automate the sequence, that pruning can lock the repository and take time, and that `--keep-within` can keep an additional oldest snapshot. Therefore schedule a completed, checked `forget` **and** `prune` frequently enough to finish before day 30, use an explicit 21-day cutoff with a post-run audit that no retained snapshot, WAL archive, staging object, or recoverable dependency is older than 21 days, and alert/escalate on any exception. A retention policy that merely says `--keep-within 30d` is insufficient: the oldest-snapshot safeguard, a failed backup, failed prune, shared pack files, or a late job can leave recoverable deleted content past the limit. Configure `prune` to remove unreferenced data rather than deliberately retain unused packs, and record its completion and verification. [Restic snapshot removal and pruning](https://restic.readthedocs.io/en/stable/060_forget.html).
6. Keep the deletion ledger until the post-prune audit proves the last backup dependency capable of restoring the deleted content is gone. The **21-day window applies only to backup dependencies**. It must never extend live erasure deadlines: on Business deletion, remove all Business-linked live data—including every PDF object version and multipart `+segments` remnant—within seven days, then retain only the deletion ledger and backup dependencies until their separate expiry/audit. Do not wait for a 21-day or nominal day-30 backup batch to clean the live store.
7. Once per month and after material backup changes, start an isolated Swiss recovery VPS; restore a selected point; verify PostgreSQL, a sample of stored PDF digests and authorized retrieval, RLS, and worker configuration; replay the deletion ledger; then permit no access until those checks pass. Destroy the recovery environment and its local restore data after the evidence is recorded.

The isolated environment protects production from an unsafe restore and satisfies the Swiss placement constraint when it contains real data. It is not staging: staging receives fictional data only and separate credentials. Neither a successful `restic snapshots` command, a provider replica count, nor an hourly cron entry proves the one-hour RPO or recovery within one business day. The drill must measure the age of the recovered point and elapsed restoration time.

## Illustrative monthly budget

All Infomaniak published figures below state that they exclude VAT. The Swiss Federal Tax Administration lists the normal VAT rate as 8.1%. The VAT column is an illustrative 8.1% calculation, **not** a finding that every charge is taxable to the eventual purchaser at that rate. The user has accepted the CHF 100 target as **VAT-inclusive**; the VAT-exclusive figures remain only procurement assumptions that expose the source prices and calculation. [Infomaniak VPS Lite](https://www.infomaniak.com/en/hosting/vps-lite), [Swiss Backup pricing](https://www.infomaniak.com/en/swiss-backup/prices), and [Swiss VAT rates](https://www.estv.admin.ch/en/vat-rates-switzerland).

Assumptions: one production VPS; once-monthly isolated staging/recovery use billed as one full VPS Lite month; one small pilot store whose unpriced Public Cloud usage is capped at CHF 5.00; an eligible paid-domain Mail Starter address with no separate monthly mailbox fee; and **CHF 2.00/month (CHF 24/year)** reserved for the preferred `easy-quote.ch` renewal at Infomaniak. CHF 2.00 is a deliberately conservative, unquoted allowance—not an asserted `.ch` tariff—because no current public Infomaniak renewal tariff for that TLD was established. The Mail Starter page says it provides one free custom-domain address when the domain is paid, but the prior email research correctly leaves exact entitlement and quota to procurement. A public query to SWITCH's official RDAP endpoint for `easy-quote.ch` returned HTTP 404 on 2026-09-10; that is a point-in-time absence of a public RDAP record, **not** a registrar availability guarantee, a name/right clearance, or permission to order. No availability order, account, or purchase was made. [Mail Starter description](https://www.infomaniak.com/en/support/faq/2497/discover-the-different-infomaniak-suites-ksuite-pro-my-ksuite) and [SWITCH RDAP endpoint](https://rdap.nic.ch/domain/easy-quote.ch).

| Item | CHF/month excl. VAT | VAT treatment / evidence | Status |
| --- | ---: | --- | --- |
| Production VPS Lite, 4 vCPU / 8 GB / 160 GB | 18.00 | Published monthly price excludes VAT. | Published |
| On-demand isolated staging/recovery VPS Lite | 18.00 | One full-month charge is assumed because monthly recovery testing is required; a shorter billing entitlement was not verified. | Assumption using published plan price |
| Swiss Backup, 1 TB cloud-backup capacity | 4.18 | Published capacity price excludes VAT. | Published |
| Swiss Backup device entitlement | 1.84 | Pricing page separately lists a `Device` at CHF 1.84/month. It is unclear whether the Swift/Restic cloud-space route requires one; reserve it until confirmed. | Unresolved entitlement, reserved |
| Managed live S3-compatible PDF storage | 5.00 | Public Cloud docs establish ICU billing but not the relevant rate. This is a hard planning allowance for storage, requests, and egress—not a published price. | Unknown vendor price, capped assumption |
| Preferred `easy-quote.ch` renewal amortisation | 2.00 | CHF 24/year conservative allowance for the stated Infomaniak `.ch` preference; no public current Infomaniak `.ch` tariff was established. RDAP's point-in-time 404 is not an availability or rights result. | Assumption |
| Infomaniak account-email route | 0.00 | Mail Starter is documented as one free custom-domain address on a paid domain. If unavailable, the advertised Mail Service starts at CHF 2.29/month. | Entitlement unresolved |
| Independent content-free availability alert | 0.00 | [UptimeRobot](https://uptimerobot.com/pricing/) advertises a Free plan with 50 monitors and five-minute checks. Use only a content-free health endpoint and external alert recipient. Its processing locations/terms require separate privacy assessment. | Published zero-price external option; not selected |
| **Illustrative infrastructure total before AI/speech and contingency** | **49.02** |  |  |
| **Illustrative VAT at 8.1%** | **3.97** |  | Assumption |
| **Illustrative infrastructure total including VAT** | **52.99** |  |  |

The user provisionally allocates the VAT-inclusive CHF 100 ceiling as CHF 60 infrastructure, CHF 25 AI/speech, and CHF 15 contingency. The **CHF 52.99** illustrative VAT-inclusive total therefore leaves **CHF 7.01** within the infrastructure allocation. The arithmetic difference from the whole CHF 100 ceiling is CHF 47.01, but it is already allocated to AI/speech and contingency; it is **not** an inference ceiling or permission to spend it on infrastructure. The VAT-exclusive CHF 49.02 and its CHF 50.98 difference from CHF 100 remain illustrative procurement calculations only. Actual S3 charges, transfers, domain, mailbox entitlement, taxes, and monitoring privacy review can reduce the CHF 7.01 infrastructure room.

If Mail Starter cannot be used, adding the published Mail Service starting price produces a CHF 51.31 VAT-exclusive / CHF 55.47 illustrative VAT-inclusive total, leaving CHF 4.53 within the provisional infrastructure allocation. The prior email research documents a quota and public-price conflict that must be resolved before purchase. No budget line covers human operating time, legal review, paid support, a second provider, an availability replica, or a production inference provider.

## Delivery proof and procurement questions

Before any selection or real-data use, delivery/procurement must produce evidence for each item below:

1. **Compute:** representative concurrent PDF publication on the chosen VPS, worker restart behaviour, disk growth, patching, and no public database/admin-storage ports.
2. **Live store:** a Swiss-location/DPA/subprocessor/support review; actual ICU rate card and egress fees; at-rest encryption and key-management answer; a disposable test proving private-by-default ACLs, denied anonymous access, credential scope, upload/download digest match, and complete version/segment deletion.
3. **Backup isolation:** whether a separate account is available; exact required device entitlement; credential permissions; write-only/immutability/object-lock capability; recovery-key custody; retention behaviour; and which provider-wide failures are outside the route's protection.
4. **Recovery:** an observed completed backup interval of no more than one hour; a restore to an isolated Swiss VPS; database/PDF manifest consistency; RLS and deletion-ledger replay; proof temporary AI/speech content was excluded; a 21-day dependency-set cutoff plus completed Restic `forget`/`prune` and audit proving no recoverable deleted content exceeds 30 days; and measured restoration inside one business day.
5. **Operational cost:** the actual selected domain and renewal, Mail Starter entitlement and delivery limits, VAT treatment, Public Cloud storage/request/egress meter, monitoring processor terms, and an explicit metered-inference allowance with 50%/80% warnings and the configured block at its cap.

## Sources and limits

Claims about products and technical behaviour use provider documentation, upstream software documentation/source, PostgreSQL documentation, and the Swiss Federal Tax Administration. The report does not rely on comparison sites. Pricing, support, security, and data-location pages can change; amounts and entitlements require a dated procurement check. “Unresolved” means the reviewed primary material did not establish the required fact, not that the provider cannot supply it.
