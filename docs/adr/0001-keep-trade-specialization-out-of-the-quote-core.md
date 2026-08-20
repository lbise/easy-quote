# Keep trade specialization out of the Quote core

Easy Quote will use one trade-agnostic Quote model, publication lifecycle, and AI output structure. A concept belongs in the core when it affects the commercial meaning, calculation, publication, or revision of Quotes across trades. Trade vocabulary and Business-specific defaults remain editable content because the reference corpus shows common commercial structure but does not justify separate workflows or schemas for individual trades.

## Consequences

The core represents ordered sections and lines, descriptions, technical details, quantities, units, pricing, commercial treatment, performer, material supplier, adjustments, VAT, terms, and Quote Revisions. Rooms, floors, facade orientations, work phases, and product positions are section names or descriptive content rather than typed fields. Quote-level notes hold shared specifications, while line-level technical details hold position-specific differences. Every Artisan can use the complete workflow without configuring a trade profile or catalog.

An Artisan Business may reuse confirmed section names, descriptions, technical details, units, prices, performer and material-supplier defaults, standard terms, trade vocabulary, and AI suggestion and clarification wording. Easy Quote copies this content into a Quote as ordinary editable data. Configuration may improve suggestions and clarifications but must not add trade-specific states, validation rules, screens, or AI schemas. Onboarding may collect a free-text Business description, but it will not require a closed trade classification.

Easy Quote will not derive production prices, discounts, warranties, or commercial terms from the reference corpus. In particular, the sole carpenter/joiner pricing source is an invoice rather than a catalog. The Artisan must confirm reusable prices and terms.

The MVP defers typed locations and product positions, inherited specification overrides, diagrams, nested product components, dimension-aware configurators, material cut lists, specialized trade calculations, trade-standard rule engines, arbitrary custom fields, and trade-, canton-, and service-specific compliance rules. Generic publication and calculation checks remain part of the core. Until repeated evidence justifies dedicated structure, the Artisan records relevant dimensions, configurations, standards, and bundled component quantities as technical text.
