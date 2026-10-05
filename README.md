# PromptShield 🛡️

> **Companies choosing between AI productivity and data protection — PromptShield ends that choice.**

PromptShield is a real-time, LLM-agnostic privacy firewall that intercepts user prompts before they reach any LLM, detects and classifies sensitive data across 15+ entity categories via a 5-layer deterministic pipeline, applies configurable privacy policies, and computes an explainable 0–100 risk score — all with sub-500ms firewall overhead. Built with India's DPDP Act 2023 compliance in mind.

## Quickstart

```bash
# 1. Backend
cd backend
python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python -m spacy download en_core_web_sm
cp .env.example .env  # fill in GROQ_API_KEY
uvicorn app.main:app --reload --port 8000

# 2. Frontend
cd frontend
npm install
npm run dev  # → http://localhost:5173
```

## Architecture

```
┌────────────────────────────────────────────────────────────────────┐
│                        PromptShield UI                             │
│   Inspect · Chat · Policies · Audit                               │
└──────────────────────────┬─────────────────────────────────────────┘
                           │ REST / WebSocket
┌──────────────────────────▼─────────────────────────────────────────┐
│                     FastAPI Backend (:8000)                         │
│                                                                    │
│  ┌─── Detection Pipeline (5 Layers) ──────────────────────────┐   │
│  │ L1 Presidio (spaCy NER + built-in recognizers)              │   │
│  │ L2 Custom recognizers (Aadhaar/PAN/GSTIN/UPI/IFSC/creds)   │   │
│  │ L3 Semantic classifier (TF-IDF + LogReg, p_sensitive)       │   │
│  │ L4 Risk engine (weighted scoring, 0–100, 4 bands)           │   │
│  │ L5 Response scanner (leak detection + de-tokenization)      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                    │
│  ┌─── Sanitization ───┐  ┌─── Policy ────┐  ┌─── Token Vault ──┐ │
│  │ ALLOW/MASK/REDACT/  │  │ YAML-driven   │  │ In-memory,       │ │
│  │ ANONYMIZE/TOKENIZE/ │  │ Hot-reloadable│  │ session-scoped,  │ │
│  │ BLOCK               │  │ 4 presets     │  │ TTL-expiring     │ │
│  └─────────────────────┘  └───────────────┘  └──────────────────┘ │
│                                                                    │
│  ┌─── LLM Providers ──────────────────────────────────────────┐   │
│  │ Groq (llama-3.3-70b-versatile) │ Ollama (llama3.2:3b)     │   │
│  │ Non-streaming by design — full response scanned before     │   │
│  │ display (Layer 5)                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                    │
│  ┌─── Audit ──────────┐  ┌─── Metrics ───────────────────────┐   │
│  │ SQLite (SQLModel)   │  │ In-memory ring buffer             │   │
│  │ Every action logged │  │ p50/p95 latency, entity histogram │   │
│  └─────────────────────┘  └───────────────────────────────────┘   │
└────────────────────────────────────────────────────────────────────┘
```

## Features

- **15+ Entity Types**: PERSON, EMAIL, PHONE, CREDIT_CARD, IN_AADHAAR, IN_PAN, IN_UPI, IN_GSTIN, IN_IFSC, IN_VOTER_ID, CREDENTIAL, PASSWORD, ORGANIZATION, LOCATION, DATE_TIME, URL
- **Verhoeff & Luhn Validation**: Zero false positives on checksummed IDs — order numbers don't flag
- **Semantic Sensitivity**: ML classifier catches M&A rumors, security incidents, unreleased products
- **5 Sanitization Modes**: ALLOW, MASK, REDACT, ANONYMIZE (format-valid fakes), TOKENIZE (reversible)
- **Explainable Risk Score**: 0–100 with narrated reasons and 4 bands (SAFE/SUSPICIOUS/HIGH/CRITICAL)
- **Auto-Block**: CRITICAL-risk prompts never reach the LLM
- **Response Scanning**: Catches LLM data leakage and re-identification attempts
- **Policy Presets**: Healthcare, Finance, Legal, Developer — hot-reloadable
- **Audit Trail**: Every action logged with full latency breakdown (DPDP Act 2023)
- **Sub-500ms Firewall Overhead**: LLM latency tracked separately

## Benchmark

See [benchmark/RESULTS.md](benchmark/RESULTS.md) for per-recognizer precision/recall/F1 and latency numbers.

## Model Card

| Model | Size | License | Purpose | Typical Latency |
|-------|------|---------|---------|-----------------|
| spaCy en_core_web_sm | 12 MB | MIT | NER (Layer 1) | ~15ms |
| TF-IDF + CalibratedLR | <1 MB | Custom | Semantic sensitivity (Layer 3) | <15ms |
| llama-3.3-70b-versatile (Groq) | 70B | Llama 3.3 Community | LLM responses | 1–5s |
| llama3.2:3b (Ollama) | 3B | Llama 3.2 Community | Local LLM fallback | 2–10s |

## Roadmap

- [ ] Redis-backed token vault (multi-worker support)
- [ ] PostgreSQL audit store (production-grade)
- [ ] OCR pipeline (scan documents before LLM)
- [ ] OpenAI-compatible `/v1/chat/completions` drop-in proxy endpoint
- [ ] Docker Compose deployment

## License

MIT
