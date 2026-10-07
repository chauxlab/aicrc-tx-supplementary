# Supplementary material — Artificial Intelligence in Therapeutic Decision-Making in Colorectal Cancer

Supplementary material for the integrative review (Whittemore & Knafl, 2005) on artificial intelligence applied to therapeutic decision-making in colorectal cancer (`AICRC_TX`). The manuscript is in preparation.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23222097.svg)](https://doi.org/10.5281/zenodo.23222097)

It documents the review process — protocol, search, screening, critical appraisal, and data extraction — for the complete corpus of **227 included studies**.

No full-text PDFs are hosted here. Every included, excluded, and unretrieved study is identified by DOI, PMID, and/or a direct record link (`primary_url`), so the original article can be retrieved from its source of record.

## Contents

| Path | Contents |
|---|---|
| `protocol/` | Review protocol (approved 2026-09-09; not registered in a public repository). |
| `search/` | Combined database search corpus (PubMed + Europe PMC, RIS, 848 entries / 846 unique records after deduplication). |
| `screening/` | `included-studies.csv` (n=227), `excluded-full-text.csv` (n=44, with reason), `full-text-not-retrieved.csv` (n=99 title/abstract-included records whose full text could not be retrieved). |
| `prisma/` | `prisma-counts.csv` — exact n at every PRISMA 2020 stage. |
| `risk_of_bias/` | Critical appraisal for all 227 studies (MMAT 2018, PROBAST, reduced TRIPOD-AI), the frozen applicability rules (v2) and the applicability ratings, with codebook. |
| `extraction/` | Full extraction dataset (n=227), codebook v1.0, and the blinded verification (discrepancies and adjudication). |
| `analysis/` | Evidence-map cross-tabulations (therapeutic decision × data modality / validation level / translation stage) and AUC by validation level. |
| `logs/` | Decision, workflow, and data-quality logs. |

## Review design

- **Databases:** PubMed (three decision sub-blocks) and Europe PMC, searched 2026-09-09; 927 + 636 raw records → 846 after deduplication.
- **Screening:** title/abstract by two independent reviewers with consensus (370 included / 476 excluded); full text 271 retrieved (99 not retrieved), consensus 227 included / 44 excluded.
- **Appraisal:** MMAT 2018 for all studies; PROBAST and a reduced TRIPOD-AI checklist for prediction-model studies; applicability rules (v2) frozen before an independent agreement sample.
- **Extraction:** codebook v1.0 frozen after a 12-study pilot; a blinded second extraction of 24 studies (10.6%) showed 95.3% agreement, with discrepancies adjudicated against the full text.

## AI use statement

AI tools were used in the screening support, critical appraisal, data extraction, and drafting processes of this review: Claude (Anthropic). All AI-assisted outputs were reviewed by the authors, who take full intellectual responsibility for the content. The authors adhere to COPE guidelines on AI in publication ethics and confirm that this use has been managed responsibly and ethically. Records flagged for review in the extraction dataset are documented in `extraction/extraction-codebook.md`.

## Citing this repository

If you reuse this material, please cite the associated manuscript and, for the dataset itself, this repository's Zenodo record: https://doi.org/10.5281/zenodo.23222097.

## License

Data and documentation are released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). No third-party full texts are included.
