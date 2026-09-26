# Local inference

The standalone example below was run on one NVIDIA RTX PRO 6000 Blackwell Server GPU using Python 3.11.14, vLLM 0.20.2, Transformers 5.12.1, PyTorch 2.11.0 / CUDA 13.0, and xgrammar 0.1.32. The environment passed dependency consistency checking. [requirements-smoke.txt](requirements-smoke.txt) records the exact Linux environment; the original producer did not supply a complete dependency lockfile.

Create a fresh Python 3.11 environment and install `pip install -r requirements-smoke.txt`. Use a compatible CUDA driver. Transformers 4.x does not load this exported processor correctly; pinning only vLLM can resolve to that older version. The xgrammar pin keeps the dependency set compatible with vLLM and Transformers. See [the smoke receipt](smoke/validation.json) and [saved output](smoke/prediction.json). This single-image check validates loading, generation, and output structure, and does not rerun paper accuracy measurements.

Keep both weight shards, their index, tokenizer, processor, and chat template in the same model directory. `inference_spec.json` contains the exact prompts and generation settings extracted from the supplied single-image runner.

The following usage snippet performs one request on one visible GPU and saves the raw response before strict JSON parsing. Set `model_dir` and `image_path` to your local files. It is usage documentation, not the paper's evaluation pipeline.

```python
import json
import os
from pathlib import Path

def main():
    model_dir = Path("./image2scenegraph-model").resolve()
    image_path = Path("./example.png")
    spec = json.loads((model_dir / "inference_spec.json").read_text())
    for key, value in spec["environment"].items():
        os.environ[key] = value

    from PIL import Image
    from vllm import LLM, SamplingParams

    llm = LLM(
        model=str(model_dir), tokenizer=str(model_dir),
        trust_remote_code=False, dtype=spec["dtype"],
        tensor_parallel_size=spec["tensor_parallel_size"],
        max_model_len=spec["max_model_len"], max_num_seqs=1,
        disable_log_stats=False,
        gpu_memory_utilization=spec["gpu_memory_utilization"],
        limit_mm_per_prompt={"image": 1, "video": 0},
        mm_processor_kwargs={"max_pixels": spec["max_pixels"]},
        seed=spec["seed"], gdn_prefill_backend=spec["gdn_prefill_backend"],
        compilation_config=spec["compilation_config"],
    )
    messages = [
        {"role": "system", "content": spec["system_prompt"]},
        {"role": "user", "content": [
            {"type": "image", "image": "<image>"},
            {"type": "text", "text": spec["user_prompt"]},
        ]},
    ]
    prompt = llm.get_tokenizer().apply_chat_template(
        messages, tokenize=False, add_generation_prompt=True,
        enable_thinking=spec["enable_thinking"],
    )
    sampling = SamplingParams(
        temperature=spec["temperature"], top_p=spec["top_p"], top_k=spec["top_k"],
        max_tokens=spec["max_new_tokens"], seed=spec["seed"],
        repetition_penalty=spec["repetition_penalty"],
        frequency_penalty=spec["frequency_penalty"],
    )
    with Image.open(image_path) as source:
        image = source.convert("RGB")
    try:
        result = llm.generate([{
            "prompt": prompt,
            "multi_modal_data": {"image": image},
            "mm_processor_kwargs": {"max_pixels": spec["max_pixels"]},
        }], sampling, use_tqdm=False)[0].outputs[0]
    finally:
        image.close()

    Path("prediction.raw.txt").write_text(result.text, encoding="utf-8")
    Path("prediction.status.json").write_text(json.dumps({
        "finish_reason": result.finish_reason,
        "output_tokens": len(result.token_ids),
    }, indent=2), encoding="utf-8")
    prediction = json.loads(result.text)
    Path("prediction.json").write_text(
        json.dumps(prediction, ensure_ascii=False, indent=2), encoding="utf-8",
    )


if __name__ == "__main__":
    main()
```

The native output uses `[x0, y0, x1, y1]` boxes. See [OUTPUT_FORMAT.md](OUTPUT_FORMAT.md) before using it with dataset annotations. A `finish_reason` of `length` marks token-limit termination even if the resulting text parses. A successful JSON parse is not a schema or accuracy check.

The source runner also implements deterministic JSON recovery; that implementation is not part of this candidate. Its recovery behavior and the paper's graph evaluation procedures are relevant to reproducing the reported scores. This snippet deliberately exposes parsing errors and preserves the raw response for inspection.
