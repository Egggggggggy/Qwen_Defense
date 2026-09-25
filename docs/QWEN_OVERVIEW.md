# Qwen Overview (Stage 2)

## Scope
This document summarizes model and deployment considerations for a defence-oriented Human–Robot Interaction (HRI) prototype using Qwen-family models, with emphasis on practical constraints from low-VRAM hardware.

## Candidate Model Direction
- Primary direction: Qwen2.5-VL family for multimodal reasoning (vision + language).
- Operational reality for MX330 (2 GB VRAM): full GPU-resident inference is unlikely to be viable for 3B-class models.
- Recommended baseline: CPU-first inference with optional partial GPU acceleration when available.

## Hardware-Aware Constraints
- Target user device: Intel i7-1165G7, 16 GB RAM, NVIDIA MX330 2 GB VRAM.
- Implication: prioritize smaller context windows, reduced image resolution, and conservative batch/token settings.
- Avoid assumptions about CUDA stability until validated on local Windows environment.

## System Objectives in HRI Defence Context
- Detect or classify potentially unsafe, adversarial, or policy-violating inputs in text and visual channels.
- Provide explainable safety decisions (allow, block, escalate, or request clarification).
- Maintain deterministic fallback behavior when confidence is low or resources are constrained.

## Recommended Early Architecture
1. **Input Gate**: sanitize and normalize text/image metadata.
2. **Safety Analysis Layer**: run policy checks, lightweight heuristics, and model-based risk scoring.
3. **Decision Layer**: map risk score to action (allow/block/escalate).
4. **Audit Layer**: log decisions and rationale for reproducibility and incident review.

## Stage 2 Deliverables Alignment
- Define threat model specific to HRI interaction channels.
- Document defence strategy trade-offs for CPU-first and constrained-memory execution.
- Defer model download/integration until threat model and policy boundaries are accepted.
