# Hardware Requirements and Feasibility (Stage 3)

## My hardware

| Component | Specification |
| --------- | ------------- |
| CPU       | i7-1165G7     |
| RAM       | 16 GB         |
| GPU       | NVIDIA MX330  |
| VRAM      | 2 GB          |

## Practical implications for Qwen2.5-VL-3B-Instruct

### 1) VRAM constraint is the main bottleneck
With only 2 GB VRAM, full GPU-resident inference for a 3B multimodal model is generally not realistic.

### 2) CPU inference is likely required
A CPU-first path is the most realistic baseline for this system. GPU acceleration may be partial/limited (if used at all), depending on runtime and quantization support.

### 3) Memory-aware operation is mandatory
To reduce memory pressure:
- keep image resolution conservative
- keep prompt/context lengths short
- keep generation limits small (`max_new_tokens`)
- avoid batching

## Expected resource profile (qualitative)

Because this sandbox does not match your local Windows MX330 environment and no local benchmark has run yet, values below are planning guidance, not measured results.

### Expected CPU usage
- High during inference (sustained multi-core usage likely).
- Defence pre-check steps should be low to moderate CPU.

### Expected RAM usage
- Defence-only path: low to moderate.
- Full model load + inference: likely high relative to 16 GB RAM and may approach system limits depending on dtype/quantization/runtime.

### Expected VRAM usage
- Defence-only path: negligible VRAM.
- Full 3B VLM on GPU: likely exceeds practical 2 GB VRAM budget for normal operation.

### Storage requirements
- Python environment + packages: moderate.
- Qwen2.5-VL-3B-Instruct model files: large (multi-GB class download).
- Logs/results/test assets: low to moderate unless datasets grow.

## Is GPU acceleration practical?
**Partially at best; not guaranteed.**
- 2 GB VRAM is very restrictive for this model size.
- Any GPU usage would likely require aggressive memory strategies and still may fail.
- Project should not depend on successful full-GPU inference.

## Is CPU inference necessary?
**Yes, treat CPU inference as the primary supported path.**

## Is quantization practical?
**Potentially, but must be verified with exact stack support.**
- 4-bit/8-bit approaches may reduce memory, but compatibility for Qwen2.5-VL + Windows + selected backend must be tested in Stage 4/5.
- If unsupported or unstable in your setup, fallback must remain CPU-only baseline/diagnostic mode.

## Recommended operating modes
1. **Defence-only diagnostic mode**
   - run validation/OCR/detection/policy without model inference.
2. **Baseline constrained inference mode**
   - minimal image size and token budget; CPU-first.
3. **Optional experimental quantized mode**
   - enabled only after successful verification and documented caveats.

## Known limitations
- Final RAM/VRAM/latency numbers are **NOT YET MEASURED** on your actual machine.
- This stage intentionally avoids large downloads and installation-heavy validation.

## Verification plan (next stages)
- Implement and run `scripts/setup_check.py` on the actual Windows machine.
- Confirm PyTorch/CUDA visibility for MX330.
- Validate whether any quantized/offloaded run is stable.
- Record measured baseline vs defence performance.

## Reference links
1. Qwen2.5-VL-3B-Instruct model card: https://huggingface.co/Qwen/Qwen2.5-VL-3B-Instruct
2. Transformers Qwen2.5-VL docs: https://huggingface.co/docs/transformers/en/model_doc/qwen2_5_vl
3. PyTorch install selector (Windows/CUDA): https://pytorch.org/get-started/locally/
