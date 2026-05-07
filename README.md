<div align="center">

# 🏗️ AI-VIBE-WEBSITE-BUILDER-V1

### **Real-Time AI Website Builder**
*Next.js 15 · Clerk Auth · Prisma ORM · tRPC · E2B · Multi-LLM Streaming*

[![Next.js](https://img.shields.io/badge/Next.js-15.0+-000000?style=for-the-badge&logo=next.js&logoColor=white)](https://nextjs.org)
[![Clerk](https://img.shields.io/badge/Clerk-Auth-6C47FF?style=for-the-badge)](https://clerk.com)
[![Prisma](https://img.shields.io/badge/Prisma-6.0+-2D3748?style=for-the-badge&logo=prisma)](https://prisma.io)
[![tRPC](https://img.shields.io/badge/tRPC-11.0+-2596BE?style=for-the-badge)](https://trpc.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

**[🚀 Live Demo](https://ai-vibe-builder-v1.vercel.app)** · **[📖 Docs](#documentation)** · **[⭐ Star](https://github.com/mk-knight23/AI-VIBE-WEBSITE-BUILDER-V1)**

</div>

---

## 🎯 Build Websites With Natural Language

AI-VIBE-WEBSITE-BUILDER-V1 turns your text descriptions into production-ready websites — in real-time. Describe what you want, watch the code stream in, and see your site rendered live. Full auth, project management, and export.

> **Pillar 3, Iteration 1** — The foundation of AI-powered web creation.

---

## ✨ Features

| Feature | Status | Description |
|---------|--------|-------------|
| 💬 **Natural Language Input** | ✅ | Describe your site in plain English |
| 🌊 **Real-Time Streaming** | ✅ | See code generated token-by-token |
| 👁️ **Live Preview** | ✅ | Instant browser preview alongside code |
| 🔐 **Clerk Authentication** | ✅ | Social login, magic links, MFA |
| 💾 **Project Persistence** | ✅ | Prisma + PostgreSQL, unlimited projects |
| 🔄 **Iteration Loop** | ✅ | "Make the header blue" → instant update |
| 📤 **Export Code** | ✅ | Download clean HTML/CSS/JS |
| 🤖 **Multi-LLM** | ✅ | Claude, GPT-4o, Gemini routing |
| 🛡️ **E2B Sandbox** | ✅ | Code executes in isolated container |
| 🎨 **Template Library** | ✅ | 20+ starter templates |

---

## 🏗️ Architecture

```
src/
├── app/
│   ├── layout.tsx               # ClerkProvider + tRPC Provider
│   ├── page.tsx                 # Landing page
│   ├── dashboard/page.tsx       # Project dashboard
│   └── project/[id]/page.tsx    # Builder interface
├── modules/
│   └── projects/
│       ├── server/
│       │   └── procedures.ts    # tRPC router (create, update, stream)
│       └── ui/
│           ├── components/
│           │   ├── auto-generator.tsx    # LLM streaming orchestrator
│           │   ├── message-form.tsx      # Chat input with file upload
│           │   ├── fragment-web.tsx      # Sandboxed preview iframe
│           │   ├── messages-container.tsx # Conversation history
│           │   ├── generation-preview.tsx # Code + preview split view
│           │   └── project-header.tsx    # Title, share, export
│           └── views/
│               ├── BuilderView.tsx       # Main builder layout
│               └── ProjectsView.tsx      # Dashboard grid
├── server/
│   ├── trpc.ts                  # tRPC initialization
│   ├── routers/
│   │   ├── projects.ts          # Project CRUD
│   │   └── generation.ts        # AI generation endpoints
│   └── providers/
│       ├── anthropic.ts         # Claude streaming
│       ├── openai.ts            # GPT-4o streaming
│       └── gemini.ts            # Gemini streaming
└── prisma/
    └── schema.prisma            # User, Project, Message, Fragment
```

---

## 🌊 Streaming Architecture

The builder uses **Server-Sent Events** for real-time code streaming:

```typescript
// server/providers/anthropic.ts
export async function streamWebsite(
  prompt: string,
  context: BuildContext,
  onChunk: (chunk: string) => void
) {
  const stream = await anthropic.messages.stream({
    model: 'claude-sonnet-4-6',
    max_tokens: 8192,
    system: WEBSITE_BUILDER_SYSTEM_PROMPT,
    messages: [{ role: 'user', content: buildPrompt(prompt, context) }]
  })

  for await (const chunk of stream) {
    if (chunk.type === 'content_block_delta') {
      onChunk(chunk.delta.text)
    }
  }
}
```

---

## 🗄️ Database Schema

```prisma
model Project {
  id          String     @id @default(cuid())
  userId      String
  name        String
  description String?
  messages    Message[]
  fragments   Fragment[]
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt
  user        User       @relation(fields: [userId], references: [id])
}

model Fragment {
  id          String  @id @default(cuid())
  projectId   String
  html        String  @db.Text
  css         String  @db.Text
  javascript  String  @db.Text
  iteration   Int     @default(1)
  isActive    Boolean @default(true)
  project     Project @relation(fields: [projectId], references: [id])
}
```

---

## 🚀 Quick Start

```bash
git clone https://github.com/mk-knight23/AI-VIBE-WEBSITE-BUILDER-V1.git
cd AI-VIBE-WEBSITE-BUILDER-V1
npm install
cp .env.example .env.local
```

### Environment Variables

```env
# Auth
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_...
CLERK_SECRET_KEY=sk_...

# Database
DATABASE_URL=postgresql://user:pass@host:5432/vibe

# AI Providers
ANTHROPIC_API_KEY=sk-ant-...
OPENAI_API_KEY=sk-...
GOOGLE_AI_API_KEY=...

# E2B Sandbox
E2B_API_KEY=e2b_...

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

```bash
npx prisma migrate dev     # Run migrations
npm run dev                # → http://localhost:3000
```

---

## 📦 Commands

```bash
npm run dev          # Next.js Turbopack dev
npm run build        # Production build
npm run start        # Production server
npx prisma studio    # Database GUI
npx prisma migrate   # Run migrations
npm run lint         # ESLint
npm run type-check   # TypeScript check
```

---

<div align="center">

**Built with 🏗️ by [Kazi Musharraf](https://mkazi.live)**

*Part of the [AI-VIBE Ecosystem](https://github.com/mk-knight23/AI-VIBE-ECOSYSTEM) · Built in India 🇮🇳*

</div>
