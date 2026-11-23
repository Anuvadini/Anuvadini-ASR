# Anuvadini ASR

Streaming-first automatic speech recognition with **one dedicated model per language** — the 22 scheduled Indian languages (Assamese, Bengali, Bodo, Dogri, Gujarati, Hindi, Kannada, Kashmiri, Konkani, Maithili, Malayalam, Manipuri, Marathi, Nepali, Odia, Punjabi, Sanskrit, Santali, Sindhi, Tamil, Telugu, Urdu) and Indian-accented English — plus a spoken language-identification model.

## Architecture (each language model)
- Input: 16 kHz mono, 80-dimensional log-mel features (25 ms window, 10 ms hop); 8 kHz telephony audio up-sampled
- Encoder: 8× convolutional subsampling (80 ms frames) → **17 Conformer blocks**, width 512, 8 attention heads, macaron feed-forward 2,048, depthwise convolution kernel 31
- Decoders: hybrid **RNN-Transducer** (LSTM prediction network 640, joint network 640) with an auxiliary **CTC** head
- Vocabulary: 1,024 subword units per language, including English subwords for native code-mixing

| Component | Parameters |
|---|---|
| Convolutional subsampling | 2,493,952 |
| Conformer encoder (17 × 512) | 103,029,248 |
| RNN-T prediction network | 3,937,920 |
| RNN-T joint network | 1,395,585 |
| CTC head | 525,825 |
| **Total per language** | **111,382,530** |
| All 23 language models | 2,561,798,190 |
| Spoken language identification (8 × 256 Conformer, 23 classes) | 12,945,432 |

## Streaming
Cache-aware chunked attention — the same weights serve real-time streaming and full-context batch transcription.
| Mode | Chunk | First-token latency target |
|---|---|---|
| Standard streaming | 160 ms | ≤ 220 ms |
| Low-latency | 80 ms | ≤ 150 ms |
| Batch (offline) | Full context | — |

## Features
Automatic language identification and code-switching; named-entity, number and date normalisation; domain-term biasing from glossaries; punctuation, casing and inverse text normalisation; word-level timestamps and confidence; 8 kHz telephony robustness.

## Deployment
| Item | Value |
|---|---|
| Size | 222.8 MB per language model (FP16); 5.12 GB for all 23 |
| GPU | All 23 models resident on a single GPU |
| Capacity design | 30,00,00,000 minutes of audio per year = ≥ 570.8× real time sustained on one GPU |
| Interfaces | REST (batch) and WebSocket (streaming); Docker / OCI container; air-gapped |


---
© Anuvadini Foundation. All rights reserved. Documentation only.
