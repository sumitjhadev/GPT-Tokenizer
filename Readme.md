# 🔤 BPE Tokenizer From Scratch

> Building a GPT-style Byte Pair Encoding (BPE) tokenizer from the ground up — no shortcuts, no black boxes.

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)](https://jupyter.org/)
[![tiktoken](https://img.shields.io/badge/tiktoken-OpenAI-412991?style=flat-square)](https://github.com/openai/tiktoken)
[![SentencePiece](https://img.shields.io/badge/SentencePiece-Google-4285F4?style=flat-square)](https://github.com/google/sentencepiece)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)

---

## 🧠 Overview

Tokenization is the unglamorous foundation that quietly explains most of an LLM's weirdest behaviors — spelling failures, arithmetic mistakes, non-English performance gaps, and cryptic `<|endoftext|>` glitches all trace back to how text gets chopped into tokens *before* the model ever sees it.

This project implements **Byte Pair Encoding (BPE)** — the algorithm behind GPT-2, GPT-4, and LLaMA's tokenizers — entirely from scratch in Python, then compares it against production-grade implementations (`tiktoken`, `sentencepiece`).

No pretrained tokenizer is used as a crutch. Every core piece — pair counting, merging, encoding, decoding, and regex-based pre-splitting — is built and understood line by line.

---

## ✨ What's Inside

| Component | Description |
|---|---|
| **Raw byte tokenization** | Converting UTF-8 text into a base vocabulary of 256 byte tokens |
| **Pair frequency counting** | Finding the most frequent adjacent token pair in a sequence |
| **Iterative merging** | Building a custom vocabulary by repeatedly merging the most common pair |
| **`encode()` / `decode()`** | Full round-trip text ↔ token conversion, verified for correctness |
| **GPT-2 regex pre-tokenizer** | Using GPT-2's exact regex pattern to force sensible split boundaries before BPE |
| **`tiktoken` comparison** | Benchmarking against OpenAI's production tokenizer (GPT-2 & `cl100k_base`) |
| **SentencePiece training** | Training a BPE model the way LLaMA 2 does — with byte-fallback and identity normalization |
| **Unicode deep dive** | Working with a real-world, multi-script Unicode test corpus (emoji, CJK, Hebrew, Arabic, Devanagari, Zalgo text, and more) |

---

## 📐 Core Algorithm

```
1. Encode text → raw UTF-8 bytes (256 possible token IDs)
2. Count all adjacent token pairs
3. Find the most frequent pair
4. Merge it into a new token ID
5. Repeat until the target vocabulary size is reached
```

This produces a **learned, data-driven vocabulary** — common substrings (like `" the"`, `"ing"`, `"tion"`) get merged into single tokens, giving a meaningful **compression ratio** over raw bytes.

```python
vocab_size = 276          # 256 base bytes + 20 learned merges
compression ratio ≈ 1.27x
```

---

## 🔍 Key Concepts Covered

- **Why tokenization breaks things** — spelling, string reversal, arithmetic, non-English languages, trailing whitespace warnings, and glitch tokens like `SolidGoldMagikarp`
- **UTF-8 vs UTF-16 vs UTF-32** encoding trade-offs
- **Byte Pair Encoding** as a compression + vocabulary-learning algorithm
- **Regex-forced splits** (GPT-2's pattern) to prevent nonsensical merges across categories like letters, digits, and punctuation
- **`tiktoken`**: how OpenAI's production tokenizers work, and why GPT-4's tokenizer merges leading spaces while GPT-2's doesn't
- **SentencePiece**: BPE over raw Unicode code points (instead of bytes), with `byte_fallback` for rare characters — the approach used by LLaMA and Mistral
- **Special tokens** (`<|endoftext|>`) and vocabulary construction (`encoder.json` + `vocab.bpe`)

---

## 🚀 Getting Started

### Prerequisites
```bash
pip install regex tiktoken sentencepiece
```

### Run it
Open the notebook in Jupyter, JupyterLab, or Google Colab:

```bash
jupyter notebook Tokenization.ipynb
```

All cells run top to bottom — no external dataset required beyond the embedded sample text (a public-domain excerpt on Unicode internals, used purely for representative token statistics).

---

## 📊 Sample Results

```
Length of text  : 23,328 characters
Length of bytes : 24,597 tokens (raw UTF-8)

After 20 BPE merges (vocab_size = 276):
Length of ids   : 19,438 tokens
Compression     : 1.27x
```

**Round-trip correctness:**
```python
>>> decode(encode(text)) == text
True
```

---

## 🗂️ Repository Structure

```
.
├── Tokenization.ipynb   # Main notebook — full BPE implementation & experiments
├── README.md            # You are here
└── requirements.txt     # Python dependencies
```

---

## 🎓 Inspiration & References

This project follows the spirit of Andrej Karpathy's [**"Let's build the GPT Tokenizer"**](https://github.com/karpathy/minbpe) walkthrough and the accompanying `minbpe` reference implementation.

Further reading:
- [A Programmer's Introduction to Unicode](https://www.reedbeta.com/blog/programmers-intro-to-unicode/) — Nathan Reed
- [tiktoken](https://github.com/openai/tiktoken) — OpenAI
- [SentencePiece](https://github.com/google/sentencepiece) — Google
- [minbpe](https://github.com/karpathy/minbpe) — Andrej Karpathy

---

## 🛣️ Roadmap

- [ ] Match `minbpe`'s efficiency with a heap-based merge implementation
- [ ] Add special-token handling (`<|endoftext|>`-style) to `encode()`/`decode()`
- [ ] Train a full-scale vocabulary (32K+) on a larger corpus
- [ ] Package as an installable CLI tokenizer

---

## 📜 License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

---

<p align="center">
  <i>Tokenization is at the heart of much weirdness in LLMs — don't brush it off. 🔤</i>
</p>
