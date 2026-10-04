# Fuzzing as a Generator for Stress-Testing PII Detection Models

## Abstract

Personal data leakage remains one of the most significant risks in large-scale conversational AI systems. Detecting personally identifiable information (PII) in user-generated text is a challenging task because the distribution of sensitive entities is highly variable, and models trained on naturally occurring corpora often fail when exposed to adversarial, obfuscated, or structurally perturbed inputs. In this work, we explore a fuzzing-inspired methodology for stress-testing PII detection systems by generating synthetic yet realistic privacy-sensitive variations of conversational data.

We present a pipeline that combines a privacy-attack style data generator with PII annotation and evaluation workflows to assess model robustness under controlled adversarial transformations. The system leverages conversational data sampled from WildChat, injects and obfuscates PII-like entities, and evaluates how well detection models recover the original information under realistic perturbations. We benchmark a rule-based baseline (Microsoft Presidio) against a transformer-based detection model and analyze the resulting robustness trade-offs. Our findings highlight that synthetic stress tests can reveal blind spots in standard PII detection pipelines and provide a principled framework for evaluating model resilience.

Keywords: PII detection, privacy, adversarial robustness, synthetic data generation, NLP, transformer models.

## 1. Introduction

The proliferation of large language models and real-world conversational systems has intensified the need for reliable detection and redaction of personally identifiable information. In production settings, models must not only recognize obvious names, emails, and phone numbers, but also remain robust to lexical variation, formatting changes, partially obfuscated strings, and adversarial transformations that may bypass conventional detectors.

This repository implements a research-oriented pipeline for stress-testing PII detection systems through fuzzing-inspired synthetic data generation. The central idea is simple: if a detector can reliably recover PII under diverse transformations, then it is more robust to realistic deployment conditions. Conversely, if performance degrades sharply under obfuscation or entity mutation, the model is likely vulnerable to privacy leakage in the wild.

We formulate the problem as a named-entity recognition (NER)-style task, where sensitive entities are labeled in BIO format and evaluated under controlled synthetic perturbations. The project contributes a reusable pipeline for:

- generating synthetic privacy-sensitive samples from conversational text;
- applying obfuscation and entity mutation strategies inspired by fuzzing;
- converting annotations into BIO labels for downstream training and evaluation;
- comparing baseline and transformer-based detector performance.

## 2. Motivation

Current state-of-the-art PII detection systems often perform well on clean, standard benchmark datasets but struggle when users introduce non-standard naming conventions, formatting noise, or adversarial variations. This is especially relevant in informal online conversations, where PII may appear in fragmented, stylized, or context-dependent forms.

A robust privacy pipeline must be evaluated not only on natural data, but also on transformed data that simulates realistic adversarial scenarios. This motivates the use of synthetic stress testing as a complement to standard evaluation datasets. In other words, the goal is not only to measure average detection accuracy, but to identify failure modes that emerge under variation and corruption.

## 3. Method Overview

The project implements a complete research workflow for privacy stress testing.

### 3.1 Data Source

We leverage conversational samples from WildChat as the starting point for realistic text generation. These conversations provide naturalistic settings for PII to appear in varied and context-rich ways.

### 3.2 Fuzzing-Inspired Generation

The core generator introduces controlled perturbations that simulate potential privacy-leakage scenarios. These include:

- insertion of PII-like entities;
- obfuscation and formatting variations;
- entity mutation that preserves semantic intent while modifying surface form;
- synthetic transformations that challenge conventional detector assumptions.

This process is implemented in the project’s core fuzzer module and is designed to create stress-test examples that remain realistic while exposing brittle detection behavior.

### 3.3 Labeling and Training Format

Generated examples are converted into token-level BIO labels, enabling standard sequence-labeling pipelines. This provides a consistent interface for both baseline detectors and transformer-based models.

### 3.4 Baseline and Model Evaluation

The repository includes:

- a baseline detector wrapper based on Microsoft Presidio;
- a transformer-based model trained for entity-level PII detection;
- evaluation utilities for measuring detection quality across synthetic and processed sets.

## 4. System Architecture

The repository is organized as follows:

```text
PII-Detection-Stress-Testing-using-Fuzzers-as-Generators/
├── data/
│   ├── raw/                     # Original WildChat-derived samples
│   ├── synthetic/              # Obfuscated / fuzzed synthetic data
│   └── processed/               # BIO-formatted training and evaluation sets
├── src/
│   ├── fuzzer.py               # Core generator for PII insertion and obfuscation
│   ├── detectors/
│   │   ├── base.py             # Abstract detector interface
│   │   ├── presidio.py         # Presidio-based baseline detector
│   │   └── transformer.py      # Transformer-based detector implementation
│   └── utils.py                # Data handling and BIO conversion utilities
├── notebooks/
│   ├── train_bert.ipynb        # Fine-tuning workflow for transformer models
│   └── evaluate.ipynb          # Analysis, evaluation, and plotting
├── models/                     # Model checkpoints and trained weights
├── requirements.txt            # Python dependencies
├── main.py                     # Entry point for the project workflow
├── eval_datafog.py             # Evaluation script for synthetic robustness testing
├── report_datafog_full.json    # Evaluation report artifact
├── gpt_span_metrics_per_sample_v3.csv
├── README.md
└── .gitignore
```

## 5. Experimental Setup

The project is designed to support end-to-end experimentation on privacy-stress evaluation. In practice, the workflow includes:

1. loading conversational data;
2. identifying candidate spans for privacy-sensitive entities;
3. injecting or transforming those entities via fuzzing logic;
4. generating token-level BIO annotations;
5. training or evaluating detection models;
6. comparing performance across clean and adversarial settings.

This setup explicitly emphasizes robustness analysis rather than only standard accuracy metrics.

## 6. Contributions

This project makes the following contributions:

- It introduces a fuzzing-inspired framework for stress-testing PII detection models.
- It demonstrates how synthetic data generation can expose model vulnerabilities under realistic obfuscation patterns.
- It integrates a practical pipeline from raw conversational data to annotated detection evaluations.
- It provides both a baseline detector and a transformer-based alternative, enabling comparative analysis.

## 7. Usage

To install dependencies:

```bash
pip install -r requirements.txt
```

Run the project workflow:

```bash
python main.py
```

For evaluation-oriented experiments, use the provided scripts in the repository root and notebooks for analysis and plotting.

## 8. Expected Research Significance

The project is positioned at the intersection of privacy-preserving NLP, adversarial robustness, and data-centric AI evaluation. Rather than treating PII detection as a static classification problem, the repository explores how synthetic stress testing can reveal hidden weaknesses in detection pipelines before deployment. This is critical for systems that operate on unstructured user content and must maintain high recall while preventing privacy leakage.

## 9. Outlook

Future directions include expanding the fuzzing generator to include additional entity categories, evaluating multilingual settings, and comparing multiple detector architectures under standardized robustness benchmarks. The broader goal is to establish reliability-oriented evaluation practices for privacy-sensitive NLP systems.

## 10. Summary

This repository presents a reproducible research-oriented pipeline for stress-testing PII detection systems using fuzzing-inspired synthetic data generation. It combines realistic conversational data, adversarial privacy transformations, and evaluation workflows to support robust and interpretable analysis of privacy-preserving NLP models.

---

This README is intended to reflect the framing of a research paper and to communicate the motivation, method, and contribution of the project in a concise and publication-style format.
