# Loading samples

Download `sample_index.jsonl.gz` and the shard containing the desired sample. Resolve a repository revision first so all downloaded files belong to the same release.

```python
import gzip, hashlib, json, tarfile
from huggingface_hub import HfApi, hf_hub_download

repo = "ChartGalaxyPP/ChartGalaxyPlusPlus"
revision = HfApi().dataset_info(repo).sha
index = hf_hub_download(repo, "sample_index.jsonl.gz", repo_type="dataset", revision=revision)
sample_id = "d3-js_horizontal_group_bar_chart_horizontal_group_bar_chart_03/sample_28444"
with gzip.open(index, "rt") as f:
    entry = next(r for r in map(json.loads, f) if r["sample_id"] == sample_id)
shard = hf_hub_download(repo, entry["shard"], repo_type="dataset", revision=revision)
with tarfile.open(shard, "r:gz") as archive:
    raw = archive.extractfile(entry["annotation_file"]).read()
    assert hashlib.sha256(raw).hexdigest() == entry["annotation_sha256"]
    record = json.loads(raw)
    graph = record["scene_graph"]
    spatial = record["spatial_relations"]
    if record["source_type"] == "synthetic":
        png = archive.extractfile(record["image_file"]).read()
        assert hashlib.sha256(png).hexdigest() == record["image_sha256"]
    else:
        print(record["source_url"], record["image_url"], record["image_archive_url"])
```

Each shard is independently extractable. A real shard contains JSON files only. A synthetic shard contains one PNG and one JSON per chart. Example files duplicate an existing training record and do not add to the dataset count.

Source-page URLs are not direct image URLs. Some real charts retain a public archive URL plus exact member information; see [DATA_FORMAT.md](DATA_FORMAT.md). Current URL availability is not guaranteed. Preserve frame selection and conversion before comparing a retrieved source with its annotation-input checksum.

For a full download, use `snapshot_download(repo, repo_type="dataset", revision=revision)` from `huggingface_hub`, then run `sha256sum -c SHA256SUMS` in the downloaded directory. Underlying data-table files and pipeline implementation code are not included.
