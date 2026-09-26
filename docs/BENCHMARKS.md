# Application data

Download the three application packages from [GitHub Releases](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/tag/v1.0). The main dataset and Image2SceneGraph model are on Hugging Face.

| Package | Contents |
| --- | --- |
| `image-to-scene-graph.tar.gz` | 1,000-chart test index and scoring references; checkpoint-100000 and ten baselines; 11,000 inference records, predictions/failures, and updated scores; request text, model settings, paper table, metric definitions |
| `qa.tar.gz` | 1,266 questions and answers on 1,000 chart IDs; all 1,006 original evaluated image encodings, mappings, and available source references |
| `scene-graph-preservation-metadata.tar.gz` | Original graph inputs, final scoring references and predictions, 10,999 saved prompts, 51 failures, 11,000 updated scores, model settings and paper table |
| `scene-graph-preservation-images-*.tar.gz` | 10,949 generated PNGs, split into independently extractable archives |

Extract all generation archives into the same directory. Member paths share the `scene_graph_preservation/` prefix. `release-assets.json` records archive counts, sizes, and hashes. Each complete component also contains `SHA256SUMS` for its files.

The two graph benchmarks retain their exact historical experiment identities. Use `sample_id` or `benchmark_id` to join to the current main dataset index; retained Parquet coordinates and content hashes describe the historical inputs. Some layout PNGs and their graph attributes changed during replacement of decorative assets. The saved benchmark results must not be interpreted as a new evaluation on those modified PNGs.

Real source images remain references in the main dataset and applications A/C. QA includes its original evaluated images under its separate delivery policy. Benchmark image encodings and generated outputs are not additional records in the main dataset's 217,195-chart total.

Proximity scores use Boolean `is_near`, with no true-negative credit. See each package's metric definitions for thresholds and aggregation. Parent F1 is global micro in image-to-scene-graph prediction and image macro in scene graph preservation.

QA contains the supplied questions, answers, and original images; actual graph inputs, prompts, responses, judgments, and detailed scoring rules were not supplied. See [QA details](QA.md). These packages contain data and documentation; annotation, training, evaluation, and review pipeline implementations are excluded.
