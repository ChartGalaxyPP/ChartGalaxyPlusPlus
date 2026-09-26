# Data format

The `real` and `synthetic` directories each have `train` and `test` splits. Each JSON file contains one chart and its annotations. Real records contain references; synthetic records refer to a same-stem PNG in the same archive.

## Shared fields

| Field | Meaning |
| --- | --- |
| `sample_id` | Unique frozen sample identity |
| `benchmark_id` | Secondary test identity; empty string for training records |
| `source_type` | `real` or `synthetic` |
| `track` | `real`, `layout`, or `nb2`; the latter two are generation tracks |
| `source_channel` | Retained source collection or generation channel |
| `width`, `height` | Annotation-input image dimensions in pixels |
| `image_sha256` | SHA-256 of the exact annotation-input image file bytes |
| `scene_graph` | Parsed scene graph JSON object |
| `scene_graph_sha256` | SHA-256 of the compact UTF-8 JSON serialization with `ensure_ascii=False` and one trailing LF |
| `spatial_relations` | JSON array containing stored spatial relationships |
| `spatial_policy` | Identifier of the spatial relationship selection policy |
| `annotation_version` | `training-0.6` or `heldout-v2-target-0.6` |
| `human_gold` | True for the 1,000 manually verified test charts; false for the training split |

## Scene graphs

The [JSON schema](scene_graph.schema.json) describes the graph structure. The top-level fields are `high_level_description`, `style_description`, and `compositional_deconstruction`; the latter has `background` and `nodes` fields.

Each node has `label`, `node_kind`, `parent`, `bbox`, and descriptive attributes. `node_kind` is `element` or `group`. Elements carry `element_type`, `role`, and `role_family`; groups carry `group_type`. Text elements also carry their recognized `text`. Node IDs are labels such as `<text 1>` or `<chart 4>`. `parent` points to another node label or the implicit `ROOT`.

Bounding boxes are `[y0, x0, y1, x1]` in normalized integer coordinates from 0 to 1000. To convert to image pixels, multiply x coordinates by `width / 1000` and y coordinates by `height / 1000`. There is one hierarchical relationship for each explicit node, including links to `ROOT`.

## Spatial relationships

Every relationship has `relation_type`, `from_id`, and `to_id`. Endpoints reference graph node labels.

| `relation_type` | Additional fields |
| --- | --- |
| `relative_position` | `directions`: applicable `left`, `right`, `above`, or `below` relations from the first node to the second |
| `aligned_with` | `basis`: aligned edges or centers (`left`, `right`, `top`, `bottom`, `center_x`, `center_y`) |
| `bbox_overlaps` | No additional fields; boxes overlap with positive area |
| `bbox_distance` | Boolean `is_near`, plus diagnostic `distance_px` and `distance_ratio` (normalized by the image diagonal) |

The policy nominates nearby siblings, prioritizes group structure, and stores complete bundles of applicable relationship types for selected pairs. Hierarchy is preserved separately through `parent`. Missing spatial relationships are unrecorded pairs, not negative examples.

## Real-image references

No real-image binary column is included. At least one source-page, image, or public archive reference is present for every real record.

| Field | Meaning |
| --- | --- |
| `source_url`, `source_urls` | Retained source webpage URL and complete URL list; a webpage is not necessarily an image download |
| `image_url` | Available direct image URL, or null |
| `image_url_candidates` | Candidate assets associated with source metadata; these may refer to different images on the same page |
| `image_url_provenance` | How `image_url` was obtained or matched; null when no direct URL is assigned |
| `url_status` | Reference category: `source_page_reference`, `image_url_from_source_metadata`, `image_url_hash_matched`, or `public_archive_member_reference` |
| `source_image_sha256` | SHA-256 of the original source file when retained; may differ from the prepared input hash |
| `source_frame_index` | Zero-based frame selected from an animated or multi-frame source, or null |
| `image_archive_url` | Pinned URL of an existing public upstream TAR archive, or null |
| `image_archive_member` | Exact member name in that archive |
| `image_archive_offset`, `image_archive_length` | Member data offset and length, in bytes |
| `image_archive_sha256` | SHA-256 of the complete upstream archive |
| `input_conversion` | Recorded conversion from source image to annotation input when known; null means unspecified |

Archive references point to **existing upstream archives**; no real images are uploaded here. To obtain a member from an uncompressed archive, use the inclusive HTTP byte range `offset` to `offset + length - 1`, provided the server supports ranged reads. Alternatively, download the archive and extract the exact member. Verify the source file against `source_image_sha256`.

The annotation input may be an RGB PNG prepared from a JPEG, transparent image, EXIF-oriented image, or selected animation frame. Preserve frame selection and conversion before comparing with `image_sha256`. The supplied dimensions describe that prepared input. Do not assume that the current main image on a source webpage is the annotated asset.

`image_url_hash_matched` records an existing content match during preparation, not a guarantee of continued availability. Live URL checking is not required for release. Candidate assets and source-metadata URLs are retained without a promise of reachability or an exact content match.

## Synthetic images

`image_file` gives the same-stem PNG filename inside the archive. `image_sha256` hashes these released file bytes. Author-bearing PNG text/EXIF metadata is removed while retaining pixel data. Dimensions remain those used by the annotations.

## Boolean proximity

Let d be the minimum Euclidean gap between two axis-aligned boxes in image pixels, and let A be the smaller box area. `is_near` is true exactly when the boxes have no positive-area intersection, d <= 0.06 * min(width, height), and d <= sqrt(A). Edge or corner contact is near. Containment and positive-area overlap are not near. The thresholds use pixel geometry after converting normalized boxes. Stored distance fields are diagnostic; evaluation uses the Boolean flag.

## Checksums

Hash `scene_graph` after serializing it with Python `json.dumps(graph, ensure_ascii=False, separators=(",", ":")) + "\n"`, encoded as UTF-8. `annotation_sha256` in the index hashes the exact JSON member bytes. `image_sha256` hashes the PNG bytes for synthetic records and the historical annotation input bytes for real references. `manifest.json` lists all data shards and their SHA-256; `SHA256SUMS` covers public files except itself.
