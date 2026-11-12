# Byte-Pair Encoding (BPE) Tokenizer — From Scratch in Python

A zero-dependency, byte-level **Byte-Pair Encoding (BPE)** tokenizer implemented in pure Python.

---

## Why I Built This

While building **ForkContext**—an AI context-continuity platform that manages persistent project memory across ChatGPT, Claude, and Gemini—controlling token budgets, context window limits, and cross-model text chunking became critical.

Rather than treating tokenization as a black box, I built a byte-level BPE tokenizer from scratch in Python to deeply understand:
1. How raw UTF-8 text is compressed into subword token IDs.
2. How multi-byte Unicode characters (emojis, accented characters, non-Latin scripts) behave when split across token boundaries.
3. Why tokenization edge cases—like rank-order merging, prefix-overlapping special tokens, and cross-whitespace boundaries—directly affect LLM prompt compression and accuracy.

---

## What is Byte-Pair Encoding (BPE)?

BPE is the subword compression algorithm behind modern Large Language Models.
- **Word-level tokenizers** suffer from massive vocabularies and `<unk>` (unknown token) failures on typos, code identifiers, or new words.
- **Character-level tokenizers** keep vocabularies tiny, but turn text into overly long sequences that waste context window capacity.

**Byte-level BPE** solves both:
1. It starts with a fixed **256-token base vocabulary** representing every raw UTF-8 byte (`0–255`).
2. During **training**, it iteratively counts adjacent token pairs and merges the most frequent pair into a new token ID (`256`, `257`, ...).
3. Common words collapse into single tokens, while rare words, code syntax, and emojis break down cleanly into reusable subwords with **zero `<unk>` tokens**.

---

## Real End-to-End Walkthrough Example

Suppose we train our tokenizer on `"abababcabc"` with `max_merges=4`[cite: 6]:

### Step 1: UTF-8 Byte Initialization
The text converts into UTF-8 byte IDs (`'a' -> 97`, `'b' -> 98`, `'c' -> 99`)[cite: 6]:
```text
Text:   a   b   a   b   a   b   c   a   b   c
Tokens: 97, 98, 97, 98, 97, 98, 99, 97, 98, 99
