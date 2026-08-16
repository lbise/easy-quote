# Artisan Quote reference corpus

This corpus contains sanitized, text-based reconstructions of six privately supplied Swiss artisan documents. It exists to support product, Quote-model, capture, PDF, and validation decisions without storing the source PDFs or identifying their parties.

## Corpus

| Sample | Kind | Trade | Source shape | Notable patterns |
| --- | --- | --- | --- | --- |
| [Earthworks and drainage](quotes/earthworks-drainage.md) | Quote | Earthworks | 3 pages | Work sections, unit lines, acceptance block, price-adjustment terms |
| [Replacement windows](quotes/replacement-windows.md) | Quote | Window fabrication and installation | 7 pages | Technical specification, product positions, discount, warranty |
| [Terrace and planting bed](quotes/terrace-landscaping.md) | Quote | Landscaping | 2 pages | Simple project sections, materials and labour, VAT summary |
| [Plastering and painting](quotes/plastering-painting.md) | Quote | Plastering and painting | 3 pages | Room-based scope, options, exclusions, estimates, levy |
| [Interior carpentry renovation](quotes/interior-carpentry.md) | Quote | Carpentry/joinery | 4 scanned pages | Two scope sheets, mixed units, handwritten selection and discount |
| [Ventilated facade pricing source](pricing/carpentry-facade-invoice.md) | Invoice used as a pricing source | Carpentry/joinery | 4 pages | Area-based sections, bundled work, deposit deduction |

The corpus therefore covers five trade families and five Quotes. The sixth document is intentionally classified as an invoice rather than a Quote; it is useful only as evidence for reusable carpenter/joiner descriptions, units, and prices.

## Sanitization

The source PDFs remain outside the repository. These derivatives:

- replace every person, business, address, direct contact, account, tax identifier, signature, document number, and project reference with neutral placeholders;
- omit logos, letterhead artwork, signatures, metadata, and the original filenames;
- paraphrase descriptions while retaining their commercial meaning;
- alter and round quantities and monetary amounts while retaining realistic ordering and calculation patterns;
- retain only non-identifying standards, units, document structure, and layout observations;
- call out illegible or ambiguous source content rather than guessing.

The reconstructions are reference evidence, not templates, legal advice, accounting records, or calculation test fixtures.

## Coverage gaps and unavailable material

At the time of preparation, the following material was unavailable:

- a true carpenter/joiner catalog or price list; the supplied pricing source is a single invoice;
- English-language Quotes;
- German- or Italian-language Quotes;
- a sequence showing one Quote through draft, finalization, customer negotiation, and explicit revision;
- a duplicated Quote reused for a different Customer;
- accepted and rejected Quote examples;
- examples with alternative VAT statuses, including a non-VAT-registered Artisan Business;
- examples for emergency work, time-and-materials work, or uncertain quantities settled after completion;
- machine-readable exports from quoting software;
- general import datasets or historical customer/catalog data.

The source owner may later provide or correct this gap list. Future additions must follow the same sanitization rules and must never commit the originals.

## Cross-sample observations

The corpus demonstrates that the MVP must be able to represent:

- sections by phase, room, facade, floor, or product position;
- lines priced by item, metre, square metre, cubic metre, hour, lump sum, or package;
- bundled descriptions with a single amount and component quantities embedded in prose;
- optional, estimated, excluded, included, and customer-supplied work;
- section subtotals, page carry-forwards, discounts, levies, VAT, deposits, and final totals;
- long technical descriptions, commercial terms, warranties, validity, payment terms, and acceptance blocks;
- incomplete or inconsistent source input, handwritten amendments, and customer selections;
- multi-page layouts with repeated identity, Quote number, date, headings, and page numbering.
