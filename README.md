# Mandarin-English Prompt Injection Detection Layer

This repository contains the practical artefact for an MSc Cybersecurity dissertation project on multilingual prompt-injection detection for LLM-based applications.

The project evaluates whether user prompts can be classified as **Safe**, **Suspicious**, or **Malicious** before they reach an LLM-based chatbot. It focuses on three language categories:

- English
- Mandarin Chinese
- Mandarin-English code-switched prompts

The practical work is implemented as a **Google Colab/Jupyter Notebook experimental pipeline**, not as a deployed web application or FastAPI service.

---

## Project Aim

The aim of this project is to design and evaluate a multilingual prompt-injection detection layer for LLM-based applications. The detection layer screens prompts before they would be passed to an LLM and maps predictions to practical decisions:

| Predicted Label | Prototype Decision |
|---|---|
| Safe | Allow the prompt to pass to the LLM |
| Suspicious | Flag the prompt for caution or review |
| Malicious | Block the prompt from being passed directly to the LLM |

---

## Practical Artefact

The practical artefact consists of two main parts:

1. **Experimental pipeline**  
   A Google Colab-based workflow that loads the dataset, trains/evaluates models, and compares detection performance.

2. **Prototype detection workflow**  
   An interactive prompt-testing workflow that accepts user input and returns predictions from the rule-based, embedding-based, and transformer-based detectors.

---

## Dataset

The experiment uses a balanced multilingual dataset of 900 prompts:

| Language Category | Safe | Suspicious | Malicious | Total |
|---|---:|---:|---:|---:|
| English | 100 | 100 | 100 | 300 |
| Mandarin | 100 | 100 | 100 | 300 |
| Mandarin-English code-switched | 100 | 100 | 100 | 300 |
| **Total** | **300** | **300** | **300** | **900** |

The final dataset was divided using an 80/20 stratified split:

- **720 training prompts**
- **180 testing prompts**

Stratification was applied using both language category and label so that all classes remained balanced in both the training and testing sets.

Expected input files:

```text
train_720_balanced_random_utf8.csv
test_180_balanced_random_utf8.csv
```

These files are to be uploaded into the Colab environment.

---

## Detection Methods

The notebook implements and compares three detection approaches.

### 1. Rule-Based Baseline

The rule-based detector uses manually defined keyword and regular-expression patterns associated with prompt-injection behaviour, such as instruction override, hidden prompt probing, and policy bypass attempts.

This method is transparent and lightweight, but it depends heavily on explicit wording.

### 2. Embedding-Based Classifier

The embedding-based detector uses:

```text
paraphrase-multilingual-MiniLM-L12-v2
```

Prompt texts are converted into multilingual sentence embeddings, and a Logistic Regression classifier is trained to classify prompts as Safe, Suspicious, or Malicious.

### 3. Transformer-Based Classifier

The transformer-based detector uses:

```text
distilbert-base-multilingual-cased
```

The model is fine-tuned for three-class prompt classification. A one-epoch run is first used to confirm that the training pipeline works, followed by a final three-epoch run.

---

## Results Summary

The models were evaluated on the same balanced 180-prompt test set.

| Method | Accuracy | Macro Precision | Macro Recall | Macro F1-score |
|---|---:|---:|---:|---:|
| Rule-Based Baseline | 52.78% | 70.52% | 52.78% | 49.25% |
| Embedding-Based Classifier | 98.89% | 98.89% | 98.89% | 98.89% |
| Transformer Classifier, 1 Epoch | 94.44% | 94.43% | 94.44% | 94.43% |
| Transformer Classifier, 3 Epochs | 96.67% | 96.74% | 96.67% | 96.68% |

The embedding-based classifier achieved the strongest performance on the controlled test set.

---

## Notebook Workflow

The notebook follows this workflow:

```text
GPU availability check
→ Install required packages
→ Load training and testing datasets
→ Clean labels and language categories
→ Define shared evaluation functions
→ Run rule-based baseline
→ Generate multilingual embeddings
→ Train and evaluate embedding-based classifier
→ Prepare transformer dataset
→ Fine-tune multilingual DistilBERT
→ Evaluate transformer model
→ Save result tables and confusion matrices
→ Run interactive prompt-detection workflow
```

---

## Key Outputs

The notebook produces outputs such as:

```text
model_results_summary.csv
model_results_by_language.csv
model_results_by_label.csv
model_confusion_matrices.csv
misclassified_examples_all_methods.csv
test_predictions_all_methods.csv
mandarin_english_pi_practical_results.zip
```

It also generates confusion-matrix images for the evaluated models.

---

## Installation and Running

This project was designed to run in **Google Colab Pro**, especially for transformer-based training.

### 1. Open the notebook in Google Colab

Upload the notebook:

```text
Mandarin_English_PI_Practical.ipynb
```

### 2. Upload the dataset files

Upload the following files into the Colab runtime:

```text
train_720_balanced_random_utf8.csv
test_180_balanced_random_utf8.csv
```

### 3. Install dependencies

The notebook installs the main dependencies using:

```python
!pip install -q -U sentence-transformers transformers datasets accelerate evaluate openpyxl
```

### 4. Run the cells in order

Run each notebook cell from top to bottom. For transformer training, using a GPU runtime is recommended.

In Colab:

```text
Runtime → Change runtime type → GPU
```

---

## Main Dependencies

The project uses:

- Python
- pandas
- numpy
- scikit-learn
- sentence-transformers
- transformers
- datasets
- torch
- evaluate
- matplotlib
- openpyxl

---

## Prototype Detection Workflow

At the end of the notebook, an interactive workflow accepts user input and tests it using the three detectors.

The workflow returns:

```text
Input prompt
Predicted label
Decision: Allow / Flag / Block
```

This demonstrates how a prompt-screening layer could operate before user content reaches an LLM-based chatbot.

---

## Limitations

This project is a prototype-level academic artefact, not a production-ready security system.

Main limitations include:

- The dataset is researcher-created and controlled.
- The workflow is not deployed as a live chatbot or API.
- Real-world prompts may be more diverse than the test data.
- Some short or unusual prompts may be over-classified as Suspicious or Malicious.
- The project focuses only on English, Mandarin, and Mandarin-English code-switched prompts.

Future work should evaluate the approach on larger real-world datasets, additional languages, live chatbot environments, and stronger defence-in-depth controls.

---

## Security and Ethical Notice

This project is intended for defensive AI security research. It focuses on detecting and reducing prompt-injection risk, not on enabling misuse. Prompt examples and outputs should be handled responsibly, and any future deployment should include broader safeguards such as logging, access control, output monitoring, rate limiting, and continuous evaluation.

---

## Author

**Simon Ugochukwu Awaogu**  
MSc Cybersecurity  
University of Sunderland
