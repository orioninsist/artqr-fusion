# ArtQR Fusion

ArtQR Fusion is a Google Colab notebook for creating readable artistic QR codes from a logo, brand image, or artwork. It does not simply paste a logo on top of a QR code. The notebook generates a clean QR code, blends the QR structure into the artwork with luminance and halftone logic, protects the finder patterns and quiet zone, then validates the result with QR decoders before packaging delivery files.

The project is designed for Fiverr, Upwork, and client delivery workflows where the final result must look branded and still scan reliably.

## What It Produces

- Logo-aware artistic QR codes
- Halftone logo QR designs
- Clean styled QR alternatives
- Print-ready PNG and PDF outputs
- JPG delivery files
- Transparent PNG variants when enabled
- Clean vector QR SVG for the non-artistic base QR
- Social media preview image
- QR test report
- Customer delivery brief
- Fiverr / Upwork readiness checklist
- ZIP delivery package

Important vector note: the artistic logo-fused QR is delivered as high-resolution raster artwork, usually PNG/JPG/PDF. The clean base QR can be exported as SVG. Do not sell the artistic halftone/logo-fused result as a true editable vector unless it has been manually vectorized and retested.

## Google Colab Workflow

This repository is Colab-first. The intended workflow is:

1. Open `qrcode_logo_merge.ipynb` in Google Colab.
2. Run the setup cells.
3. Upload the customer logo or artwork.
4. Enter the customer QR link or text in `qr_data`.
5. In the commercial form, choose:
   - `service_tier`: `basic`, `standard`, or `premium`
   - `portfolio_example`: the closest use case
   - `style_preset`: optional manual style override
6. Run the remaining cells.
7. Download `qr_fusion_delivery.zip`.
8. Manually scan the final QR on a phone before sending it to the client.

## Service Packages

| Package | Best For | Outputs |
| --- | --- | --- |
| Basic | Simple clean branded QR | Final PNG, clean QR, prepared artwork, JPG, test report |
| Standard | Client-ready branded QR | Basic outputs plus soft/balanced/strong variants, print PNG/PDF, social preview, clean SVG |
| Premium | Fiverr/Upwork premium delivery | Standard outputs plus logo-aware halftone final, layout pack, transparent variant, customer brief, readiness checklist |

Suggested gig wording:

- Basic: I will create a clean branded QR code with your logo and verify that it scans.
- Standard: I will create a high-quality artistic QR code package with print-ready files and multiple design variations.
- Premium: I will create a premium logo-aware artistic QR delivery pack with halftone styling, social preview, print files, and scan test report.

## Portfolio Examples To Generate

Use the notebook's `portfolio_example` dropdown to generate portfolio samples one by one:

1. `restaurant_qr`
2. `instagram_linktree_qr`
3. `business_card_qr`
4. `product_label_qr`
5. `luxury_brand_qr`
6. `event_ticket_qr`
7. `colorful_logo_qr`
8. `black_white_clean_qr`
9. `halftone_logo_qr`
10. `social_media_preview_qr`

Recommended portfolio structure:

```text
portfolio/
  01_restaurant_qr.png
  02_instagram_linktree_qr.png
  03_business_card_qr.png
  04_product_label_qr.png
  05_luxury_brand_qr.png
  06_event_ticket_qr.png
  07_colorful_logo_qr.png
  08_black_white_clean_qr.png
  09_halftone_logo_qr.png
  10_social_media_preview_qr.png
```

## Readability Guarantee

The notebook tests generated QR files with available decoder engines:

- `pyzbar` / ZBar
- OpenCV QR detector
- ZXing C++ when available

Commercial acceptance is controlled by `min_decoder_passes`. Premium delivery should usually require at least 2 decoder passes. The ZIP package includes a test report showing which files passed, which decoders read them, and what text/link was decoded.

Practical guarantee wording for gigs:

> I verify the final QR with multiple decoder tools and include a scan test report. For printed use, please test one sample print before mass production because paper, ink, size, lighting, and material can affect scanning.

## Buyer Brief Questions

Ask every customer for:

- Target URL or text for the QR code
- Logo/artwork file, preferably high resolution PNG/SVG/PDF
- Brand colors or preferred style
- Usage place: website, menu, sticker, business card, poster, product label, packaging, event ticket
- Preferred package: Basic, Standard, or Premium
- Print size if the QR will be printed
- Any text or layout notes

## Quality Guide

Best results come from:

- Short URLs instead of long tracking links
- High-resolution, high-contrast logos
- Simple artwork with clear shapes
- QR error correction level `H`
- Quiet zone of 4-6 modules
- Manual phone scan before delivery

Risk cases:

- Very long URLs create dense QR codes and reduce visual freedom.
- Thin text inside logos may disappear in halftone styles.
- Low contrast artwork can need stronger QR contrast.
- Transparent or dark backgrounds may scan differently across apps.
- Print materials can reduce contrast, so 300 DPI PDF/PNG should be tested before bulk printing.

## Colab Requirements

The notebook installs dependencies inside Colab:

```bash
apt-get install -y libzbar0
pip install "Pillow<12" opencv-python pyzbar segno numpy zxing-cpp
```

No separate local setup is required for the intended Colab workflow.

## Delivery Files

The generated `qr_fusion_delivery.zip` can include:

- `final_readable_art_qr.png`
- `clean_qr.png`
- `prepared_artwork.png`
- `commercial/halftone_logo_qr.png`
- `commercial/styled_clean_qr.png`
- `commercial/art_qr_soft.png`
- `commercial/art_qr_balanced.png`
- `commercial/art_qr_strong.png`
- `commercial/print_ready_300dpi.png`
- `commercial/print_ready_300dpi.pdf`
- `commercial/final_delivery.jpg`
- `commercial/clean_qr_vector.svg`
- `commercial/social_preview_1080.png`
- `commercial/qr_test_report.txt`
- `commercial/customer_delivery_brief.txt`
- `commercial/gig_readiness_checklist.txt`
- `delivery_manifest.txt`

## Fiverr / Upwork Gig Starter

Title ideas:

- I will create a custom artistic QR code with your logo
- I will design a scannable branded QR code for print and social media
- I will create a premium logo QR code with scan test report

Short description:

> I will turn your logo or brand artwork into a scannable custom QR code. You will receive high-resolution files for web or print, plus a QR scan test report so you can deliver it with confidence.

Revision policy suggestion:

- Basic: 1 revision
- Standard: 2 revisions
- Premium: 3 revisions

Do not promise unlimited redesigns. Separate link changes, logo changes, and full style changes as new revisions.
