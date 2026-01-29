# Ethereum Historical Society — Research Plan
*Project Scope & Architecture Draft*

---

## Vision

A living knowledge base documenting Ethereum's evolution — from whitepaper to present — with an AI chat interface that makes the protocol's history and design accessible to anyone.

**Two core functions:**
1. **Educational Resource** — Timeline-based exploration of Ethereum's evolution, hard forks, EIPs, and architectural decisions
2. **Knowledge Agent** — Chat interface connected to primary sources (EIPs, research forums, blog posts, client code) for answering questions about current and historical functionality

---

## Data Sources

### Primary Sources (to index)

| Source | URL | Content Type |
|--------|-----|--------------|
| **EIP Repository** | github.com/ethereum/EIPs | Standards, proposals, rationale |
| **Ethereum Research** | ethresear.ch | Research discussions, pre-EIP ideas |
| **Vitalik's Blog** | vitalik.eth.limo | Deep dives, philosophy, design rationale |
| **Ethereum Blog** | blog.ethereum.org | Official announcements, hard fork summaries |
| **Client Repos** | geth, reth, nethermind, besu, etc. | Implementation details, changelogs |
| **Ethereum Magicians** | ethereum-magicians.org | Governance, EIP discussions |
| **Yellow Paper** | ethereum.github.io/yellowpaper | Formal specification |
| **Execution Specs** | github.com/ethereum/execution-specs | Python reference implementation |
| **Consensus Specs** | github.com/ethereum/consensus-specs | Beacon chain specification |

### Secondary Sources (for context)

- Week in Ethereum News archive
- EthHub documentation (archived)
- Ethereum Foundation YouTube (Devcon talks)
- Notable Twitter/X threads from core devs

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Frontend (Next.js)                      │
├─────────────────────────────────────────────────────────────┤
│  Timeline View  │  Search  │  Chat Interface  │  EIP Browser│
└────────┬────────┴────┬─────┴────────┬─────────┴──────┬──────┘
         │             │              │                │
         ▼             ▼              ▼                ▼
┌─────────────────────────────────────────────────────────────┐
│                      API Layer (Hono/Next)                  │
├─────────────────────────────────────────────────────────────┤
│  /timeline  │  /search  │  /chat  │  /eips  │  /forks      │
└──────┬──────┴─────┬─────┴────┬────┴────┬────┴───────┬──────┘
       │            │          │         │            │
       ▼            ▼          ▼         ▼            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Knowledge Layer                          │
├─────────────────────────────────────────────────────────────┤
│  Vector DB (embeddings)  │  Structured DB (metadata)        │
│  - Document chunks       │  - EIP metadata                  │
│  - Semantic search       │  - Fork dates/blocks             │
│                          │  - Source relationships          │
└──────────────────────────┴──────────────────────────────────┘
       ▲                              ▲
       │         Indexing Pipeline    │
       │                              │
┌──────┴──────────────────────────────┴───────────────────────┐
│                     Data Ingestion                          │
├─────────────────────────────────────────────────────────────┤
│  GitHub Sync  │  RSS/Feeds  │  Web Scrape  │  API Pulls    │
│  (EIPs, code) │  (blogs)    │  (forums)    │  (stats)      │
└─────────────────────────────────────────────────────────────┘
```

---

## Core Features

### 1. Timeline View
Visual timeline of Ethereum's history:
- Hard forks (Frontier → Homestead → DAO Fork → Byzantium → ... → Dencun)
- Major EIPs mapped to forks
- Block numbers and dates
- Clickable nodes → deep dive content

### 2. EIP Browser
- Full EIP database with metadata
- Status tracking (Draft → Review → Final)
- Dependency graphs (which EIPs relate to which)
- Implementation status across clients

### 3. Fork Encyclopedia
For each hard fork:
- Summary of changes
- EIPs included
- Block number / date
- Client versions
- Community context / controversy (if any)

### 4. AI Chat Interface
- RAG-based Q&A over all indexed sources
- Example queries:
  - "Why did Ethereum switch from PoW to PoS?"
  - "What EIPs were included in the Shapella upgrade?"
  - "How does EIP-4844 blob pricing work?"
  - "Show me the original DAO fork discussion"
- Source citations with links

### 5. Search
- Full-text search across all sources
- Filters: source type, date range, author, EIP status
- Semantic search (find conceptually related content)

---

## Tech Stack (Proposed)

| Layer | Tech | Rationale |
|-------|------|-----------|
| Frontend | Next.js 14 + React | Vercel-native, SSR for SEO |
| Styling | Tailwind CSS | Fast iteration |
| Chat UI | Vercel AI SDK | Streaming, built for this |
| API | Next.js API routes or Hono | Simple, serverless |
| Vector DB | Pinecone / Weaviate / Supabase pgvector | Semantic search |
| Structured DB | Supabase / PlanetScale | Metadata, relationships |
| Embeddings | OpenAI / Voyage | Document chunking |
| LLM | Claude / GPT-4 | Chat responses |
| Ingestion | GitHub Actions + cron | Keep sources fresh |
| Hosting | Vercel | Easy deploys |

---

## Data Model

### EIP
```typescript
interface EIP {
  number: number
  title: string
  status: 'Draft' | 'Review' | 'Last Call' | 'Final' | 'Stagnant' | 'Withdrawn'
  type: 'Standards Track' | 'Meta' | 'Informational'
  category?: 'Core' | 'Networking' | 'Interface' | 'ERC'
  authors: string[]
  created: Date
  requires?: number[]  // dependent EIPs
  includedIn?: string  // fork name
  summary: string
  content: string      // full markdown
  discussions: string  // link to discussion
}
```

### HardFork
```typescript
interface HardFork {
  name: string
  block: number
  date: Date
  eips: number[]
  summary: string
  clients: ClientVersion[]
  controversy?: string
}
```

### Document (for RAG)
```typescript
interface Document {
  id: string
  source: 'eip' | 'ethresearch' | 'vitalik' | 'client' | 'magicians'
  url: string
  title: string
  content: string
  author?: string
  date?: Date
  embedding: number[]
  chunks: Chunk[]
}
```

---

## Research Tasks

### Phase 1: Data Mapping (Week 1)
- [ ] Inventory all EIPs, categorize by fork
- [ ] Build complete hard fork timeline with block numbers
- [ ] Identify key Vitalik blog posts to index
- [ ] Map ethresear.ch structure, identify indexing approach
- [ ] Survey client repos for relevant docs/changelogs

### Phase 2: Ingestion Pipeline (Week 2)
- [ ] Set up GitHub sync for EIP repo
- [ ] Build scraper for Vitalik's blog
- [ ] Build scraper for ethresear.ch
- [ ] Set up embedding pipeline
- [ ] Design chunking strategy for long documents

### Phase 3: Knowledge Base (Week 3)
- [ ] Set up vector DB
- [ ] Index all sources
- [ ] Build search API
- [ ] Test retrieval quality

### Phase 4: Frontend (Week 4)
- [ ] Timeline component
- [ ] EIP browser
- [ ] Chat interface
- [ ] Search UI

### Phase 5: Polish & Launch
- [ ] SEO optimization
- [ ] Performance tuning
- [ ] Documentation
- [ ] Launch 🚀

---

## Open Questions

1. **Scope for MVP?** Full vision is big. What's the smallest useful version?
   - Option A: Just the timeline + fork encyclopedia (static, no AI)
   - Option B: Just the chat interface over EIPs
   - Option C: Both, minimal UI

2. **Hosting costs?** Vector DBs and LLM calls add up. Free tier limits?

3. **Update frequency?** Real-time GitHub sync or daily batch?

4. **Community contribution?** Allow edits/suggestions?

---

## Next Steps

1. Validate scope with Everdred
2. Scaffold Next.js project
3. Start with EIP ingestion (most structured source)
4. Build timeline view as first UI
5. Add chat once knowledge base is populated

---

*Created by Applekid — January 29, 2026*
