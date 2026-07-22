# Navium AI

A multi-agent AI platform built on a microservices architecture — chat, coding, vision, PDF RAG, presentation generation, and image generation, all routed through a LangGraph-style supervisor and served behind a unified API gateway.

## 🧱 Architecture

Microservices, each independently deployable via Docker:

| Service | Responsibility |
|---|---|
| **gateway** | Single entry point — request routing, auth middleware, header-forwarding proxy |
| **auth** | Firebase-backed authentication, user model/routes |
| **agent** | Core AI engine — supervisor graph routes requests to 8 specialized agents |
| **billing** | Razorpay integration, credit plans, payment tracking |
| **chat** | Conversation & message persistence |

Shared infra: **Redis** (rate limiting / caching / pub-sub), **MongoDB** (per-service data stores), **AWS S3** (file storage for uploads and generated artifacts).

## 🤖 Agents

Routed through a supervisor graph (`supervisor.graph.js` + `router.node.js` + `state.js`):

- **chat.agent.js** — general conversational agent
- **coding.agent.js** — code generation/assistance
- **imageGen.agent.js** — AI image generation
- **pdf.agent.js** — PDF processing
- **pdfRag.agent.js** — RAG-based PDF Q&A (vector store + embeddings)
- **ppt.agent.js** — presentation/slide generation
- **search.agent.js** — web search (Tavily)
- **vision.agent.js** — image/vision understanding

## 🧰 Tech Stack

**Backend**
- Node.js + Express
- MongoDB (per-service)
- Redis (shared)
- Docker + Docker Compose
- Firebase Auth
- Razorpay (billing/payments)
- AWS S3 (file storage)
- Tavily API (web search)
- Vector store + embeddings (RAG)

**Frontend**
- React + Vite
- Redux (state management: conversations, messages, user)
- Axios

## 📁 Project Structure

```
backend/
├── gateway/
│   ├── controllers/user.controller.js
│   ├── middlewares/auth.middleware.js
│   ├── utils/proxyWithHeaders.js
│   └── index.js
├── services/
│   ├── agent/
│   │   ├── agents/           # 8 specialized agents
│   │   ├── config/           # rate limiting, db, multer
│   │   ├── graph/            # supervisor routing logic
│   │   ├── controllers/
│   │   ├── routes/
│   │   └── utils/            # credits, embeddings, S3, memory, tavily
│   ├── auth/
│   │   ├── config/           # db, firebase
│   │   ├── models/user.model.js
│   │   └── controllers/ routes/
│   ├── billing/
│   │   ├── config/           # credits, plans, razorpay
│   │   ├── models/payment.model.js
│   │   └── controllers/ routes/
│   └── chat/
│       ├── config/db.js
│       ├── models/           # conversation, message
│       └── controllers/ routes/
├── shared/redis/redis.js
└── docker-compose.yml

frontend/
└── src/
    ├── components/            # ChatArea, Sidebar, Navbar, BillingDrawer, ArtifactPanel...
    ├── features/              # agent, billing, conversation, message APIs
    ├── redux/                 # conversation, message, user slices + store
    ├── hooks/useCurrentUser.jsx
    └── pages/Home.jsx
```

## 🚀 Getting Started

### Backend (all services via Docker Compose)
```bash
cd backend
docker-compose up --build
```

Each service also runs standalone:
```bash
cd backend/services/<service-name>
npm install
node index.js
```

Required environment variables (per service — see individual `.env` files):
```
MONGO_URL=
REDIS_URL=
FIREBASE_SERVICE_ACCOUNT=   # do NOT commit serviceAccount.json — load from env/secrets manager
RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
S3_BUCKET_NAME=
TAVILY_API_KEY=
```


### Frontend
```bash
cd frontend
npm install
npm run dev
```

## 📡 Core Functionality

- **Multi-agent orchestration** — supervisor graph decides which specialized agent handles each request based on intent
- **Credit-based billing** — Razorpay checkout, credit deduction per agent call, plan management
- **Conversation memory** — persisted chat history with per-conversation and per-message models
- **File handling** — uploads via Multer → S3, downloadable artifact URLs
- **RAG PDF Q&A** — document embedding + vector store retrieval for grounded answers
- **Rate limiting** — per-agent request throttling via Redis

## 📄 License

Internal project — all rights reserved.
