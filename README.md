# MikanSam2Onnx

Publishes [SAM 2.1](https://github.com/facebookresearch/sam2) exported to ONNX as GitHub release assets, so [MikanXR](https://github.com/MikanXR/MikanXR) can download a ready-to-run segmenter for its occlusion matte instead of asking every user to install a PyTorch environment and run an export.

One release per checkpoint, named by the tag MikanXR's model catalog points at:

| Release | Checkpoint | Catalog entry |
|---|---|---|
| `sam2.1-small-onnx-1` | `sam2.1_hiera_small.pt` | `sam2_small` |
| `sam2.1-tiny-onnx-1` | `sam2.1_hiera_tiny.pt` | `sam2_tiny` |

## What a release contains

Only what MikanXR's `Sam2Segmenter` loads at runtime:

- `encoder.onnx`: the image encoder, taking the frame resized to 1024x1024 as RGB in [0,1] with the ImageNet normalization inside, and returning the image embedding (`image_embed`, with the predictor's no-memory embedding added) and the two high-resolution feature maps (`high_res_feats_0`, `high_res_feats_1`) as the predictor itself keeps them
- `decoder.onnx`: the prompt encoder and mask decoder, taking those three with `point_coords` (1, N, 2) in the 1024 input space and `point_labels` (1, N) int32, and returning three candidate masks as 256x256 logits (`low_res_masks`) with a score each (`iou_scores`). The point axis is dynamic on purpose: SAM2's padding points take part in the decoder's attention, so a fixed-size graph padded with them is a different prompt
- `manifest.json`, which MikanXR's downloader reads for the file list, sizes and SHA-256 hashes
- `LICENSE` and `NOTICE.md`

## How the graphs are made

The exports are produced and validated by the scripts in [MikanXR/ReconstructionExperiments](https://github.com/MikanXR/ReconstructionExperiments): `tools/sam2_export_encoder.py`, `tools/sam2_export_decoder.py --check` (which runs the ONNX chain against the PyTorch predictor on real frames, agreeing to 1e-5 on the logits with identical thresholded masks), and `tools/sam2_release_bundle.py`, which lays out the release folders published here. Publishing is by hand with `gh release create <tag> <folder>/*`.

## License

The graphs are derived from the SAM 2.1 checkpoints, released by Meta under the Apache License 2.0, which is the `LICENSE` here and in every release. See [NOTICE.md](NOTICE.md).
