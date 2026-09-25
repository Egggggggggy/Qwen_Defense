# Defence Research (Stage 2)

## Scope
This document compares practical defences for the selected primary threat model:
**OCR-driven visual prompt injection** in Qwen2.5-VL-style multimodal input.

## Published / Reported Technique Families

### 1) Input sanitisation and validation
Common recommendations: treat all external image/text as untrusted, validate size/format/length, and reject malformed payloads early.

### 2) OCR inspection for instruction-like text
Research and security guidance increasingly highlight that injected text inside images can act like hidden prompts. OCR extraction enables policy checks before multimodal generation.

### 3) Prompt/instruction filtering
Pattern-based and policy-based filters can detect phrases associated with prompt override attempts (e.g., “ignore previous instructions”).

### 4) Prompt separation / hierarchy enforcement
A defensive layer can separate user intent from extracted image text and apply precedence rules before model inference.

### 5) Output-side safety checks
Even when input passes, output filtering can catch obvious policy-violating responses.

## Interpretation for this project (hardware-aware)
Given 16 GB RAM and MX330 2 GB VRAM:
- Heavy secondary models are risky for memory and latency.
- A lightweight deterministic defence is preferred for Stage 2/3.
- CPU-first pre-inference checks are practical and measurable.

## Compared options

### Option A: Heavy classifier-based guard model
- Pros: potentially better semantic detection.
- Cons: extra memory/latency; poor fit for low-resource hardware.

### Option B: Rule-only text filter without OCR
- Pros: simplest implementation.
- Cons: misses instructions embedded in images.

### Option C: OCR + rule-based risk scoring + policy decision (**selected**)
- Pros: directly targets selected threat; low compute; explainable decisions; easy evaluation.
- Cons: depends on OCR quality; brittle against novel obfuscation.

## Proposed Defence for implementation stages

### Name
**Qwen-VL Input Defence Pipeline (QVIDP)**

### High-level flow
1. Validate image/text input constraints.
2. Extract OCR text from image (or run in diagnostic mode if OCR unavailable).
3. Detect suspicious instruction patterns in:
   - user prompt
   - OCR text
   - cross-modal combinations
4. Score risk and apply policy:
   - benign → allow to Qwen
   - suspicious → block or sanitise
5. (Optional) output-side safety check.
6. Log decision metadata for evaluation.

## Why this is suitable for student research
- Reproducible locally.
- Clear baseline-vs-defence comparison.
- Supports TP/TN/FP/FN reporting.
- Keeps security focus on defensive controls.

## Known limitations
- Not a complete defence against all multimodal attacks.
- Rule set needs tuning for false positives/negatives.
- OCR failures reduce detection quality.

## References
1. Qwen2.5-VL model context: https://huggingface.co/Qwen/Qwen2.5-VL-3B-Instruct
2. Qwen2.5-VL technical report: https://arxiv.org/abs/2502.13923
3. OWASP LLM security guidance: https://owasp.org/www-project-top-10-for-large-language-model-applications/
4. Multimodal prompt injection paper index: https://arxiv.org/abs/2509.05883
