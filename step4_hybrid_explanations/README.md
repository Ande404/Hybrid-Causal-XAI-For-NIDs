# **Step 4: Hybrid Explainer \- Usage Guide**

## **Quick Start**

### **1\. Prerequisites**

Ensure you have completed Steps 1-3:

* `best_lstm.pt` \- Trained LSTM model  
* `scaler.joblib` \- Feature scaler  
* `causal_graph.gpickle` \- Causal graph  
* `causal_discovery_data.csv` \- Preprocessed data

### **2\. Run the Hybrid Explainer**

```bash
 step4_hybrid_explainer.ipynb
```
This will:

* Load all required models and data  
* Generate explanations for sample alerts  
* Save JSON and PNG visualizations

### **3\. Output Files**

* hybrid\_explanation\_0.json       \# Structured explanation (JSON)  
* hybrid\_explanation\_0.png        \# Visual explanation  
* hybrid\_explanation\_100.json  
* hybrid\_explanation\_100.png  
* hybrid\_explanation\_500.json  
* hybrid\_explanation\_500.png

---

## **Interpreting Results**

### **XAI Component (What)**

**High Importance Score (\> 0.2)**

* Feature strongly influenced the model's decision  
* Should be included in explanation to analyst

**Positive vs Negative Importance**

* Positive: Feature pushes prediction toward "Important"  
* Negative: Feature pushes prediction toward "Irrelevant"

**Example:**
```bash
Feature: SCAS  
   Value: 1.0  
   Importance: 0.42     
   → SCAS \= 1.0 (outlier) strongly suggests this is an important alert  
```   

### **Causal Component (Why/How)**

**Root Causes**

* Features with no incoming edges in causal graph  
* The "original cause" that started the causal chain  
* Most actionable for mitigation

**Causal Chains**

* Path from root cause to outcome  
* Shows HOW the alert developed

**Example:**
```bash
Root Cause: SignatureMatchesPerDay \= 5000  
   Causal Chain: SignatureMatchesPerDay → SignatureIDSimilarity → SCAS → Label  
     
   Interpretation: High signature matching frequency (5000/day) caused increased similarity to known patterns (0.92), which triggered outlier detection (SCAS=1), leading to alert classification as Important.  
```   

### **Recommendations**

#### **Severity Levels:**
* **HIGH**: Confidence \> 90%, requires immediate action  
* **MEDIUM**: Confidence 70-90%, investigate soon  
* **LOW**: Confidence \< 70%, routine monitoring

#### **Action Types:**
1. **Immediate Actions** (do now)

   * Block IPs  
   * Enable rate limiting  
   * Apply emergency patches  
2. **Investigation Steps** (analyze)

   * Check logs  
   * Correlate with other events  
   * Review threat intelligence  
3. **Root Cause Mitigation** (prevent recurrence)

   * Update signatures  
   * Reconfigure systems  
   * Improve monitoring 


---

## **Common Scenarios**

### **Scenario 1: SSH Brute Force**

**Alert Characteristics:**

* IntPort \= 22 (SSH)  
* AlertCount \= 250 (many attempts)  
* SignatureIDSimilarity \= 0.95 (matches known patterns)

**Hybrid Explanation:**

* **XAI**: IntPort (0.15), AlertCount (0.10) are important  
* **Causal**: Proto → SignatureIDSimilarity → SCAS → Label  
* **Action**: Block IP, enable fail2ban, change SSH port

### **Scenario 2: Novel Attack (Zero-Day)**

**Alert Characteristics:**

* SCAS \= 1.0 (outlier)  
* SignatureMatchesPerDay \= 15 (rare)  
* SignatureIDSimilarity \= 0.10 (doesn't match known)

**Hybrid Explanation:**

* **XAI**: SCAS (0.42) is most important  
* **Causal**: No strong causal chain (truly novel)  
* **Action**: Manual investigation required, possible zero-day

### **Scenario 3: False Positive**

**Alert Characteristics:**
* SignatureMatchesPerDay \= 180000 (very common)  
* SCAS \= 0.0 (inlier, normal)  
* IntPort \= 53 (DNS)

**Hybrid Explanation:**
* **XAI**: Low importance scores across all features  
* **Causal**: Follows normal traffic patterns  
* **Action**: Mark as benign, consider tuning the signature


