# ChartGalaxy++ QA

This package contains **1,266 questions on 1,000 chart IDs**, reference answers, **1,000 scene graphs**, and image URL metadata. It distributes **URLs only, without original image files**. The supplied revision is `urls_uniform_20260926`.

Question and answer strings and scene graphs follow the supplied revision. Stable question IDs, chart IDs, question-type codes, and the 1,006 evaluated image hashes are retained. Some answers are strings containing JSON lists; preserve these strings when loading the data.

## Files

| File | Contents |
| --- | --- |
| [questions.json](questions.json) | All 1,266 questions and reference answers, image references, and chart IDs |
| [questions.csv](questions.csv) | The same records as UTF-8 CSV; `image_reference` is JSON encoded in a cell |
| `scene_graphs.jsonl.gz` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | The 1,000 supplied scene graphs, with `chart_id` as the join key |
| `charts.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | Chart IDs, associated question IDs, and evaluated image hashes |
| `image_references.jsonl` ([download archive](https://github.com/ChartGalaxyPP/ChartGalaxyPlusPlus/releases/download/v1.0/qa.tar.gz)) | Image URLs, selection/resize parameters, dimensions, and available provenance |
| [manifest.json](manifest.json) | Dataset counts, revision, source hashes, and URL coverage |
| `SHA256SUMS` | Checksums for all package files except the checksum list itself |

## Image references

The `image_reference` object preserves the supplied image locator:

- `{"url": "https://..."}` identifies an image URL.
- `{"url": "https://...", "resize": [width, height]}` also records the supplied target dimensions.
- `{"url": "https://...parquet", "format": "parquet", "row": 1617, "column": "figure_path"}` identifies an image stored in a public Parquet file. Select the zero-based row and indicated column; the URL points to the data file, not a standalone image.
- `{"url": ""}` marks an image for which the supplied package contains only a local PNG. Those PNGs are excluded from this release.

The convenience field `image_url` is populated only for image URLs. It is empty for Parquet references and unavailable URLs; consult `image_reference` to distinguish these cases. Existing source-page metadata is retained separately as `source_url`. Missing URLs are empty strings in JSON and empty cells in CSV.

| Image reference | Charts |
| --- | ---: |
| Image URL | 441 |
| Public Parquet URL with row/column selector | 59 |
| URL unavailable | 500 |

These are chart counts. Six chart IDs have two evaluated image encodings, giving 1,006 image hashes. Join a question to image metadata using `image_sha256`, and to its supplied graph using `chart_id`. Retain the per-question hash when distinguishing historical evaluation inputs. Image URLs and Parquet selectors have not been checked for current availability or returned image bytes.

The main dataset retains its separate delivery policy: real charts are references and synthetic charts include PNGs. QA contains no image files and does not add records to the main dataset's chart count.

## Scene graphs

Each line in `scene_graphs.jsonl.gz` contains `chart_id` and `scene_graph`. The graph's `nodes` and `relations` are preserved as supplied, including labels, node types, boxes, parent links, text, and relation attributes. Questions about the same chart share one graph record.

## File integrity

JSON and CSV records agree. Package checksums cover every supplied file. The graph records and question/answer strings were checked against the supplied source package.

## Attribution and license

Contributed ChartGalaxy++ question/answer data, annotations, and documentation follow the project's CC BY-NC 4.0 release policy. Referenced imagery and upstream datasets retain their owners' rights and required attribution. Source collections such as InfoChartQA remain identified in the provenance records. The annotation license does not grant rights to third-party imagery.
