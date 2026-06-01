# SLD-CKD LLM-ReKAP: Knowledge-Driven Risk Prediction for Incident CKD in Steatotic Liver Disease

[![Python 3.12](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/downloads/release/python-3120/)
[![R 4.4.0](https://img.shields.io/badge/R-4.4.0-blue.svg)](https://cran.r-project.org/)
[![RAPIDS cuML](https://img.shields.io/badge/RAPIDS-cuML-orange.svg)](https://rapids.ai/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


## 📖 Overview

Steatotic liver disease (SLD) accelerates incident chronic kidney disease (CKD) via cross-organ crosstalk, yet traditional data-driven models often fail to capture these underlying mechanistic drivers. 

To address this, we developed the **LLM-ReKAP (Large Language Model-Retriever Knowledge Graph-Augmented Predictive)** framework. This framework shifts from traditional data-driven statistical fitting to **knowledge-driven semantic reasoning**. By utilizing a multi-agent workflow to construct an evidence-weighted Knowledge Graph (KG) from biomedical literature, and dynamically integrating LLM-derived orthogonal risk signals with established clinical baseline scores, the framework achieves state-of-the-art discrimination and net clinical benefit.

---

## ⚙️ Installation & Setup

### Hardware Requirements
* **GPU:** NVIDIA GPU highly recommended for local LLM deployment.

### Software Setup
1. Clone the repository:
```bash
git clone https://github.com/DongchengLuo/SLD-CKD-KG-LLM.git
cd SLD-CKD-KG-LLM
```
2. Install the required Python dependencies:
```bash
pip install -r requirements.txt

```
3. (Optional but recommended) Ensure you have an OpenAI-compatible local server (e.g., [vLLM](https://github.com/vllm-project/vllm), [Ollama](https://ollama.com/)) running for privacy-preserving local LLM inference.

---

## 🚀 Part 1: Quick Start (Interactive Web UI)

For clinicians, end-users, and evaluators, the framework provides a unified, **fully customizable Web UI**. You do **not** need to hardcode paths or API keys into the scripts. Everything is configurable directly within your browser.

### Launching the Interface
Run the following command in your terminal:
```bash
streamlit run webui.py
```

### 🎛️ Dynamic Configuration via UI:
Once the UI launches (typically at `http://localhost:8501`), you can dynamically configure the following from the sidebar and main panels:
* **API Endpoints & Keys:** Input your Gemini API keys for KG construction, and your OpenAI/Local Endpoint URLs (e.g., `[http://127.0.0.1:8000/v1](http://127.0.0.1:8000/v1)`) for privacy-preserving retrieval and reasoning.
* **Model Selection:** Flexibly switch between foundation models (e.g., `gemini-1.5-pro`, `moonshotai/kimi-k2-instruct-0905`, local `Qwen/DeepSeek` variants).
* **Data Uploads:** Upload raw PDFs, structured JSON knowledge bases, or CSV reference standards directly via the file uploader—no path hardcoding required.
* **Prompt Engineering:** View, edit, and fine-tune the system prompts for Entity Retrieval, Relationship Extraction, and Abstract Rating directly in the UI text areas.

### Workflow Modules:
1. **Knowledge Graph Construction:** Upload PDFs to extract and normalize biomedical entities and relationships.
2. **Clinical Risk Retrieval:** Input patient data (JSON) to algorithmically retrieve patient-specific subgraphs.
3. **Final Clinical Scoring:** Generate an interpretable clinical report detailing projected risk percentages (3, 5, 10, and 15-year horizons) and Combined Model Cutoffs.

*(Note: A live demo is available at [https://lizzette-periproctic-nondefensibly.ngrok-free.dev](https://lizzette-periproctic-nondefensibly.ngrok-free.dev))*

---

## 🧠 Part 2: Under the Hood (Modular Agent Pipeline)

For developers and researchers performing batch processing or exploring Explainable AI (XAI), the pipeline is divided into specialized scripts. Each script contains a centralized configuration block at the top, allowing you to quickly map paths or environment variables for large-scale cohort execution.

### Phase 1: Knowledge Graph Construction & Refinement
* **`Information_extraction_agent.py`:** Parses raw PDF research articles into structured JSON triples (entities, relationships, and metadata).
* **`KG_refiner_agent(entity_summarization).py`:** Consolidates definitions and contexts for individual medical entities.
* **`KG_refiner_agent(entity_group_summarization).py`:** Synthesizes functional and semantic entity groups.

### Phase 2: Parallel Agentic Retrieval
* **`entity_screening_agent.py`:** Screens and selects the most relevant biological entities for a specific patient profile.
* **`relationship_screening_agent.py`:** Filters and ranks critical mechanistic relationships tailored to the patient's comorbidities.
* **`literature_screening_agent.py`:** Ranks and selects the Top 25/50 most relevant research abstracts based on semantic alignment and Journal Impact Factor.

### Phase 3: Population Clustering & Knowledge Synthesis
* **`cluster.py`:** Calculates composite retrieval scores and utilizes GPU-accelerated RAPIDS `cuml` to perform K-Means clustering on the patient cohort.
* **`KG_summary_generation.py`:** Generates comprehensive methodological reports outlining SLD-CKD risk pathways for specific patient clusters.

### Phase 4: Predictive Reasoning (LLM-ReKAP)
* **`LLM-ReKAP.py` (and `Consensus Clinical Risk.py`):** The final Predictor Agent. Synthesizes the generated KG reports and individual patient profiles to output quantitative risk projections and confidence scores.

### Phase 5: Interpretability & Explainable AI (XAI)
*Deconstructs the LLM's internal reasoning mechanisms to bridge the gap between "black-box" AI and clinical consensus.*
* **`SHAP.py`:** Calculates SHapley Additive exPlanations to evaluate global feature contributions across semantic clusters.
* **`IG.py`:** Implements token-level Integrated Gradients (IG) to quantify the exact predictive influence of specific narrative tokens in the LLM's input embeddings.
* **`IG_aggregation.py`:** Aggregates gradient scores to translate the LLM's non-linear predictive mechanisms into standard, clinically interpretable hazard ratios (via a surrogate Cox model).

---

## 📊 Data Availability

- **UK Biobank (UKBB):** Raw clinical data are available to approved researchers via the [UK Biobank portal](https://www.ukbiobank.ac.uk).
- **Nanfang Cohort:** Deidentified clinical data and intermediate reasoning logs contain sensitive individual health information. These are available from the corresponding author upon reasonable request and subject to institutional ethical approval.

## 📝 Citation

If you find this code or framework useful in your research, please cite our paper:

```bibtex
@article{luo2026agentic,
  title={An agentic knowledge graph reasoning framework derives orthogonal risk signals for incident CKD in steatotic liver disease},
  author={Luo, Dongcheng and Zhang, Qi and Hong, Chang and Qin, Xianhui and Xiao, Lushan and Ye, Ziliang and Li, Yang and Zhang, Hanzhi and Liu, Li},
  journal={TBD},
  year={2026}
}
```

