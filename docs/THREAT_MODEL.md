# Threat Model (Stage 2)

## Purpose
Define credible threats against a defence-focused HRI assistant and establish protection goals before implementation.

## Assets to Protect
- **Safety-critical decisions** produced during human-robot interaction.
- **Operator trust and situational awareness**.
- **System integrity** (prompts, policies, configurations, logs).
- **Sensitive mission context** and any user-provided operational data.

## Trust Boundaries
1. Human operator input boundary (text/commands/images).
2. External data boundary (uploaded files, sensor-derived content, or third-party feeds).
3. Model runtime boundary (inference stack, tokenizer, prompt templates).
4. Logging/telemetry boundary (stored outputs, traces, metadata).

## Threat Categories

### 1) Prompt Injection / Instruction Override
- **Vector**: crafted natural-language commands designed to bypass policy.
- **Impact**: unsafe action recommendations or policy evasion.
- **Mitigations**:
  - strict system prompt hierarchy;
  - policy classifier before action output;
  - reject/clarify on conflict with safety policy.

### 2) Adversarial or Malicious Visual Input
- **Vector**: manipulated images, overlays, or misleading scene content.
- **Impact**: incorrect risk assessment or false situational interpretation.
- **Mitigations**:
  - image pre-validation (format, size, sanity checks);
  - confidence thresholding and fallback responses;
  - escalation path for low-confidence visual conclusions.

### 3) Data Exfiltration / Sensitive Leakage
- **Vector**: prompts attempting to extract hidden instructions, logs, or sensitive context.
- **Impact**: exposure of operational or policy details.
- **Mitigations**:
  - output filtering/redaction;
  - deny-list for sensitive internal fields;
  - minimum-necessary logging policy.

### 4) Resource Exhaustion (DoS-like Behavior)
- **Vector**: oversized inputs, repeated long-context requests, expensive image processing loops.
- **Impact**: degraded availability on constrained hardware.
- **Mitigations**:
  - hard limits on token count, image dimensions, and request rate;
  - timeout and cancellation policy;
  - graceful fallback to lightweight checks.

### 5) Unsafe Automation / Hallucinated Guidance
- **Vector**: model generates high-confidence but incorrect safety advice.
- **Impact**: harmful operator decisions.
- **Mitigations**:
  - enforce “decision support, not autonomous control” boundary;
  - require explicit confidence framing;
  - include human-in-the-loop confirmation for high-risk outputs.

## Security Goals (Stage 2 Baseline)
- **G1**: Prevent direct policy override via text or multimodal prompts.
- **G2**: Prevent leakage of internal policy and sensitive context.
- **G3**: Maintain service availability under constrained compute.
- **G4**: Ensure uncertain outputs fail safely and are reviewable.

## Residual Risks (Accepted for Stage 2)
- Model-level hallucination cannot be fully eliminated.
- Visual adversarial robustness is partial without specialized detectors.
- Hardware variability may affect mitigation reliability until local validation is complete.
