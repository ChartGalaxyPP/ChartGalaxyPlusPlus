# Scene graph preservation benchmark

This package follows the paper's final selection of **11 image-generation models × 1,000 test charts**. It contains **10,949 generated PNG files**, **51 failed-generation records retained at zero score**, saved generation prompts, original graph inputs, final scoring references and predictions, and **11,000 per-image score records**.

All 88 paper-table values were recomputed from the saved scores. Preparation makes no new generation or judge calls. Pipeline code is excluded.

## Files

| File | Contents |
| --- | --- |
| [original_inputs.jsonl.gz](original_inputs.jsonl.gz) | 1,000 original source graphs, runner-format graphs, hashes, and exact main-dataset record bindings |
| [scoring_references.jsonl.gz](scoring_references.jsonl.gz) | 1,000 final reference graphs after independent hierarchy reconstruction |
| `generation_records/<model>.jsonl.gz` | 1,000 task records per model with saved prompt text/hash, generated image path/hash, generation status, and recorded parameters |
| `images/<model>/<benchmark_id>.png` | Successful generated images; original bytes preserved |
| `predictions/<model>.jsonl.gz` | Final reconstructed prediction graphs; explicit null graph entries for generation failures |
| `scores/<model>.jsonl.gz` | Final per-image scores, matches, attribute scores, and saved description judgments |
| [generation_failures.json](generation_failures.json) | All 51 generation failures, retained in the evaluation denominator |
| [model_configurations.json](model_configurations.json) | Settings for the 11 selected models; internal cloud billing project removed |
| [metric_definitions.json](metric_definitions.json) | Frozen metric and aggregation contract |
| [paper_table.json](paper_table.json) | Final selected rows and original snapshot identity |
| [manifest.json](manifest.json) | Counts and package scope |
| `SHA256SUMS` | File checksums, excluding the checksum list itself |

Join by `(model, benchmark_id)`. Score records use `sample_key` for the same benchmark ID. Main-dataset source images are located through the `dataset_record` in `original_inputs.jsonl.gz`; this package adds generated outputs only, and does not bundle real source-image files. It does not increase the main dataset's 217,195-chart count.

## Preserve the distinct graph versions

The generation inputs contain **99,010 original nodes**. The final scoring references contain **101,587 nodes**, after independent hierarchy reconstruction. The reconstructed references were used for evaluation, not as replacement generation inputs. Keep `source_scene_graph`, `runner_scene_graph`, and final scoring graphs separate. Native dataset/final scoring bounding boxes use **yxyx**; runner-format graph boxes use **xyxy**, normalized to 0–1000.

Each successful generated image's SHA-256 was checked against the image identity used to reconstruct its scored prediction. Final reference/prediction graph hashes and all original score hashes were also verified. PNG integrity checks verify file structure and checksums; they do not assess visual quality or semantic correctness.

`scene_graph_sha256` identifies the original graph file bytes, before wrapping the parsed graph in JSONL. To verify the actual distributed files, use `SHA256SUMS`. Score export replaces private description-cache paths with hash keys and metric-source paths with filenames; numerical scores and judgments are preserved.

## Actual generation conditions

Use each task's saved prompt together with its model configuration. Input budgets differ by model, so supplying the same full graph to every model does not reproduce the paper's conditions. The saved prompt text is preserved exactly rather than regenerated from current code.

There are **10,999 saved prompts**. One Qwen-Image-2512 task failed its input budget check before producing a prompt or image; its explicit failure record is retained. The other 50 generation failures still have saved prompts.

The Qwen-Image-SP paper row uses **`qwen_image_sp_elements`**: groups are filtered before element serialization and budgeting. The historical `qwen_image_sp` output set is excluded. Its nested historical source settings are provenance; the selected configuration's element-only policy and saved prompts define this run.

Configurations retain available model IDs, weights, budgets, seeds, resolution rules, and decoding parameters. They do not guarantee that a third-party hosted model can be called at the same historical revision today. No private endpoints, credentials, or executable pipeline implementations are included. Raw extraction graphs from before hierarchy reconstruction are not included in this package.

## Scoring and aggregation

Use the frozen final metric revision in `metric_definitions.json`. Node assignment uses five equally weighted applicable attributes and maximum-weight one-to-one matching. GPT-5.6 Luna supplies final description scores after assignment; all selected score records have complete description judgments.

**Node, Parent, and Spatial F1 are image-level means over all 1,000 tasks per model.** Spatial relation counts are pooled within each image first. Individual attribute columns use pooled applicable matched-node means. This application's image-macro Parent F1 differs from the image-to-scene-graph application's global-micro Parent F1. Generation failures have zero structural scores and remain in the denominator.

Five records retain `hierarchy_model_review_complete: false`. Preserve their flags; model review is not human verification. The records remain in the final paper selection.

The CC BY-NC 4.0 policy applies to contributed benchmark data and documentation. Source-image rights and third-party model/service terms are not replaced by that license.

## Image versions

These benchmark records retain the exact image and annotation identities used in the recorded experiments. Some layout images in the main dataset have subsequently received replacement decorative assets; those updated images must not be substituted when reproducing historical scores. Scoring references and input hashes in this package identify the evaluated version.

Proximity is evaluated as Boolean `is_near` with no positive-area overlap, a maximum gap of 0.06 times the image short side, and a maximum gap of 1.0 times the square root of the smaller box area. Both distance thresholds must hold. True-negative pairs do not contribute to F1.
