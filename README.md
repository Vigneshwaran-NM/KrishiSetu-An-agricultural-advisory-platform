# Krishi-Setu — An Agricultural Advisory Platform

<div align="center">

**An AI-powered farming assistant that speaks your language.**

*Voice Queries • Market Intelligence • Weather Insights • Community Knowledge • Multilingual*

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Next.js](https://img.shields.io/badge/Next.js-15-black?logo=next.js&logoColor=white)](https://nextjs.org)
[![Ollama](https://img.shields.io/badge/Ollama-Llama_3.1_8B-FF6B35)](https://ollama.com)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

</div>

---

## Overview

**Krishi-Setu** (meaning *Agriculture Bridge* in Sanskrit) is a full-stack AI advisory platform designed specifically for Indian farmers. It bridges the gap between farmers and modern agricultural knowledge using:

- **Voice Interaction** — Farmers speak in their regional language; the app transcribes, answers, and reads the response back aloud
- **Llama 3.1 8B (via Ollama)** — Local LLM generates expert crop advisory with DO/DON'T guidance
- **RAG Pipeline** — FAISS vector store indexes ICAR knowledge documents for context-aware, citation-backed answers
- **Market Intelligence** — Real-time price lookup, multi-market arbitrage analysis, and AI-powered demand forecasting
- **Weather Integration** — Live weather and soil data via AgroMonitoring API + OpenStreetMap geocoding
- **Community Knowledge Network** — Shared farmer tips surfaced via semantic search
- **9-Language Support** — English, Hindi, Tamil, Telugu, Kannada, Marathi, Punjabi, Gujarati, Bengali

---

## Architecture

```
Farmer (Browser / Mobile)
    |
Next.js 15 Frontend — Voice UI, Market Cards, Weather Card, Demo Queries
    |  (HTTP -> localhost:5000)
FastAPI Backend
    |-- POST /voice          — Audio STT (Whisper) -> Advisory -> TTS (gTTS)
    |-- POST /voice-query    — Text query -> Advisory (translated)
    |-- GET  /price          — Market price + demand forecast
    |-- GET  /weather        — Live weather + soil data
    |-- GET  /health         — Service health check
         |
         |-- RAG Service      <- FAISS + sentence-transformers (ICAR docs)
         |-- Advisory Service <- Llama 3.1 8B via Ollama
         |-- Market Service   <- Multi-market arbitrage + demand forecasting
         |-- Weather Service  <- AgroMonitoring API + OSM Nominatim
         |-- Community Service<- JSON-based tips (semantic search)
         |-- Translation      <- deep-translator (Google Translate)
         |-- Voice Service    <- openai-whisper (STT) + gTTS (TTS)
              |
         data/
              |-- prices.json          — Crop prices + demand_forecast
              |-- market_trends.json   — Historical trends per crop
              |-- community_tips.json  — Crowdsourced farmer tips
              |-- rag/                 — ICAR PDF source documents
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Next.js 15, TypeScript, Tailwind CSS v4, Shadcn UI, Lucide Icons, Outfit (Google Fonts) |
| **Backend** | Python, FastAPI, Uvicorn, Pydantic |
| **AI / LLM** | Llama 3.1 8B via Ollama (local, offline-capable) |
| **RAG** | LangChain, FAISS, `sentence-transformers` (`all-MiniLM-L6-v2`) |
| **Voice** | `openai-whisper` (STT), gTTS (TTS), Web Audio API |
| **Weather** | AgroMonitoring API, Open-Meteo, OSM Nominatim (geocoding) |
| **Translation** | `deep-translator` (Google Translate wrapper) |

---

## Features

### Voice Advisory (Multilingual)
- Hold-to-speak mic — records audio, transcribes with Whisper, generates advice with Llama 3.1
- Text-to-speech reply via gTTS — plays audio response back in farmer's language
- Text input alternative for typed queries

### RAG-Backed ICAR Knowledge
- ICAR agricultural documents indexed into a FAISS vector store
- Semantic retrieval provides citations alongside every answer
- Answers include structured DO and DON'T guidance

### Market Intelligence & Demand Forecasting
- Live crop price lookup across 19+ crops with market source
- Multi-market arbitrage analysis
- Net gain calculation after estimated transport cost
- Demand Forecast badge per crop (High / Stable / Low)
- Sell Now / Wait recommendation

### Live Weather & Soil Data
- City-based weather lookup
- Soil moisture and soil temperature via AgroMonitoring API
- Auto-detects user location via browser geolocation

### Community Knowledge Network
- 50+ crowdsourced farmer tips
- Semantic search surfaces the most relevant tips

### Full Internationalization (9 Languages)
- Language switcher in the UI header
- All UI labels, loading steps, section titles, and demo queries translate instantly

---

## Setup Instructions

### Prerequisites

- Python 3.10+, Node.js 18+, Ollama, Git, FFmpeg

### LLM Setup

```bash
ollama pull llama3.1:8b
```

### Backend

```bash
cd backend
python -m venv venv && venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --port 5000 --reload
```

### Frontend

```bash
cd frontend
npm install
# Create frontend/.env.local with: NEXT_PUBLIC_BACKEND_URL=http://localhost:5000
npm run dev
```

Open **`http://localhost:3000`**

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Service health check |
| `POST` | `/voice` | Audio -> Whisper STT -> LLM -> gTTS |
| `POST` | `/voice-query` | Text query -> LLM advisory |
| `GET` | `/price?crop=tomato` | Market price + demand forecast |
| `GET` | `/weather?city=Madurai` | Live weather + soil data |

---

## License

MIT License

---

<div align="center">
  <strong>Built for Indian Farmers 🌾 — Krishi-Setu bridges the knowledge gap in agriculture</strong>
</div>
