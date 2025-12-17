# Bfloat Developer Overview

## Project Summary

**Bfloat** is an AI-powered platform that enables users to create React Native/Expo mobile applications through a conversational chat interface. Users describe their app ideas in natural language, and the system generates complete, deployable mobile applications using Large Language Models (LLMs). The platform supports live preview, multi-platform deployment (iOS, Android, Web), and includes backend provisioning via Convex.

---

## Tech Stack

### Frontend
- **Framework**: React Router v7 (formerly Remix) with React 18
- **Language**: TypeScript
- **Styling**: Tailwind CSS with Radix UI components
- **State Management**: 
  - Nanostores for reactive state
  - Zustand for complex stores
  - React Query (TanStack Query) for server state
- **Code Editor**: CodeMirror 6 with syntax highlighting for multiple languages
- **UI Components**: Radix UI primitives, custom component library
- **Build Tool**: Vite

### Backend
- **Runtime**: Node.js 20+
- **Framework**: Express.js
- **Database**: PostgreSQL with Prisma ORM
- **Queue System**: BullMQ with Redis
- **File Storage**: AWS S3
- **Authentication**: Clerk
- **Payments**: Stripe

### AI/LLM Integration
- **Providers**: OpenAI, Anthropic (Claude), Google (Gemini), DeepSeek, XAI
- **SDK**: Vercel AI SDK (`ai` package)
- **Special Agent**: Claude Code (Anthropic) for code generation
- **Model Routing**: OpenRouter for multi-provider access

### Deployment & Infrastructure
- **Mobile Preview**: Snack SDK (Expo Snack)
- **Backend Provisioning**: Convex Cloud
- **Containerization**: Docker (with E2B support)
- **Deployment Platforms**: Fly.io, Google Cloud Platform
- **CI/CD**: Cloud Build (GCP)

### Development Tools
- **Code Quality**: ESLint, Prettier
- **Type Checking**: TypeScript (strict mode)
- **Git Hooks**: Husky with lint-staged
- **Package Manager**: npm

---

## Architecture Overview

### High-Level Flow

```
User Chat Input
    ↓
React Router Route Handler
    ↓
Redis Stream Initiation
    ↓
BullMQ Worker Queue
    ↓
Claude Code Agent (MCP Tools)
    ↓
Code Generation & File Updates
    ↓
Snack SDK Preview Update
    ↓
User Sees Live Changes
```

### Key Architectural Patterns

1. **Streaming Architecture**: LLM responses are streamed via Redis Streams for real-time updates
2. **Queue-Based Processing**: Background workers handle LLM requests via BullMQ
3. **File-Based Projects**: Projects stored as JSON file structures in S3
4. **MCP (Model Context Protocol)**: Uses MCP tools for file operations, linting, verification
5. **Server-Side Rendering**: React Router provides SSR capabilities

---

## Core Features

### 1. Chat-Based App Creation
- Conversational interface for describing app features
- Multi-turn conversations with context preservation
- Support for file uploads and images in prompts
- Real-time streaming responses

### 2. Live Preview
- Instant preview using Expo Snack SDK
- Hot reloading of code changes
- Mobile-responsive preview

### 3. Code Editor
- Multi-file code editor with CodeMirror
- Syntax highlighting for JavaScript, TypeScript, Python, CSS, HTML, JSON, Markdown
- File tree navigation
- Real-time file updates

### 4. Multi-Platform Deployment
- **iOS**: App Store deployment via Expo EAS
- **Android**: Google Play Store deployment
- **Web**: Expo web builds
- Deployment status tracking

### 5. Backend Integration
- Automatic Convex backend provisioning
- Convex schema, queries, mutations, and actions generation
- OAuth integration with Convex Cloud

### 6. Project Management
- Multiple projects per user
- Project templates and versions
- Project sharing and public projects
- Favorite projects

### 7. Subscription Management
- Stripe integration for payments
- Tiered subscriptions (Free, Pro, Super)
- Usage limits based on subscription tier

---

## Project Structure

```
bfloat-app-engineer/
├── app/
│   ├── components/          # React components
│   │   ├── chat/           # Chat interface components
│   │   ├── editor/         # Code editor components
│   │   ├── project/        # Project management UI
│   │   ├── billing/        # Subscription/billing UI
│   │   ├── settings/       # User settings
│   │   └── ui/             # Reusable UI components
│   ├── routes/             # React Router routes (file-based routing)
│   │   ├── api.*.ts       # API endpoints
│   │   └── *.tsx          # Page components
│   ├── lib/                # Core libraries
│   │   ├── llm/           # LLM integration code
│   │   │   ├── stream-text.server.ts
│   │   │   ├── claude-code-prompt.ts
│   │   │   └── ...
│   │   ├── db.server.ts   # Database utilities
│   │   ├── redis.server.ts # Redis client
│   │   ├── storage.ts      # S3 file storage
│   │   └── ...
│   ├── hooks/              # Custom React hooks
│   ├── stores/             # State management stores
│   │   ├── editor.ts      # Editor state
│   │   ├── files.ts       # File management
│   │   ├── prompt.ts      # Chat prompt state
│   │   └── snack.ts       # Snack SDK integration
│   ├── scripts/            # Background workers & scripts
│   │   ├── worker.ts      # Main BullMQ worker
│   │   ├── claude-code.ts # Claude Code agent orchestration
│   │   └── convex.ts      # Convex deployment
│   ├── dao/                # Data Access Objects
│   ├── utils/              # Utility functions
│   ├── types/              # TypeScript type definitions
│   └── root.tsx           # Root component
├── prisma/
│   ├── schema.prisma      # Database schema
│   └── migrations/       # Database migrations
├── public/                # Static assets
├── mc3po/                 # MCP server implementation
└── server.js              # Production server entry point
```

---

## Key Integrations

### LLM Providers
- **OpenAI**: GPT models via AI SDK
- **Anthropic**: Claude models (including Claude Code)
- **Google**: Gemini models
- **DeepSeek**: DeepSeek models
- **XAI**: Grok models
- **OpenRouter**: Multi-provider routing

### External Services
- **Clerk**: Authentication and user management
- **Stripe**: Payment processing and subscriptions
- **AWS S3**: File storage for project data
- **Redis**: Queue management and streaming
- **Convex**: Backend-as-a-Service provisioning
- **Expo Snack**: Live preview service
- **Supabase**: Optional backend integration

---

## Database Schema

### Core Models (Prisma)

**Project**
- Stores project metadata, files (as S3 keys), messages history
- Supports templates, versions, and public sharing
- Tracks Supabase and Convex deployment info

**Subscription**
- User subscription status and Stripe integration
- Tiers: free, pro, super
- Tracks billing periods and cancellation

**DeployedApp**
- Tracks deployment status for iOS/Android/Web
- Links to projects
- Stores deployment URLs and build artifacts

**AccountSettings**
- User preferences (theme, language, timezone)
- OAuth tokens for integrations (Supabase, Convex)

**Integrations**
- Third-party service integrations (Expo, iOS certificates)

---

## State Management

### Client-Side State
- **Nanostores**: Reactive state for UI (theme, sidebar, etc.)
- **Zustand**: Complex stores (editor, files, workbench)
- **React Query**: Server state caching and synchronization

### Server-Side State
- **Redis Streams**: Real-time LLM response streaming
- **BullMQ**: Background job queue for async processing
- **PostgreSQL**: Persistent data storage

### State Flow Example (Chat → Code Generation)

1. User submits message → `Chat` component
2. Message added to local state → `useState`
3. Stream initiated → `initiateLlmStream()` → API route
4. Job queued → BullMQ worker picks up
5. LLM processes → Streams chunks to Redis
6. Client polls Redis → Updates UI in real-time
7. Code changes → File store updated → Snack preview refreshes

---

## Development Setup

### Prerequisites
- Node.js >= 20.0.0
- PostgreSQL database
- Redis server
- AWS S3 bucket
- API keys for LLM providers
- Clerk account for authentication
- Stripe account for payments

### Environment Variables

Key environment variables needed (see `.env.example` or `dev.md`):

```env
# Database
DATABASE_URL="postgresql://..."
DIRECT_URL="postgresql://..."

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# AWS S3
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
PROJECT_DATA_BUCKET_NAME=...

# Authentication
CLERK_PUBLISHABLE_KEY=...
CLERK_SECRET_KEY=...

# LLM APIs
ANTHROPIC_API_KEY=...
OPENAI_API_KEY=...
GOOGLE_API_KEY=...
DEEPSEEK_API_KEY=...

# Convex
CONVEX_TOKEN=...

# Stripe
STRIPE_SECRET_KEY=...
STRIPE_WEBHOOK_SECRET=...

# App
APP_URL=http://localhost:3000
NODE_ENV=development
```

### Installation Steps

1. **Clone and install dependencies**
   ```bash
   npm install
   ```

2. **Set up database**
   ```bash
   npm run db:migrate
   ```

3. **Start development server**
   ```bash
   npm run dev
   ```

4. **Start worker process** (separate terminal)
   ```bash
   npm run worker
   ```

### Development Scripts

- `npm run dev` - Start dev server with hot reload
- `npm run build` - Build for production
- `npm start` - Run production server (starts both web and worker)
- `npm run worker` - Run worker process only
- `npm run db:migrate` - Run database migrations
- `npm run lint` - Run ESLint
- `npm run format` - Format code with Prettier

---

## Key Workflows

### 1. Chat → Code Generation Flow

1. User types message in chat interface
2. `Chat` component calls `handlePromptSubmit()`
3. Message sent to `/api/init-stream` endpoint
4. `initiateStream()` creates Redis stream and queues BullMQ job
5. Worker picks up job and calls `generateExpoApp()` (Claude Code)
6. Claude Code agent uses MCP tools to:
   - Read existing files
   - Create/update files
   - Run linting (`lint_app`)
   - Verify app (`verify_app`)
   - Configure Convex (`configure_convex`)
7. Stream chunks written to Redis
8. Client polls Redis stream and updates UI
9. File changes trigger Snack preview update

### 2. Deployment Flow

1. User clicks "Deploy" in project
2. `DeploymentModal` component opens
3. User selects platform (iOS/Android/Web)
4. API endpoint (`/api/deploy`) handles deployment
5. For iOS/Android: Expo EAS build initiated
6. For Web: Expo web build
7. Deployment status tracked in `DeployedApp` model
8. User receives notifications on completion

### 3. Convex Backend Provisioning

1. User creates project requiring backend
2. System checks for Convex access token
3. If missing, OAuth flow initiated (`/api/oauth.convex.connect`)
4. Convex project created via API
5. Convex files deployed (`app/scripts/convex.ts`)
6. Convex URL stored in project config
7. App automatically connects to Convex backend

---

## Code Quality & Standards

### Linting & Formatting
- **ESLint**: Configured with TypeScript, React, and import plugins
- **Prettier**: Code formatting with import sorting
- **Pre-commit hooks**: Automatically format and lint on commit

### Type Safety
- **TypeScript**: Strict mode enabled
- **Prisma**: Type-safe database queries
- **Zod**: Runtime validation for API inputs

### Code Organization
- File-based routing (React Router)
- Co-located components and styles
- Server/client code separation (`.server.ts` suffix)
- Shared utilities in `lib/` and `utils/`

---

## Testing & Debugging

### Development Tools
- React Query DevTools
- React Router DevTools
- Browser DevTools for client-side debugging

### Logging
- Pino logger for server-side logging
- Console logging for client-side debugging
- Structured logging with context

### Common Debugging Areas
- Redis stream polling (check stream IDs)
- BullMQ job status (check queue)
- LLM response streaming (check Redis entries)
- File updates (check S3 and local state sync)

---

## Deployment

### Production Build
```bash
npm run build
npm start
```

### Docker Deployment
- `Dockerfile` for standard deployment
- `Dockerfile.gcp` for Google Cloud Platform
- `e2b.Dockerfile` for E2B containerization

### Platform-Specific
- **Fly.io**: Configured via `fly.toml`
- **GCP**: Cloud Build via `cloudbuild.yaml`
- **E2B**: Template configuration in `e2b.toml`

---

## Future Roadmap

Based on `Features` file, planned enhancements include:
- Marketplace for builders
- Figma integration
- Image upload support
- Social features (sharing, remixing, trending)
- Agent vs Composer modes
- Game Studio mode
- Flutter support via zapp.run
- Custom templates
- TestFlight distribution for iOS

---

## Key Files to Understand

### Entry Points
- `app/root.tsx` - Root component with providers
- `server.js` - Production server entry
- `app/entry.client.tsx` - Client-side hydration
- `app/entry.server.tsx` - Server-side rendering

### Core Logic
- `app/lib/llm/stream-text.server.ts` - LLM streaming orchestration
- `app/scripts/claude-code.ts` - Claude Code agent integration
- `app/scripts/worker.ts` - Background worker implementation
- `app/routes/api.init-stream.ts` - Stream initiation endpoint

### State Management
- `app/stores/files.ts` - File management store
- `app/stores/editor.ts` - Editor state
- `app/stores/snack.ts` - Snack SDK integration

### Components
- `app/components/chat/Chat.tsx` - Main chat interface
- `app/components/editor/` - Code editor components
- `app/components/project/` - Project management UI

---

## Common Development Tasks

### Adding a New LLM Provider
1. Add API key to environment variables
2. Update `app/lib/llm/model-router.ts` or `openrouter-model.ts`
3. Add provider to model selection logic

### Adding a New Route
1. Create file in `app/routes/` following React Router conventions
2. Export loader/action functions as needed
3. Route automatically registered via file-based routing

### Modifying Database Schema
1. Update `prisma/schema.prisma`
2. Create migration: `npm run db:create <name>`
3. Apply migration: `npm run db:migrate`

### Adding a New Component
1. Create component in appropriate `app/components/` subdirectory
2. Use TypeScript with proper typing
3. Follow existing component patterns (Radix UI, Tailwind)

---

## Resources & Documentation

- **React Router Docs**: https://reactrouter.com
- **Prisma Docs**: https://www.prisma.io/docs
- **Vercel AI SDK**: https://sdk.vercel.ai
- **Expo Snack**: https://docs.expo.dev/snack
- **Convex Docs**: https://docs.convex.dev
- **Clerk Docs**: https://clerk.com/docs

---

## Getting Help

- Check `dev.md` for detailed development guide
- Review `README.md` for setup instructions
- Examine existing code patterns in `app/components/` and `app/routes/`
- Check Prisma schema for data models
- Review LLM integration code in `app/lib/llm/`

---

*Last Updated: Based on current codebase analysis*
