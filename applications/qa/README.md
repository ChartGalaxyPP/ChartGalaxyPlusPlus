# ChartGalaxy++ QA

This package contains **1,266 questions**, grouped into **1,000 chart IDs**, and **all 1,006 original evaluated image files** (530,006,283 bytes). The supplied benchmark revision is `coverage1000_1266_20260923`. The paper's main QA comparison and ablations use all 1,266 questions.

All supplied question fields are preserved: question text, question IDs, reference-answer strings, chart IDs, numeric question-type codes, relative image paths, and per-question image hashes. Some answers are strings containing JSON lists; do not automatically parse or normalize them into another answer representation.

The paper compares image-only and scene-graph-augmented QA. The scene graphs are produced by the dataset annotation pipeline. This package distributes the questions, reference answers, original images, and their mappings. Download the dataset package from the project GitHub Releases.

## Files

| File | Contents |
| --- | --- |
| [questions.json](questions.json) | All 1,266 questions and reference answers |
| [questions.csv](questions.csv) | The same records as UTF-8 CSV |
| `charts.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | Chart IDs, associated question IDs, and evaluated image encodings |
| `image_references.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | One entry per evaluated image SHA-256, with dimensions, references, delivery status, and exact main-dataset matches when available |
| `images/` | All 1,006 original evaluated image files, including real images |
| [manifest.json](manifest.json) | Dataset counts, source hashes, benchmark revision, and image coverage |
| `SHA256SUMS` | Checksums for all package files except the checksum list itself |

## Image identity and delivery

Open each question's **`image`** path relative to this QA directory. Join a question to `image_references.jsonl` using **`image_sha256`** for dimensions and available provenance. Every path resolves to its original evaluated file, without resizing or re-encoding. Count charts using `chart_id`; do not count image files as charts. Six chart IDs have two image encodings each. They include resolution or encoding differences, so preserve the image hash assigned to each question instead of substituting one file for all questions on a chart.

Image delivery is scoped by component. **QA includes all original evaluated images**. In the **main dataset**, real images remain URL/source/archive references only, while synthetic images are provided as PNGs. QA images do not add records to the main dataset's 217,195-chart count.

| Provenance status | Image encodings | Meaning |
| --- | ---: | --- |
| `main_dataset_synthetic` | 79 | Exact byte matches to synthetic images before asset replacement |
| `public_reference_available` | 345 | Retained source-page, image, or upstream archive references are available |
| `source_reference_unresolved` | 582 | Public source references remain unresolved; original image files are included |

All 1,006 entries have `delivery_status: bundled_evaluation_image` and a local `image_file` path. The table describes the separate `source_reference_status` field. Its 582 unresolved provenance records affect 793 questions, but do not prevent access to their images. Available URLs remain optional provenance metadata; their current reachability and returned bytes have not been tested.

During preparation, 140 evaluated image files had an exact byte match to the pre-replacement main dataset, all in its test split (79 synthetic and 61 real). Other exact source or decoded-pixel matches are tracked during preparation, but should not be mistaken for membership in the main dataset. An image that lacks a byte match may be resized, re-encoded, or from another source; absence of a match does not establish which explanation applies.

`main_dataset_matches` identifies corresponding files and records where verified. It does not assert that the dataset's graph was the exact simplified scene graph used in the QA experiments.

## File integrity

The manifest records dataset counts and source checksums. Numeric `qtype` codes are preserved in the question files.

The release checks file integrity, JSON/CSV agreement, image decoding, and question-to-image mappings. Questions and reference answers are preserved without modification.

## Attribution and license

Contributed ChartGalaxy++ question/answer data and documentation follow the project's CC BY-NC 4.0 release policy. Original source images retain their owners' rights. Source collections such as InfoChartQA are identified in the reference records when verified. Do not infer a new license for third-party imagery from this benchmark's annotation license.
