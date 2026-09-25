# Scene-graph-augmented QA

The supplied ChartGalaxy++ QA benchmark contains **1,266 questions on 1,000 chart IDs**, with **1,006 evaluated image encodings**. Reference-answer strings and per-question image identities are preserved. The project owner confirmed that the main comparison and ablations include all 1,266 questions.

The QA package includes questions in JSON/CSV, all 1,006 original evaluated image files, chart-to-question mappings, available source references, a manifest, and checksums. The complete QA package, including its original images, is available through GitHub Releases. Its documentation and small metadata files will be available through the GitHub repository. See [application downloads](BENCHMARKS.md).

Six chart IDs have two image encodings or resolutions. Use `chart_id` when counting charts and the per-question `image_sha256` when selecting the actual evaluated image. Some reference answers are JSON-encoded lists stored as strings; keep them verbatim until applying the benchmark's answer-equivalence rules.

Open each question's `image` path relative to the QA directory. All original image bytes are included, including real images, without resizing or re-encoding. This QA-specific delivery rule does not change the main dataset: its real images remain URL/source/archive references only, and its synthetic images are supplied as PNGs. QA images do not increase the main dataset's chart count.

Available source references are retained separately from image delivery. The pre-replacement main dataset had 79 exact matches to these synthetic images, 345 files with public source references, and 582 files whose public references remain unresolved. All three groups have local image files. Existing URLs have not been checked for current availability.

The supplied package does not include the actual QA scene graphs and concise labels, the three prompting templates, model configurations, model responses and judgments, ablation inputs/results, or the detailed scoring rules. Those materials remain necessary to document and inspect the paper's experiments. This is not a complete executable evaluation package.
