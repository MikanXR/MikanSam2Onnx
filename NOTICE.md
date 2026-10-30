# Notice

The ONNX graphs published in this repository's releases are exported from the SAM 2.1 checkpoints of [facebookresearch/sam2](https://github.com/facebookresearch/sam2), Copyright (c) Meta Platforms, Inc. and affiliates, released under the Apache License 2.0. The license text is in `LICENSE` beside this file and in every release.

Each release carries the graphs of one checkpoint:

- `sam2.1-small-onnx-1`: `sam2.1_hiera_small.pt`
- `sam2.1-tiny-onnx-1`: `sam2.1_hiera_tiny.pt`

The export changes the model's form, not its weights: the image encoder folds the ImageNet normalization into the graph and emits the predictor's own feature split, and the prompt decoder takes a dynamic number of points. The export scripts and their validation against the PyTorch predictor live in [MikanXR/ReconstructionExperiments](https://github.com/MikanXR/ReconstructionExperiments).
