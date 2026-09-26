# Scene-graph-augmented QA

The supplied ChartGalaxy++ QA benchmark contains **1,266 questions on 1,000 chart IDs**, with **1,006 evaluated image encodings**. Reference-answer strings and per-question image identities are preserved. The paper's main comparison and ablations use all 1,266 questions.

The QA package includes questions in JSON/CSV, image URLs, chart-to-question mappings, available source references, a manifest, and checksums. The QA dataset archive contains no image files and is available through GitHub Releases. Its documentation and small metadata files are available in the GitHub repository. See [application downloads](BENCHMARKS.md).

Six chart IDs have two image encodings or resolutions. Use `chart_id` when counting charts and the per-question `image_sha256` when selecting the actual evaluated image. Some reference answers are JSON-encoded lists stored as strings; keep them verbatim until applying the benchmark's answer-equivalence rules.

Each question has an `image_url` field and a separate `source_url` field. Missing URLs are empty strings (`""`) in JSON and empty cells in CSV. Source pages and archive links are not substituted for direct image URLs. This applies to all QA images, including synthetic images. In the main dataset, real images remain URL/source/archive references only and synthetic images are supplied as PNGs. QA images do not increase the main dataset's chart count.

The retained metadata provides image URLs for 290 evaluated image encodings and source-page URLs only for another 54; both URL fields are empty for the remaining 662. Additional references and provenance are retained in `image_references.jsonl`. Existing URLs have not been checked for current availability or matching image bytes.

The paper compares image-only and scene-graph-augmented QA using graphs from the dataset annotation pipeline. See the [QA package manifest](../applications/qa/manifest.json) for dataset counts and source checksums.
