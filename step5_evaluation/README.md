# Step 5: Quick Start Guide

## Run Evaluation in 3 Steps

### Step 1: Prerequisites
```bash
# Install dependencies (if not already installed)
pip install pandas numpy matplotlib seaborn
```

Ensure you have completed Steps 1-4: and make sure your step 4 folder ( ```step4_hybrid_explanations```) has these files:

 - `hybrid_explanation_fixed_549227.json`
 - `hybrid_explanation_fixed_67703.json`
 - `hybrid_explanation_fixed_1086374.json`
 - `hybrid_explanation_fixed_1134888.json`
 - `hybrid_explanation_fixed_706915.json`

### Step 2: Run Evaluation
```bash
step5_evaluation.ipynb
```

### Step 3: View Results
```bash
# Check the evaluation_results/ directory
ls evaluation_results/

# Open visualizations
open evaluation_results/evaluation_metrics_visualization.png
open evaluation_results/per_alert_analysis.png
open evaluation_results/method_comparison_heatmap.png

```

---

## What You'll Get

### Quantitative Data (CSV)
- `evaluation_metrics.csv` - Per-alert metrics
- `method_comparison_table.csv` - XAI vs Causal vs Hybrid

### Visualizations (PNG)
- `evaluation_metrics_visualization.png` 
- `per_alert_analysis.png` - Detailed breakdown
- `method_comparison_heatmap.png` - 3-way comparison

### Summary Report (TXT)
- `EVALUATION_SUMMARY.txt` - Comprehensive analysis

---

## Expected Results

### Key Metrics You Should See:
-  **Causal Coverage**: ~40% (2 out of 5 top features have causal paths)
-  **Completeness**: 100% (all alerts have XAI + Causal + Recommendations)
-  **Complementarity**: ~75% (XAI and Causal focus on different features)
-  **Information Density (Hybrid)**: ~1.25 (2.5× more than XAI-only)

### What to Look For:
1. **Bar charts** showing Hybrid outperforms baselines
2. **Heatmap** showing Hybrid has highest information density
3. **Per-alert breakdown** showing variation across alerts

