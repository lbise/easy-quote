# Quote PDF standard prototype

Throwaway comparison for [Decide the Quote PDF content and visual standard](https://github.com/lbise/easy-quote/issues/14). No layout has been selected yet. All Business, Customer, scope, pricing and commercial terms are fictional review fixtures, not recommended legal wording.

## Open the comparison

Open `quote-pdf-standard.prototype.html` directly in a browser, or run this from the repository root:

```sh
python3 -m http.server
```

Visit [the comparison](http://localhost:8000/docs/prototypes/quote-pdf-standard.prototype.html?variant=A). The bottom arrows switch layouts, as do keyboard left/right arrows when no form control is focused. The URL preserves the variant. Other controls reset on reload.

- A, compact table. Short Quote on one page, long Quote on two.
- B, summary first. Short Quote on two pages, long Quote on three.
- C, section-led. Full-width descriptions and separate pricing rows. Short Quote on two pages, long Quote on three.

Use the controls for French/English, short/long, VAT registration, fictional logo, accent and optional Customer signature area. These are review controls, not a proposed template designer. Browser print hides the controls. The language switch selects paired fictional text, it does not demonstrate live translation.

## PDF samples

Samples use VAT registration, no logo, accent on, and no signature area. Each layout uses identical content and calculations for the selected fixture.

| Layout | French short | French long | English short | English long |
| --- | --- | --- | --- | --- |
| A | [PDF](quote-pdf-samples/A-fr-short.pdf) | [PDF](quote-pdf-samples/A-fr-long.pdf) | [PDF](quote-pdf-samples/A-en-short.pdf) | [PDF](quote-pdf-samples/A-en-long.pdf) |
| B | [PDF](quote-pdf-samples/B-fr-short.pdf) | [PDF](quote-pdf-samples/B-fr-long.pdf) | [PDF](quote-pdf-samples/B-en-short.pdf) | [PDF](quote-pdf-samples/B-en-long.pdf) |
| C | [PDF](quote-pdf-samples/C-fr-short.pdf) | [PDF](quote-pdf-samples/C-fr-long.pdf) | [PDF](quote-pdf-samples/C-en-short.pdf) | [PDF](quote-pdf-samples/C-en-long.pdf) |

First-page previews: [A](quote-pdf-samples/A-fr-short.png), [B](quote-pdf-samples/B-fr-short.png), [C](quote-pdf-samples/C-fr-short.png).

## Checks and limits

- Chromium rendered 96 combinations of layout, language, fixture, signature, VAT and logo without document content crossing the footer or overflowing horizontally. No JavaScript errors occurred.
- Twelve generated PDFs have the expected A4 page counts, selectable text, page labels, Business and Customer names, acceptance wording and totals. Long examples retain the continued technical reference.
- Short calculation: CHF 8’486.00 less CHF 420.00 discount, plus CHF 653.35 VAT, gives CHF 8’719.35. Long calculation: CHF 15’586.50 less CHF 780.00 discount, plus CHF 1’199.33 VAT, gives CHF 16’005.83.
- Browser print samples are review evidence, not a selected production renderer. Pagination uses fixture-specific page breaks and does not establish arbitrary-content pagination correctness, accessibility conformance, physical printer quality or other-browser support.
- The short fixture has six lines, the long fixture eight. Included, optional and excluded lines remain visible but do not enter the fixed total. Long bookcase details explicitly continue under the original line number.

Review the hierarchy, readability and page count before choosing a layout or combining parts. The ticket stays open pending the human verdict and remaining content/layout decisions.
