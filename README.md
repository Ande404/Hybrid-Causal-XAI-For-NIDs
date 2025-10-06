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

---

## Installation
### Requirements
- Python 3.10+  
- 16GB RAM (minimum)  
- macOS, Linux, or Windows or Google CoLab 

### Setup
```bash
# Clone repository
git clone "https://github.com/Ande404/LTSMModelWithXAI2"
cd LTSMModelWithXAI2

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
LTSMModelWithXAI2/
├── README.md
├── requirements.txt
├── dataset-labeled-anon-ip.csv        
│
├── step1_lstm_xai/
│   ├── recreate_lstm_xai.ipynb        
│   ├── best_lstm.pt                   
│   ├── scaler.joblib                  
│   └── xai_comparison_results.csv     
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
│   └── final_causal_graph.png         
│
├── step4_hybrid_explanations/         
│   └── hybrid_explainer.ipynb
│
└── step5_evaluation/                  
    └── user_study.ipynb
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

**Expected Runtime:** 15–20 minutes on M1 Mac

### Step 2: Causal Discovery
```bash
cd step2_causal_discovery
python causal_discovery.ipynb
```
**Outputs:**
- causal_graph.gpickle  
- final_causal_graph.png  
- *_edges.csv  

**Expected Runtime:** 15–30 minutes on M1 Mac

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

---

## Limitations
- Causal sufficiency assumptions may not hold  
- Dataset imbalance (1.5% important alerts)  
- Computational cost of causal discovery  
- Domain-specific generalization  

---

## Future Work
- Complete Step 4: Hybrid explanation generation  
- Complete Step 5: User study with SOC analysts  
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
  howpublished={\url{https://github.com/Ande404/LTSMModelWithXAI2}}
}
```

---

## Contributing
This is a research project for academic purposes. Contributions, suggestions, and feedback are welcome.

---

## License


---

## Acknowledgments
- TalTech Security Operations Center for providing the dataset  
- Kalakoti et al. (2025) for baseline methodology  
- causal-learn, PyTorch, and scikit-learn communities  

---

## Contact
- Andrew Luwaga,  Brindha Sivakumar, Tasneem Kayed
- luwaga@umich.edu, brindhas@umich.edu, tasneeem@umich.edu
- College of Engineering and Computer Science, University of Michigan-Dearborn

> Note: This is an ongoing research project. Steps 4 and 5 are in development. Check back for updates.
