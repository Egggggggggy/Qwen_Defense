# Qwen Overview (Stage 2)

## What Qwen is
Qwen is a family of large language models (LLMs) and multimodal models released by Alibaba Cloud's Qwen team.

## What Qwen2.5 is
Qwen2.5 is a generation of Qwen models with improved instruction following and reasoning across multiple model sizes.

## What Qwen2.5-VL is
Qwen2.5-VL is the vision-language branch of Qwen2.5. It is designed to process both images (and in some variants video) and text.

## What Qwen2.5-VL-3B-Instruct is
`Qwen2.5-VL-3B-Instruct` is the 3B-parameter instruction-tuned Qwen2.5-VL checkpoint distributed on Hugging Face.

## What a Vision-Language Model (VLM) is
A VLM is a model that jointly handles visual inputs and language inputs, then generates language outputs conditioned on both modalities.

## What “3B” means
“3B” means approximately 3 billion parameters. It is smaller than 7B/72B variants, but still large for low-VRAM consumer GPUs.

## What “VL” means
“VL” means Vision-Language.

## What “Instruct” means
“Instruct” means the model is instruction-tuned to follow user/system instructions in chat-style tasks.

## How images are processed (Transformers usage)
In the Hugging Face Transformers workflow, images are passed through the model processor (`AutoProcessor`) which prepares vision tensors compatible with the Qwen2.5-VL model implementation.

## How text is processed
Text is formatted with chat templates (when using chat-style inputs), tokenized by the processor/tokenizer stack, and converted to input IDs/attention structures for generation.

## How image and text are combined
The processor assembles multimodal inputs (text + vision tensors), and the model performs autoregressive generation conditioned on both modalities.

## How inference works (high-level)
1. Load model and processor.
2. Build multimodal message input.
3. Preprocess with processor.
4. Call `generate(...)`.
5. Decode output tokens.

## Relevant architecture/implementation notes (verified scope)
- We can safely state the runtime interfaces from official model/docs (model + processor + generation flow).
- We do **not** claim undocumented internal layer details in this project documentation.

## Relevance to this project
Given the target hardware (MX330 2 GB VRAM), this project should assume CPU-first or constrained offload operation and avoid assuming full GPU-resident inference for 3B VL checkpoints.

## References
1. Hugging Face model card: https://huggingface.co/Qwen/Qwen2.5-VL-3B-Instruct
2. Qwen2.5-VL GitHub repository: https://github.com/QwenLM/Qwen2.5-VL
3. Qwen2.5-VL technical report (arXiv): https://arxiv.org/abs/2502.13923
4. Transformers model docs (Qwen2.5-VL): https://huggingface.co/docs/transformers/en/model_doc/qwen2_5_vl
5. Transformers multimodal chat templates: https://huggingface.co/docs/transformers/en/chat_templating_multimodal

## Verification note
This sandbox could not directly fetch several upstream domains during this run, so links are provided for traceability and should be re-opened on your local network for direct verification.
