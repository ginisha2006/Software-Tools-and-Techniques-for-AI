# Assignment 2: The "Smart Labeling Pipeline" Challenge

> **Total Marks:** 20  
> **Deadline:** 7:00 PM, 15th February, 2026

---

## 🎯 Challenge

Build a **cost-effective, high-quality labeling pipeline** using:

- Human annotation
- Programmatic rules
- Large Language Models (LLMs)

The objective is to create an effective labeling workflow **before funding runs out**.

### Dataset

A set of **300 reviews** from the provided IMDB dataset.

### Reference Notebook

Use the provided Jupyter Notebook hosted on GitHub.

### Tools

- Label Studio
- Python
- pandas
- numpy
- sklearn
- snorkel
- modAL
- cleanlab

---

# Task 1 — The Human as Annotator

## Objective

Establish a **Gold Standard** dataset and measure human consensus.

---

## 1. Label Studio Setup

- Host **Label Studio locally**.
- Create a folder and a virtual environment inside it.
- Install Label Studio in the virtual environment.
- Launch it using:

```bash
label-studio start
```

Label Studio should open in the default browser. If it does not, copy the localhost URL shown in the terminal and open it manually.

### Project Configuration

1. Create a project named:

```text
Movie_Sentiments
```

2. Import:

```text
movie_reviews_300.csv
```

3. Choose the **NLP → Text Classification** template.
4. Configure the XML so that the available labels are **single-choice**:

   - Positive
   - Negative
   - Neutral

5. The review text must come from the `review` column in `movie_reviews_300.csv`.

> Do **not** change the column names in the provided CSV.

---

## 2. Distributed Annotation

Since the group has at least three members, assign the following roles:

| Role | Member |
|---|---|
| Annotator A | Student 1 |
| Annotator B | Student 2 |
| Annotator C | Student 3 |

### Action

Annotators A, B, and C must independently label the **first 100 reviews**.

### Deliverables

Export the annotation sets as:

```text
annotator_a.csv
annotator_b.csv
annotator_c.csv
```

---

## 3. Metrics Implementation

### Requirements

- Write a Python function to parse CSV files into Pandas DataFrames.
- Implement **Fleiss' Kappa from scratch**.
- Do **not** use a library for your own implementation.
- Measure agreement between the **three raters**.
- Compare your result with the `statsmodels` implementation.

---

## 4. Conflict Resolution

### Identify Conflicts

Compare the labels assigned by Annotators A, B, and C to the same movie.

A conflict is any review where the three annotators do **not unanimously agree**.

### Print Samples

Display **5 examples** of conflicting reviews and show:

- Annotator A's label
- Annotator B's label
- Annotator C's label

### Automated Adjudication Logic

| Situation | Final Label |
|---|---|
| Two annotators agree | Use the majority label |
| All three annotators disagree | Assign `Neutral` |

### Outcome

Save the final resolved labels as:

```text
gold_standard_100.csv
```

> Refer to the Jupyter Notebook section for **Task 1**.

---

# Task 2 — Weak Supervision: The "Lazy" Labeler

## Objective

Label the remaining **200 reviews programmatically** to save time.

---

## 1. Heuristic Development

- Analyze `gold_standard_100.csv` for useful patterns.
- Write at least **3 Python heuristic functions**.

Possible examples include:

- Regex rules for words such as `"horrible"`
- Length-based checks

### Action

Apply the heuristics to the remaining **200 unlabeled reviews**.

---

## 2. Snorkel Implementation

- Wrap the heuristics as Snorkel `@labeling_function`s.

### Metrics to Report

| Metric | Description |
|---|---|
| Coverage | Percentage of data that receives a label |
| Conflict Rate | Cases where different rules assign contradictory labels to the same review |

For example, a conflict occurs if:

```text
Rule A → Positive
Rule B → Negative
```

for the same review.

---

## 3. Adjudication

Use **Majority Vote** to generate probabilistic labels (weak labels) for the 200 reviews.

### Deliverable

```text
weak_labels_200.csv
```

> Refer to the Jupyter Notebook section for **Task 2**.

---

# Task 3 — Active Learning: The Budget Optimizer

## Objective

Simulate cost savings by training a model iteratively.

---

## 1. Setup

### Seed Dataset

```text
gold_standard_100.csv
```

### Pool

The **200 weakly labeled reviews**.

For the simulation, treat their labels as **hidden or unknown**.

---

## 2. Query Strategy Implementation

Implement the following strategies **from scratch**:

- Least Confidence sampling
- Entropy Sampling

---

## 3. Iterative Loop

1. Train a `LogisticRegression` model using the Seed dataset.
2. Run a loop for **5 iterations**.
3. During each iteration:

   - Select the **top 10 most uncertain samples** from the Pool.
   - Reveal their ground-truth labels from the dataset.
   - Add them to the training set.
   - Retrain the model.
   - Log the test accuracy.

---

## 4. Visualization

Create a **Learning Curve**:

- **X-axis:** Number of Labels
- **Y-axis:** Accuracy

Also compare:

- Active Learning
- Random Sampling

> Refer to the Jupyter Notebook section for **Task 3**.

---

# Task 4 — AI vs. AI: LLM & Noise Detection

## Objective

Use LLMs for bulk labeling and detect potential hallucinations or noisy labels.

---

## 1. LLM Pipeline

### Prompt Engineering

Design a **Few-Shot Prompt** containing **3 examples** from:

```text
gold_standard_100.csv
```

### Action

Send the remaining unlabeled samples from the Pool, approximately **150 reviews**, to the **Gemini API Free Tier**.

Extract the generated labels into a CSV file.

### Output

```text
llm_labels_150.csv
```

---

## 2. Noise Hunting — Cleanlab Logic

1. Train a simple `LogisticRegression` model on:

```text
llm_labels_150.csv
```

2. Identify **High Confidence Disagreements**.

### Criteria

A suspicious disagreement occurs when:

```text
Model Probability > 0.90 for Class A
```

but:

```text
LLM Label = Class B
```

### Deliverable

Print the **top 5 suspicious reviews**.

> Refer to the Jupyter Notebook section for **Task 4**.

---

# 📦 Submission Requirements

Submit the following:

- The completed Jupyter Notebook.
- A screenshot of the **Label Studio annotation interface**.
- The following CSV files:

```text
annotator_a.csv
annotator_b.csv
annotator_c.csv
gold_standard_100.csv
weak_labels_200.csv
llm_labels_150.csv
```

Finally, **zip all resources into a single file** and submit it through the Google Form that will be circulated towards the end of the deadline.
