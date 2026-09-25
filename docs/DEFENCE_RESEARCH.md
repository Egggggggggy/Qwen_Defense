# Defence Research Notes (Stage 2)

## Objective
Capture practical defence strategies for an HRI assistant powered by Qwen-class models, with low-resource deployment constraints.

## Research Summary

### 1) Policy-First Architecture Outperforms Prompt-Only Control
- Relying only on prompt instructions is insufficient against deliberate prompt injection.
- A separate policy enforcement layer should make final allow/block/escalate decisions.
- Practical outcome: model output is treated as evidence, not authority.

### 2) Multilayer Input Defence is Required
- Input normalization and validation reduce malformed or abusive payloads early.
- Token/image caps are essential on low-VRAM systems to preserve availability.
- Structured refusal and clarification patterns reduce unsafe action drift.

### 3) Confidence-Aware Responses are Critical in HRI
- The assistant should explicitly surface uncertainty for ambiguous or low-quality inputs.
- High-impact recommendations should require operator confirmation.
- Escalation modes should be deterministic and auditable.

### 4) Constrained Hardware Changes Defence Priorities
- On 2 GB VRAM, availability controls are as important as content safety controls.
- CPU-first execution may increase latency; timeout policy and fallback path become mandatory.
- Smaller inputs and staged analysis pipelines improve reliability.

## Proposed Stage 2 Defence Baseline
1. **Pre-Processing Controls**
   - enforce text length and image dimension limits;
   - validate supported formats and reject malformed content.
2. **Risk Scoring**
   - combine keyword/rule checks with model-derived risk signals;
   - classify requests by risk level (low/medium/high).
3. **Decision Policy**
   - low risk: allow with monitored output;
   - medium risk: constrained response + warning;
   - high risk: block and escalate to operator workflow.
4. **Output Guardrails**
   - remove sensitive/internal details from responses;
   - add refusal templates for policy-violating requests.
5. **Auditability**
   - log input fingerprints, risk labels, and action decisions;
   - retain rationale fields for post-incident review.

## Open Questions for Stage 3
- Which Qwen variant provides acceptable quality-latency trade-offs on CPU-first mode?
- What threshold values best balance false positives and false negatives in risk scoring?
- Which redaction/filtering strategy is sufficient without excessive operator friction?
- What minimum offline test set is needed to validate adversarial resilience?
