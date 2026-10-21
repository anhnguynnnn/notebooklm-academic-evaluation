# Q3 — ESG Message Triage: Prompt Evaluation and Baseline Comparison

## 1. Objective

This experiment evaluates an LLM-based ESG message triage approach for classifying incoming operational messages into three categories:

- `SUSTAINABILITY_INCIDENT`
- `ENERGY_EFFICIENCY`
- `WASTE_RECYCLING`

The evaluation compares the refined LLM prompt's outputs with a keyword-based rule system. It examines classification consistency, urgency, sentiment, routing, and limitations.

## 2. Experimental setup

**Task:** Classify ESG-related messages and return structured JSON.

**LLM approach:** Prompt-based classification using category definitions, urgency guidance, sentiment analysis, follow-up requirements, recommended teams, escalation reasoning, data sensitivity risk, and a brief summary.

**Baseline:** A keyword-based rule system implemented in Google Colab.

**Test cases:** Three simulated operational messages covering a water leak, unnecessary overnight air conditioning, and contaminated recycling bins.

**Evaluation criteria:**
- Category and urgency agreement
- Sentiment consistency
- Follow-up and team-routing agreement
- Quality of escalation reasoning and summaries
- Potential errors, limitations, and operational risks

**Reproducibility note:** Record the actual LLM provider, model name, access method, date, and exact prompt version used before submission. The results below reflect the outputs observed in the experiment.

## 3. Test inputs

### Test 1 — Water leak

**Input message:** A water leak in Building C has reportedly been running all morning.

**Expected category:** `SUSTAINABILITY_INCIDENT`

### Test 2 — Energy efficiency

**Input message:** The air conditioning is reportedly running overnight in an empty office, potentially resulting in unnecessary energy consumption.

**Expected category:** `ENERGY_EFFICIENCY`

### Test 3 — Recycling contamination

**Input message:** An employee reports that recycling bins are contaminated again and that no one appears to be checking them.

**Expected category:** `WASTE_RECYCLING`

## 4. Observed LLM outputs

### Test 1 — Water leak

```json
{
  "issue_category": "SUSTAINABILITY_INCIDENT",
  "urgency": "HIGH",
  "sentiment": "NEUTRAL",
  "followup_required": true,
  "recommended_team": "FACILITIES",
  "escalation_reason": "The reported water leak has been ongoing all morning and requires prompt investigation and repair to prevent continued water wastage and potential property damage. No immediate threat to life or serious injury is stated.",
  "data_sensitivity_risk": "LOW",
  "brief_summary": "A water leak has reportedly been running in Building C all morning."
}
```

### Test 2 — Energy efficiency

```json
{
  "issue_category": "ENERGY_EFFICIENCY",
  "urgency": "MEDIUM",
  "sentiment": "NEUTRAL",
  "followup_required": true,
  "recommended_team": "ENERGY_MANAGEMENT",
  "escalation_reason": "",
  "data_sensitivity_risk": "LOW",
  "brief_summary": "The air conditioning is reportedly running overnight in an empty office, potentially resulting in unnecessary energy consumption."
}
```

### Test 3 — Recycling contamination

```json
{
  "issue_category": "WASTE_RECYCLING",
  "urgency": "MEDIUM",
  "sentiment": "NEGATIVE",
  "followup_required": true,
  "recommended_team": "WASTE_MANAGEMENT",
  "escalation_reason": "The reported recurring recycling contamination and apparent lack of monitoring require investigation and corrective action to improve waste sorting and recycling practices.",
  "data_sensitivity_risk": "LOW",
  "brief_summary": "The employee reports that the recycling bins are contaminated again and that no one appears to be checking them."
}
```

## 5. Rule-based baseline outputs

The keyword-based baseline returned the following results:

| Field | Test 1: Water leak | Test 2: Air conditioning | Test 3: Recycling |
|---|---|---|---|
| Category | SUSTAINABILITY_INCIDENT | ENERGY_EFFICIENCY | WASTE_RECYCLING |
| Urgency | HIGH | MEDIUM | MEDIUM |
| Sentiment | NEGATIVE | NEGATIVE | NEGATIVE |
| Follow-up | True | True | True |
| Recommended team | FACILITIES | ENERGY_MANAGEMENT | WASTE_MANAGEMENT |
| Sensitivity risk | LOW | LOW | LOW |

The baseline generated short, generic escalation reasons and summaries that largely repeated the input messages.

## 6. Comparison and analysis

| Evaluation dimension | Observed result | Interpretation |
|---|---|---|
| Category | All three categories agreed | Both approaches handled these three examples consistently. |
| Urgency | All three urgency levels agreed | The approaches produced consistent urgency labels for this small test set. |
| Team routing | All three teams agreed | The outputs directed each message to a relevant operational team. |
| Sentiment | LLM labelled Tests 1 and 2 neutral; baseline labelled all three negative | The baseline's default-negative behaviour may overstate negative sentiment. The LLM differentiated between neutral descriptions and a complaint. |
| Follow-up | All outputs required follow-up | Both approaches identified the need for further action in these examples. |
| Escalation reasoning | LLM provided more contextual explanations in Tests 1 and 3; its Test 2 reason was empty | LLM reasoning was more informative in some cases but inconsistent. A required-field validation rule should prevent missing escalation explanations where one is required. |
| Summaries | LLM produced concise, readable summaries; baseline mostly repeated input | The LLM added useful structure, although summaries still need checking for factual fidelity. |

### Critical evaluation

The keyword-based baseline is simple, predictable, and inexpensive to run. However, it depends on predefined terms and rules, making it less flexible when messages use unfamiliar wording or require contextual interpretation. Its negative-sentiment default also creates a systematic weakness in the observed examples.

The LLM produced more contextual escalation explanations and distinguished neutral reports from negative complaints in two tests. However, it returned an empty escalation reason for the energy-efficiency case, demonstrating that a fluent model does not guarantee complete or consistently structured output.

The three test cases show agreement on category, urgency, and routing, but this is not enough to establish overall accuracy or superiority. The sample is small, simulated, and covers only three issue types. A larger evaluation with labelled examples and predefined expected outputs is needed before drawing general conclusions.

## 7. Responsible deployment and improvements

1. **Validate the JSON schema:** Check required fields, allowed category values, urgency levels, and team names before accepting an output.
2. **Apply deterministic routing rules:** Use explicit rules for urgent incidents and emergency escalation instead of relying entirely on model-generated reasoning.
3. **Protect personal information:** Minimise collected data, restrict access, define retention periods, and avoid sending unnecessary identifying details to an external model.
4. **Add human oversight:** Route ambiguous, high-risk, or incomplete cases to a staff member for review.
5. **Expand testing:** Include more messages, different writing styles, misspellings, ambiguous cases, irrelevant messages, and prompt-injection attempts.
6. **Measure performance:** Evaluate category accuracy, per-class precision and recall, urgency errors, missing fields, and routing mistakes against a labelled test set.
7. **Prevent unsupported claims:** Instruct the model to distinguish stated facts from inferred risks and to avoid inventing details.

## 8. Conclusion

The experiment suggests that an LLM can provide useful contextual explanations and structured summaries while maintaining agreement with a keyword-based baseline on the three tested categories. However, the empty escalation reason and sentiment differences highlight the need for validation and careful evaluation.

A hybrid approach is recommended: use an LLM for contextual interpretation, deterministic rules for schema validation and critical escalation, and human review for uncertain or high-risk cases. The current results are preliminary and do not establish production readiness.

## 9. Evidence and reproducibility

- **Test inputs:** Three simulated ESG operational messages.
- **LLM evidence:** The three JSON outputs recorded above.
- **Baseline evidence:** Results from the keyword-based implementation in Google Colab.
- **Code:** Add the baseline code or a link to the relevant notebook if permitted.
- **Prompt:** Add the exact refined prompt used in the experiment.
- **Screenshots:** Optional; the observed outputs are transcribed above.
- **Limitations:** The exact model identity, prompt version, and test date should be documented to support reproducibility.
