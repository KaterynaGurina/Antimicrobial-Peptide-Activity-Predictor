# Antimicrobial Peptide Activity Predictor

Binary sequence classifier predicting antimicrobial activity from raw amino acid 
sequences. Built for COMP 432 (Machine Learning) at Concordia University.  
Dataset courtesy of Dr. Ré Mansbach's physics lab.

---

## Problem

Antimicrobial peptides (AMPs) are short proteins that disrupt bacterial membranes 
and are a promising alternative to traditional antibiotics. Experimental screening 
is slow and expensive — this project trains ML models to pre-screen sequences 
computationally.

**Key challenge:** The dataset is 96.5% negative, and label 0 means "not confirmed 
active", not "confirmed inactive." A model predicting "inactive" for everything 
scores 96.5% accuracy while finding zero real AMPs. AUROC and average precision 
(AP) are used instead, as they are robust to class imbalance and don't require 
a fixed decision threshold.

---

## Dataset

- 304,001 labeled peptide sequences (80/20 train/test split pre-defined)
- Label 1 = experimentally confirmed antimicrobial activity
- Label 0 = no confirmed activity (noisy: may simply be untested)
- Sequence lengths: 2–50 amino acids (mean ~28)

---

## Encodings Compared

| Encoding | Features per position | Description |
|---|---|---|
| One-hot | 20 | Binary vector, one 1 per amino acid identity |
| Physicochemical | 5 | Hydropathy, charge, MW, polarity, isoelectric point (AAindex) |
| Combined (normalized) | 25 | Concatenation of above; physicochemical values StandardScaler-normalized |

---

## Results

| Model | Encoding | AUROC | Avg Precision |
|---|---|---|---|
| Logistic Regression | One-hot | 0.9373 | 0.356 |
| CNN | One-hot | 0.9648 | 0.617 |
| CNN | Physicochemical | 0.9529 | 0.523 |
| CNN | Combined (normalized) | **0.9690** | **0.686** |
| BiLSTM | One-hot | 0.9538 | 0.535 |

---

## Key Findings

- **Combined encoding + CNN is the best model** (AUROC 0.9690, AP 0.686)
- **Normalization was critical**: without StandardScaler, the combined encoding 
  hurt performance (AUROC 0.953) because MW values (75–204) dominated the binary 
  one-hot values (0 or 1)
- **CNN outperformed BiLSTM** — with average sequence length of only 28 amino acids, 
  local motif filters are sufficient; no long-range dependencies to capture
- **Physicochemical-only encoding underperformed** despite being biologically motivated: 
  5 real values lose exact amino acid identity that one-hot preserves

---

## Data

Dataset provided by Dr. Ré Mansbach's physics lab (Concordia University).

Place the four files in the same directory as the notebook:
`peptide_train.txt`, `peptide_test.txt`, `function_train.txt`, `function_test.txt`

---

## Setup

```bash
pip install torch numpy pandas scikit-learn matplotlib
```

**GPU recommended** — each CNN epoch takes ~1 min on T4 GPU.  
In Google Colab: Runtime → Change runtime type → T4 GPU.

---

## Citation

Renaud, N., Gaudreault, F., and Mansbach, R.A.  
"Latent spaces for antimicrobial peptide design."  
*Digital Discovery*, 2021.
