# Defence Architecture (Stage 3)

## Defence Name
**Qwen-VL Input Defence Pipeline (QVIDP)**

## Design Goal
Provide a lightweight, reproducible defence layer for `Qwen2.5-VL-3B-Instruct` that detects and handles OCR-driven visual prompt injection before model inference.

## Primary Threat Alignment
This architecture is designed specifically for:
- malicious instruction text embedded in images
- multimodal instruction override attempts (image text + user text)

## End-to-End Flow

```text
User
  |
  |  image + prompt
  v
[1] Input Validation Layer
  - file type/size checks
  - text length checks
  - basic normalization
  |
  v
[2] OCR / Image Text Extraction Layer
  - extract readable text from image (if available)
  - fallback diagnostic path when OCR unavailable
  |
  v
[3] Suspicious Instruction Detection Layer
  - rule-based pattern matching
  - cross-modal checks (prompt + OCR text)
  - risk scoring
  |
  v
[4] Policy Decision Layer
  +-------------------------------+
  | benign      -> allow          |
  | suspicious  -> block/sanitise |
  | uncertain   -> escalate       |
  +-------------------------------+
     |                      |
     | allow                | block/sanitise/escalate
     v                      v
[5] Qwen2.5-VL Inference    Safe response template
  - CPU-first, constrained  (no model call)
    settings
  |
  v
[6] Optional Output Safety Check
  - lightweight output filter
  |
  v
[7] Structured Result + Security Logging
  - decision, reason, timing, errors
  - minimal sensitive data retention
```

## Core Components

### 1) Input Validation
Responsibilities:
- reject unsupported image formats
- reject oversized images beyond configured limits
- cap user prompt length
- assign request ID for traceability

### 2) OCR / Image Text Extraction
Responsibilities:
- extract candidate instruction text from image content
- normalize extracted text for downstream checks
- record OCR availability/error state

### 3) Detection Engine
Responsibilities:
- detect prompt-injection phrases (e.g., override/ignore hierarchy)
- detect system-prompt exfiltration attempts
- correlate user text with image-extracted text
- output risk labels and reason codes

### 4) Policy Engine
Responsibilities:
- deterministic decision mapping from risk label to action
- configurable thresholds for block/sanitise/escalate
- produce human-readable decision rationale

### 5) Sanitiser / Safe Handler
Responsibilities:
- remove/neutralize suspicious instruction fragments
- return refusal/safe fallback when blocking
- preserve benign task context when possible

### 6) Qwen Interface Layer
Responsibilities:
- run baseline model inference only for allowed inputs
- enforce low-memory generation settings
- return response with timing metadata

### 7) Logging Layer
Responsibilities:
- write structured security logs to `results/logs/`
- include timestamp, request ID, decision, reason, action, latency, errors
- avoid storing full sensitive raw content unless explicitly required

## Decision Contract (Target)

Allowed input:
```json
{
  "allowed": true,
  "decision": "benign",
  "reason": "no suspicious instruction patterns detected",
  "model_response": "...",
  "latency": 0.0
}
```

Blocked/sanitised input:
```json
{
  "allowed": false,
  "decision": "blocked",
  "reason": "suspected visual prompt injection",
  "model_response": null
}
```

## Why this architecture fits your hardware
- Detection and policy steps are lightweight and CPU-friendly.
- Costly VLM inference is gated behind defence checks.
- Pipeline can operate in diagnostic mode even when full model inference is unavailable.

## Limitations
- Rule-based detection can miss obfuscated attacks.
- OCR quality can limit detection reliability.
- Not a full defence against all multimodal adversarial methods.
