# Ethereum Historical Society — Technical Specification
*Detailed Implementation Guide*

---

## Table of Contents

1. [Overview](#overview)
2. [Tech Stack](#tech-stack)
3. [Project Structure](#project-structure)
4. [UI/UX Specification](#uiux-specification)
5. [Timeline Component Deep Dive](#timeline-component-deep-dive)
6. [Data Architecture](#data-architecture)
7. [API Specification](#api-specification)
8. [AI Chat System](#ai-chat-system)
9. [Data Ingestion Pipeline](#data-ingestion-pipeline)
10. [Scaling Evolution Content](#scaling-evolution-content)
11. [Component Library](#component-library)
12. [Database Schema](#database-schema)
13. [Deployment](#deployment)

---

## Overview

### Product Summary

Ethereum Historical Society is an interactive, educational platform that visualizes Ethereum's evolution through a vertical scrolling timeline. Users can explore hard forks, EIPs, scaling milestones, and research discussions, with an AI chat interface for deeper exploration.

### Core User Journeys

1. **Explorer**: Scroll the timeline, click nodes to learn about specific events
2. **Researcher**: Search for specific EIPs, discussions, or concepts
3. **Learner**: Ask the AI questions about Ethereum's history and design
4. **Developer**: Look up implementation details, client changes, specs

---

## Tech Stack

*Aligned with Actions SDK demo patterns*

### Frontend
| Technology | Version | Purpose |
|------------|---------|---------|
| React | 18.x | UI framework |
| Vite | 5.x | Build tool, dev server |
| TypeScript | 5.x | Type safety |
| Tailwind CSS | 3.x | Utility-first styling |
| shadcn/ui | latest | Component primitives |
| Framer Motion | 11.x | Animations, scroll effects |
| TanStack Query | 5.x | Data fetching, caching |
| Zustand | 4.x | State management |
| React Router | 6.x | Routing |

### Backend
| Technology | Version | Purpose |
|------------|---------|---------|
| Hono | 4.x | API framework |
| Node.js | 20.x | Runtime |
| Drizzle ORM | latest | Database queries |
| Zod | 3.x | Validation |

### AI/Knowledge
| Technology | Purpose |
|------------|---------|
| OpenAI / Anthropic | LLM for chat |
| Vercel AI SDK | Streaming chat UI |
| Pinecone / Supabase pgvector | Vector storage |
| LangChain.js | RAG orchestration |

### Infrastructure
| Technology | Purpose |
|------------|---------|
| Vercel | Frontend hosting |
| Vercel Edge Functions | API endpoints |
| Supabase | PostgreSQL + pgvector |
| GitHub Actions | CI/CD, data sync |

---

## Project Structure

```
ethereum-historical-society/
├── apps/
│   ├── web/                    # Main frontend application
│   │   ├── src/
│   │   │   ├── components/
│   │   │   │   ├── ui/         # shadcn components
│   │   │   │   ├── timeline/   # Timeline-specific components
│   │   │   │   ├── chat/       # AI chat components
│   │   │   │   ├── eip/        # EIP browser components
│   │   │   │   └── layout/     # Layout components
│   │   │   ├── hooks/          # Custom React hooks
│   │   │   ├── lib/            # Utilities, helpers
│   │   │   ├── pages/          # Route pages
│   │   │   ├── stores/         # Zustand stores
│   │   │   ├── styles/         # Global styles
│   │   │   ├── types/          # TypeScript types
│   │   │   └── main.tsx
│   │   ├── public/
│   │   │   ├── icons/          # Fork/client logos
│   │   │   └── images/
│   │   ├── index.html
│   │   ├── vite.config.ts
│   │   ├── tailwind.config.ts
│   │   └── package.json
│   │
│   └── api/                    # Hono API service
│       ├── src/
│       │   ├── routes/
│       │   │   ├── timeline.ts
│       │   │   ├── eips.ts
│       │   │   ├── search.ts
│       │   │   ├── chat.ts
│       │   │   └── index.ts
│       │   ├── services/
│       │   │   ├── embedding.ts
│       │   │   ├── retrieval.ts
│       │   │   └── llm.ts
│       │   ├── db/
│       │   │   ├── schema.ts
│       │   │   └── client.ts
│       │   └── index.ts
│       └── package.json
│
├── packages/
│   ├── data/                   # Shared data types, constants
│   │   ├── src/
│   │   │   ├── forks.ts        # Hard fork definitions
│   │   │   ├── eips.ts         # EIP metadata
│   │   │   ├── milestones.ts   # Key milestones
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   └── ingestion/              # Data ingestion scripts
│       ├── src/
│       │   ├── sources/
│       │   │   ├── github-eips.ts
│       │   │   ├── ethresearch.ts
│       │   │   ├── vitalik-blog.ts
│       │   │   └── clients.ts
│       │   ├── embeddings.ts
│       │   └── sync.ts
│       └── package.json
│
├── pnpm-workspace.yaml
├── package.json
├── tsconfig.base.json
└── README.md
```

---

## UI/UX Specification

### Design System

**Colors** (Ethereum-inspired)
```css
:root {
  /* Primary - Ethereum purple/blue gradient */
  --eth-purple: #627EEA;
  --eth-purple-dark: #3C3C3D;
  --eth-purple-light: #8C8DFC;
  
  /* Timeline era colors */
  --era-frontier: #1B1B1B;        /* 2015 - Dark origins */
  --era-homestead: #2D5016;       /* 2016 - Growth green */
  --era-metropolis: #4A1A7A;      /* 2017-2019 - Purple expansion */
  --era-beacon: #FF6B35;          /* 2020-2022 - Beacon orange */
  --era-merge: #00D395;           /* 2022+ - Green sustainable */
  
  /* Neutrals */
  --bg-primary: #0A0A0B;
  --bg-secondary: #141416;
  --bg-tertiary: #1C1C1F;
  --text-primary: #FFFFFF;
  --text-secondary: #A1A1AA;
  --text-muted: #71717A;
  
  /* Accents */
  --accent-success: #22C55E;
  --accent-warning: #F59E0B;
  --accent-error: #EF4444;
  --accent-info: #3B82F6;
}
```

**Typography**
```css
/* Headings */
font-family: 'Inter', system-ui, sans-serif;
font-weight: 600;

/* Body */
font-family: 'Inter', system-ui, sans-serif;
font-weight: 400;

/* Code/Technical */
font-family: 'JetBrains Mono', monospace;
```

**Spacing Scale**
```
4px, 8px, 12px, 16px, 24px, 32px, 48px, 64px, 96px, 128px
```

---

### Page Layout

```
┌──────────────────────────────────────────────────────────────────┐
│  Header: Logo | Timeline | EIPs | Chat | Search          [Theme]│
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                      HERO SECTION                          │ │
│  │         "The Complete History of Ethereum"                 │ │
│  │              [Scroll to explore ↓]                         │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                                                            │ │
│  │                    TIMELINE SECTION                        │ │
│  │                    (see detailed spec)                     │ │
│  │                                                            │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌─────────────────────┐  ┌──────────────────────────────────┐ │
│  │                     │  │                                  │ │
│  │   CHAT INTERFACE    │  │      DETAIL PANEL                │ │
│  │   (collapsible)     │  │      (selected item)             │ │
│  │                     │  │                                  │ │
│  └─────────────────────┘  └──────────────────────────────────┘ │
│                                                                  │
├──────────────────────────────────────────────────────────────────┤
│  Footer: About | GitHub | Data Sources | API                    │
└──────────────────────────────────────────────────────────────────┘
```

---

## Timeline Component Deep Dive

### Visual Structure

```
                         ┌─────────────────────────────────────┐
                         │          ERA: FRONTIER              │
                         │           2015-2016                 │
                         └─────────────────────────────────────┘
                                          │
                                          │
    ┌──────────────────────┐              │
    │ July 30, 2015        │              │
    │ ━━━━━━━━━━━━━━━━━━━  │              ●──── Block 0
    │ FRONTIER LAUNCH      │             ╱│╲
    │                      │            ╱ │ ╲
    │ Genesis block mined. │           ╱  │  ╲
    │ Ethereum goes live.  │          ╱   │   ╲
    │                      │         ╱    │    ╲
    │ [View details →]     │        ●     ●     ●
    └──────────────────────┘       EIP   Blog  Code
                                          │
                                          │
                                          │
                              ┌───────────┴───────────┐
                              │                       │
                              ●──── Block 200,000     │
                             ╱│                       │
    ┌──────────────────────┐╱ │                       │
    │ March 14, 2016       │  │                       │
    │ ━━━━━━━━━━━━━━━━━━━  │  │                       │
    │ HOMESTEAD FORK       │  ●                       │
    │                      │ EIP-2                    │
    │ First planned hard   │ EIP-7                    │
    │ fork. Gas costs      │ EIP-8                    │
    │ adjusted.            │  │                       │
    │                      │  │                       │
    │ EIPs: 2, 7, 8        │  │                       │
    │ [View details →]     │  │                       │
    └──────────────────────┘  │                       │
                              │                       │
                         ┌────┴───────────────────────┴────┐
                         │         ERA: HOMESTEAD          │
                         │           2016-2017             │
                         └─────────────────────────────────┘
                                          │
                                          │
                              ┌───────────┴───────────┐
                              │                       │
                              ●──── Block 1,150,000   │
                             ╱│╲                      │
                            ╱ │ ╲                     │
    ┌──────────────────────┐  │  ╲                    │
    │ July 20, 2016        │  │   ●                   │
    │ ━━━━━━━━━━━━━━━━━━━  │  │  Controversy         │
    │ THE DAO FORK         │  │                       │
    │ ⚠️ CONTROVERSIAL      │  │                       │
    │                      │  ●                       │
    │ State intervention   │ EIP-779                  │
    │ to recover funds.    │  │                       │
    │ Birth of ETC.        │  │                       │
    │                      │  │                       │
    │ [View details →]     │  │                       │
    └──────────────────────┘  │                       │
                              │                       │
                              ▼                       ▼
                         (continues...)
```

### Timeline Node Types

```typescript
type TimelineNodeType = 
  | 'hard_fork'      // Major network upgrade
  | 'eip'            // Ethereum Improvement Proposal
  | 'research'       // Research post / discussion
  | 'milestone'      // Non-fork milestone (launch, merge, etc.)
  | 'scaling'        // Scaling-related development
  | 'controversy'    // Notable community debate
  | 'client'         // Client release / change

interface TimelineNode {
  id: string
  type: TimelineNodeType
  date: Date
  blockNumber?: number
  title: string
  summary: string
  era: Era
  
  // Relationships
  relatedEips?: number[]
  relatedNodes?: string[]
  sources: Source[]
  
  // Display
  importance: 'major' | 'significant' | 'minor'
  side: 'left' | 'right'  // Which side of the timeline
  
  // Content
  content?: string        // Markdown content for detail view
  links?: ExternalLink[]
}

type Era = 
  | 'frontier'     // 2015-2016
  | 'homestead'    // 2016-2017
  | 'metropolis'   // 2017-2019 (Byzantium, Constantinople)
  | 'istanbul'     // 2019-2020
  | 'beacon'       // 2020-2022 (Beacon chain era)
  | 'merge'        // 2022 (The Merge)
  | 'shanghai'     // 2023
  | 'cancun'       // 2024+
```

### Timeline Component Implementation

```tsx
// components/timeline/Timeline.tsx

import { useRef, useEffect, useState } from 'react'
import { motion, useScroll, useTransform } from 'framer-motion'
import { TimelineNode } from './TimelineNode'
import { TimelineConnector } from './TimelineConnector'
import { EraMarker } from './EraMarker'
import { useTimelineData } from '@/hooks/useTimelineData'
import { useTimelineStore } from '@/stores/timelineStore'

export function Timeline() {
  const containerRef = useRef<HTMLDivElement>(null)
  const { scrollYProgress } = useScroll({ container: containerRef })
  
  const { data: nodes, isLoading } = useTimelineData()
  const { selectedNode, setSelectedNode, activeEra } = useTimelineStore()
  
  // Group nodes by era
  const nodesByEra = groupByEra(nodes)
  
  return (
    <div 
      ref={containerRef}
      className="relative h-screen overflow-y-auto scroll-smooth"
    >
      {/* Progress indicator */}
      <TimelineProgress progress={scrollYProgress} />
      
      {/* Central spine */}
      <div className="absolute left-1/2 top-0 bottom-0 w-px bg-gradient-to-b from-eth-purple/20 via-eth-purple to-eth-purple/20" />
      
      {/* Era sections */}
      {Object.entries(nodesByEra).map(([era, eraNodes]) => (
        <section 
          key={era}
          className="relative min-h-screen py-24"
          data-era={era}
        >
          <EraMarker era={era as Era} />
          
          <div className="relative max-w-6xl mx-auto px-8">
            {eraNodes.map((node, index) => (
              <TimelineEntry
                key={node.id}
                node={node}
                index={index}
                isSelected={selectedNode?.id === node.id}
                onSelect={() => setSelectedNode(node)}
              />
            ))}
          </div>
        </section>
      ))}
    </div>
  )
}

function TimelineEntry({ 
  node, 
  index, 
  isSelected, 
  onSelect 
}: TimelineEntryProps) {
  const isLeft = node.side === 'left'
  
  return (
    <motion.div
      initial={{ opacity: 0, x: isLeft ? -50 : 50 }}
      whileInView={{ opacity: 1, x: 0 }}
      viewport={{ once: true, margin: "-100px" }}
      transition={{ duration: 0.5, delay: index * 0.1 }}
      className={cn(
        "relative flex items-start gap-8 mb-16",
        isLeft ? "flex-row" : "flex-row-reverse"
      )}
    >
      {/* Content card */}
      <TimelineCard
        node={node}
        isSelected={isSelected}
        onClick={onSelect}
        className={cn(
          "w-[400px]",
          isLeft ? "text-right" : "text-left"
        )}
      />
      
      {/* Connector to central spine */}
      <TimelineConnector 
        type={node.type}
        importance={node.importance}
        side={node.side}
      />
      
      {/* Central node */}
      <TimelineNodeMarker
        type={node.type}
        importance={node.importance}
        blockNumber={node.blockNumber}
        isSelected={isSelected}
      />
      
      {/* Related items (EIPs, sources) */}
      <RelatedItems
        node={node}
        side={isLeft ? 'right' : 'left'}
      />
    </motion.div>
  )
}
```

### Timeline Card Component

```tsx
// components/timeline/TimelineCard.tsx

import { Card, CardHeader, CardContent } from '@/components/ui/card'
import { Badge } from '@/components/ui/badge'
import { cn } from '@/lib/utils'

interface TimelineCardProps {
  node: TimelineNode
  isSelected: boolean
  onClick: () => void
  className?: string
}

export function TimelineCard({ 
  node, 
  isSelected, 
  onClick,
  className 
}: TimelineCardProps) {
  return (
    <Card
      onClick={onClick}
      className={cn(
        "cursor-pointer transition-all duration-300",
        "hover:shadow-lg hover:shadow-eth-purple/20",
        "border-border/50 hover:border-eth-purple/50",
        isSelected && "border-eth-purple ring-2 ring-eth-purple/20",
        className
      )}
    >
      <CardHeader className="pb-2">
        {/* Date and block */}
        <div className="flex items-center gap-2 text-sm text-muted-foreground mb-1">
          <time dateTime={node.date.toISOString()}>
            {formatDate(node.date)}
          </time>
          {node.blockNumber && (
            <>
              <span>•</span>
              <span className="font-mono">Block {node.blockNumber.toLocaleString()}</span>
            </>
          )}
        </div>
        
        {/* Title with type badge */}
        <div className="flex items-start gap-2">
          <NodeTypeBadge type={node.type} />
          <h3 className="text-lg font-semibold leading-tight">
            {node.title}
          </h3>
        </div>
      </CardHeader>
      
      <CardContent>
        <p className="text-sm text-muted-foreground mb-3">
          {node.summary}
        </p>
        
        {/* Related EIPs */}
        {node.relatedEips && node.relatedEips.length > 0 && (
          <div className="flex flex-wrap gap-1">
            {node.relatedEips.map(eip => (
              <Badge 
                key={eip} 
                variant="secondary"
                className="text-xs font-mono"
              >
                EIP-{eip}
              </Badge>
            ))}
          </div>
        )}
        
        {/* Controversy indicator */}
        {node.type === 'controversy' && (
          <div className="mt-2 flex items-center gap-1 text-amber-500 text-sm">
            <AlertTriangle className="w-4 h-4" />
            <span>Community debate</span>
          </div>
        )}
      </CardContent>
    </Card>
  )
}

function NodeTypeBadge({ type }: { type: TimelineNodeType }) {
  const config = {
    hard_fork: { label: 'Hard Fork', color: 'bg-red-500/20 text-red-400' },
    eip: { label: 'EIP', color: 'bg-blue-500/20 text-blue-400' },
    research: { label: 'Research', color: 'bg-purple-500/20 text-purple-400' },
    milestone: { label: 'Milestone', color: 'bg-green-500/20 text-green-400' },
    scaling: { label: 'Scaling', color: 'bg-cyan-500/20 text-cyan-400' },
    controversy: { label: 'Debate', color: 'bg-amber-500/20 text-amber-400' },
    client: { label: 'Client', color: 'bg-gray-500/20 text-gray-400' },
  }
  
  const { label, color } = config[type]
  
  return (
    <Badge className={cn("text-xs", color)}>
      {label}
    </Badge>
  )
}
```

---

## Scaling Evolution Content

### Scaling Timeline Nodes

The timeline should prominently feature Ethereum's scaling journey:

```typescript
const SCALING_MILESTONES: TimelineNode[] = [
  {
    id: 'scaling-plasma-2017',
    type: 'scaling',
    date: new Date('2017-08-11'),
    title: 'Plasma Whitepaper',
    summary: 'Vitalik and Joseph Poon publish Plasma, proposing child chains for scaling.',
    era: 'metropolis',
    importance: 'major',
    sources: [{ type: 'paper', url: 'https://plasma.io/plasma.pdf' }],
    content: `
## Plasma: Scalable Autonomous Smart Contracts

Plasma proposed a framework for building scalable applications by creating 
child chains that periodically commit to the main chain...
    `
  },
  {
    id: 'scaling-state-channels-2018',
    type: 'scaling',
    date: new Date('2018-03-01'),
    title: 'State Channels Mature',
    summary: 'Raiden Network and other state channel solutions gain traction.',
    era: 'metropolis',
    importance: 'significant',
  },
  {
    id: 'scaling-rollup-research-2019',
    type: 'scaling',
    date: new Date('2019-08-29'),
    title: 'Rollups Emerge',
    summary: 'Vitalik proposes rollups as primary L2 scaling solution.',
    era: 'istanbul',
    importance: 'major',
    sources: [{ 
      type: 'blog', 
      url: 'https://vitalik.eth.limo/general/2021/01/05/rollup.html' 
    }],
  },
  {
    id: 'scaling-optimism-launch-2021',
    type: 'scaling',
    date: new Date('2021-01-16'),
    title: 'Optimism Mainnet Alpha',
    summary: 'First optimistic rollup launches on mainnet.',
    era: 'beacon',
    importance: 'major',
  },
  {
    id: 'scaling-arbitrum-launch-2021',
    type: 'scaling',
    date: new Date('2021-08-31'),
    title: 'Arbitrum One Launch',
    summary: 'Arbitrum launches, bringing competition to optimistic rollups.',
    era: 'beacon',
    importance: 'major',
  },
  {
    id: 'scaling-eip4844-2024',
    type: 'scaling',
    date: new Date('2024-03-13'),
    title: 'EIP-4844: Proto-Danksharding',
    summary: 'Dencun upgrade brings blob transactions, reducing L2 costs 10-100x.',
    era: 'cancun',
    importance: 'major',
    relatedEips: [4844],
  },
  // ... more scaling milestones
]
```

### Scaling Section Content

```typescript
const SCALING_EVOLUTION = {
  chapters: [
    {
      title: "The Scalability Problem",
      era: "2015-2017",
      content: `
When Ethereum launched, it could process ~15 transactions per second. 
For a "world computer," this was clearly insufficient...
      `,
      keyEvents: ['cryptokitties-congestion', 'gas-price-spikes'],
    },
    {
      title: "Layer 2 Exploration",
      era: "2017-2019",
      content: `
The community explored various L2 approaches:
- **State Channels**: Off-chain transactions with on-chain settlement
- **Plasma**: Child chains with fraud proofs
- **Sidechains**: Independent chains bridged to Ethereum
      `,
      keyEvents: ['plasma-whitepaper', 'raiden-launch'],
    },
    {
      title: "The Rollup Revolution",
      era: "2019-2021",
      content: `
Rollups emerged as the winning scaling paradigm, offering:
- **Optimistic Rollups**: Assume validity, allow fraud proofs
- **ZK Rollups**: Cryptographic validity proofs
      `,
      keyEvents: ['rollup-centric-roadmap', 'optimism-launch', 'arbitrum-launch'],
    },
    {
      title: "Proto-Danksharding Era",
      era: "2024+",
      content: `
EIP-4844 introduced blob transactions, dramatically reducing L2 data costs
while maintaining Ethereum's security guarantees...
      `,
      keyEvents: ['eip4844-dencun', 'blob-economics'],
    },
  ]
}
```

---

## API Specification

### Endpoints

```typescript
// Timeline API
GET /api/timeline
  Query: { era?: Era, type?: TimelineNodeType[], limit?: number, offset?: number }
  Response: { nodes: TimelineNode[], total: number, eras: Era[] }

GET /api/timeline/:id
  Response: TimelineNode (with full content)

GET /api/timeline/eras
  Response: { eras: EraInfo[] }

// EIP API
GET /api/eips
  Query: { status?: EIPStatus, category?: EIPCategory, fork?: string, search?: string }
  Response: { eips: EIP[], total: number }

GET /api/eips/:number
  Response: EIP (with full content and relationships)

GET /api/eips/:number/related
  Response: { eips: EIP[], nodes: TimelineNode[] }

// Search API
GET /api/search
  Query: { q: string, sources?: Source[], limit?: number }
  Response: { results: SearchResult[], total: number }

POST /api/search/semantic
  Body: { query: string, limit?: number, threshold?: number }
  Response: { results: SemanticSearchResult[] }

// Chat API
POST /api/chat
  Body: { message: string, conversationId?: string }
  Response: ReadableStream (SSE with tokens)

GET /api/chat/:conversationId/history
  Response: { messages: ChatMessage[] }

// Forks API
GET /api/forks
  Response: { forks: HardFork[] }

GET /api/forks/:name
  Response: HardFork (with EIPs and context)
```

### Response Types

```typescript
interface TimelineResponse {
  nodes: TimelineNode[]
  total: number
  eras: Era[]
  pagination: {
    offset: number
    limit: number
    hasMore: boolean
  }
}

interface SearchResult {
  id: string
  type: 'eip' | 'node' | 'document'
  title: string
  snippet: string
  score: number
  source: string
  url?: string
}

interface SemanticSearchResult extends SearchResult {
  similarity: number
  chunks: {
    text: string
    similarity: number
  }[]
}

interface ChatMessage {
  role: 'user' | 'assistant'
  content: string
  sources?: Source[]
  timestamp: Date
}
```

---

## AI Chat System

### RAG Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      User Query                             │
│              "How does EIP-4844 work?"                      │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Query Processing                         │
│  1. Embed query                                             │
│  2. Extract entities (EIP numbers, dates, concepts)         │
│  3. Classify intent (explain, compare, find, summarize)     │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Retrieval Layer                          │
├─────────────────────────────────────────────────────────────┤
│  Vector Search          │  Keyword Search   │  Entity Lookup │
│  (semantic similarity)  │  (BM25)          │  (EIP-4844)    │
└───────────┬─────────────┴────────┬──────────┴───────┬───────┘
            │                      │                  │
            ▼                      ▼                  ▼
┌─────────────────────────────────────────────────────────────┐
│                    Reranking & Fusion                       │
│  Combine results, rerank by relevance, deduplicate          │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Context Assembly                         │
│  Select top-k chunks, format with source attribution        │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    LLM Generation                           │
│  System: You are an Ethereum historian...                   │
│  Context: [retrieved chunks with sources]                   │
│  User: How does EIP-4844 work?                              │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Response                                 │
│  EIP-4844, also known as Proto-Danksharding, introduces...  │
│  [Sources: EIP-4844, Vitalik's Danksharding FAQ]            │
└─────────────────────────────────────────────────────────────┘
```

### Chat Implementation

```typescript
// api/src/routes/chat.ts

import { Hono } from 'hono'
import { streamSSE } from 'hono/streaming'
import { z } from 'zod'
import { retrieveContext } from '../services/retrieval'
import { generateResponse } from '../services/llm'

const chatRouter = new Hono()

const chatSchema = z.object({
  message: z.string().min(1).max(2000),
  conversationId: z.string().optional(),
})

chatRouter.post('/', async (c) => {
  const body = await c.req.json()
  const { message, conversationId } = chatSchema.parse(body)
  
  // Retrieve relevant context
  const context = await retrieveContext(message, {
    limit: 8,
    includeEips: true,
    includeNodes: true,
    includeDocuments: true,
  })
  
  // Build prompt
  const systemPrompt = buildSystemPrompt(context)
  
  // Stream response
  return streamSSE(c, async (stream) => {
    const response = await generateResponse({
      system: systemPrompt,
      messages: [{ role: 'user', content: message }],
      stream: true,
    })
    
    for await (const chunk of response) {
      await stream.writeSSE({
        data: JSON.stringify({ 
          type: 'token', 
          content: chunk 
        }),
      })
    }
    
    // Send sources at end
    await stream.writeSSE({
      data: JSON.stringify({
        type: 'sources',
        sources: context.sources,
      }),
    })
  })
})

function buildSystemPrompt(context: RetrievalContext): string {
  return `You are an expert Ethereum historian with deep knowledge of the protocol's evolution.

Your knowledge comes from primary sources including EIPs, research posts, Vitalik's writings, and client implementations.

When answering:
1. Be accurate and cite specific EIPs, dates, or sources when relevant
2. Explain technical concepts clearly for developers
3. Acknowledge uncertainty when the sources don't cover something
4. Connect historical context to current state when helpful

Retrieved context:
${context.chunks.map(c => `[${c.source}]: ${c.text}`).join('\n\n')}

Available sources for citation:
${context.sources.map(s => `- ${s.title} (${s.type}): ${s.url}`).join('\n')}`
}
```

### Chat UI Component

```tsx
// components/chat/ChatInterface.tsx

import { useState, useRef, useEffect } from 'react'
import { useChat } from 'ai/react'
import { Card } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { ScrollArea } from '@/components/ui/scroll-area'
import { ChatMessage } from './ChatMessage'
import { SourceCard } from './SourceCard'

export function ChatInterface() {
  const scrollRef = useRef<HTMLDivElement>(null)
  
  const { 
    messages, 
    input, 
    handleInputChange, 
    handleSubmit,
    isLoading,
    data: streamData,
  } = useChat({
    api: '/api/chat',
  })
  
  // Extract sources from stream data
  const sources = streamData?.find(d => d.type === 'sources')?.sources ?? []
  
  // Auto-scroll on new messages
  useEffect(() => {
    scrollRef.current?.scrollIntoView({ behavior: 'smooth' })
  }, [messages])
  
  return (
    <Card className="flex flex-col h-[600px] w-full max-w-2xl">
      {/* Header */}
      <div className="px-4 py-3 border-b">
        <h3 className="font-semibold">Ask about Ethereum's history</h3>
        <p className="text-sm text-muted-foreground">
          Powered by EIPs, research, and protocol documentation
        </p>
      </div>
      
      {/* Messages */}
      <ScrollArea className="flex-1 p-4">
        {messages.length === 0 ? (
          <ChatEmptyState />
        ) : (
          <div className="space-y-4">
            {messages.map((message) => (
              <ChatMessage 
                key={message.id} 
                message={message} 
              />
            ))}
            {isLoading && <ChatLoadingIndicator />}
          </div>
        )}
        <div ref={scrollRef} />
      </ScrollArea>
      
      {/* Sources panel (when available) */}
      {sources.length > 0 && (
        <div className="px-4 py-2 border-t bg-muted/30">
          <p className="text-xs font-medium mb-2">Sources</p>
          <div className="flex gap-2 overflow-x-auto">
            {sources.map((source, i) => (
              <SourceCard key={i} source={source} compact />
            ))}
          </div>
        </div>
      )}
      
      {/* Input */}
      <form onSubmit={handleSubmit} className="p-4 border-t">
        <div className="flex gap-2">
          <Input
            value={input}
            onChange={handleInputChange}
            placeholder="Ask about The Merge, EIP-1559, scaling..."
            disabled={isLoading}
          />
          <Button type="submit" disabled={isLoading || !input.trim()}>
            Send
          </Button>
        </div>
      </form>
    </Card>
  )
}

function ChatEmptyState() {
  const suggestions = [
    "What was The DAO hack and how did Ethereum respond?",
    "Explain how EIP-1559 changed gas pricing",
    "What's the difference between optimistic and ZK rollups?",
    "How did Ethereum transition from PoW to PoS?",
  ]
  
  return (
    <div className="text-center py-8">
      <h4 className="font-medium mb-4">Try asking about...</h4>
      <div className="space-y-2">
        {suggestions.map((s, i) => (
          <button
            key={i}
            className="block w-full text-left px-4 py-2 rounded-lg bg-muted/50 hover:bg-muted text-sm transition-colors"
          >
            {s}
          </button>
        ))}
      </div>
    </div>
  )
}
```

---

## Database Schema

```typescript
// db/schema.ts

import { pgTable, text, timestamp, integer, jsonb, vector } from 'drizzle-orm/pg-core'

// Timeline nodes
export const timelineNodes = pgTable('timeline_nodes', {
  id: text('id').primaryKey(),
  type: text('type').notNull(), // hard_fork, eip, research, etc.
  date: timestamp('date').notNull(),
  blockNumber: integer('block_number'),
  title: text('title').notNull(),
  summary: text('summary').notNull(),
  content: text('content'), // Markdown
  era: text('era').notNull(),
  importance: text('importance').notNull().default('minor'),
  side: text('side').notNull().default('left'),
  relatedEips: jsonb('related_eips').$type<number[]>(),
  relatedNodes: jsonb('related_nodes').$type<string[]>(),
  sources: jsonb('sources').$type<Source[]>(),
  links: jsonb('links').$type<ExternalLink[]>(),
  metadata: jsonb('metadata'),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow(),
})

// EIPs
export const eips = pgTable('eips', {
  number: integer('number').primaryKey(),
  title: text('title').notNull(),
  status: text('status').notNull(),
  type: text('type').notNull(),
  category: text('category'),
  authors: jsonb('authors').$type<string[]>(),
  created: timestamp('created'),
  requires: jsonb('requires').$type<number[]>(),
  includedIn: text('included_in'), // Fork name
  summary: text('summary'),
  content: text('content').notNull(), // Full markdown
  discussions: text('discussions'),
  lastUpdated: timestamp('last_updated'),
})

// Hard forks
export const hardForks = pgTable('hard_forks', {
  name: text('name').primaryKey(),
  block: integer('block').notNull(),
  date: timestamp('date').notNull(),
  summary: text('summary').notNull(),
  content: text('content'),
  eips: jsonb('eips').$type<number[]>(),
  clients: jsonb('clients').$type<ClientVersion[]>(),
  controversy: text('controversy'),
})

// Documents (for RAG)
export const documents = pgTable('documents', {
  id: text('id').primaryKey(),
  source: text('source').notNull(), // eip, ethresearch, vitalik, etc.
  sourceId: text('source_id'), // Original ID in source system
  url: text('url').notNull(),
  title: text('title').notNull(),
  content: text('content').notNull(),
  author: text('author'),
  date: timestamp('date'),
  metadata: jsonb('metadata'),
  createdAt: timestamp('created_at').defaultNow(),
})

// Document chunks (for vector search)
export const documentChunks = pgTable('document_chunks', {
  id: text('id').primaryKey(),
  documentId: text('document_id').references(() => documents.id),
  chunkIndex: integer('chunk_index').notNull(),
  content: text('content').notNull(),
  embedding: vector('embedding', { dimensions: 1536 }),
  metadata: jsonb('metadata'),
})

// Chat conversations
export const conversations = pgTable('conversations', {
  id: text('id').primaryKey(),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow(),
})

export const chatMessages = pgTable('chat_messages', {
  id: text('id').primaryKey(),
  conversationId: text('conversation_id').references(() => conversations.id),
  role: text('role').notNull(), // user, assistant
  content: text('content').notNull(),
  sources: jsonb('sources').$type<Source[]>(),
  createdAt: timestamp('created_at').defaultNow(),
})
```

---

## Component Library

### shadcn/ui Components to Install

```bash
npx shadcn-ui@latest init

npx shadcn-ui@latest add \
  button \
  card \
  input \
  badge \
  scroll-area \
  dialog \
  sheet \
  tabs \
  tooltip \
  command \
  popover \
  separator \
  skeleton \
  avatar \
  dropdown-menu
```

### Custom Components

| Component | Purpose |
|-----------|---------|
| `Timeline` | Main vertical timeline view |
| `TimelineCard` | Individual event card |
| `TimelineConnector` | Lines connecting nodes |
| `EraMarker` | Era section headers |
| `ChatInterface` | AI chat panel |
| `ChatMessage` | Individual chat message |
| `SourceCard` | Citation/source display |
| `EIPCard` | EIP summary card |
| `EIPBrowser` | Full EIP list/search |
| `ForkDetail` | Hard fork deep dive |
| `SearchCommand` | Global search (⌘K) |

---

## Deployment

### Vercel Configuration

```json
// vercel.json
{
  "buildCommand": "pnpm build",
  "outputDirectory": "apps/web/dist",
  "framework": "vite",
  "functions": {
    "apps/api/src/**/*.ts": {
      "runtime": "nodejs20.x"
    }
  },
  "rewrites": [
    { "source": "/api/:path*", "destination": "/apps/api/src/:path*" }
  ]
}
```

### Environment Variables

```bash
# Database
DATABASE_URL=postgresql://...
DATABASE_URL_UNPOOLED=postgresql://...

# Vector DB
PINECONE_API_KEY=...
PINECONE_INDEX=ethereum-history

# LLM
OPENAI_API_KEY=...
# or
ANTHROPIC_API_KEY=...

# GitHub (for EIP sync)
GITHUB_TOKEN=...

# Optional
SENTRY_DSN=...
```

---

## Next Steps

1. **Validate spec** with Everdred
2. **Scaffold project** with Vite + React + Tailwind + shadcn
3. **Set up database** (Supabase with pgvector)
4. **Build Timeline component** first (core visual)
5. **Ingest EIPs** as first data source
6. **Add chat** once knowledge base has content
7. **Deploy to Vercel**

---

*Specification by Applekid — January 29, 2026*
