# Image-to-scene-graph benchmark

This package contains the paper-selected **Image2JSON checkpoint-100000 and ten baselines**, evaluated on the same **1,000 test charts**. It includes **11,000 per-image score records**, the final paper table, metric definitions, and exact main-dataset test bindings. All 88 table values were recomputed from these saved scores, without new model or judge calls.

The package also contains all 11,000 final prediction/failure records, 11,000 inference records with the saved responses and exact request text, 1,000 scoring references, and all eleven model protocols. Annotation, training, evaluation, and review pipeline code is excluded; this is an experimental-data package rather than an executable evaluation toolkit.

## Files

| File | Contents |
| --- | --- |
| `test_index.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/image-to-scene-graph.tar.gz)) | 1,000 main-dataset test records with sample and benchmark IDs, content hashes, and Parquet locations |
| `scores/<model>.jsonl.gz` | 1,000 score records per selected model, including matched-node attributes and structural counts |
| `predictions/<model>.jsonl.gz` | Scored native graphs, partial-recovery projections, or explicit failures; score hashes and exact denominators |
| `inference_records/<model>.jsonl.gz` | Saved model responses, original/supplied-repaired outputs, request text, and attempt outcomes |
| `scoring_references.jsonl.gz` | Exact 1,000 evaluator reference graphs and their original hashes |
| `protocols/<model>.json` and `protocols/assets/` | Inference settings, archived task definitions, decoder schemas, and grammars |
| `PREDICTIONS.md` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/image-to-scene-graph.tar.gz)) | Raw/final distinctions, failed outputs, request provenance, and recovery |
| [paper_table.json](paper_table.json) | Paper-selected model rows, metrics, aggregation contract, and source-result hash |
| [metric_definitions.json](metric_definitions.json) | Frozen metric and aggregation settings |
| [manifest.json](manifest.json) | Package counts, selection, and remaining materials |
| `SHA256SUMS` | File checksums, excluding the checksum list itself |

Use `identity.id` to join each score to `test_index.jsonl`'s `benchmark_id`. Its `sample_id` joins to the released `sample_index.jsonl.gz`; retained Parquet coordinates describe the historical packaging. Real source images remain URL/archive references; synthetic images are included as PNGs in the main dataset. This benchmark does not duplicate source-image binaries.

The exported model selection excludes the historical checkpoint-16500. Status fields retain failed and partially recovered predictions; do not discard those records when aggregating.

## Scoring and aggregation

Element/group kind is a hard matching constraint. Concrete type is a soft field: equal types receive 1, otherwise 0. Bounding box, description, text, color, and type have equal weights of 0.2, renormalized over applicable reference fields. Bounding-box similarity uses normalized GIoU before maximum-weight one-to-one assignment. Initial assignment uses the frozen lexical description proxy; the final description score comes from GPT-5.6 Luna's integer 0–5 judgment divided by 5, without rematching.

- **Node F1:** compute soft F1 for each image and average over all 1,000 images.
- **Parent F1:** sum correct, reference, and predicted direct-parent edges across images, including ROOT edges, then compute global micro F1.
- **Spatial F1:** pool relation counts within each image, compute F1, then average over all 1,000 images. Relations comprise relative position, alignment, bounding-box overlap, and Boolean box proximity.
- **Type, Box, Text, Color, Description:** pooled means over applicable matched-node fields.

Failed outputs remain in the denominator. Existing partial recovery and rejected-node denominators are preserved. These saved scores support recomputing aggregates. The accompanying references, scored predictions, request text, and configuration data expose the inputs and outputs; evaluation implementation code is not included.

`original_score_sha256` records the original private score file's hash. The public JSONL wrapper adds provenance fields, so its serialized bytes have a different hash. Main-dataset graph hashes and evaluator-specific reference hashes are different identity fields and must not be substituted for one another.

The reference annotations are model-produced and are not human gold. The CC BY-NC 4.0 policy applies to contributed benchmark data and documentation; source-image and third-party model rights remain with their owners.

## Image versions

These benchmark records retain the exact image and annotation identities used in the recorded experiments. Some layout images in the main dataset have subsequently received replacement decorative assets; those updated images must not be substituted when reproducing historical scores. Scoring references and input hashes in this package identify the evaluated version.

Proximity is evaluated as Boolean `is_near` with no positive-area overlap, a maximum gap of 0.06 times the image short side, and a maximum gap of 1.0 times the square root of the smaller box area. Both distance thresholds must hold. True-negative pairs do not contribute to F1.
