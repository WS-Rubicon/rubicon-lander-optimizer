# Statistical Analysis for Winner Determination

## Method: Chi-Squared Test for CVR Comparison

### Why Chi-Squared
- Simple, well-understood
- Works for proportions (conversions / clicks)
- No external libraries needed - pure math

### Formula

For comparing two variants:

```
Observed:
  Variant A: conversions_a, non_conversions_a (clicks_a - conversions_a)
  Variant B: conversions_b, non_conversions_b (clicks_b - conversions_b)

Expected (under null hypothesis of equal CVR):
  total_clicks = clicks_a + clicks_b
  total_conv = conversions_a + conversions_b
  overall_cvr = total_conv / total_clicks

  expected_conv_a = clicks_a * overall_cvr
  expected_nonconv_a = clicks_a * (1 - overall_cvr)
  expected_conv_b = clicks_b * overall_cvr
  expected_nonconv_b = clicks_b * (1 - overall_cvr)

Chi-squared statistic:
  X² = Σ (observed - expected)² / expected

  X² = (conv_a - exp_conv_a)² / exp_conv_a
     + (nonconv_a - exp_nonconv_a)² / exp_nonconv_a
     + (conv_b - exp_conv_b)² / exp_conv_b
     + (nonconv_b - exp_nonconv_b)² / exp_nonconv_b

Degrees of freedom: 1

Decision:
  X² > 3.841 → p < 0.05 → statistically significant
  X² > 6.635 → p < 0.01 → highly significant
  X² > 10.828 → p < 0.001 → very highly significant
```

### For 3-Way Comparison

Compare each pair and also do overall test:
1. Champion vs Challenger A
2. Champion vs Challenger B
3. Challenger A vs Challenger B

Winner = highest CVR with at least one significant pairwise comparison.

### Minimum Sample Size

Rule of thumb for detecting a 20% relative CVR lift:
- If baseline CVR = 2%, need ~2,000 clicks per variant
- If baseline CVR = 4%, need ~1,000 clicks per variant

Practical minimums (balancing speed vs accuracy):
- **Hard minimum**: 50 clicks per variant (never decide before this)
- **Preferred minimum**: 100 clicks per variant
- **Ideal**: 200+ clicks per variant

### Early Stopping Rules

Even before statistical significance:
- If a variant has 200+ clicks and 0 conversions: flag for review
- If a variant's CVR is < 25% of champion's CVR after 100 clicks: consider early pause
- If all variants perform similarly after 500+ clicks each: declare tie, test bigger changes

### Tie-Breaking

When p >= 0.05 (not significant):
1. If one variant has 20%+ higher CVR: pick it as "probable winner" (note low confidence)
2. If all within 10% CVR of each other: keep champion, generate bolder challengers
3. If cycle has run 7+ days without significance: force-end, pick highest CVR

### Output Format

```json
{
  "analysis": {
    "variants": [
      {"id": "solar-v47", "clicks": 312, "conversions": 9, "cvr": 2.88},
      {"id": "solar-v47-a", "clicks": 298, "conversions": 14, "cvr": 4.70},
      {"id": "solar-v47-b", "clicks": 305, "conversions": 8, "cvr": 2.62}
    ],
    "pairwise_tests": [
      {"pair": ["solar-v47", "solar-v47-a"], "chi2": 1.42, "p_approx": 0.23, "significant": false},
      {"pair": ["solar-v47", "solar-v47-b"], "chi2": 0.04, "p_approx": 0.84, "significant": false},
      {"pair": ["solar-v47-a", "solar-v47-b"], "chi2": 1.89, "p_approx": 0.17, "significant": false}
    ],
    "winner": "solar-v47-a",
    "confidence": "low",
    "recommendation": "probable_winner_insufficient_data",
    "note": "Challenger A has highest CVR but not statistically significant. Running longer recommended."
  }
}
```
