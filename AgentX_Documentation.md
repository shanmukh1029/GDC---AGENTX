# 🤖 AgentX — Project Documentation

## What Is It?

**AgentX** is an open-source, production-ready **AI chatbot starter kit** built by [Eldorado5002](https://github.com/Eldorado5002). It lets anyone create and deploy a **personalized, memory-powered AI conversation buddy** in under 5 minutes — with zero backend coding required.

The entire behavior, personality, and memory of the AI agent is controlled from a single config file: `agent.config.js`.

---

## What Was Built?

A **full-stack Next.js web application** that wraps Google's Gemini AI with:

- A polished chat UI (dark-themed, mobile-friendly)
- A persistent memory system (stored in browser `localStorage`)
- A depth-aware conversation engine (AI evolves as you talk more)
- A command palette for switching topics (like Spotlight / VS Code)
- A shareable agent link (visitors can chat with *your* AI buddy)
- A secure server-side API proxy (your Gemini key never touches the browser)

---

## What Is It Used For?

| Use Case | Description |
|---|---|
| **Personal AI Buddy** | An AI that remembers your name, interests, goals, background — and gets smarter every session |
| **Custom AI Agent** | Businesses or developers can re-brand and deploy their own specialized AI agent |
| **Shareable Profile** | Share a link so friends can ask your AI buddy questions about you |
| **Learning Project** | A clean, well-structured reference for building AI-powered Next.js apps |

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Framework** | Next.js 15 (App Router) |
| **Frontend** | React 19 (Client Components) |
| **AI Model** | Google Gemini (`gemini-2.5-flash-lite` by default) |
| **Styling** | Vanilla CSS (`globals.css`) with CSS variables |
| **Fonts** | Inter (body) + Space Grotesk (headings) via Google Fonts |
| **Storage** | Browser `localStorage` (no database needed) |
| **Deployment** | Vercel (one-click deploy supported) |
| **API Security** | Server-side Next.js API Route (`/api/chat/route.js`) |

**Dependencies are minimal** — only `next`, `react`, and `react-dom`. No extra libraries.

---

## Architecture

```
┌──────────────────┐      POST /api/chat      ┌──────────────────────┐
│  User Browser     │ ──────────────────────▶  │  Next.js API Route    │
│  (React App)      │ ◀──────────────────────  │  /app/api/chat/       │
│  page.js          │      JSON response        │  route.js             │
└──────────────────┘                           └──────────┬───────────┘
                                                           │  Gemini API
                                                           │  (server-side only)
                                                           ▼
                                                  ┌──────────────────┐
                                                  │  Google Gemini    │
                                                  │  (configurable)   │
                                                  └──────────────────┘
```

The Gemini API key **never** reaches the browser. All AI calls go through the secure Next.js backend route.

---

## File Structure

```
AgentX-main/
├── agent.config.js          ← ⭐ THE ONLY FILE YOU NEED TO EDIT
├── .env.example             ← Copy to .env.local and add your Gemini API key
├── app/
│   ├── api/
│   │   └── chat/
│   │       └── route.js     ← Secure server-side Gemini proxy
│   ├── globals.css          ← Full design system (CSS variables, components)
│   ├── layout.js            ← Root layout with Google Fonts
│   └── page.js              ← Entire React frontend (~744 lines)
├── next.config.js           ← Minimal Next.js config
├── package.json             ← Dependencies (only next, react, react-dom)
├── features.png             ← Feature screenshot
└── README.md                ← Full setup and customization guide
```

---

## Key Features

### 🧠 Persistent Memory
- AI extracts personal facts (name, age, location, interests, goals, etc.) from conversation
- Memory is saved in `localStorage` and survives page refreshes and browser restarts
- Every N messages (default: 5), a batch is sent to Gemini for memory extraction
- Arrays (like interests/goals) are **deduplicated and merged** — never overwritten

### 📈 Depth-Aware Intelligence
The AI's personality **automatically evolves** through 3 stages:

| Stage | Triggers After | Behavior |
|---|---|---|
| **Intro** | Message 0 | Warm, welcoming, asks open-ended questions |
| **Getting to Know** | Message 4 | Connects topics to your known interests/goals |
| **Deep Dive** | Message 10 | Acts like a brilliant trusted friend, debates and challenges |

### ⌘ Command Palette
A Spotlight/VS Code-style modal to switch topics:
- **Personalized Picks** — based on your stored interests and goals
- **Recent Topics** — jump back to previous conversations
- **Live Trending** — real-time trending topics from Gemini (cached 1 hour)
- Full **keyboard navigation** (`↑↓` navigate, `Enter` select, `Esc` close)

### 🔗 Shareable Agent
- Generates a URL with your memory encoded in base64
- Anyone who opens the link can talk to *your* AI buddy
- Visitor mode has its own greeting system and read-only memory access

### 🔒 Security
- API key lives only in `.env.local` on the server
- Frontend never sees the key
- Built-in retry logic (5s → 10s backoff) for Gemini rate limit errors (HTTP 429)

### 📊 API Budget Efficiency (Free Tier)
| Action | API Calls |
|---|---|
| Trending topics (cached 1hr) | 0–1 |
| Welcome back greeting | 1 |
| 10 chat messages | 10 |
| Memory extraction (batched × 2) | 2 |
| **Total for a typical session** | **~13–14** ✅ |

Well within Gemini's free tier limit of 15 RPM.

---

## How Memory Works (Step-by-Step)

```
1. User sends messages
        ↓
2. Messages are queued in memQueueRef
        ↓
3. Every 5th message → batch sent to Gemini
        ↓
4. Gemini extracts facts based on memorySchema in agent.config.js
        ↓
5. Facts are merged into existing memory (arrays deduplicated)
        ↓
6. Updated memory saved to localStorage
        ↓
7. Memory injected into system prompt on next message
```

### Default Memory Keys

| Key | Icon | Type |
|---|---|---|
| `name` | 👤 | string |
| `age` | 🎂 | string |
| `location` | 📍 | string |
| `background` | 🎓 | string |
| `interests` | ❤️ | array |
| `goals` | 🎯 | array |
| `current_situation` | 📌 | string |
| `personality` | ✨ | string |
| `topics_discussed` | 💬 | array (auto-tracked) |

---

## What You Can Customize (`agent.config.js`)

| Config Key | What It Controls |
|---|---|
| `name` | Agent name shown in header |
| `emoji` | Agent avatar emoji |
| `tagline` | Welcome screen headline |
| `description` | Welcome screen subtitle |
| `personality` | Core AI personality prompt |
| `coreRules` | Rules the AI always follows |
| `depthStages` | Behavior at each conversation depth level |
| `memorySchema` | What personal facts to extract and remember |
| `memoryBatchSize` | How often memory extraction runs (every N messages) |
| `trendingCategories` | Topic categories shown on home screen |
| `fallbackTrends` | Default topics when API is unavailable |
| `trendCacheDuration` | How long to cache trending topics (ms) |
| `visitorGreeting` | How the AI introduces itself to visitors |
| `model` | Which Gemini model to use |

---

## How to Run It

### 1. Install dependencies
```bash
cd AgentX-main
npm install
```

### 2. Set up API key
```bash
cp .env.example .env.local
# Edit .env.local:
# GEMINI_API_KEY=your_key_here
```
Get a free key at [Google AI Studio](https://aistudio.google.com/app/apikey).

### 3. Start dev server
```bash
npm run dev
# Open http://localhost:3000
```

### 4. Deploy to Vercel
```bash
npx vercel
# Add GEMINI_API_KEY in Vercel Dashboard → Settings → Environment Variables
```

---

## UI Flow (How a User Experiences It)

```
[App loads]
      │
      ├─ New user?        → Name screen → Topic screen → Chat
      │
      ├─ Returning user?  → Restored chat + "Welcome back" greeting
      │
      └─ Visitor link?    → Visitor banner + Agent intro about owner
```

---

## Design System

| CSS Token | Color | Usage |
|---|---|---|
| `--bg` | `#0c0c11` | Page background |
| `--surface` | `#13131a` | Header, sidebar |
| `--accent` | `#7c6fff` | Primary purple |
| `--green` | `#34d399` | Live memory indicator |
| `--text` | `#e2e2ea` | Primary text |

**Fonts:** Inter (body text) + Space Grotesk (headings)

---

## Roadmap (Planned Features)

- [ ] Database backend (Firebase/Supabase) for cross-device memory sync
- [ ] User authentication
- [ ] Voice input (speech-to-text)
- [ ] Multi-model support (GPT-4, Claude)
- [ ] Conversation export (PDF / Markdown)
- [ ] Light mode + custom color themes
- [ ] Mobile PWA with offline support

---

## Summary

AgentX is essentially a **"build your own AI friend" starter kit** — a clean, minimal, production-grade codebase that abstracts all AI complexity behind one config file. It is ideal for developers who want to ship a personalized AI chatbot fast, without building authentication, memory systems, or API proxies from scratch.
