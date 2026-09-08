# Transactional email feasibility

Verified: 2026-09-08  
Issue: [#31](https://github.com/lbise/easy-quote/issues/31)  
Decision owner: [#18](https://github.com/lbise/easy-quote/issues/18)

## Result

Infomaniak Mail Service has a documented authenticated SMTP route that is technically sufficient for a small Better Auth email and password pilot. This is not a provider selection or deployment approval. It only removes the question of whether the preferred Swiss candidate has a documented path.

The narrow use here is one-to-one operational mail: email verification, password reset, account-security notices, and already-approved inactivity or deletion notices. It excludes Customer records, Quote prose or PDFs, Business Guidance, marketing, and any customer-Quote delivery. No account, DNS record, credential, message, purchase, or configuration was made during this check.

No alternative was examined. Infomaniak has documented application SMTP, delivery-failure reports, domain authentication, and a low-volume price and limit, so the ticket's condition for researching one did not arise.

## Fit with Better Auth core

Better Auth core asks the application to supply the delivery function. Its `sendVerificationEmail` callback receives the Artisan email and verification URL, and its `sendResetPassword` callback receives the email and reset URL. Better Auth says it works with any transactional email provider. The service does not provide a built-in sender or operate a paid Better Auth Infrastructure add-on. [Better Auth email documentation](https://better-auth.com/docs/concepts/email).

Infomaniak documents authenticated sending from an application at `mail.infomaniak.com`, port 587 with STARTTLS, using the complete sending address and its generated password. SMTP authentication is mandatory. It documents this for website and CMS application mail, including contact forms and e-commerce. [Authenticated website sending](https://www.infomaniak.com/en/support/faq/2023/use-authenticated-email-sending-from-a-website).

That makes SMTP a practical adapter behind those two Better Auth callbacks. The cited Infomaniak guide is scoped to websites or CMSs hosted by Infomaniak. It does not expressly document SMTP relay from a Fastify host elsewhere. If the selected Swiss application host is not Infomaniak, get that confirmation before relying on this result. Account-security and lifecycle notices are separate application mail. Better Auth's cited callbacks do not make them automatic, so a later design must own their triggering, translation, rate limits, and tests. Infomaniak's API does **not** expose a connection to its email service, according to its mail-server protocol guide. This route is SMTP, not an HTTP sending API. [Mail server ports and protocols](https://www.infomaniak.com/en/support/faq/468/understanding-mail-server-ports-and-protocols).

## Permitted small-pilot use and limits

Infomaniak's documentation distinguishes ordinary Mail Service sending from high-recipient sending. It says its Newsletter tool is the suitable tool for a large number of recipients. The Mail Service limit is 100 recipients per message. [Recipient limit](https://www.infomaniak.com/en/support/faq/580/understanding-the-limits-on-the-number-of-recipients-per-email). Its application-SMTP guide documents automated website mail, including contact forms and e-commerce, which is the closest documented use to this one-to-one account mail. That is not the route for bulk marketing. No reviewed public term expressly classifies Better Auth mail as an allowed transactional category or marketing as prohibited, so confirm that interpretation with Infomaniak before purchase. This research does not establish permission for marketing or future Quote delivery.

For the cautious pilot assumption, use the published Mail Service Starter limit: one address and 200 recipient deliveries in a rolling 24-hour period. The same limit page lists 500 for Premium and says a justified, authenticated written request can change rules for a paid address. It counts each recipient separately. [Outgoing-mail limits](https://www.infomaniak.com/en/support/faq/2065/understanding-the-limits-on-outgoing-emails-per-24-hours). Keep the application below 200 per rolling day and rate-limit verification and reset requests. Do not treat an increased limit as approved until Infomaniak confirms it.

The public price page lists Mail Service from CHF 2.29 per month. [Mail Service pricing](https://www.infomaniak.com/en/ksuite/service-mail/prices). That leaves CHF 97.71 of the CHF 100 monthly infrastructure pilot ceiling for the application, database, domain, and any other selected infrastructure. The mail cost itself fits. The total cannot yet be confirmed because those other costs and the final plan are outside this research. The pricing comparison says 500 emails per day while the limits FAQ says Starter has 200, so procurement must confirm the exact plan and effective quota before relying on either number.

## Sender identity and secrets

A later setup needs a dedicated sender address on an Easy Quote-controlled domain, the associated Mail Service, and the generated SMTP password. The application must use that same complete address as its SMTP user and sender. Do not share a human mailbox password.

Before sending, the domain owner must configure and verify SPF, DKIM, and DMARC. Infomaniak's Global Security tool checks all three. If DNS lives elsewhere, it exposes the DKIM record to add there. DNS changes may take up to 48 hours to propagate. [SPF, DKIM, and DMARC](https://www.infomaniak.com/en/support/faq/2692/automatically-check-spfdkimdmarc). This is a future approval and DNS task, not an instruction to make the change now.

Keep the SMTP password and `BETTER_AUTH_SECRET` only in the selected deployment secret store. Give the running service the least access needed, rotate and revoke the SMTP device password if exposed, and do not place either secret in source control, client bundles, error reports, or logs. Application logs must record only a redacted delivery event and provider error class. They must not hold reset or verification URLs, tokens, SMTP credentials, or message bodies.

## Failure handling and monitoring

The authenticated-SMTP path returns delivery failures to the sending mailbox. Infomaniak sends an initial temporary-failure report while it retries, then a final non-delivery report with the reason if delivery still fails. It documents failures for invalid recipients, SPF, recipient spam filtering, full mailboxes, rate limits, and sender reputation. [Delivery-failure reports](https://www.infomaniak.com/en/support/faq/858/troubleshooting-a-mail-issue-following-an-error-report).

This is enough to operate a small pilot only if somebody monitors the dedicated sender mailbox and the application records accepted, SMTP-rejected, temporary-failure, and final-failure events without content. A final failure must not mark an address verified, reset a password, or silently complete a deletion or inactivity notice. The public material reviewed here does not document a bounce webhook, event API, or delivery dashboard. Reconciliation of mailbox reports and a support path are therefore operational work still to design.

## Processing location and approval gaps

Infomaniak says its Mail Service is developed, hosted, and operated in Switzerland, and that it backs up mail in two Swiss data centres. Its trust centre also says it provides a processing agreement under GDPR and the Swiss FADP. [Trust centre](https://www.infomaniak.com/en/trust-center). Its legal-documents page links the current [data-processing agreement](https://www.infomaniak.com/documents/privacy/DPA/Accord_sur_le_traitement_des_donnees_(DPA).pdf) and [technical and organisational measures annex](https://www.infomaniak.com/documents/privacy/DPA/appendix/DPA_annexe_mesures_techniques_organisationnelles_v20240501.pdf).

That establishes the provider's stated Swiss storage and its published DPA material. It does not establish the terms that Easy Quote needs to approve. The reviewed primary sources do not give a Mail Service-specific subprocessor list, contractual support-access countries, retention for SMTP queues, sent-message logs, bounces, or backup deletion, nor a documented webhook retention period. "Unlimited" mailbox storage on the price page is capacity, not a retention commitment. Review the DPA and annex, obtain the applicable agreement, confirm those points with Infomaniak, and define sender-mailbox retention before processing real Artisan identities.

Infomaniak advertises email, phone, and chat support seven days a week. [Mail Service](https://www.infomaniak.com/en/ksuite/service-mail). It does not state the country from which each support interaction or account-access request is handled. Treat that as an approval gap rather than assuming that Swiss storage answers it.

Swiss sender storage also does not keep an email in Switzerland after hand-off. The SMTP service delivers to the recipient's mail server, and that provider and mailbox can be outside Switzerland or the EEA. The delivery-failure documentation itself distinguishes Infomaniak's sending server from the recipient server. Easy Quote must describe this as recipient-directed international transmission, not promise Swiss-only email processing. The sender provider's storage location and the recipient mailbox location are separate facts.

## Conditions before a later implementation decision

1. Confirm the exact Mail Service plan, contract price, 24-hour quota, and whether the sender mailbox can receive and retain delivery reports within the pilot budget.
2. Approve the DPA, annex, subprocessors, support-access countries, queue, log, bounce, backup, and deletion retention. Record the controller and processor roles.
3. Authorise the sender domain and make the required SPF, DKIM, and DMARC DNS changes. Use a dedicated SMTP credential in the deployment secret store.
4. Design an operator response for SMTP rejection, temporary and final delivery failure, and quota exhaustion. Prove it with disposable accounts before real data.
5. Keep every message to the stated account and lifecycle purpose. Include no Customer, Quote, PDF, or Business Guidance content. State plainly that recipient mailbox processing can occur abroad.

No provider was selected by this report. #18 owns that choice.

## Follow-up: VPS and free-address entitlement

Verified: 2026-09-08

Status: the user has confirmed Infomaniak VPS subject to entitlement and terms. This note checks that entitlement only. It does not reopen the choice or approve provider processing.

Neither VPS Lite nor Cloud VPS includes a ready-to-use Mail Service mailbox. Infomaniak calls the VPS an unmanaged offer, separate from its messaging offer. [VPS distinction](https://www.infomaniak.com/en/support/faq/1730/understanding-the-difference-between-a-cloud-server-and-a-vps). The Cloud VPS page includes 2,500 free Newsletter credits per month, not an email address. Its own description calls them campaign credits. [Cloud VPS](https://www.infomaniak.com/en/hosting/vps-cloud). VPS Lite does not list either a Mail Service or Newsletter credits among its included features. [VPS Lite](https://www.infomaniak.com/en/hosting/vps-lite). Do not substitute the Cloud VPS Newsletter allowance for account mail. It is a campaign product and outside this research's allowed use.

There are two different "free address" offers. Mail Starter gives one free address on a paid custom domain. Infomaniak says the paid domain is the cost for that simple offer, and that its price may be under CHF or EUR 5 per year depending on the domain. It identifies Starter as a one-address Mail Service. [Suites and Mail Starter](https://www.infomaniak.com/en/support/faq/2497/discover-the-different-infomaniak-suites-ksuite-pro-my-ksuite). This is the relevant entitlement for an Easy Quote sender such as `auth@example.ch`. It is separate from the VPS charge and from the paid Mail Service price shown elsewhere.

The personal my kSuite offer is also one free address, but only on an Infomaniak generic domain such as `ik.me`, `etik.com`, or `ikmail.com`. It is not a VPS benefit or a custom-domain mailbox. It requires a valid mobile number, limits one address per account, and is limited to listed OECD-country residents. [my kSuite limits](https://www.infomaniak.com/en/support/faq/2485/understanding-the-limitations-of-the-my-ksuite-offer). It can create a device password for IMAP and SMTP, but that documentation describes mail-client configuration, not permission for automated application sending. [my kSuite passwords](https://www.infomaniak.com/en/support/faq/2904/manage-my-ksuite-passwords-ikme-etikcom-ikmailcom). Do not use it as the Easy Quote sender.

Mail Starter is a Mail Service, so the documented Mail Service device-password procedure covers an SMTP password for website sending. It uses `mail.infomaniak.com`, port 587 with STARTTLS and authenticated SMTP. [Mail Service device password](https://www.infomaniak.com/en/support/faq/1321/add-a-device-create-a-mail-password-from-the-mail-service) and [SMTP settings](https://www.infomaniak.com/en/support/faq/468/understanding-mail-server-ports-and-protocols). Use the cautious Starter quota already recorded above: 200 recipient deliveries in a rolling 24-hour period. [Outgoing-mail limits](https://www.infomaniak.com/en/support/faq/2065/understanding-the-limits-on-outgoing-emails-per-24-hours).

An external registrar is not ruled out. Infomaniak's ordering guide says an existing external domain may need its MX record set in the DNS zone. [Order email addresses](https://www.infomaniak.com/en/support/faq/1944/order-email-addresses-from-infomaniak). It does not explicitly say in the same source that Mail Starter's free-address entitlement applies to every external domain. Confirm that entitlement, the selected TLD's renewal charge, the exact Starter quota, and the apparent conflict with the public paid Mail Service price of "from CHF 2.29/month" before purchase. Those are the remaining commercial ambiguities. No account, order, login, DNS change, credential, or message was made for this follow-up.
