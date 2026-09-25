# Image2JSON output format

The model emits a JSON object with `high_level_description`, `style_description`, and `elements`. `elements.background` describes the background, and `elements.layout` is a flat list of element and group nodes. A node's `parent` refers to another node label or the implicit `ROOT`.

| Native model output | Dataset representation |
| --- | --- |
| `high_level_description` | Same field |
| `style_description` | Same field |
| `elements.background` | `compositional_deconstruction.background` |
| `elements.layout` | `compositional_deconstruction.nodes` |
| `node_kind` | Same field; `element` or `group` |
| `type` on an element | `element_type` |
| `type` on a group | `group_type` |
| `bbox: [x0, y0, x1, y1]` | `bbox: [y0, x0, y1, x1]` |
| `label`, `parent`, `desc`, `text`, `color_palette`, `role`, `role_family` | Preserve applicable fields |

Both box formats use a 0–1000 coordinate grid. For a native box, pixel coordinates are `(x0 * width/1000, y0 * height/1000, x1 * width/1000, y1 * height/1000)`. Use the original image dimensions; do not interpret the grid values directly as pixels.

The `type` vocabulary is `text`, `image`, or `shape` for elements, and `chart`, `axis`, `legend`, `legend_item`, `data_item`, `series`, or `panel` for groups. The native prompt requests a `node_kind` and the roles applicable to each item. Treat missing, contradictory, or unknown node types as validation errors rather than silently guessing replacements.

For example, a native element box `[100, 200, 400, 500]` becomes `[200, 100, 500, 400]` in the dataset representation. Preserve the label and parent when changing the field names and box order.

Validate unique labels, existing parent references, hierarchy acyclicity, box ordering and bounds, and required fields after conversion. Serialization conversion alone does not guarantee conformance to the [dataset schema](https://huggingface.co/datasets/ChartGalaxyPP/ChartGalaxyPlusPlus/blob/main/scene_graph.schema.json).

The model does **not** emit the dataset's separate `spatial_relations` list. Such records depend on node geometry and a specified spatial sampling policy. Do not fill a missing list with invented relations or compare native JSON against the dataset without adapting its representation.

Retain raw generated text and generation termination information. If a response is truncated, syntax repair may yield a parseable partial graph; record that status separately from schema validity and semantic accuracy. The usage example uses strict JSON parsing so that a malformed response remains visible.
