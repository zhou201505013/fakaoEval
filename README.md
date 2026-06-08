# fakaoEval: A Legal Reasoning Benchmark with SOLO Taxonomy Annotation

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Paper](https://img.shields.io/badge/Paper-IPM-blue)](#citation)

**fakaoEval** is a benchmark dataset for evaluating large language models (LLMs) in legal reasoning. It is built from **China's Unified Qualification Exam for Legal Professions** (国家统一法律职业资格考试, colloquially known as *fakao* 法考) and related coaching materials. The dataset contains **4,308 question-answer pairs** annotated with the **SOLO taxonomy** (Structure of Observed Learning Outcome) to characterize legal knowledge, reasoning, and abstraction at multiple cognitive levels.

<!-- > 📄 **Associated paper**: *"fakaoEval: Evaluate large language models in legal reasoning using the structure of observed learning outcome taxonomy"* — accepted by **Information Processing & Management**. -->

---

## Table of Contents

- [Overview](#overview)
- [Data Format](#data-format)
- [SOLO Taxonomy Annotation](#solo-taxonomy-annotation)
- [Statistics](#statistics)
  
---

## Overview

Evaluating LLMs in legal reasoning remains challenging because existing benchmarks are often task-specific, artificially curated, or limited to surface-level score comparisons. **fakaoEval** addresses these issues by:

- **Real-world source**: Built from China's official legal professional examination, the recognized standard for assessing legal capacity in China.
- **Dual format**: Contains both **subjective** (essay) and **objective** (multiple-choice) questions, covering a wide range of legal knowledge areas including civil law, criminal law, commercial law, etc.
- **Cognitive-level annotation**: A subset of 884 questions is annotated with SOLO taxonomy levels (Unistructural to Extended Abstract) by legal professionals, enabling fine-grained evaluation of LLMs' knowledge, reasoning, and abstraction abilities.

### Dataset Components

| Component            | Source                                     | Count | Description                                         |
| -------------------- | ------------------------------------------ | ----- | --------------------------------------------------- |
| **Subjective**       | 2007–2017 fakao exams + coaching materials | 2,203 | Essay-type questions (case text + question, merged) |
| **Objective**        | JEC-QA (10% sample of training set)        | 2,105 | Multiple-choice questions (stem + options, merged)  |
| **fakaoEval**        | Combined                                   | 4,308 | Complete benchmark                                  |
| **Annotated subset** | Human-annotated                            | 884   | SOLO-level labels for evaluation                    |

---

## Data Format

### Main Dataset (`dataset.json`)

All entries share the same unified structure:

```json
{
  "id": 1,
  "type": "subjective",
  "question": "案情：李某与张某签订了...\n请分析本案中李某与张某之间的房屋买卖合同是否有效？",
  "answer": "根据《中华人民共和国民法典》第143条..."
}
```

| Field      | Type           | Description                                                                                                               |
| ---------- | -------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `id`       | int            | Unique identifier (1–4308, randomly shuffled)                                                                             |
| `type`     | string         | `"subjective"` (essay) or `"objective"` (multiple-choice)                                                                 |
| `question` | string         | Full question text. For subjective: case material + question stem. For objective: stem + formatted options (A./B./C./D.). |
| `answer`   | string / array | Reference answer. Subjective: text string. Objective: array of correct option letters, e.g. `["C", "D"]`.                 |

#### Subjective Example

```json
{
  "id": 1,
  "type": "subjective",
  "question": "2018年10月21日，甲向乙借款50万元，以自己价值30万元的C家用轿车设立抵押...\n丁的质权是否设立?为什么?法律依据为何?",
  "answer": "设立。因为动产质权善意取得的构成要件有三..."
}
```

#### Objective Example

```json
{
  "id": 1845,
  "type": "objective",
  "question": "根据我国民事诉讼法及司法解释的规定，下列表述哪些是正确的?\nA. 陈洪基于与陈涛之间的养父子关系...\nB. 丙公司与丁公司合同纠纷一案...\nC. 赵某与孙某合伙协议纠纷一案...\nD. 甲公司与乙公司货款纠纷一案...",
  "answer": ["C", "D"]
}
```

### Annotated Subset (`annotated.json`)

The 884 human-annotated questions include an additional `level` field:

```json
{
  "id": 2646,
  "type": "subjective",
  "question": "材料一：平等是社会主义法律的基本属性...\n根据以上材料...谈谈法律面前人人平等的原则对于推进严格司法的意义。",
  "answer": "（一）坚持依法治国首先要坚持依宪治国...",
  "level": "Relational"
}
```

| Field      | Type           | Description                                      |
| ---------- | -------------- | ------------------------------------------------ |
| `id`       | int            | Matches the corresponding `id` in `dataset.json` |
| `type`     | string         | `"subjective"` or `"objective"`                  |
| `question` | string         | Full question text                               |
| `answer`   | string / array | Reference answer                                 |
| `level`    | string         | SOLO taxonomy level name                         |

---

## SOLO Taxonomy Annotation

### SOLO Levels

| Level             | int | Description                                                               |
| ----------------- | --- | ------------------------------------------------------------------------- |
| Prestructural     | 1   | The task is not attacked appropriately; no understanding is demonstrated. |
| Unistructural     | 2   | One relevant aspect is addressed; understanding is nominal.               |
| Multistructural   | 3   | Several independent aspects are addressed but not interrelated.           |
| Relational        | 4   | Relevant aspects are integrated into a coherent structure.                |
| Extended Abstract | 5   | The coherent whole is generalized to a higher level of abstraction.       |

> **Note**: Prestructural (level 1) does not appear in the annotated subset, as all questions in fakaoEval require at least minimal legal understanding.

---

## Statistics

|                         | Subjective | Objective | fakaoEval |
| ----------------------- | ---------- | --------- | --------- |
| **# questions**         | 2,203      | 2,105     | **4,308** |
| **Avg. length (chars)** | 1,085.8    | 81.6      | 595.1     |

**SOLO annotation distribution (annotated subset, 884 questions):**

| SOLO Level            | Subjective | Objective | Total   |
| --------------------- | ---------- | --------- | ------- |
| Prestructural (1)     | 0          | 0         | 0       |
| Unistructural (2)     | 39         | 106       | 145     |
| Multistructural (3)   | 66         | 283       | 349     |
| Relational (4)        | 279        | 68        | 347     |
| Extended Abstract (5) | 42         | 1         | 43      |
| **Annotated total**   | **426**    | **458**   | **884** |
| **Avg. SOLO level**   | 3.76       | 2.92      | 3.33    |

> 884 out of 4,308 questions (~20.5%) are annotated with SOLO levels by legal professionals. The remaining questions can be evaluated using the LLM-as-a-Judge pipeline described in the paper.

### File Structure

```bash
fakaoEval/
├── dataset.json           # 4,308 question-answer pairs (full benchmark)
├── annotated.json         # 884 human-annotated samples with SOLO levels
└── README.md
```

<!-- ## Citation

If you use fakaoEval in your research, please cite the following paper:

```bibtex
@article{zhou2025fakaoeval,
  title     = {fakaoEval: Evaluate Large Language Models in Legal Reasoning
               Using the Structure of Observed Learning Outcome Taxonomy},
  author    = {Zhou, Zhiwei and Li, Xianneng},
  journal   = {Information Processing \& Management},
  year      = {2025}
}
``` -->
