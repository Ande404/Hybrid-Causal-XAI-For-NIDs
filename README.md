
# NIDS Alert Classification with LSTM + XAI

This project reproduces the approach from **Kalakoti et al. (2025)** for classifying intrusion detection system (IDS) alerts into *important* vs *irrelevant*.  
It uses a **Long Short-Term Memory (LSTM)** network combined with **explainable AI (XAI)** methods (SHAP, LIME, DeepLIFT and IG) to make the model’s predictions interpretable.

---

## 📂 Project Files
- `test.ipynb` → Main notebook to train and evaluate the model, and run explainability analysis.
- `requirements.txt` → Project Dependecies
- `best_lstm.pt` → (Optional) My Pretrained weights (You may want to delete this in your local repo so that you can generate your own).
- `xai_metrics_sample.csv` → Example output that i got with computed XAI metrics.
- `xai_comparison_plots.png` → This is the image with XAI methods compared that you will get when you run the model


---

## ⚙️ Setup Instructions

### Option 1: Run Locally (Python on your computer)
1. **Install Python**
   - [Download Python 3.10+](https://www.python.org/downloads/)  
   - On Windows: make sure to check **“Add Python to PATH”** during install.
   - On macOS: Python 3 usually comes pre-installed, You can check which version you have buy running this command in the terminal
   ```bash
   python3 --version
   ```

2. **Install Jupyter Notebook**
   Open a terminal/command prompt and run:
   ```bash
   pip install notebook
   ```

3. **Download the Dataset**
    - download the dataset from this repo [here](https://github.com/ristov/nids-alert-data)

4. **Install the requirements**
    - cd to your repo and then run this in the terminal:
    ```bash
    pip install -r requirements.txt
    ```
5. **Launch Jupyter Notebook**
    - run this command in the terminal
    ```bash
    jupyter notebook
    ```
    - A browser window will open
    - Open test.ipynb and run the cells in order.

### Option 2: Run in Google Colab (no install required )
1. Download the dataset from this repo [here](https://github.com/ristov/nids-alert-data) 

2. Go to Google Colab

3. Click “Upload” and upload the file test.ipynb.

4. Upload your dataset (dataset-labeled-anon-ip.csv) in the Colab environment.

5. Run the notebook cells in order.

6. 💡 Colab gives you free access to CPUs/GPUs. For faster training, go to
Runtime → Change runtime type → Hardware accelerator → GPU.