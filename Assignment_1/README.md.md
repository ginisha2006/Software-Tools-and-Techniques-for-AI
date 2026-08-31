# Assignment 1: The "Box Office Bomb" Data Pipeline

> **Total Marks:** 20  
> **Deadline:** 7:00 PM, 22nd January, 2026

---

## Background Story

You are working as a **data engineer at a media analytics startup**. Your team is conducting a study on financial failures in the film industry. They want to understand the relationship between:

- Production Budgets
- Estimated Losses
- Critical Reception (IMDb / Metacritic scores)

There is no clean dataset available. You have identified the Wikipedia page **"List of biggest box-office bombs"** as your primary source. However, this data is designed for human readers rather than machines and contains:

- **Visual noise:** Status symbols such as `§` and `†` attached to titles.
- **Ambiguous numbers:** Budgets and losses may be ranges, such as `$100–160`.
- **Messy references:** Footnotes such as `[nb 2]` and currency symbols mixed with values.

Your manager has adopted a **"Strict Schema" policy**. You must build a pipeline that ingests this dirty data, validates it using **Pydantic** to ensure type safety, and enriches it using the **OMDb API**.

---

# 🎯 Objective

Design and implement an end-to-end pipeline that:

1. Scrapes the specific **"Box Office Bomb"** table from Wikipedia.
2. Validates and cleans the data using **Pydantic models**, including converting ranges to averages and stripping symbols.
3. Enriches valid entries with metadata such as **Plot** and **Ratings** using the OMDb API.
4. Categorizes the financial impact for analysts.

---

# Tasks & Evaluation

## Task 1 — Scrape the "Bombs" Table

**Marks: 4**

### Story Context

The data is locked in an HTML table. You need to extract the **raw text exactly as it appears** before attempting any cleaning.

### Requirements

1. Target the Wikipedia page:

   `https://en.wikipedia.org/wiki/List_of_biggest_box-office_bombs`

2. Programmatically extract the main table:

   **"Biggest box-office bombs"**

3. Parse the following **raw string columns** for every row:

   | Column | Example |
   |---|---|
   | Film Title | `Jungle Cruise §` |
   | Year | `2021` |
   | Net Production Budget | `$200` or `$100–160` |
   | Estimated Loss | Target the **Nominal** column, not the inflation-adjusted column |

### Evaluation

- **Targeting — 2 marks:** Correctly identifies the table and handles nested headers such as Budget vs. Loss sub-columns.
- **Extraction — 2 marks:** Accurately extracts raw strings for all rows without losing data.

> **Important:** When extracting movie titles, extract the entire raw string, including symbols and references. To extract the complete text content of an element, including symbols or characters outside nested tags, use `.get_text()` directly on the parent element.
>
> Example:
>
> ```python
> text = soup.th.get_text()
> ```

---

## Task 2 — Pydantic Data Parsing & Validation

**Marks: 6**

### Story Context

> **"Garbage In, Garbage Out."**

Your manager forbids passing raw strings to the analysis layer. You must implement a validation layer using **Pydantic**. This layer acts as a firewall, rejecting bad data and cleaning messy formats.

### Requirements

Define a Pydantic model class, for example `MovieData`, implementing the following validators.

### 1. Title Cleaning

- Remove footnote markers such as `[nb 2]` and `[1]`.
- Remove special status symbols:
  - `§` — streaming
  - `†` — currently playing

**Example:**

```text
Input:  Jungle Cruise §
Output: Jungle Cruise
```

### 2. Numeric Parsing — Budget & Loss

- Strip currency symbols such as `$`.
- Remove reference tags.
- Handle ranges such as `100–160`.
- Parse both numbers and calculate their average.

**Example:**

```text
Input:  $100–160
Output: 130.0
```

### 3. Year Validation

Ensure the year is a valid integer.

### Implementation Constraint

- Use Pydantic's `@field_validator` decorator for v2 **or** `@validator` for v1.
- Rows that fail validation, such as unparsable numbers, must be **dropped or logged**.
- Invalid rows must **not crash the script**.

### Evaluation

| Criterion | Marks |
|---|---:|
| Correct regex logic for cleaning titles and symbols `§`, `†` | 2 |
| Range handling and average calculation | 2 |
| Effective use of Pydantic to enforce `float` and `int` types | 2 |

---

## Task 3 — Enrich with OMDb Data

**Marks: 4**

### Story Context

Financial data tells us how much money was lost, but it does not explain **why**. Metadata can help investigate possible causes.

### Requirements

For each validated movie object from Task 2:

1. Query the **OMDb API** using the cleaned **Title** and **Year**.
2. Extract and store the following fields:

   - Plot
   - Metascore
   - IMDb Rating (`imdbRating`)
   - Director
   - Language

### Error Handling

- If the API returns `Response: "False"`, handle it gracefully.
- If a field contains `"N/A"`, store it as `None` or `NaN`.
- Do **not** discard the Wikipedia row simply because OMDb data is missing.

### Evaluation

| Criterion | Marks |
|---|---:|
| Correct construction of API request parameters | 1 |
| Accurate extraction of all 5 required fields | 2 |
| Robust handling of missing data | 1 |

---

## Task 4 — Data Consistency Check

**Marks: 2**

### Story Context

APIs are not perfect. For example, querying **"The Mummy"** might return the 1999 movie instead of the 2017 movie.

### Requirements

1. Compare the **Wikipedia Year** with the **OMDb Year**.
2. Create a column named `Match_Status`.

### Match Status Rules

| Condition | Match_Status |
|---|---|
| Years match, allowing a tolerance of ±1 year | `Verified` |
| Years differ by more than 1 | `Mismatch` |
| OMDb returned no data | `Not Found` |

### Evaluation

- Correct comparison logic while handling integers and strings — **2 marks**

---

## Task 5 — Final Dataset & Categorization

**Marks: 4**

### Story Context

The final output will be consumed by financial analysts who group losses into tiers.

### Requirements

1. Create a Pandas DataFrame from the processed objects.
2. Add a new column named `Loss_Category` based on the cleaned **Estimated Loss**.

### Loss Categories

| Estimated Loss | Category |
|---|---|
| Loss ≥ $100M | `Catastrophic` |
| Loss between $50M and $100M | `Severe` |
| Loss < $50M | `Moderate` |

3. Save the final dataset as:

```text
box_office_failures.csv
```

### Required Columns

```text
Title
Year
Director
Language
Budget_Millions
Loss_Millions
Loss_Category
IMDb_Rating
Metascore
Match_Status
```

### Evaluation

| Criterion | Marks |
|---|---:|
| Correct calculation of `Loss_Category` | 2 |
| Clean CSV formatting with correct headers and no dirty artifacts | 2 |

---

# 📦 Submission Requirements

- Submit the **link to the Colab notebook** with all cells executed and output visible.
- The Colab notebook should be shared with access to **IIT Gandhinagar**.
- No changes will be entertained after the submission deadline.
- **Code Quality:** The code must be modular.
- The **Pydantic model must be defined as a distinct class**.
