
# Hybrid Causal-XAI Framework for Network Intrusion Detection Systems

A novel framework combining causal inference with explainable AI to improve the actionability, trustworthiness, and diagnostic utility of deep learning-based Network Intrusion Detection Systems (NIDS).

## Project Overview
Modern NIDS based on deep learning achieve high accuracy but suffer from two critical flaws:

- **Opacity:** Black-box models that analysts cannot trust or understand  
- **Correlation vs. Causation:** They identify patterns but not root causes, leading to alert fatigue

This project addresses these issues through a hybrid framework that:

- Uses causal discovery to identify root causes of attacks  
- Applies XAI techniques to explain model predictions  
- Combines both to generate actionable, trustworthy explanations for security analysts  

---

## Research Question
Can a hybrid framework that combines causal inference for root cause analysis with post-hoc XAI for alert justification significantly improve the actionability, trustworthiness, and diagnostic utility of an AI-based NIDS?

---

## Methodology
### Step 1: Baseline NIDS Model
We adopt a deep learning–based NIDS model architecture similar to the one used by Kalakoti et al. in “Evaluating Explainable AI for Deep Learning-Based NIDS Alert Classification” [1] (Link to paper [here](https://www.scitepress.org/Papers/2025/131807/131807.pdf))
- **Model:** Long Short-Term Memory (LSTM) network  
- **Task:** Binary classification (benign vs. malicious traffic)  
- **Dataset:** TalTech NIDS alerts (1.4M samples, 42 features)  
- **Performance:** 99.5% accuracy on test set  

### Step 2: Causal Discovery
- **Algorithms:** PC (constraint-based) and GES (score-based)  
- **Features:** 10 network features (5 SOC-expert + 5 XAI-derived)  
- **Output:** Directed Acyclic Graph (DAG) showing causal relationships  
- **Domain Knowledge:** Applied 11 forbidden edges based on network security constraints  

### Step 3: XAI Integration
- **Methods:** SHAP, LIME, Integrated Gradients, DeepLIFT  
- **Evaluation:** Faithfulness, complexity, robustness, reliability  
- **Best Performer:** DeepLIFT (highest faithfulness and reliability)  

### Step 4: Hybrid Explanation Generation
Combines XAI feature importance with causal pathways  
**Format:** "Alert flagged because [XAI], root cause is [Causal]"  

### Step 5: Evaluation
- **Quantitative:** Faithfulness and robustness metrics  
- **Qualitative:** User study with security analysts  

---

## Dataset
**TalTech NIDS Alert Dataset**  
- **Source:** Suricata NIDS at Tallinn University of Technology SOC  
- **Period:** January-March 2022 (60 days)  
- **Size:** 1,395,324 alerts  
- **Classes:** Important (1.5%) vs. Irrelevant (98.5%)  
- **Features:** 47 (network traffic + metadata + similarity scores)  
- **Link:** GitHub Repository here [here](https://github.com/ristov/nids-alert-data)
- **License** licenced under Creative Commons Attribution 4.0 International License. click [here](https://creativecommons.org/licenses/by/4.0/) to see license

---

## Installation
### Requirements
- Python 3.10+  
- 12GB RAM (minimum)  
- macOS, Linux, or Windows or Google CoLab 

### Setup
```bash
# Clone repository
git clone "https://github.com/Ande404/Hybrid-Causal-XAI-For-NIDs/"
cd Hybrid-Causal-XAI-For-NIDs

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Download dataset
download the dataset from "https://github.com/ristov/nids-alert-data"

# Place dataset-labeled-anon-ip.csv in project root
```

### Dependencies
```
torch>=2.0.0
pandas>=2.0.0
numpy>=1.24.0
scikit-learn>=1.2.0
matplotlib>=3.7.0
causal-learn>=0.1.3
shap>=0.42.0
lime>=0.2.0
captum>=0.6.0
networkx>=3.0
tqdm>=4.65.0
joblib>=1.2.0
```

---

## Project Structure
```
Hybrid-Causal-XAI-For-NIDs/
├── README.md
├── requirements.txt
├── dataset-labeled-anon-ip.csv //this is not included in repo      
│
├── step1_lstm_xai/
│   ├── recreate_lstm_xai.ipynb        
│   ├── best_lstm.pt                   
│   ├── scaler.joblib                  
│   ├── xai_comparison_results.csv  
│   ├── xai_comparison_plots.png 
│   └── Understanding LTSM and XAI methods.md  
│
├── step2_causal_discovery/
│   ├── causal_discovery.ipynb            
│   ├── causal_graph.gpickle           
│   ├── causal_discovery_data.csv      
│   ├── pc_edges.csv                   
│   ├── ges_edges.csv                  
│   ├── final_causal_edges.csv         
│   ├── causal_adjacency_matrix.csv    
│   ├── causal_graphs_comparison.png   
│   ├── final_causal_graph.png 
│   └── Understanding our Casual Results.md            
│
├── step4_hybrid_explanations/         
│   ├── step4_hybrid_explainer.ipynb 
│   ├── hybrid_explanation_demo.json 
│   ├──  hybrid_explanation_demo.png
│   ├── results_explanation.md  
│   └── README.md
│
└── step5_evaluation/                  
    ├── step5_evaluation.ipynb
    ├── Understanding our Evaluation Results.md
    ├── README.md
    └── evaulation_results/
        ├── evaluation_metrics_visualization.png
        ├── evaluation_metrics.csv
        ├── EVALUATION_SUMMARY.txt
        ├── method_comparison_heatmap.png
        ├── method_comparison_table.csv
        └── per_alert_analysis.png
```

---

## Usage
### Step 1: Train LSTM and Evaluate XAI
```bash
# Run in Jupyter notebook
jupyter notebook step1_lstm_xai/recreate_lstm_xai.ipynb

# Or convert to script
jupyter nbconvert --to script recreate_lstm_xai.ipynb
python recreate_lstm_xai.py
```
**Outputs:**
- best_lstm.pt - Trained LSTM model  
- scaler.joblib - Feature scaler  
- xai_comparison_results.csv - Evaluation metrics for 4 XAI methods
- xai_comparison_plots.png - image with XAI methods compared that you will get when you run the model  

**Expected Runtime:** 5 minutes on M1 Mac

### Step 2: Causal Discovery
```bash
cd step2_causal_discovery
python causal_discovery.ipynb
```
**Outputs:**
- causal_graph.gpickle - NetworkX graph object (for Step 4)
- final_causal_graph.png - Visualization for paper
- *_edges.csv - Edge lists from PC, GES, and consensus

**Expected Runtime:** 5 minutes on M1 Mac

### Step 4: Hybrid Explainer (XAI + Casual)

Run the Hybrid Explainer
```bash
 step4_hybrid_explainer.ipynb
```
This will:

* Load all required models and data  
* Generate explanations for sample alerts  
* Save JSON and PNG visualizations

And Generate

 **Output Files**
- `hybrid_explanation_fixed_{alert_id}.json` - Structured data for programmatic access
- `hybrid_explanation_fixed_{alert_id}.png` - Visualization for presentations


**Expected Runtime:** < 10s  on M1 Mac

### Step 5: Hybrid Explainer Evaluation

#### Install the Prerequisite dependencies
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

#### Run Evaluation
```bash
step5_evaluation.ipynb
```

#### View Results
```bash
# Check the evaluation_results/ directory
ls evaluation_results/

# Open visualizations
open evaluation_results/evaluation_metrics_visualization.png
open evaluation_results/per_alert_analysis.png
open evaluation_results/method_comparison_heatmap.png

```

**Expected Runtime:** < 10s  on M1 Mac

#### Output Files

**Quantitative Data (CSV)**
- `evaluation_metrics.csv` - Per-alert metrics
- `method_comparison_table.csv` - XAI vs Causal vs Hybrid

**Visualizations (PNG)**
- `evaluation_metrics_visualization.png` 
- `per_alert_analysis.png` - Detailed breakdown
- `method_comparison_heatmap.png` - 3-way comparison

**Summary Report (TXT)**
- `EVALUATION_SUMMARY.txt` - Comprehensive analysis

---

## Key Results
### XAI Evaluation (Step 1)
| Method | Faithfulness | Monotonicity | Max Sensitivity | Complexity | RMA | RRA |
|--------|---------------|---------------|------------------|-------------|------|------|
| LIME | 0.42 ± 0.18 | 59.6% | 0.36 ± 0.12 | 3.03 ± 0.07 | 0.62 | 0.53 |
| SHAP | 0.40 ± 0.29 | 64.5% | 0.02 ± 0.09 | 2.47 ± 0.21 | 0.65 | 0.47 |
| IG | 0.18 ± 0.38 | 73.7% | 0.18 ± 0.25 | 2.17 ± 0.41 | 0.59 | 0.34 |
| DeepLIFT | 0.76 ± 0.27 | 78.4% | 0.00 ± 0.00 | 2.26 ± 0.33 | 0.78 | 0.68 |

**Winner:** DeepLIFT outperforms on all metrics.

### **Causal Discovery (Step 2\)**

* **PC Algorithm**: Discovered 42 directed edges  
* **GES Algorithm**: Discovered 29 directed edges  
* **Consensus**: 20 edges agreed by both algorithms  
* **Root Causes**: Proto, ExtPort (features with no incoming edges)  
* **Direct Causes of Alerts**: SCAS, SignatureIDSimilarity, Similarity, SignatureMatchesPerDay, IntPort, AlertCount, ProtoSimilarity

**Key Causal Pathways:**
```bash
SignatureMatchesPerDay → SignatureIDSimilarity → SCAS → Label  
Proto → SignatureIDSimilarity → SCAS → Label

IntPort → Label (direct cause)
```

### **Hybrid Explanations (Step 4\)**

Successfully implemented hybrid explainer combining XAI \+ Causal analysis.

**Example Output:**

* Prediction: Important (95% confidence)  
* XAI Top Feature: SCAS (importance: 0.42)  
* Root Cause: SignatureMatchesPerDay (5000/day)  
* Causal Chain: SignatureMatchesPerDay → SignatureIDSimilarity → SCAS  
* Recommendation: Block IP, investigate correlated hosts

### **Hybrid Explainer Evaluation (Step 5\)**

Successfully perfomed a quantitative evaluation of the Hybrid Causal-XAI Framework for Network Intrusion Detection Systems (NIDS)

**Expected Results:**

* Causal Coverage: ~36% (2 out of 5 top features have causal paths)
* Completeness: 100% (all alerts have XAI + Causal + Recommendations)  
* Complementarity: ~70% (XAI and Causal focus on different features) 
* Causal Chain: SignatureMatchesPerDay → SignatureIDSimilarity → SCAS  
* Information Density (Hybrid): ~1.25 (2.0× more than XAI-only)

**What to Look For**

1. Bar charts showing Hybrid outperforms baselines
2. Heatmap showing Hybrid has highest information density
3. Per-alert breakdown showing variation across alerts 

---
## **Limitations**

1. **Causal Discovery Assumptions:**  
   * Assumes causal sufficiency (no hidden confounders)  
   * Uses observational data (not from controlled experiments)  
   * Relies on conditional independence tests  
2. **Dataset Constraints:**  
   * Single organization (TalTech)  
   * 60-day collection period  
   * Imbalanced classes (1.5% important alerts)  
3. **Computational Requirements:**  
   * Causal algorithms are computationally expensive (O(n³) for PC)  
   * XAI methods require model re-evaluation for each sample  
4. **Generalization:**  
   * Results specific to Suricata NIDS  
   * May not transfer to other NIDS platforms or network environments
6. **Causal Graph Coverage Gap**
   * Most XAI-important features aren't in causal graph
7. **Signature Rule Quality Dependency**
   * False positives from noisy signatures
8. **Context Limitation**
   * Can't distinguish benign repetition from malicious patterns without temporal/user context

---

## Future Work  
- Test on more datasets (CIC-IDS2017, NSL-KDD)  
- Real-time explanation generation  
- Develop SOC dashboard  
- Extend to multi-class classification  
- Explore counterfactual explanations  

---

## Citation
If you use this work, please cite:

```bibtex
@misc{nids-causal-xai-2025,
  title={Hybrid Causal-XAI Framework for Network Intrusion Detection Systems},
  author={Andrew Luwaga,  Brindha Sivakumar, Tasneem Kayed},
  year={2025},
  howpublished={\url{https://github.com/Ande404/Hybrid-Causal-XAI-For-NIDs/}
}
```

---

## Contributing
This is a research project for academic purposes. Contributions, suggestions, and feedback are welcome.

---

## License
> Licensed under the Apache License 2.0 — see the [LICENSE](/LICENSE) file for details.

---
## References
> [1] Kalakoti, R., Vaarandi, R., Bahşi, H., & Nõmm, S. (2025). Evaluating Explainable AI for Deep Learning-Based Network Intrusion Detection System Alert Classification. In Proceedings of the 11th International Conference on Information Systems Security and Privacy. DOI: 10.5220/0013180700003899

## Acknowledgments
- TalTech Security Operations Center for providing the dataset  
- causal-learn, PyTorch, and scikit-learn communities  

---

## Contact
- Andrew Luwaga,  Brindha Sivakumar, Tasneem Kayed
- luwaga@umich.edu, brindhas@umich.edu, tasneeem@umich.edu
- College of Engineering and Computer Science, University of Michigan-Dearborn
