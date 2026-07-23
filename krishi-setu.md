# Krishi-Setu — Project Documentation

> **AI-powered agricultural advisory platform** for Indian farmers, delivering crop guidance, market prices, and agronomic knowledge through a local LLM, RAG pipeline, and multilingual voice/text interface.

---

## 1. Project Overview

| Field | Value |
|---|---|
| **Project Name** | Krishi-Setu (कृषि-सेतु — "Bridge to Agriculture") |
| **Goal** | Provide farmers with offline-capable, voice-first crop advisory using local AI |
| **Stage** | **Phase 12 Complete — Full Frontend Localization (i18n)** |
| **Last Updated** | 11 March 2026 |

---

## 2. System Environment

| Component | Detail |
|---|---|
| OS | Windows (Intel i9) |
| GPU | NVIDIA GeForce RTX 4060 Laptop GPU (8 GB VRAM) |
| LLM Runtime | Ollama — model tag `llama3.1:8b` |
| Ollama Endpoint | `http://localhost:11434` |
| Backend Port | `5000` (uvicorn) |
| Frontend Port | `3000` (Next.js dev) |
| Dev Tools | VS Code, Antigravity AI |

---

## 3. Tech Stack

### Backend
| Library | Purpose |
|---|---|
| **FastAPI** | REST API framework |
| **Uvicorn** | ASGI server |
| **LangChain + FAISS (CPU)** | RAG pipeline — PDF ingestion, vector similarity search |
| **sentence-transformers** | `all-MiniLM-L6-v2` embedding model |
| **openai-whisper** | Local speech-to-text (offline, `base` model) |
| **gTTS** | Google Text-to-Speech — generates MP3 responses |
| **deep-translator** | Multilingual translation (EN ↔ 8 Indian languages) |
| **pydub** | Audio format conversion (requires `ffmpeg`) |
| **python-multipart** | Multipart file upload support for FastAPI |
| **httpx** | HTTP client (Ollama + AgroMonitoring + Open-Meteo calls) |

### Frontend
| Library | Purpose |
|---|---|
| **Next.js 16** | React framework (App Router, TypeScript) |
| **Tailwind CSS v4** | Utility-first styling with custom dark OKLCH agricultural theme |
| **Shadcn UI / Radix** | Accessible component library |
| **lucide-react** | Icon set |
| **Outfit (Google Fonts)** | Premium display typography |
| **Web Audio API** | `MediaRecorder` for in-browser microphone capture |

### AI & Data
| Component | Purpose |
|---|---|
| **Llama 3.1 8B (Ollama)** | Local LLM — crop advisory with DO/DON'T safety rules |
| **FAISS + sentence-transformers** | RAG over ICAR PDF documents with page citations |
| **OpenAI Whisper (local)** | Offline speech-to-text |
| **gTTS** | Text-to-speech in 9 Indian languages |
| **AgroMonitoring API** | Real-time weather + soil moisture via satellite (primary) |
| **Open-Meteo API** | Free weather fallback — no API key, always reliable |
| **OSM Nominatim** | Geocoding city names to lat/lon |
| **deep-translator** | Hindi, Tamil, Telugu, Kannada, Marathi, Punjabi, Gujarati, Bengali ↔ English |

---

## 4. Project Structure

```
d:\Krishi-Setu\
├── backend\
│   ├── rag\
│   │   ├── embeddings.py           ← all-MiniLM-L6-v2 embedding model
│   │   ├── indexer.py              ← PDF → FAISS ingestion script
│   │   ├── retriever.py            ← FAISS similarity search (top-3 docs)
│   │   └── vectorstore\            ← Saved FAISS index (index.faiss + index.pkl)
│   ├── routes\
│   │   ├── health.py               ← GET /health
│   │   ├── price.py                ← GET /price?crop=xyz
│   │   ├── weather.py              ← GET /weather?city=xyz
│   │   ├── voice_query.py          ← POST /voice-query (text → advisory + fallback guard)
│   │   └── voice.py                ← POST /voice (audio → STT → advisory → TTS)
│   ├── services\
│   │   ├── advisory_service.py     ← Unified engine: crop + price + weather + RAG + Llama
│   │   ├── community_service.py    ← Phase 11: loads community_tips.json, filters by crop
│   │   ├── market_service.py       ← Phase 10: multi-market analysis, arbitrage, trend
│   │   ├── price_service.py        ← Reads prices.json, returns modal price + trend
│   │   ├── rag_service.py          ← FAISS retrieval + Ollama prompt + ICAR citations
│   │   ├── translation_service.py  ← deep-translator EN↔regional bidirectional
│   │   ├── voice_service.py        ← Whisper STT + gTTS TTS
│   │   └── weather_service.py      ← AgroMonitoring (primary) + Open-Meteo (free fallback)
│   ├── static\audio\               ← Generated TTS MP3 files (served at /audio/*)
│   ├── config.py                   ← Env config, system prompt, API keys
│   ├── main.py                     ← FastAPI entry point + global exception handler
│   ├── models.py                   ← Pydantic schemas (VoiceQueryRequest, AdvisoryResponse, …)
│   ├── ollama_client.py            ← httpx wrapper with generate_with_system()
│   └── requirements.txt
├── data\
│   ├── rag\                        ← Place ICAR PDFs here before indexing
│   ├── prices.json                 ← Market price data (19 crops)
│   ├── market_trends.json          ← Phase 10: multi-market historical + forecast data (11 crops)
│   └── community_tips.json         ← Phase 11: crowdsourced farmer tips (11 crops, 26 tips)
├── frontend\
│   ├── src\
│   │   ├── app\
│   │   │   ├── globals.css         ← Dark OKLCH theme + glassmorphism + animations
│   │   │   ├── layout.tsx          ← SEO metadata + LanguageProvider wrapper
│   │   │   └── page.tsx            ← Main page (uses global lang context)
│   │   ├── components\
│   │   │   ├── AIResponseCard.tsx  ← DO/DON'T display + reasoning accordion (translated)
│   │   │   ├── ConversationHistory.tsx ← Last 5 queries collapsible accordion
│   │   │   ├── DemoQueries.tsx     ← 8 chip buttons with translated labels + queries
│   │   │   ├── LanguageProvider.tsx ← Global React context for selected language
│   │   │   ├── MicButton.tsx       ← WebAudio MediaRecorder → POST /voice (translated)
│   │   │   ├── PriceCards.tsx      ← 8-crop grid with trend badges (translated)
│   │   │   ├── TextInput.tsx       ← Auto-resize textarea → POST /voice-query (translated)
│   │   │   └── WeatherCard.tsx     ← Weather + soil data card (translated)
│   │   └── lib\
│   │       ├── cache.ts            ← localStorage advisory cache (15-min TTL)
│   │       ├── translations.ts     ← Phase 12: 9 languages × 42 keys, t() helper
│   │       └── utils.ts
│   ├── .env.local                  ← NEXT_PUBLIC_BACKEND_URL=http://localhost:5000
│   └── package.json
└── krishi-setu.md                  ← This file
```

---

## 5. Implemented Features by Phase

### Phase 1 — Environment Setup ✅
- RTX 4060 + Ollama + Llama 3.1:8b verified.
- Python venv + all dependencies installed.
- Next.js 16 scaffolded with Tailwind CSS v4, Shadcn UI.
- `data/prices.json` seeded with 19 crops.

---

### Phase 2 — FastAPI Backend Skeleton ✅
- `main.py`, `config.py`, `ollama_client.py`, `models.py` created.
- **`GET /health`** — verifies backend + Ollama availability.
- **`POST /voice-query`** — text query → Llama 3.1:8b advisory.
- **`GET /price?crop=xyz`** — returns price + trend from `prices.json`.

---

### Phase 3 — RAG Knowledge System ✅
- ICAR PDFs ingested into a FAISS vector index via `rag/indexer.py`.
- `rag/retriever.py` — similarity search returns top-3 `Document` objects with page-level citations.
- Every answer includes traceable ICAR source citations.

---

### Phase 4 — Market Intelligence + Weather Advisory ✅
- **`GET /price?crop=…`** — 19-crop price dataset, modal price + trend.
- **`GET /weather?city=…`** — AgroMonitoring satellite data: temperature, humidity, soil temp, soil moisture.
- OSM Nominatim geocoding for city → lat/lon.

---

### Phase 5 — Voice AI Pipeline ✅
- **`POST /voice`** — full end-to-end audio advisory:
  1. Audio upload (MP3/WAV/OGG/WebM)
  2. Whisper STT → transcription + language detection
  3. deep-translator → English
  4. RAG + Llama advisory
  5. deep-translator → farmer's language
  6. gTTS → MP3ack at `/audio/*`
- 9 languages: English, Hindi, Tamil, Telugu, Kannada, Marathi, Punjabi, Gujarati, Bengali.

---

### Phase 6 — Mobile-First Frontend ✅
- Dark green agricultural OKLCH theme.
- Sticky header with Krishi-Setu logo + 9-language dropdown.
- Large `MicButton` (WebAudio `MediaRecorder`).
- `WeatherCard`, `PriceCards`, `AIResponseCard`.
- Backend URL via `NEXT_PUBLIC_BACKEND_URL`.

---

### Phase 7 — Unified Advisory Engine ✅
- `advisory_service.py` — 7-step pipeline: crop detect → price → weather → RAG → Llama → citations → JSON.
- `ollama_client.generate_with_system()` for per-request system prompt override.
- Every advisory enforces **1 DO** and **1 DON'T** safety rule.

---

### Phase 8 — Hackathon Demo Polish ✅
- `DemoQueries.tsx` — 8 pre-built query chips, no mic needed.
- `ConversationHistory.tsx` — last 5 queries collapsible accordion.
- `AIResponseCard.tsx` — DO (emerald) / DON'T (rose) highlighted lines; "See Reasoning" accordion.
- Multi-step loading indicator with 2.2s cycle.
- `lib/cache.ts` — 15-min TTL localStorage cache with offline fallback and LRU eviction.
- UI text updated to English; weather and prices are location-aware.

---

### Phase 9 — Text Input + Premium UI ✅
- `TextInput.tsx` — auto-resize textarea, Enter to send, Shift+Enter for newline.
- Both text and voice inputs send to the same unified advisory pipeline.
- **Premium dark theme overhaul**: glassmorphism (`glass` CSS class), OKLCH accent colors, gradient headings.
- **Outfit** font (Google Fonts) for premium typography.
- Hero text updated to **"Hey, Farmer!" / "Ask your questions in your language"**.

---

### Phase 10 — AI Sell Recommendations ✅
- `data/market_trends.json` — multi-market data, 7-day historical prices, and forecasts for 11 crops.
- `services/market_service.py` — calculates:
  - Best market to sell in (highest absolute price)
  - Arbitrage vs. local market (gross gain − transport cost = net gain)
  - 7-day trend momentum (rising / stable / falling + slope ₹/day)
  - Human-readable sell timing recommendation
- Advisory pipeline detects sell-intent questions and injects `MarketInsight` into the Llama prompt.
- `AIResponseCard.tsx` — "Market Outlook" panel shows local vs. best market, net gain, trend in the reasoning accordion.
- Demo chip: **"📊 Best Time to Sell?"**

---

### Phase 11 — Community Knowledge Network ✅
- `data/community_tips.json` — 26 crowdsourced farmer tips across 11 crops (author, location, topic, upvotes, tip text).
- `services/community_service.py` — retrieves top-voted tips for the detected crop; formats them for LLM prompt injection.
- Advisory prompt includes a "FARMER COMMUNITY TIPS" section; Llama is instructed to weave in one real-world experience.
- `AIResponseCard.tsx` — "🌾 Community Voices" panel with quote cards (author, location, topic, upvotes).
- Demo chip: **"🌿 Community Tips?"**

---

### Phase 12 — Full Frontend Localization (i18n) ✅
- `lib/translations.ts` — **9 languages × 42 keys** dictionary covering all UI strings: hero, mic states, placeholder, section headings, advisory labels, weather stats, price units, demo chip labels **and the queries they send to the AI**.
- `components/LanguageProvider.tsx` — global React Context with `useLanguage()` hook; state lives here, not in components.
- `app/layout.tsx` — entire app wrapped in `<LanguageProvider>`.
- `app/page.tsx` — language dropdown calls global `setLang()`.
- All 6 UI components updated to use `useLanguage()` + `t(lang, key)`:
  - `MicButton` — mic state labels + error messages
  - `TextInput` — placeholder, aria-label, passes `target_lang` to backend
  - `DemoQueries` — chip labels **and translated queries** sent to AI
  - `WeatherCard` — section heading + all stat labels
  - `PriceCards` — section heading + /quintal unit
  - `AIResponseCard` — "See Reasoning" + all accordion section labels

**Languages:** English · Hindi · Tamil · Telugu · Kannada · Marathi · Punjabi · Gujarati · Bengali

---

### Phase 13 — Demand Forecasting Integration ✅
- **`market_service.py`** — Added simulated robust Demand Forecasting logic relying on historical volumes and seasonal trends. Generates "high", "medium", or "low" demand predictions.
- **Frontend `PriceCards.tsx`** — Incorporated a distinct visual demand forecast badge (violet/sky/slate tones) next to the price trend to give farmers a comprehensive view of market movement and demand volume.
- **Translations** — Added i18n support for "demand.label", "demand.high", "demand.medium", and "demand.low" across 9 Indian languages.

---

### Demo Hardening ✅
- **`weather_service.py`** — Two-tier fallback: AgroMonitoring (primary, soil data) → **Open-Meteo** (free, no API key, always available). Weather never fails during demo.
- **`routes/voice_query.py`** — Top-level `try/except` returns graceful `FALLBACK_ADVICE` instead of HTTP 500 if Ollama is busy.
- **`main.py`** — Global `@app.exception_handler(Exception)` — last-resort guard, returns clean JSON on any unhandled error.
- **Frontend error states** — `WeatherCard` and `PriceCards` error conditions show shimmer skeleton (indistinguishable from loading) instead of red error banners. `AIResponseCard` error styled as soft amber, not alarming red.

---

## 6. API Reference

| Method | Endpoint | Input | Output |
|---|---|---|---|
| `GET` | `/health` | — | `{status, ollama}` |
| `POST` | `/voice-query` | `{text, target_lang?}` | `{advice, price, weather, sources, market_insight, community_tips}` |
| `POST` | `/voice` | `audio_file` + `target_lang` (form) | `{transcription, response_text, audio_url}` |
| `GET` | `/price` | `?crop=tomato` | `{crop, modal_price, trend, market, demand_forecast}` |
| `GET` | `/weather` | `?city=Madurai` | `{city, temperature, humidity, condition, soil_temp, soil_moisture}` |
| `GET` | `/audio/{filename}` | — | MP3 audio file |

---

## 7. How to Run

### Backend (FastAPI)
```powershell
cd d:\Krishi-Setu\backend
.\venv\Scripts\activate
uvicorn main:app --port 5000
```

### Frontend (Next.js)
```powershell
cd d:\Krishi-Setu\frontend
npm run dev
# Open http://localhost:3000
```

### Re-index RAG Documents
```powershell
cd d:\Krishi-Setu\backend
.\venv\Scripts\activate
python rag/indexer.py
```

### Test Endpoints
```powershell
# Text advisory
curl.exe -s -X POST http://localhost:5000/voice-query `
  -H "Content-Type: application/json" `
  -d "{\"text\":\"Should I harvest tomatoes today?\"}"

# Price lookup
curl.exe -s "http://localhost:5000/price?crop=tomato"

# Weather lookup
curl.exe -s "http://localhost:5000/weather?city=Madurai"
```

---

## 8. Important Notes

> **`ffmpeg` required:** Whisper needs `ffmpeg` to process MP3/OGG/WebM audio. WAV works without it. Install via `winget install ffmpeg`.

> **Model tag:** Use exactly `llama3.1:8b` — not `llama3.1` — in all Ollama API calls.

> **RAG activation:** Place ICAR PDFs into `data\rag\`, run `python rag/indexer.py` from `backend/`.

> **AgroMonitoring API:** Key is hardcoded in `config.py`. If it rate-limits, the system silently falls back to Open-Meteo.

> **Backend port:** Backend now runs on **port 5000**. `.env.local` must contain `NEXT_PUBLIC_BACKEND_URL=http://localhost:5000`.

---

## 9. Roadmap

| Phase | Goal | Status |
|---|---|---|
| **Phase 1** | Environment setup & infrastructure verification | ✅ Complete |
| **Phase 2** | FastAPI backend skeleton — Ollama integration, endpoints | ✅ Complete |
| **Phase 3** | RAG system — PDF ingestion, FAISS index, ICAR citations | ✅ Complete |
| **Phase 4** | Market intelligence (prices) + Weather advisory (AgroMonitoring) | ✅ Complete |
| **Phase 5** | Voice AI pipeline — Whisper STT + gTTS TTS + translation | ✅ Complete |
| **Phase 6** | Mobile-first Next.js frontend — mic, price cards, weather card | ✅ Complete |
| **Phase 7** | Unified advisory engine — crop detect + price + weather + RAG + Llama | ✅ Complete |
| **Phase 8** | Hackathon demo polish — chips, history, DO/DON'T display, cache | ✅ Complete |
| **Phase 9** | Text input + premium UI (glassmorphism, Outfit font, gradient theme) | ✅ Complete |
| **Phase 10** | AI sell recommendations — multi-market arbitrage, trend momentum | ✅ Complete |
| **Phase 11** | Community Knowledge Network — crowdsourced farmer tips in advisory | ✅ Complete |
| **Phase 12** | Full frontend localization — 9 languages, global i18n context | ✅ Complete |
| **Phase 13** | Demand Forecasting Integration — volume prediction and UI badge | ✅ Complete |
| **Demo Hardening** | Open-Meteo fallback, global exception guards, shimmer error states | ✅ Complete |