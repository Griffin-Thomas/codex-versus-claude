# Evaluation

Fun fact: These evaluations were performed by `gpt-5-codex` itself.

## `gpt-5-codex`
```
Fabric fit (15): 14 / 15
Architecture (15): 13 / 15
Data engineering (15): 12 / 15
ML plan (15): 12 / 15
BI layer (10): 5 / 10
Ops/monitoring (10): 7 / 10
Validation (10): 8 / 10
Clarity (10): 9 / 10
--------------------------------
TOTAL: 80 / 100

Notes:
- Biggest strengths:
- Bronze→Silver quality steps and medallion layout are coherent and Fabric-native
- ML pipeline describes relevant features and daily scoring cadence
- Biggest risks/gaps:
- DAX measures reference non-existent fields (`carrier`, `Orders`) so BI layer would fail
- RLS filter uses `USERPRINCIPALNAME()` against region values, which would block legitimate access without a mapping table
- Would I run this as-is in Fabric? [No] Why?
- BI layer defects and incomplete RLS mapping require fixes before deployment.
```

**Assessment:** `gpt-5-codex` stays within Fabric tooling and the architecture hits all required sections, but execution details are uneven. The Bronze→Gold plan and ML pipeline are sensible, yet BI deliverables break because the risk table they define never exposes `carrier`, and the RLS pattern cannot map email identities to region strings. Validation queries exist but the reconciliation query needs refinement to explain acceptable gaps. Overall the solution is viable after BI/RLS rework, hence the mid-range score.

## `claude-sonnet-4-5-20250929`
```
Fabric fit (15): 15 / 15
Architecture (15): 15 / 15
Data engineering (15): 14 / 15
ML plan (15): 15 / 15
BI layer (10): 10 / 10
Ops/monitoring (10): 8 / 10
Validation (10): 10 / 10
Clarity (10): 9 / 10
--------------------------------
TOTAL: 96 / 100

Notes:
- Biggest strengths:
- Detailed medallion architecture with aligned notebooks and tables
- PySpark pipelines cover typing, deduping, late_flag derivation, and monitoring hooks
- ML workflow includes train/test split, MLflow tracking, scheduled retraining, and batch scoring outputs
- Would I run this as-is in Fabric? [Yes] Why?
- End-to-end plan is production-caliber and the BI exposure is immediately consumable.
- Biggest risks/gaps:
- Warehouse RLS predicate equates region values to `USER_NAME()`, which will not authorize regional users without a lookup or mapping.
```

**Assessment:** `claude-sonnet-4-5-20250929` delivers a comprehensive Fabric-native design with explicit notebooks, Warehouse publishing, monitoring, and validation artifacts. The ML section goes beyond the minimum, logging metrics and persisting the model, while the BI layer adds actionable risk segmentation and reusable DAX measures. The primary corrective action is to replace the simplistic RLS predicate with a bridge table mapping users or groups to regions. Aside from that, the proposal is immediately deployable.

## Overall Comparison
- **Score differential:** `claude-sonnet-4-5-20250929` leads by 16 points (96 vs. 80) owing to a fully wired BI layer, richer data engineering controls, and stronger ML governance.
- **Tie-breakers:** `claude-sonnet-4-5-20250929` is more practical for real Fabric deployment—only the RLS mapping needs adjustment—while `gpt-5-codex`’s BI defects would stall adoption. Both address cost/latency, but `claude-sonnet-4-5-20250929` articulates scaling and scheduling impacts more clearly.
- **Recommendation:** Proceed with `claude-sonnet-4-5-20250929` as the production baseline and use `gpt-5-codex`'s narrative only for supplementary ideas after fixing its BI/RLS gaps.
