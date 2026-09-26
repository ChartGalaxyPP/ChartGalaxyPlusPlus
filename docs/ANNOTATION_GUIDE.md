# Reading the annotations

An annotation describes an image at three levels: individual visible elements, semantic groups and their parent links, and selected spatial relationships. The [complete schema](scene_graph.schema.json) defines required fields and allowed values; [DATA_FORMAT.md](DATA_FORMAT.md) describes the packaged fields.

## Elements and groups

| Node kind | Types | Purpose |
| --- | --- | --- |
| Element | `text`, `image`, `shape` | Individual visible regions |
| Group | `chart`, `axis`, `legend`, `legend_item`, `data_item`, `series`, `panel` | Regions that organize related children |

Every explicit node has a `label`, `parent`, `bbox`, `desc`, `color_palette`, and `node_kind`. Its type is recorded in `element_type` or `group_type`, depending on its kind. Each label is unique within one chart; it is not a global dataset identifier. Pair labels with `sample_id` when joining annotations from multiple images.

`parent` encodes the hierarchy and refers to an existing label or `ROOT`. The root is implicit: it is counted in dataset statistics but is not another object in the `nodes` list. For N explicit nodes, there are N hierarchy links and N + 1 nodes including the root. Do not count a group as a visual element or add a second explicit root when calculating these totals.

## Semantic roles

Element roles distinguish visual function from rendering type. A text element can be a heading, tick label, value label, or source credit; an image or shape can also serve a data-related function.

| Role family | Roles |
| --- | --- |
| `data_expression` | `data_mark`, `value_label`, `direct_readout` |
| `reading_reference` | `axis_line`, `tick_mark`, `tick_label`, `axis_title`, `gridline`, `legend_key`, `legend_label`, `legend_title`, `entity_label`, `reference_line`, `annotation_connector` |
| `context` | `heading`, `explanation`, `source_credit`, `subject_depiction` |
| `presentation` | `boundary`, `separator`, `emphasis`, `ornament` |

Recognized visible wording belongs in a text node's `text` field. `desc` provides an appearance and reconstruction description. Keep both fields when using annotations: a description is not a substitute for recognized text.

## Boxes and spatial relationships

Dataset boxes use **`[y0, x0, y1, x1]`**, with integer coordinates normalized to 0–1000. Multiply horizontal coordinates by `width / 1000` and vertical coordinates by `height / 1000` to obtain pixels. These are axis-aligned logical boxes, not segmentation masks.

The separate `spatial_relations` list uses node labels as endpoints. The `sibling-knn2-budget100-geometric-boolean-v1` policy preserves the original sibling-pair selection and enforces **at most 100 spatial relationship records per chart** across all four spatial types combined. The budget is a record limit, not a node-pair limit. The original selected records, their order, types, endpoints and distance fields are unchanged; `is_near` is added to the existing distance records. Hierarchy links are stored separately through `parent` and do not consume this budget. An unrecorded pair is not a negative label.

`is_near` is a Boolean on each stored `bbox_distance` record. Positive-area overlap and containment are false; exact edge or corner contact is true. Otherwise the shortest box gap must be at most both **0.06 × min(width, height)** and **1.0 × min(sqrt(area_a), sqrt(area_b))**. Compute gaps and areas in pixels using the actual image width and height. The Boolean uses unrounded geometry; `distance_ratio` remains gap divided by the image diagonal.

## Example and limitations

The [included training example](../examples/README.md) contains the exact image, graph object, spatial list, and row identity from the dataset. It illustrates the file format and is not an independent annotation-quality measurement.

Annotations are model-produced and `human_gold` is false. Schema and identity checks establish structural consistency; they do not establish the correctness of every recognized value, box, group, or visual claim in the source image.

Image2SceneGraph's native predictions use a different layout field and box order. Consult the model's output-format guide before comparing a prediction to this dataset.
