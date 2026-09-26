# Image2SceneGraph model

ChartGalaxy++ Image2SceneGraph predicts elements, groups, hierarchy, text, bounding boxes, and appearance descriptions from a chart image. The model package contains the full Qwen3.5-4B-family checkpoint at step 100,000.

The nine original model files total 9,339,891,123 bytes: two BF16 Safetensors shards, a shard index, model configuration, tokenizer files, image processor files, and chat template. Their SHA-256 hashes match the export used for the paper's evaluation. The paper reports 89.4% element F1, 85.1% hierarchy F1, and 88.0% spatial F1 over 1,000 charts under its evaluation protocol.

Download [ChartGalaxyPP/ChartGalaxyPlusPlus-Image2SceneGraph](https://huggingface.co/ChartGalaxyPP/ChartGalaxyPlusPlus-Image2SceneGraph).

- [Inference instructions](model/INFERENCE.md)
- [Exact prompts and generation settings](model/inference_spec.json)
- [Native output format and dataset conversion](model/OUTPUT_FORMAT.md)
- [License and attribution](model/NOTICE.md)

The native prediction format is `elements.layout`, with boxes in `[x0, y0, x1, y1]` order on a 0–1000 grid. Dataset annotations use `compositional_deconstruction.nodes`, with `[y0, x0, y1, x1]` boxes. Spatial relationship records are not directly predicted by the model.

A standalone GPU smoke now passes in a pinned Python 3.11.14 / vLLM 0.20.2 / Transformers 5.12.1 environment: normal stop, valid JSON, 128 layout items, and native schema/graph validation without recovery. It is a usability check, not a new benchmark evaluation. The fine-tuning contributions use CC BY-NC 4.0, with retained upstream Qwen materials covered by their original Apache-2.0 license.
