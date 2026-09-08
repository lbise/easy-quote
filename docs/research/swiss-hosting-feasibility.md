# Swiss hosting feasibility

Research date: 2026-09-08

## Scope and verdict

This checks hosting for the agreed React and Node.js modular monolith, PostgreSQL with row-level security, a durable publication worker that renders PDFs through Playwright and Chromium, private PDF object storage, and backups. It does not choose a provider or configure one.

The Swiss location requirement applies to the application, database, live PDFs, and backups. It does not mean that every processor is Swiss. Managed authentication remains a separate approval and cost. AI and speech processing remain subject to their existing, separate approval and transient-content policy.

There is one conditional route within the CHF 100 monthly pilot target: a self-managed Infomaniak VPS Lite with PostgreSQL and a private S3-compatible store on the VM, plus Swiss Backup. The provider documents the Swiss location and the building blocks, but the one-hour recovery-point target, 30-day pruning, Chromium capacity, and recovery time are operator work that needs a documented restore drill. It is a feasible deployment shape, not a recovery guarantee.

Infomaniak's current managed Database Service does not offer PostgreSQL yet. Its shared Node.js hosting does run Node, but the documents do not establish an independently durable worker or Playwright and Chromium support. Jelastic has a more promising container route, but its public material does not prove the required backup policy or the full price. Exoscale can supply managed PostgreSQL in a Swiss zone. Its 30-day managed recovery tier alone exceeds the pilot budget. Its one-day tier might fit, but its backup location still needs confirmation.

## What the deployment must prove

The architecture decision requires private object storage, database RLS as a second defence, and a durable worker whose queue contains identifiers rather than copied Customer or Quote content. Publication succeeds only after the frozen Quote Revision and its PDF are safely stored. The security decision sets encrypted backups of no more than 30 days, a target RPO of at most one hour, recovery within one business day, and a restore test before launch. See [architecture resolution](https://github.com/lbise/easy-quote/issues/17#issuecomment-5581781011) and [security resolution](https://github.com/lbise/easy-quote/issues/16#issuecomment-5580585487).

PostgreSQL itself supplies RLS. A table owner normally bypasses it, so the application connection role must not own RLS-protected tables or hold `BYPASSRLS`. This is an application and database-role design requirement, not a hosting-plan feature. [PostgreSQL's RLS documentation](https://www.postgresql.org/docs/current/ddl-rowsecurity.html) is the source for both rules.

"Strict" below means the provider's current material directly supports the relevant building block. "Conditional" means the building block can plausibly be assembled, but a configuration, support answer, or capacity and restore test remains. "Unproven" means the public material reviewed does not establish the fact. Unproven is not a claim that the feature cannot exist.

## Comparison

| Route | Runtime and durable PDF worker | PostgreSQL and RLS | Swiss location and PDF storage | Backup target | Current result |
| --- | --- | --- | --- | --- | --- |
| Infomaniak shared Node.js hosting plus Database Service | Node sites, npm and Yarn are documented. Start, stop and restart controls are documented. A separate durable worker, Chromium system libraries, browser sandbox behaviour, and resource limits are unproven. | The managed Database Service page labels PostgreSQL "Available soon". It cannot satisfy the current PostgreSQL requirement. | Infomaniak says its Node hosting is run in its Swiss data centres. Private object storage is not documented as part of this shared-hosting product. | No source found for hourly application-consistent backups with 30-day retention. | Not a strict fit today. The missing PostgreSQL offering alone stops it. |
| Infomaniak Jelastic Cloud | Docker, Node.js, and Postgre are listed. Separate application and worker containers are possible in principle. A Playwright image can include Chromium, but Jelastic does not document the required browser dependencies, sandbox mode, persistent worker supervision, or pilot capacity. | Postgre is listed as a supported SQL database. Standard PostgreSQL RLS is available only if the deployed database permits the required application-owned roles. That exact privilege model is unproven. | Jelastic says it runs exclusively in Infomaniak Swiss data centres. Its material mentions object storage, but the reviewed pages do not establish the private-bucket controls needed for PDF downloads. | Swiss Backup can connect to a Jelastic environment, but the reviewed material does not establish hourly database and PDF recovery points or 30-day retention. | Conditional only. Useful for a later documentation and load spike, not evidence of the policy target. |
| Infomaniak VPS Lite plus Swiss Backup | A Linux VM has root access and an operating system of the customer's choice. The operator can install Node, PostgreSQL, a private S3-compatible object store, Playwright and its Chromium dependencies, then run an independent worker under the OS supervisor. A 4 vCPU and 8 GB plan is only a sizing assumption. | Strict building-block fit. The operator owns PostgreSQL version, roles, migrations, RLS policies, backups, and patching. | VPS Lite is hosted in Switzerland. The operator can keep PDF objects on its private store on that Swiss VM. Swiss Backup states that copies are held three times across two data centres in Switzerland. | Conditional. Hourly encrypted database and PDF backups to Swiss Backup, 30-day pruning, deletion-ledger replay, and restore tests are all operator duties. VPS Lite has neither snapshots nor an availability SLA. | Conditional feasible route, with the clearest evidence for Swiss placement and the CHF 100 target. |
| Exoscale compute plus DBaaS and SOS, used only as the one alternative | A Swiss-zone compute instance gives a normal VM path for Node, a worker, and a Playwright image. Chromium capacity remains a test. | Managed PostgreSQL has a TLS connection URI and plans in all zones. The public material does not prove the exact roles and privileges needed for the non-owner RLS application role. | Compute instances and object buckets are zone-bound. Exoscale lists Geneva and Zurich zones. All resources can therefore be placed in a Swiss zone. | DBaaS backups are encrypted and stored in object storage. PostgreSQL Hobbyist has one day with PITR and Premium has 30 days. Neither page identifies the physical zone of those backup objects. Confirm it before treating all backups as Swiss. | Technically conditional. The Hobbyist base may fit the target, but offers one day of recovery history and no formal SLA. The 30-day tier is over budget before the VM or authentication. |

## Infomaniak evidence

### Managed products

[Node.js Hosting](https://www.infomaniak.com/en/hosting/nodejs-hosting) says the offer starts at CHF 10.91 per month, includes one Node.js site and 250 GB of SSD space. Its [Node.js setup guide](https://www.infomaniak.com/en/support/faq/2537/create-a-nodejs-website-with-infomaniak) documents npm and Yarn, source import over SSH or SFTP, supported Git repositories, version management, execution logs, and start, stop, and restart actions. Those are real Node runtime facts. They do not document a second always-on process, a queue worker, Chrome or Chromium packages, browser sandboxing, or an OS package manager. The report therefore treats those facts as unproven, rather than impossible.

The [Database Service](https://www.infomaniak.com/en/hosting/public-cloud/database) presents automated backups, point-in-time recovery, TLS and managed operations, but its engine list marks PostgreSQL "Available soon". The listed paid plans are MySQL. It is not a current managed PostgreSQL answer for RLS.

[Jelastic Cloud](https://www.infomaniak.com/en/hosting/dedicated-and-cloud-servers/jelastic-cloud) documents containers, Docker, Node.js, Postgre, object storage, Swiss data centres, and a CHF 6.31 per month example for one 128 MB, 400 MHz, 20 GB container with an IP address. That example is not a full pilot price. A publication design needs at least an application process, a worker, a database, storage, backup capacity, and authentication. The page's usage-based calculator does not publish a fixed price for that composition.

### VPS and backup path

[Infomaniak VPS Lite](https://www.infomaniak.com/en/hosting/vps-lite) lists its 4 vCPU, 8 GB RAM, 160 GB disk plan at CHF 18.00 per month, excluding VAT. It says the product is hosted in Switzerland and has root or administrator access. It also says VPS Lite has no snapshots and no availability SLA. The [Cloud VPS](https://www.infomaniak.com/en/hosting/vps-cloud) starts at CHF 38.41 per month, includes snapshots, and states a 99.99% availability commitment. Neither availability commitment nor a snapshot replaces an application-consistent PostgreSQL and object-store restore test.

[Swiss Backup pricing](https://www.infomaniak.com/en/swiss-backup/prices) lists 1 TB of cloud backup capacity at CHF 4.18 per month, excluding VAT. [Swiss Backup cloud storage](https://www.infomaniak.com/en/swiss-backup/cloud-storage) documents S3, Swift and SFTP compatibility, three copies across two Swiss data centres, and compatibility with scripts and backup software. It lets the operator implement an hourly backup and 30-day policy. It does not itself promise that schedule or retention.

A small VPS is not managed hosting. Infomaniak supplies the VM, network protections, and the Swiss Backup service. Easy Quote would own OS updates, firewall rules, TLS, secrets, Node and Chromium updates, PostgreSQL configuration and RLS roles, MinIO or equivalent object-store access policy, queue supervision, backup job alerts, retention pruning, recovery, and security logging. The same split applies to a Jelastic container image for the parts that are not operated by the PaaS.

## RPO, retention and PDF handling

For the VPS route, the following is feasible only after a restore drill shows it works:

1. Keep the application, PostgreSQL cluster, and private PDF object store on the Swiss VM. Do not use public PDF URLs. The application authorizes each download.
2. Create encrypted backups at least hourly. The backup set must include a PostgreSQL-consistent export or WAL recovery path and the PDF objects needed by published Revisions. Transfer it to Swiss Backup through its S3, Swift, or SFTP interface.
3. Prune backup sets once they pass 30 days. Keep the deletion ledger until the corresponding backup has expired, then apply it before reopening after a restore.
4. Measure a restore into an isolated environment. Check a sampled database record, private PDF retrieval, RLS behaviour, a deletion replay, and the elapsed time. Alert if an hourly run fails or the recovery point exceeds one hour.

This approach can meet the engineering targets. It is not evidence of a one-hour provider RPO, a recovery-time guarantee, or successful Chromium rendering under real Quote load. The first delivery work needs a representative Chromium publication test and a restore test with the chosen VM size.

## Pilot cost check

The following is an illustrative, VAT-exclusive monthly floor. It uses the CHF 18.00 VPS Lite plan and CHF 4.18 for 1 TB of Swiss Backup. It assumes one solo-Artisan pilot, low PDF volume, PostgreSQL, the private object store, application, and worker all fit on the 160 GB disk, and no high-availability replica.

| Item | Monthly CHF | Evidence and assumption |
| --- | ---: | --- |
| Infomaniak VPS Lite, 4 vCPU, 8 GB RAM, 160 GB | 18.00 | Published plan price. Capacity for simultaneous Chromium rendering is not tested. |
| Swiss Backup, 1 TB cloud backup capacity | 4.18 | Published price. It is deliberately much larger than the assumed pilot data set so hourly encrypted copies can remain for 30 days. |
| Application, worker, PostgreSQL, private PDF store | 0.00 incremental | Self-operated on the VM. This is labour and risk, not a managed service. |
| Swiss hosting subtotal | 22.18 | Excluding VAT. |
| Remaining budget | 77.82 | Must cover managed authentication, domain and DNS, monitoring, alert delivery, and any fixed email or other pilot infrastructure cost. |

The CHF 100 target is feasible only if the approved managed-authentication plan and every omitted recurring service total CHF 77.82 or less, excluding VAT. This report does not price or re-approve authentication. That work remains separate, as required. AI and speech usage are also outside this hosting subtotal and remain governed by their separate provider approval.

The Cloud VPS starting figure changes the visible hosting and backup subtotal to CHF 42.59, leaving CHF 57.41 before the same unpriced items. It may be the safer operational starting point because it has snapshots and an availability commitment, but this is not a technology or provider selection and the published starting price is not a quoted 8 GB configuration.

## Exoscale alternative and cost boundary

[Exoscale's zone documentation](https://community.exoscale.com/platform/dc-zones/) lists `ch-gva-2` in Geneva and `ch-dk-2` in Zurich. It says compute instances and object-storage buckets attach to a single zone. Its [PostgreSQL connection guide](https://community.exoscale.com/product/dbaas/service-specific/postgresql/how-to/connect-with-psql/) documents a TLS PostgreSQL URI. Its [backup guide](https://community.exoscale.com/product/dbaas/how-to/backups-restore/) says DBaaS backups are encrypted and stored in object storage. The [DBaaS SLA](https://community.exoscale.com/product/dbaas/service-boundaries/sla/) states that PostgreSQL Premium plans retain backups for 30 days with PITR. That SLA also names Aiven as the managed-database technology and places backup strategy and restore validation on the customer.

The minimum 30-day managed database option is Premium-4. Exoscale's own live [DBaaS price endpoint](https://portal.exoscale.com/api/pricing/dbaas-pg) lists it at CHF 0.38966 per hour. At 720 hours this is CHF 280.56. An illustrative 4 GB Compute Medium VM is CHF 33.60 per month, 20 GiB of volume is CHF 2.02, and 50 GiB of standard object storage is CHF 0.99 using Exoscale's [compute and storage price endpoint](https://portal.exoscale.com/api/pricing/opencompute) and [object-storage price endpoint](https://portal.exoscale.com/api/pricing/sos). The resulting CHF 317.17 excludes VAT, traffic, public IPs, authentication, and monitoring. It is already over the CHF 100 target.

Thirty days is a maximum in the security policy, not a required recovery-history length. The lower plans therefore do not fail retention merely because they keep less. The SLA gives PostgreSQL Hobbyist, Startup, and Business one, two, and 14 days with PITR. The backup guide says PostgreSQL PITR can restore to any time between two backup intervals. That makes a one-hour RPO plausible, but it is not a provider RPO guarantee and needs a restore drill. On the published rates, Hobbyist-2 costs CHF 41.84 per 720 hours. With the same illustrative VM, volume, and 50 GiB of object storage, the subtotal is CHF 78.44 before VAT, traffic, public IPs, authentication, and monitoring. It fits the CHF 100 cap only if those remaining costs are CHF 21.56 or less. Its one-day recovery history may be acceptable under the policy, but it leaves little operational room. A customer-managed backup layer could change the design, but then it needs the same Swiss-location confirmation, retention process, and restore evidence as the VPS route.

## Questions to close before any purchase

1. Ask Infomaniak whether a Jelastic Node or Docker worker can remain running independently of HTTP traffic, which browser sandbox mode it permits, and whether a current Playwright image with Chromium is supported at the selected resources.
2. For any managed PostgreSQL service, create a disposable database and prove the exact non-owner application role, RLS policies, connection-pool role switching if used, encrypted connection, and denied cross-Business query. Do not assume a provider's PostgreSQL label proves this access model.
3. Get written confirmation of the physical location of every live PDF object and every database and object-store backup. For Exoscale, ask specifically where DBaaS backup objects for a `ch-gva-2` or `ch-dk-2` service reside.
4. Before real Customer data, price the approved managed-authentication plan, domain and DNS, monitoring, alert delivery, taxes, and any paid support. Recheck the CHF 100 calculation with those figures.
5. Run the hourly backup, 30-day expiry, deletion-ledger replay, and full restore drill. Run representative concurrent Playwright publication jobs on the selected size. These tests establish capacity and recovery evidence that product pages cannot.

## Primary sources

All claims above come from provider product pages, provider documentation, provider live price endpoints, PostgreSQL's official manual, or the already-approved architecture and security resolutions. No secondary comparison source was used.

- [Infomaniak Node.js Hosting](https://www.infomaniak.com/en/hosting/nodejs-hosting)
- [Infomaniak Node.js setup guide](https://www.infomaniak.com/en/support/faq/2537/create-a-nodejs-website-with-infomaniak)
- [Infomaniak Database Service](https://www.infomaniak.com/en/hosting/public-cloud/database)
- [Infomaniak Jelastic Cloud](https://www.infomaniak.com/en/hosting/dedicated-and-cloud-servers/jelastic-cloud)
- [Infomaniak VPS Lite](https://www.infomaniak.com/en/hosting/vps-lite)
- [Infomaniak Cloud VPS](https://www.infomaniak.com/en/hosting/vps-cloud)
- [Infomaniak Swiss Backup pricing](https://www.infomaniak.com/en/swiss-backup/prices)
- [Infomaniak Swiss Backup cloud storage](https://www.infomaniak.com/en/swiss-backup/cloud-storage)
- [Exoscale zones](https://community.exoscale.com/platform/dc-zones/)
- [Exoscale PostgreSQL connection guide](https://community.exoscale.com/product/dbaas/service-specific/postgresql/how-to/connect-with-psql/)
- [Exoscale DBaaS backup guide](https://community.exoscale.com/product/dbaas/how-to/backups-restore/)
- [Exoscale DBaaS SLA](https://community.exoscale.com/product/dbaas/service-boundaries/sla/)
- [Exoscale pricing](https://www.exoscale.com/pricing/), [DBaaS API](https://portal.exoscale.com/api/pricing/dbaas-pg), [compute API](https://portal.exoscale.com/api/pricing/opencompute), and [object-storage API](https://portal.exoscale.com/api/pricing/sos)
- [PostgreSQL row security](https://www.postgresql.org/docs/current/ddl-rowsecurity.html)
