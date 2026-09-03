# 🎭 Multimodal Sentiment Engine

> **STT-AI Assignment 3 — The Multimodal Sentiment Engine Challenge**  
> **Course:** Software Tools and Techniques for AI

An end-to-end data augmentation and validation pipeline for building a larger, more diverse, multilingual, and multimodal sentiment analysis dataset.

---

## 📌 Project Overview

This project extends the sentiment labeling pipeline by expanding an existing labeled dataset without requiring additional human annotation.

The pipeline combines:

- Dataset consolidation and filtering
- Classical text augmentation
- LLM-based synthetic review generation
- Diversity and sentiment consistency checks
- English-to-Hindi translation
- Multilingual sentiment validation
- Text-to-speech audio generation
- Audio feature extraction
- Speech-to-text validation
- Final dataset assembly and evaluation

---

## 🔄 Pipeline Overview

```text
Provided Labeled Datasets
        │
        ▼
Dataset Consolidation
        │
        ▼
   Consolidated Dataset
        │
        ├──────────────────────┐
        ▼                      ▼
Classical Augmentation   LLM Review Generation
        │                      │
        ▼                      ▼
Quality Filtering       Diversity Validation
        │                      │
        └──────────┬───────────┘
                   │
                   ▼
         Multilingual Translation
                   │
                   ▼
          Sentiment Preservation
                   │
                   ▼
          Synthetic Audio Generation
                   │
                   ▼
       Audio Feature Extraction + STT
                   │
                   ▼
        Final Augmented Dataset
                   │
                   ▼
      Black-Box Model Evaluation
```

---

## 🧩 Task 1 — Dataset Consolidation and Classical Augmentation

The provided sentiment datasets are consolidated to create a reliable base dataset.

The pipeline:

- Combines gold-standard and weakly labeled reviews.
- Uses a Logistic Regression baseline with TF-IDF features.
- Selects LLM-labeled reviews where the baseline confidence is at least `0.65` and agrees with the LLM label.
- Removes duplicate reviews.
- Identifies the minority sentiment class.

### Classical Augmentation

The minority class is augmented using two approaches:

- **Synonym Replacement:** Uses WordNet to replace selected words with synonyms while preserving important sentiment-bearing words.
- **Back Translation:** Translates reviews from English to Hindi and back to English.

Jaccard similarity is used as a quality filter to reject augmented reviews that are either too similar or too different from the original text.

### Outputs

```text
consolidated_base.csv
augmented_classical.csv
class_distribution.png
```

---

## 🤖 Task 2 — LLM-Based Synthetic Review Generation

A few-shot prompt is used to generate new synthetic movie reviews using an LLM.

The generation pipeline includes:

- Few-shot examples from the gold-standard dataset
- JSON-formatted output
- Batch-based generation
- Reviews across Positive, Negative, and Neutral sentiment classes
- Self-BLEU analysis for diversity measurement
- Sentiment consistency validation using the Logistic Regression baseline

Reviews where the baseline prediction disagrees with the generated sentiment label are stored separately.

### Outputs

```text
llm_generated_300.csv
llm_generated_flagged.csv
prompt_template.txt
diversity_report.txt
```

---

## 🌐 Task 3 — Multilingual Sentiment Translation

A selected subset of reviews is translated from English to Hindi to support multilingual sentiment analysis.

The validation pipeline is:

```text
English Review
      ↓
Hindi Translation
      ↓
Back Translation to English
      ↓
BLEU Score + Sentiment Validation
```

Translation quality is evaluated using:

- BLEU score
- Sentiment prediction consistency
- Quality flags
- Manual validation of selected samples

### Output

```text
bilingual_reviews.csv
```

---

## 🎙️ Task 4 — Multimodal Audio Generation

Synthetic audio reviews are generated to support voice-based sentiment analysis.

The process includes:

1. Selecting reviews from different sentiment classes.
2. Generating speech using Text-to-Speech.
3. Creating audio samples.
4. Extracting acoustic features.
5. Transcribing the generated audio using Whisper.
6. Calculating Word Error Rate (WER) for round-trip validation.

### Audio Features

The extracted features include:

- Duration
- Spectral Centroid
- Zero Crossing Rate
- MFCC mean coefficients

### Outputs

```text
audio_samples/
audio_features.csv
audio_validation.csv
```

---

## 🧪 Task 5 — Final Dataset Assembly and Evaluation

The final augmented dataset combines:

- Consolidated base reviews
- Classically augmented reviews
- Valid LLM-generated reviews
- Back-translated English reviews

Duplicate samples are removed before creating the final dataset.

The final dataset is evaluated using the provided Black-Box Evaluator and compared with the smaller consolidated dataset to measure the impact of augmentation.

### Output

```text
final_augmented_dataset.csv
```

---

## 📁 Repository Structure

```text
Assignment_3/
│
├── audio_samples/
│
├── Code_Jupyter_Notebook.ipynb
│
├── audio_features.csv
├── audio_validation.csv
├── augmented_classical.csv
├── bilingual_reviews.csv
├── class_distribution.png
├── consolidated_base.csv
├── diversity_report.txt
├── final_augmented_dataset.csv
├── llm_generated_300.csv
├── llm_generated_flagged.csv
├── prompt_template.txt
└── README.md
```


## ▶️ Running the Project

Install the required dependencies:

```bash
pip install -r requirements.txt
```

Open and run:

```text
Code_Jupyter_Notebook.ipynb
```

Run the notebook cells sequentially to reproduce the complete pipeline.

> **Note:** If an API key is required for LLM generation, store it securely in a `.env` file and do not commit it to GitHub.


## 📌 Conclusion

This project demonstrates a complete **multimodal data augmentation pipeline** for sentiment analysis.

By combining classical NLP augmentation, LLM-generated synthetic data, multilingual translation, text-to-speech generation, and automated quality validation, the pipeline expands the original dataset while introducing support for both **multilingual text and voice-based reviews**.
