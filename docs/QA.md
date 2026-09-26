# Scene-graph-augmented QA

The ChartGalaxy++ QA package contains **1,266 questions on 1,000 chart IDs**, reference answers, **1,000 scene graphs**, and URL metadata. The supplied revision is `urls_uniform_20260926`. Stable question IDs, chart IDs, and the 1,006 evaluated image hashes are preserved.

The package is available through [GitHub Releases](BENCHMARKS.md). It contains no image files. Questions and reference answers are provided as JSON and CSV; the scene graphs are in `scene_graphs.jsonl.gz`, joined to questions by `chart_id`. Each graph's nodes, relations, and attributes are preserved from the supplied package.

The image references cover 441 charts with image URLs and 59 with public Parquet URLs plus row/column selectors. The remaining 500 charts have only local PNGs in the source package; these images are excluded and their URLs are left empty. `image_reference` retains each supplied URL, resize setting, or Parquet selector. `image_url` contains only direct image links, and `source_url` retains available source-page metadata. In CSV, `image_reference` is a JSON-encoded object.

Six chart IDs have two evaluated image encodings. Count charts with `chart_id` and retain each question's `image_sha256` to distinguish historical inputs. Some answers are JSON-encoded lists stored as strings; keep them verbatim when loading the data. URLs have not been checked for current availability or returned image bytes.

The main dataset's real charts remain URL/source/archive references and its synthetic charts include PNGs. QA does not increase the main dataset's chart count. See the [QA package README](../applications/qa/README.md) for the reference schema and the [manifest](../applications/qa/manifest.json) for counts and source checksums.
