# 🔬 Real-World Stress Test #02 — Telco Customer Churn

> **Don't just ask whether a model works. Ask how it behaves when the world changes.**

This repository is part of the **Real-World Stress-Test Series** — a set of focused analytical experiments designed to examine how data-driven models behave when real-world conditions change.

Instead of building another standard customer-churn prediction project, this experiment asks:

> **How sensitive are churn predictions when customer pricing changes?**

The goal is **not** to claim that increasing prices causes customers to churn. Instead, this experiment measures how a trained churn model's predictions respond to simulated changes in `MonthlyCharges`.

---

## 🎯 Why This Experiment?

A model can perform reasonably well on historical data and still behave differently when the conditions around it change.

In customer analytics, pricing is one example of a changing business condition.

This experiment therefore focuses on:

- Model sensitivity
- Prediction stability
- Stress testing
- Responsible interpretation
- Business implications of changing input conditions

The broader question is:

> **Is model accuracy enough, or should we also examine how model predictions behave when the environment changes?**

---

## 📊 Experiment at a Glance

| Component | Description |
|---|---|
| **Dataset** | IBM Telco Customer Churn |
| **Business Domain** | Customer Retention / Telecom |
| **Target** | Churn |
| **Stress Variable** | `MonthlyCharges` |
| **Model** | Logistic Regression |
| **Stress Scenarios** | 0%, +5%, +10%, +15%, +20% |
| **Training** | Original training data only |
| **Stress Testing** | Held-out test set |
| **Model Retraining** | No |
| **Primary Focus** | Prediction sensitivity |

---

# 🧪 Experimental Design

### 1. Establish a baseline

A Logistic Regression model is trained using the original training data.

An 80/20 stratified train-test split is used to evaluate the baseline model.

### 2. Freeze the model

After training, the model is **not retrained** for the stress scenarios.

The same model is reused throughout the experiment.

This allows the analysis to isolate how changes in `MonthlyCharges` affect the model's predictions.

### 3. Apply controlled stress

Only `MonthlyCharges` is modified in the held-out test set.

The scenarios are:

```text
Normal      0%
Stress 1   +5%
Stress 2  +10%
Stress 3  +15%
Stress 4  +20%
```

These are **simulated scenarios**, not claims about actual telecom pricing changes.

### 4. Compare model predictions

The model's outputs are compared between the original test data and each stressed scenario.

The analysis focuses on changes in:

- Average predicted churn probability
- Predicted churn rate
- Individual predictions
- No Churn → Churn switches
- Segment-level sensitivity

---

# 🧠 Key Methodological Decision

A tempting approach would be to increase `MonthlyCharges` and then evaluate the stressed predictions against the original `Churn` labels using accuracy, recall, or F1-score.

That would not represent a valid counterfactual evaluation.

The historical `Churn` labels describe what happened under the **original conditions**. They do not tell us what would have happened to those same customers after a hypothetical price increase.

Therefore, this experiment does **not** treat the original labels as counterfactual outcomes.

Instead, it asks:

> **How does the model's prediction change when the input condition changes?**

This distinction is central to the experiment.

---

# 📈 Key Results

The frozen model was evaluated under the original conditions and after applying a simulated **+20% increase in `MonthlyCharges`**.

| Metric | Normal | +20% Stress |
|---|---:|---:|
| **Average predicted churn probability** | 0.2686 | 0.2759 |
| **Predicted churn rate** | 22.43% | 23.56% |

### Prediction change

- **Average predicted churn probability increased:** `0.2686 → 0.2759`
- **Absolute increase:** `0.0073` probability points
- **Predicted churn rate increased:** `22.43% → 23.56%`
- **Absolute increase in predicted churn rate:** `1.13 percentage points`
- **Predictions changed:** `1.14%`
- **Predictions switched to Churn:** `1.14%`

### What this means

Under the simulated +20% pricing scenario, the model produced a **higher predicted churn risk** and a small increase in predicted churn classifications.

However, this should be interpreted as **model prediction sensitivity**, not as evidence that a real 20% price increase would cause 1.14% of customers to churn.

---

# 🔍 What Was Actually Measured?

### Baseline model

The baseline model was evaluated using standard classification metrics, including:

- Accuracy
- Precision
- Recall
- F1-score
- ROC-AUC
- Confusion matrix

### Stress-test analysis

The stress experiment focuses on:

- Average predicted churn probability
- Predicted churn rate
- Percentage of predictions that change
- No Churn → Churn switches
- Individual probability changes
- Contract-level sensitivity
- Whether stressed values move outside the model's historical training range

---

# 🧩 Features

The model uses:

### Numerical Features

- `tenure`
- `MonthlyCharges`
- `SeniorCitizen`

### Categorical Features

- `Contract`
- `InternetService`
- `PaymentMethod`
- `PaperlessBilling`
- `Partner`
- `Dependents`

### Target

`Churn`

---

# Why Was `TotalCharges` Excluded?

`TotalCharges` was inspected and converted to numeric format during data preparation.

However, it was excluded from the final stress-test model.

The reason is methodological:

`TotalCharges` is cumulative. If `MonthlyCharges` is artificially increased while leaving historical `TotalCharges` unchanged, the hypothetical customer record can become internally inconsistent.

For this focused experiment, `MonthlyCharges` is therefore isolated as the stress variable.

---

# 🏗️ Model Pipeline

```text
Raw Telco Customer Data
          ↓
Data Quality Checks
          ↓
Feature Selection
          ↓
Train / Test Split
          ↓
Preprocessing
          ↓
Logistic Regression
          ↓
Baseline Evaluation
          ↓
     FREEZE MODEL
          ↓
Simulated Pricing Stress
          ↓
Prediction Sensitivity Analysis
          ↓
Business Interpretation
```

---

# 💡 Business Interpretation

The experiment shows that changing a business input can change model outputs even when the model itself remains unchanged.

Under the +20% simulated pricing scenario:

> **Average predicted churn probability increased from 0.2686 to 0.2759.**

And:

> **Predicted churn rate increased from 22.43% to 23.56%.**

This demonstrates why model monitoring should not stop at historical performance metrics.

For a model used in a changing business environment, it can also be useful to understand:

- Which inputs strongly affect predictions?
- How stable are predictions under plausible changes?
- Which customer segments are more sensitive?
- Are stressed inputs still within the model's historical training range?
- What assumptions are being introduced by the scenario?

---

# ⚠️ Interpretation Boundary

This experiment **does not establish**:

> "A 20% price increase will cause 1.14% of customers to churn."

Instead, it establishes:

> **Under the specified simulation, 1.14% of model predictions switched to Churn.**

These are fundamentally different statements.

### Prediction sensitivity ≠ causal customer behaviour

Real customer behaviour after a pricing change would require appropriate empirical or causal evidence, such as:

- Controlled experiments
- Historical pricing changes
- Quasi-experimental designs
- Appropriate causal inference methods

This experiment is a **model stress test**, not a causal pricing study.

---

# 📁 Repository Structure

```text
week2_telco_stress_test/
│
├── data/
│   └── WA_Fn-UseC_-Telco-Customer-Churn.csv
│        
│
├── notebook/
│   └── Telco_Churn_Stress_Test.ipynb
│
├── outputs/
│   ├── figures/
│   │   ├── stress_prediction_probability.png
│   │   ├── prediction_changes.png
│   │   └── contract_sensitivity.png
│   │
│   └── results/
│       ├── stress_test_results.csv
│       ├── contract_sensitivity_results.csv
│       └── range_check.csv
│
└── README.md
```

The dataset itself is not included in this repository.

---

# 🚀 How to Run

Clone the repository:

```bash
git clone <your-repository-url>
cd week2_telco_stress_test
```

Install the required libraries:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn joblib
```

Place the dataset at:

```text
data/raw/WA_Fn-UseC_-Telco-Customer-Churn.csv
```

Then open:

```text
notebook/Week2_Telco_Churn_Stress_Test.ipynb
```

Run the notebook cells sequentially.

---

# 📊 Outputs

The experiment produces:

### Stress Prediction Analysis

Shows how average predicted churn probability changes across the pricing scenarios.

### Prediction Change Analysis

Shows how many model predictions change as the stress level increases.

### Contract-Level Analysis

Examines whether prediction sensitivity differs across contract types.

### Range Check

Identifies whether stressed `MonthlyCharges` values move beyond the range observed during model training.

---

# ❓ Questions Behind the Experiment

The analysis is designed around several questions:

1. Does increasing `MonthlyCharges` systematically change predicted churn risk?
2. How large is the change in predicted probability?
3. How many customer classifications change?
4. Are some contract groups more sensitive?
5. Do stressed prices move beyond the model's historical training range?
6. What assumptions are introduced by the stress scenario?
7. What can the model actually tell us — and what can it not tell us?

---

# ⚠️ Limitations

This experiment has several important limitations:

- Stress scenarios are simulated.
- Historical churn labels cannot provide counterfactual outcomes.
- Only `MonthlyCharges` is changed.
- Real customer behaviour depends on many factors.
- `TotalCharges` is excluded to avoid inconsistent stressed records.
- Higher stress levels may move observations outside the training distribution.
- Only one dataset is used.
- Only one baseline model is evaluated.
- Logistic Regression is used as a focused baseline rather than a production churn system.

---

# 🧠 The Bigger Lesson

A model can perform well on historical data and still behave differently when the environment changes.

Therefore, an important question is not only:

> **"How accurate is the model?"**

but also:

> **"How does the model behave when the conditions around it change?"**

This experiment is a small example of that broader idea.

---

# 👤 My Perspective

This is intentionally a **focused analytical experiment**, not another large end-to-end machine learning project.

The emphasis is on:

- Asking a better question
- Designing a controlled experiment
- Making assumptions explicit
- Separating prediction from causality
- Testing model sensitivity
- Communicating limitations
- Connecting technical results to business decisions

> **The code supports the reasoning. The reasoning is the experiment.**

---

# 🛠️ Technologies

- Python
- Pandas
- NumPy
- Scikit-learn
- Matplotlib
- Seaborn
- Joblib
- Jupyter Notebook

---

# 👩‍💻 Author

**Lisandi Himara**

BSc (Hons) Data Science & Business Analytics

Areas of interest:

- Data Science
- Machine Learning
- Business Analytics
- Model Robustness
- Responsible AI
- Decision Support

---

## 📌 Final Takeaway

> **A model should not only be tested for how well it predicts the past — it should also be examined for how its predictions respond when the world around it changes.**