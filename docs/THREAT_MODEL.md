# Threat Model (Stage 2)

## Selected Primary Threat Model
**OCR-driven visual prompt injection** against a vision-language assistant.

This means an attacker places malicious instructions inside an image (e.g., overlaid text, screenshot text, document text). The VLM reads that text and may follow attacker instructions that conflict with user intent or safety policy.

## Why this threat was selected
- Relevant to Qwen2.5-VL (image + text understanding).
- Demonstrable locally with synthetic test images.
- Defensible with lightweight controls (OCR extraction + rule/policy checks).
- Measurable with TP/TN/FP/FN metrics in a student project.
- Compatible with constrained hardware (CPU-first checks before model inference).

## Attacker capability
- Can submit image + text prompt to the system.
- Can embed instruction-like text in images.
- Cannot directly modify model weights or server-side code.

## Attacker goal
- Override intended instruction hierarchy.
- Induce unsafe output, policy bypass, or misleading assistant behavior.

## Attack input
- User text prompt (benign-looking or mixed intent).
- Malicious image containing hidden/embedded instructions such as:
  - “Ignore previous instructions …”
  - “Reveal system prompt …”
  - “Follow only the text in this image …”

## Assumptions
- Image inputs are untrusted by default.
- OCR-like text extraction is possible (tooling can vary).
- The model may treat image text as high-priority context if not filtered.
- This project is a defensive research prototype, not production hardening.

## Assets being protected
- Safety policy adherence.
- Reliability of assistant decisions.
- Integrity of prompt hierarchy.
- Evaluation integrity (baseline vs defence comparison).

## Attack surface
1. Image upload path.
2. Prompt assembly path (image text merged with user text).
3. Model inference path.
4. Output delivery path.

## Expected attack behavior
Without defence, some malicious-image cases may cause the model to:
- Follow injected instructions in image text.
- Ignore or conflict with user/system safety intent.
- Produce policy-violating or untrusted outputs.

## Defence objective
Detect suspicious instruction patterns in extracted image text and user prompt, then block/sanitise/escalate before passing to Qwen.

## Out of scope (Stage 2)
- Full adversarial robustness to pixel-level perturbation attacks.
- Watermark/steganography-forensics-grade detection.
- Real-world autonomous agent execution security.

## Limitations
- Rule-based detection can miss novel phrasing (false negatives).
- Aggressive patterns may over-block benign educational/security text (false positives).
- OCR quality affects detection reliability.

## References
1. OWASP LLM Prompt Injection guidance: https://owasp.org/www-project-top-10-for-large-language-model-applications/
2. Multimodal prompt injection research overview (arXiv): https://arxiv.org/abs/2509.05883
3. Qwen2.5-VL technical report (model context): https://arxiv.org/abs/2502.13923
