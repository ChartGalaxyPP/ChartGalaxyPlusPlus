# ChartGalaxy++ QA

This package contains **1,266 questions on 1,000 chart IDs**, with reference answers and URL metadata for **1,006 evaluated image encodings**. It distributes **URLs only, without original image files**. The benchmark revision is `coverage1000_1266_20260923`; the paper's main QA comparison and ablations use all 1,266 questions.

Question text, question IDs, reference-answer strings, chart IDs, numeric question-type codes, and per-question image hashes are preserved. Some answers are strings containing JSON lists; do not automatically parse or normalize them into another answer representation.

The paper compares image-only and scene-graph-augmented QA using graphs produced by the dataset annotation pipeline. Download the QA data package from the project GitHub Releases.

## Files

| File | Contents |
| --- | --- |
| [questions.json](questions.json) | All 1,266 questions and reference answers, with `image_url` and `source_url` fields |
| [questions.csv](questions.csv) | The same records as UTF-8 CSV |
| `charts.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | Chart IDs, associated question IDs, and evaluated image hashes |
| `image_references.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | One entry per evaluated image hash, with URLs, dimensions, provenance, and available main-dataset matches |
| [manifest.json](manifest.json) | Dataset counts, benchmark revision, and URL coverage |
| `SHA256SUMS` | Checksums for all package files except the checksum list itself |

## Image URLs

Each question provides `image_url` for an available image link and `source_url` for an available source page. **Unavailable URLs are empty strings (`""`)**, including in CSV. Source pages and archive links are not substituted for image URLs. Additional source references, archive-member metadata, and URL candidates remain in `image_references.jsonl`.

Join questions to image references using `image_sha256`. The hash identifies the image encoding used in evaluation; URLs may return a different encoding or resolution. Six chart IDs have two image encodings, so use `chart_id` to count charts and retain the per-question hash to distinguish the evaluated inputs.

| Available URL metadata | Image encodings |
| --- | ---: |
| Image URL | 290 |
| Source page only | 54 |
| No image or source-page URL | 662 |

URLs are taken from retained metadata. Their current availability and returned bytes have not been checked. The QA archive contains no image binaries, including synthetic QA images. In the main dataset, real images remain URL/source/archive references and synthetic images are provided as PNGs.

`main_dataset_matches` records verified correspondences to the pre-replacement main dataset where available. It does not assert that the dataset graph was the exact simplified scene graph used in the QA experiments. QA images do not add records to the main dataset's chart count.

## File integrity

The package checksums cover its metadata and question files. JSON and CSV contain matching question records. Questions and reference answers are preserved without modification.

## Attribution and license

Contributed ChartGalaxy++ question/answer data and documentation follow the project's CC BY-NC 4.0 release policy. Images referenced by URLs retain their owners' rights. Source collections such as InfoChartQA are identified in the reference records when verified. The benchmark's annotation license does not grant rights to third-party imagery.
