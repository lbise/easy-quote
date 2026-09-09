# Swiss pilot deployment and recovery cost research

Verified: 2026-09-10

Issue: [#34](https://github.com/lbise/easy-quote/issues/34)

Decision owner: [#19](https://github.com/lbise/easy-quote/issues/19)

## Scope and bounded result

This report verifies components for the selected Docker Compose / Better Auth stack; it **does not select, buy, configure, or approve** a provider or real-data processing. It builds on [Swiss hosting feasibility](https://github.com/lbise/easy-quote/blob/research/swiss-hosting-feasibility/docs/research/swiss-hosting-feasibility.md) and [transactional email feasibility](https://github.com/lbise/easy-quote/blob/research/transactional-email-feasibility/docs/research/transactional-email-feasibility.md). The earlier reports establish the VPS, SMTP and policy context; this report narrows the live PDF store, encrypted backup route, recovery shape, and complete illustrative budget.

A concrete *candidate composition* is technically plausible:

- production: an Infomaniak VPS Lite with **4 vCPU, 8 GB RAM and 160 GB** at CHF 18.00/month, running the application, PostgreSQL, and the separate durable Chromium worker;
- live PDFs: Infomaniak Public Cloud Object Storage, accessed through its S3-compatible endpoint, with no public container ACL and application-mediated downloads;
- recovery copy: a separate Swiss Backup cloud-backup space, written by Restic with its own encryption key; and
- isolated staging/recovery: a second, temporary Swiss VPS Lite of the same size for fictional staging and the monthly restore drill.

The primary documentation supports the building blocks and Swiss placement. It does **not** prove the Public Cloud storage tariff, at-rest encryption, S3-credential scope, Swiss Backup append-only protection, a separate-account failure domain, one-hour RPO, one-business-day recovery, or Chromium capacity. Therefore this is a conditional delivery shape, not a deployment approval or recovery guarantee.

## Requirements carried forward

The governing decisions require Swiss hosting for the application, PostgreSQL, PDFs, backups, staging with fictional data, and any recovery environment handling real data. PDFs are private and normal downloads return preserved original bytes. Backups are encrypted, unavailable for ordinary access, retained for **at most 30 days**, and must not resurrect deleted data; recovery targets are at most one hour of lost saved work and restoration within one business day. Temporary AI/speech content never enters backups. See the [security resolution](https://github.com/lbise/easy-quote/issues/16#issuecomment-5580585487), [architecture resolution](https://github.com/lbise/easy-quote/issues/17#issuecomment-5581781011), [stack resolution](https://github.com/lbise/easy-quote/issues/18#issuecomment-5591360674), and [operations checkpoint](https://github.com/lbise/easy-quote/issues/19#issuecomment-5596432283).

The plan is one invited Artisan Business initially, expanding to no more than ten. It is not an HA design or a 24/7 support commitment. Léonard remains responsible for updates, backup review, and recovery.

## Candidate components and documented boundaries

### Compute: VPS Lite 4 vCPU / 8 GB / 160 GB

Infomaniak currently lists that VPS Lite configuration at CHF 18.00 monthly and says its VPS Lite is hosted in Switzerland. It supplies root access, so Compose can supervise separate application, worker, and PostgreSQL processes, and the operator can install the Playwright/Chromium dependencies. This is a capability inference from an unmanaged Linux VM, not a vendor statement that the application will fit or that Chromium will render reliably under pilot load. [VPS Lite product page](https://www.infomaniak.com/en/hosting/vps-lite).

The same page gives neither a database backup policy nor an application availability SLA. The prior hosting research also records that VPS Lite has no snapshots or availability SLA. A snapshot would not be a PostgreSQL- and PDF-consistent restore anyway.

### Managed live PDF store: Infomaniak Public Cloud Object Storage

Infomaniak's official Public Cloud documentation says Object Storage is built on Swift, can be used through HTTP and S3, and is intended for unstructured objects such as documents. Its S3 page provides `s3.pub1.infomaniak.cloud` (and `pub2`), says all data is in its Swiss data centres, and requires S3 clients that need it to use `forcePathStyle=true`. The apparent `us-east-1` region is explicitly only a compatibility value, not a location assertion. [Object Storage overview](https://docs.infomaniak.cloud/object_storage/) and [S3 compatibility](https://docs.infomaniak.cloud/object_storage/s3/).

This is a live object store, not the Swiss Backup product. It can meet the application-facing S3 interface, subject to the following controls and gaps:

| Area | Documented fact | Required interpretation or unresolved fact |
| --- | --- | --- |
| Swiss location | The S3 guide states all data is in Infomaniak Swiss data centres. | Obtain the exact service/DPA/subprocessor/support-access terms before real data; product-page location is not a complete processor assessment. |
| Access | Containers are private absent a read ACL. Public access is deliberately enabled by a `.r:*` read ACL. S3 bucket policies are unsupported. | Never set public ACLs or public links. Keep the S3 key server-side; browser downloads go through the application after Business authorization. The public docs do not prove a bucket/prefix-scoped S3 credential suitable for this application. |
| Stored bytes | Swift objects are not modified in place; replacing content requires a complete re-upload. The service checks uploaded objects against an MD5 checksum. | Use a write-once key for each published PDF, record a cryptographic digest and length with the Revision, and stream that stored object on download. Delivery must prove a byte-for-byte digest match; MD5 transport/integrity behaviour is not sufficient proof of the product requirement. |
| Encryption | HTTPS is shown in the S3 client configuration. | No reviewed Infomaniak Public Cloud page establishes provider-managed encryption at rest, customer-managed keys, or key-deletion behaviour for Object Storage. Confirm it or design and test application-layer encryption that decrypts to the exact published PDF bytes. Do not claim this requirement is already met. |
| Cost | Public Cloud billing uses ICU credits (CHF 1 = 50 ICU). | The reviewed official documentation does not publish the Object Storage per-GiB, request, or egress rate. A live account's meter/rate card must be checked before procurement; the budget below uses a capped planning allowance, not a vendor quote. |

Sources: [ACL documentation](https://docs.infomaniak.cloud/object_storage/acls/), [Swift object-storage documentation](https://docs.infomaniak.cloud/object_storage/swift_object_storage/), [application credentials](https://docs.infomaniak.cloud/identity/applications_credentials/), and [Public Cloud billing](https://docs.infomaniak.cloud/metering/billing/).

### One maintained self-hosted comparison: MinIO

The only self-hosted S3 implementation examined is [MinIO](https://github.com/minio/minio). Its upstream latest-release record is a 2025 security release and its `master` licence is AGPLv3. Its current upstream documentation and release record make it a maintained candidate for this bounded comparison, but its licence and support implications must be reviewed before use. [Latest upstream release](https://api.github.com/repos/minio/minio/releases/latest) and [licence](https://github.com/minio/minio/blob/master/LICENSE).

MinIO's current documentation describes S3-compatible policy-based access control with default deny, access keys that can receive an additional restrictive inline policy, versioning, lifecycle expiration, and server-side-encryption configuration. [IAM](https://docs.min.io/community/minio-object-store/administration/identity-access-management/policy-based-access-control.html), [versioning](https://docs.min.io/community/minio-object-store/administration/object-management/object-versioning.html), and [lifecycle](https://docs.min.io/community/minio-object-store/administration/object-management/object-lifecycle-management.html).

Running it on the production VPS has no incremental host charge, but it puts the application, database, PDFs, storage daemon, and their operator patch burden on the same VM. A VM loss, disk corruption, or host compromise takes the live store down with the application. It is therefore less attractive for live-PDF failure isolation than the managed Object Storage candidate, even though both still need the independent backup copy. It also does not establish capacity, HA, backups, or a zero-cost commercial/support posture. This comparison does **not** select the managed store over MinIO.

## Retention and deletion pitfalls

Object versioning is not a substitute for the independent backup and can defeat deletion. In Infomaniak Swift, deleting a versioned object removes only the latest version; older versions remain available until each is deleted. The S3 compatibility page also warns that multipart overwrite leaves old chunks in a `+segments` bucket for manual deletion. The reviewed Public Cloud documentation does not establish an automatic lifecycle-expiry control for these remnants. For a published PDF, either leave live bucket versioning off and use an immutable, never-overwritten object key, or inventory and delete every version/segment on an approved erasure or Business deletion. Verify absence with a privileged inventory before declaring deletion complete.

Conversely, an accidental lifecycle rule or broad prefix deletion could erase a published PDF while its Revision remains. MinIO documents that versioning retains all versions and lifecycle rules can expire objects automatically; it also notes that older versions consume storage until an explicit noncurrent-version expiry policy. Do not put an expiry rule on live published PDFs. [MinIO lifecycle documentation](https://docs.min.io/community/minio-object-store/administration/object-management/object-lifecycle-management.html).

The deletion ledger must outlive every recovery point that can contain the deleted record. It is not safe to remove the ledger merely because the live object is gone. On restoration, apply the ledger to the restored database and delete the associated live/recovered PDF objects before access resumes.

## Encrypted independent backup route

Swiss Backup is explicitly a **backup** space, not the live PDF store. Its product page says cloud-backup space can be accessed through Swift, S3, or SFTP and that it keeps three copies across two Swiss data centres. The capacity price page lists 1 TB at CHF 4.18/month excluding VAT. [Swiss Backup cloud storage](https://www.infomaniak.com/en/swiss-backup/cloud-storage) and [pricing](https://www.infomaniak.com/en/swiss-backup/prices).

Infomaniak documents a Restic-to-Swiss-Backup route. Its guide requires a separately created encryption key and warns that loss of that key makes data unreadable; it shows backup and restore through a Swift repository. Restic's own documentation says a repository contains encrypted backup data and metadata and requires a repository password/key. [Infomaniak Restic guide](https://docs.infomaniak.cloud/block_storage/swissbackup/) and [Restic repository documentation](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html).

A delivery design can use a dedicated Swiss Backup space/credential, a Restic repository key held in the recovery password manager, and a restricted runtime copy only for the backup job. This separates application S3 credentials from backup credentials and lets a recovery environment obtain only the credentials it needs. It does **not** prove the required failure isolation:

- the reviewed Swiss Backup sources do not establish write-only/append-only credentials, object lock, immutability, or least-privilege path scoping;
- a backup credential and Restic key left on the production VM can be used by an attacker who compromises that VM; and
- production VPS, Public Cloud Object Storage, and Swiss Backup are all Infomaniak services, so provider/account-wide outage or compromise remains a common failure domain.

Treat a separate Infomaniak account, credential scope, recovery-key custody, provider-wide failure scenario, and any immutable-backup entitlement as **unresolved vendor facts**. Do not call this route independent beyond its separate service, data copy, and credential design until those facts are answered. A different-provider Swiss backup would improve vendor failure isolation, but none was selected or researched because this ticket is bounded to the existing Infomaniak route.

## Database/PDF-consistent recovery and one-hour RPO path

PostgreSQL supports point-in-time recovery by restoring a base backup and replaying archived WAL. `pg_basebackup` can take a base backup from a running cluster. [PostgreSQL continuous archiving](https://www.postgresql.org/docs/current/continuous-archiving.html) and [`pg_basebackup`](https://www.postgresql.org/docs/current/app-pgbasebackup.html). This establishes compatibility, not a configured schedule or a recovery guarantee.

A feasible delivery procedure is:

1. Keep temporary AI/speech directories, prompts, transcripts, and raw recordings outside every backup input path. Test the exclusion.
2. At least hourly, preserve a PostgreSQL WAL/PDF recovery increment and an immutable manifest of the PDF keys, byte lengths, and digests referenced at that database recovery point. Copy every manifest object by exact key/version to a local backup staging area, then commit that set to the encrypted Restic repository. The database recovery point must not be marked successful until every referenced PDF is present and digest-checked in that backup set.
3. Run the backup frequently enough that a *completed* set is never older than one hour. Alert on a missed, failed, late, or unverifiable set. A daily base backup plus archived WAL and hourly PDF/manifest sets may meet the target; the actual cadence, copy time, and WAL volume need measurement.
4. Prune all Restic snapshots, WAL, PDF staging data, and any backup-side object versions at 30 days or sooner. Keep only the deletion ledger until the last affected recovery point has expired.
5. Once per month and after material backup changes, start an isolated Swiss recovery VPS; restore a selected point; verify PostgreSQL, a sample of stored PDF digests and authorized retrieval, RLS, and worker configuration; replay the deletion ledger; then permit no access until those checks pass. Destroy the recovery environment and its local restore data after the evidence is recorded.

The isolated environment protects production from an unsafe restore and satisfies the Swiss placement constraint when it contains real data. It is not staging: staging receives fictional data only and separate credentials. Neither a successful `restic snapshots` command, a provider replica count, nor an hourly cron entry proves the one-hour RPO or recovery within one business day. The drill must measure the age of the recovered point and elapsed restoration time.

## Illustrative monthly budget

All Infomaniak published figures below state that they exclude VAT. The Swiss Federal Tax Administration lists the normal VAT rate as 8.1%. The VAT column is an illustrative 8.1% calculation, **not** a finding that every charge is taxable to the eventual purchaser at that rate. The CHF 100 target does not say whether it is VAT-inclusive; both views are shown. [Infomaniak VPS Lite](https://www.infomaniak.com/en/hosting/vps-lite), [Swiss Backup pricing](https://www.infomaniak.com/en/swiss-backup/prices), and [Swiss VAT rates](https://www.estv.admin.ch/en/vat-rates-switzerland).

Assumptions: one production VPS; once-monthly isolated staging/recovery use billed as one full VPS Lite month; one small pilot store whose unpriced Public Cloud usage is capped at CHF 5.00; an eligible paid-domain Mail Starter address with no separate monthly mailbox fee; and a domain whose annual charge is at most CHF 5.00, amortised at CHF 0.42/month. The Mail Starter page says it provides one free custom-domain address when the domain is paid, but the prior email research correctly leaves external-domain eligibility, exact quota, and selected-TLD renewal price to procurement. The domain amount is therefore a planning assumption, not a selected TLD price. [Mail Starter description](https://www.infomaniak.com/en/support/faq/2497/discover-the-different-infomaniak-suites-ksuite-pro-my-ksuite).

| Item | CHF/month excl. VAT | VAT treatment / evidence | Status |
| --- | ---: | --- | --- |
| Production VPS Lite, 4 vCPU / 8 GB / 160 GB | 18.00 | Published monthly price excludes VAT. | Published |
| On-demand isolated staging/recovery VPS Lite | 18.00 | One full-month charge is assumed because monthly recovery testing is required; a shorter billing entitlement was not verified. | Assumption using published plan price |
| Swiss Backup, 1 TB cloud-backup capacity | 4.18 | Published capacity price excludes VAT. | Published |
| Swiss Backup device entitlement | 1.84 | Pricing page separately lists a `Device` at CHF 1.84/month. It is unclear whether the Swift/Restic cloud-space route requires one; reserve it until confirmed. | Unresolved entitlement, reserved |
| Managed live S3-compatible PDF storage | 5.00 | Public Cloud docs establish ICU billing but not the relevant rate. This is a hard planning allowance for storage, requests, and egress—not a published price. | Unknown vendor price, capped assumption |
| Domain renewal amortisation | 0.42 | Assumes a domain under CHF 5/year; exact TLD, registrar, renewal and VAT are unknown. | Assumption |
| Infomaniak account-email route | 0.00 | Mail Starter is documented as one free custom-domain address on a paid domain. If unavailable, the advertised Mail Service starts at CHF 2.29/month. | Entitlement unresolved |
| Independent content-free availability alert | 0.00 | UptimeRobot advertises a Free plan with 50 monitors and five-minute checks. Use only a content-free health endpoint and external alert recipient. Its processing locations/terms require separate privacy assessment. | Published zero-price external option; not selected |
| **Illustrative total before inference** | **47.44** |  |  |
| **Illustrative VAT at 8.1%** | **3.84** |  | Assumption |
| **Illustrative total including VAT** | **51.28** |  |  |

Under the VAT-inclusive interpretation, this leaves **CHF 48.72/month** below CHF 100 for final-domain variance, actual Public Cloud usage, any paid Mail Service, alert upgrades, and metered inference. Under the VAT-exclusive interpretation, it leaves CHF 52.56 before VAT. These amounts are ceilings, not an approved inference budget: the production provider is intentionally undecided, and actual S3 charges, transfers, domain, mailbox entitlement, taxes, and monitoring privacy review can reduce them.

If Mail Starter cannot be used, adding the published Mail Service starting price produces a CHF 49.73 VAT-exclusive / CHF 53.76 illustrative VAT-inclusive total. This is still below CHF 100 before inference, but the prior email research documents a quota and public-price conflict that must be resolved before purchase. No budget line covers human operating time, legal review, paid support, a second provider, an availability replica, or a production inference provider.

## Delivery proof and procurement questions

Before any selection or real-data use, delivery/procurement must produce evidence for each item below:

1. **Compute:** representative concurrent PDF publication on the chosen VPS, worker restart behaviour, disk growth, patching, and no public database/admin-storage ports.
2. **Live store:** a Swiss-location/DPA/subprocessor/support review; actual ICU rate card and egress fees; at-rest encryption and key-management answer; a disposable test proving private-by-default ACLs, denied anonymous access, credential scope, upload/download digest match, and complete version/segment deletion.
3. **Backup isolation:** whether a separate account is available; exact required device entitlement; credential permissions; write-only/immutability/object-lock capability; recovery-key custody; retention behaviour; and which provider-wide failures are outside the route's protection.
4. **Recovery:** an observed completed backup interval of no more than one hour; a restore to an isolated Swiss VPS; database/PDF manifest consistency; RLS and deletion-ledger replay; proof temporary AI/speech content was excluded; 30-day expiry; and measured restoration inside one business day.
5. **Operational cost:** the actual selected domain and renewal, Mail Starter entitlement and delivery limits, VAT treatment, Public Cloud storage/request/egress meter, monitoring processor terms, and an explicit metered-inference allowance with 50%/80% warnings and the configured block at its cap.

## Sources and limits

Claims about products and technical behaviour use provider documentation, upstream software documentation/source, PostgreSQL documentation, and the Swiss Federal Tax Administration. The report does not rely on comparison sites. Pricing, support, security, and data-location pages can change; amounts and entitlements require a dated procurement check. “Unresolved” means the reviewed primary material did not establish the required fact, not that the provider cannot supply it.
