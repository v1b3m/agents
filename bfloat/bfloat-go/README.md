# Bfloat Go - Developer Overview

## Project Summary

**Bfloat Go** is a minimal mobile application version of the main Bfloat builder interface. It enables users to create React Native/Expo mobile applications through a conversational chat interface on iOS and Android devices. This mobile app connects to the same backend infrastructure as the main web application (`bfloat-app-engineer`), providing a streamlined mobile-first experience for AI-powered app generation.

---

## Relationship to Main Application

### Architecture Comparison

| Aspect | Main Web App (`bfloat-app-engineer`) | Mobile App (`bfloat-go`) |
|--------|--------------------------------------|--------------------------|
| **Platform** | Web (React Router v7) | Mobile (React Native/Expo) |
| **UI Framework** | React Router, Radix UI, Tailwind CSS | React Native, Expo Router, NativeWind |
| **State Management** | Nanostores, Zustand, React Query | Nanostores, React Query |
| **Code Editor** | CodeMirror 6 (full editor) | Simplified preview-only |
| **Preview** | Expo Snack SDK (embedded) | Expo Snack SDK (embedded) |
| **Backend** | Same API endpoints | Same API endpoints |
| **Authentication** | Clerk | Clerk (via `@clerk/clerk-expo`) |

### Shared Backend Infrastructure

Both applications connect to the same backend:
- **API Base URL**: Configured via `EXPO_PUBLIC_API_BASE_URL`
- **Authentication**: Clerk authentication with Bearer tokens
- **Endpoints**: `/api/projects`, `/api/chat-new`, `/api/init-stream`, `/api/files`
- **Database**: PostgreSQL (via backend)
- **File Storage**: AWS S3 (via backend)
- **LLM Processing**: Same streaming architecture via Redis/BullMQ

---

## Tech Stack

### Frontend Framework
- **React Native**: 0.79.2 (New Architecture enabled)
- **Expo**: ~53.0.9 (SDK 53)
- **Expo Router**: ~5.0.6 (file-based routing)
- **TypeScript**: 5.8.3 (strict mode)

### UI & Styling
- **NativeWind**: ~4.1.23 (Tailwind CSS for React Native)
- **React Navigation**: v7 (Stack, Tabs, Drawer navigation)
- **React Native Reanimated**: 3.17.5 (animations)
- **@gorhom/bottom-sheet**: 5.1.4 (bottom sheets)
- **Lucide React Native**: Icons

### State Management
- **Nanostores**: Reactive state management (`@nanostores/react`)
- **TanStack Query**: 5.80.2 (server state, caching)
- **Custom Stores**: `workbench.ts`, `files.ts`, `prompt.ts`, `token.ts`

### AI Integration
- **Vercel AI SDK**: `@ai-sdk/react` 2.0.29, `ai` 5.0.29
- **Streaming**: Custom `SwitchableStream` for handling stream switches
- **Message Parsing**: Custom `StreamingMessageParser` for artifact/action parsing

### Authentication & Backend
- **Clerk**: `@clerk/clerk-expo` 2.11.3
- **Custom Fetch**: `useCustomFetch` hook with auth token injection
- **API Client**: Axios-based utilities (`use-get-request`, `use-post-request`)

### Preview & Runtime
- **Bfloat Snack Runtime**: Custom runtime (`bfloat-snack-runtime-0.7.0.tgz`)
- **Bfloat Snack SDK**: Custom SDK (`bfloat-snack-sdk.tgz`)
- **Expo Snack**: Standard Snack SDK integration

### Development Tools
- **Sentry**: Error tracking (`@sentry/react-native`)
- **LogRocket**: Session replay (commented out)
- **PostHog**: Analytics (commented out)
- **Prettier**: Code formatting
- **Patch Package**: For patching dependencies

### Native Features
- **Camera**: `expo-camera` for image capture
- **Audio**: `expo-audio` for voice input
- **Notifications**: `expo-notifications` for push notifications
- **File System**: `expo-file-system` for file operations
- **Media Library**: `expo-media-library` for photo access
- **QR Scanner**: `react-native-qrcode-svg` for QR code scanning

---

## Project Structure

```
bfloat-go/
├── app/                          # Main application code
│   ├── _layout.tsx               # Root layout with providers
│   ├── index.tsx                 # Root redirect
│   ├── (auth)/                   # Authentication routes
│   │   ├── sign-in.tsx
│   │   ├── sign-up.tsx
│   │   ├── reset-code.tsx
│   │   └── new-password.tsx
│   ├── (root)/                   # Authenticated routes
│   │   ├── (home)/               # Home screen
│   │   ├── projects/             # Project management
│   │   │   ├── [projectId].tsx   # Project detail/chat
│   │   │   ├── edit.[projectId].tsx
│   │   │   └── new.tsx           # New project creation
│   │   ├── preview/              # Preview screen
│   │   │   └── [projectId].tsx
│   │   ├── profile/              # User profile
│   │   ├── notifications/        # Notifications
│   │   └── prompt/               # Prompt interface
│   ├── components/               # Reusable components
│   │   ├── chat/                 # Chat interface components
│   │   │   ├── Chat.tsx          # Main chat component
│   │   │   ├── Messages.tsx
│   │   │   ├── UserMessage.tsx
│   │   │   ├── AssistantMessage.tsx
│   │   │   ├── Artifact.tsx      # Artifact display
│   │   │   └── ...
│   │   ├── ui/                   # UI primitives
│   │   ├── ThemeToggle.tsx
│   │   ├── ProfileBottomSheet.tsx
│   │   └── ...
│   ├── hooks/                    # Custom React hooks
│   │   ├── use-projects.ts       # Project fetching
│   │   ├── use-custom-fetch.ts   # Authenticated fetch
│   │   ├── use-transcribe.ts     # Voice transcription
│   │   ├── use-message-parser.ts # Message parsing
│   │   └── ...
│   ├── stores/                   # State stores
│   │   ├── workbench.ts          # Main workbench state
│   │   ├── files.ts              # File management
│   │   ├── prompt.ts             # Prompt state
│   │   └── token.ts              # Token storage
│   ├── lib/                      # Core libraries
│   │   ├── action-runner.ts      # Action execution
│   │   └── message-parser.ts     # Streaming parser
│   ├── screens/                  # Screen components
│   │   ├── HomeScreen.tsx
│   │   ├── ProjectScreen.tsx
│   │   ├── ProjectCreationScreen.tsx
│   │   └── ...
│   ├── utils/                    # Utilities
│   │   ├── api.ts                # API URL generation
│   │   ├── auth.ts               # Auth utilities
│   │   └── toast.ts              # Toast notifications
│   ├── types/                    # TypeScript types
│   │   ├── actions.ts            # Action types
│   │   └── artifact.ts           # Artifact types
│   └── contexts/                 # React contexts
│       ├── ThemeContext.tsx
│       └── ProfileBottomSheetContext.tsx
├── assets/                       # Static assets
│   ├── fonts/                    # Custom fonts
│   └── images/                   # Images and icons
├── ios/                          # iOS native code
│   └── BfloatGo/                 # iOS app configuration
├── app.json                      # Expo configuration
├── package.json                  # Dependencies
├── tsconfig.json                 # TypeScript config
├── tailwind.config.js            # Tailwind config
└── metro.config.js               # Metro bundler config
```

---

## Key Features

### 1. Chat-Based App Creation
- **Conversational Interface**: Natural language input for app requirements
- **Streaming Responses**: Real-time AI responses via Vercel AI SDK
- **Voice Input**: Voice transcription using `expo-audio` and backend transcription
- **Image Attachments**: Upload images/mockups to guide app generation
- **Message Parsing**: Custom parser for `<bfloatArtifact>` and `<bfloatAction>` tags

### 2. Project Management
- **Project List**: View all user projects with favorites
- **Project Creation**: Create new projects via chat interface
- **Project Editing**: Edit existing projects through chat
- **Project Preview**: Live preview using Snack SDK
- **Project Sharing**: Share projects via deep links

### 3. Live Preview
- **Snack Integration**: Embedded Snack runtime for live preview
- **Hot Reloading**: Real-time code updates
- **Screenshot Capture**: Capture app screenshots
- **Cross-platform**: Preview on iOS, Android, Web

### 4. Authentication & User Management
- **Clerk Integration**: Full authentication flow
- **Profile Management**: User profile and settings
- **Push Notifications**: Expo push notifications for project updates
- **Deep Linking**: Handle deep links for project navigation

### 5. State Management Architecture

#### Workbench Store (`stores/workbench.ts`)
Central store managing:
- **Artifacts**: Collection of code artifacts from AI
- **Files**: Project file structure and content
- **Actions**: File operations and shell commands
- **Screenshots**: Captured app screenshots
- **Streaming State**: Chat streaming status

#### Files Store (`stores/files.ts`)
Manages project files:
- File CRUD operations
- File content tracking
- Unsaved changes tracking

#### Prompt Store (`stores/prompt.ts`)
Manages chat prompt state:
- Error handling
- Prompt history

---

## Data Flow

### Chat → Code Generation Flow

```
User Input (Chat)
    ↓
Chat Component (useChat hook)
    ↓
Custom Fetch (with Clerk token)
    ↓
Backend API (/api/chat-new)
    ↓
Backend: LLM Processing (Redis Streams/BullMQ)
    ↓
Streaming Response (SSE/Stream)
    ↓
Message Parser (StreamingMessageParser)
    ↓
Artifact/Action Extraction
    ↓
Workbench Store Update
    ↓
Action Runner Execution
    ↓
File Updates
    ↓
Snack Preview Refresh
```

### Project Creation Flow

```
User: "Create a todo app"
    ↓
New Project Screen
    ↓
POST /api/projects (with prompt + files)
    ↓
Backend: Create Project in DB
    ↓
Return: { projectId, messages }
    ↓
POST /api/init-stream (initiate LLM stream)
    ↓
Navigate to Project Screen
    ↓
Chat Component: Start streaming
    ↓
Real-time Code Generation
```

### File Update Flow

```
AI Action: <bfloatAction type="file" filePath="App.tsx">
    ↓
Message Parser: Extract action
    ↓
Workbench Store: addAction()
    ↓
Action Runner: Execute file update
    ↓
Files Store: updateFile()
    ↓
Snack SDK: Update preview
```

---

## API Integration

### Authentication
All API requests include Clerk authentication token:

```typescript
// useCustomFetch hook automatically injects token
const fetch = useCustomFetch();
const response = await fetch(generateAPIUrl("/api/projects"), {
  method: "POST",
  body: formData,
});
```

### Key Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/projects` | GET | Fetch user projects |
| `/api/projects` | POST | Create new project |
| `/api/projects/[id]` | GET | Get project details |
| `/api/projects/[id]` | PATCH | Update project |
| `/api/projects/[id]` | DELETE | Delete project |
| `/api/chat-new` | POST | Chat with AI (streaming) |
| `/api/init-stream` | POST | Initiate LLM stream |
| `/api/files` | POST | Upload files |

### API URL Generation
Development: Uses `Constants.experienceUrl` or `localhost:3000`
Production: Uses `EXPO_PUBLIC_API_BASE_URL`

---

## State Management Patterns

### Nanostores Usage
```typescript
// Reactive atoms
const chatStreaming = atom(false);
const artifacts = map<Record<string, ArtifactState>>({});

// Subscribe in components
const isStreaming = useStore(workbenchStore.chatStreaming);
```

### React Query Usage
```typescript
// Server state caching
const { data, isLoading } = useQuery({
  queryKey: ["projects", userChanged],
  queryFn: fetchProjects,
  staleTime: 5 * 60 * 1000,
});
```

### Custom Stores
- **WorkbenchStore**: Singleton class managing workbench state
- **FilesStore**: File management with CRUD operations
- **PromptStore**: Prompt state and error handling

---

## Message Parsing System

### Artifact & Action Tags
The backend streams responses with special tags:

```xml
<bfloatArtifact id="artifact-1" title="App.tsx">
  <bfloatAction type="file" filePath="App.tsx">
    // File content here
  </bfloatAction>
</bfloatArtifact>
```

### StreamingMessageParser
- Parses streaming text incrementally
- Extracts artifacts and actions
- Calls callbacks for artifact/action events
- Maintains state per message ID

### Action Runner
- Executes file operations
- Manages action lifecycle (pending → running → complete)
- Tracks action completion
- Handles action reruns

---

## Navigation Structure

### Route Hierarchy
```
/ (root)
├── /(auth)          # Unauthenticated routes
│   ├── /sign-in
│   ├── /sign-up
│   └── ...
└── /(root)          # Authenticated routes
    ├── /(home)      # Home tab
    ├── /projects    # Projects tab
    │   ├── /new
    │   └── /[projectId]
    ├── /preview     # Preview tab
    ├── /profile     # Profile tab
    └── /notifications
```

### Navigation Types
- **Stack Navigation**: Project detail screens
- **Tab Navigation**: Main app tabs
- **Drawer Navigation**: Side menu (if enabled)

---

## Development Setup

### Prerequisites
- Node.js >= 18
- Expo CLI
- iOS Simulator (macOS) or Android Studio
- Backend API running (or `EXPO_PUBLIC_API_BASE_URL` configured)

### Environment Variables

```env
# Authentication
EXPO_PUBLIC_CLERK_PUBLISHABLE_KEY=...

# API
EXPO_PUBLIC_API_BASE_URL=https://api.bfloat.ai
EXPO_PUBLIC_ENV=production

# Error Tracking
EXPO_PUBLIC_SENTRY_DSN=...

# Analytics (optional)
EXPO_PUBLIC_POSTHOG_API_KEY=...
```

### Installation

```bash
# Install dependencies
npm install

# Start development server
npm start

# Run on iOS
npm run ios

# Run on Android
npm run android

# Run on Web
npm run web
```

### Development Scripts
- `npm start` - Start Expo dev server
- `npm run ios` - Run on iOS simulator
- `npm run android` - Run on Android emulator
- `npm run web` - Run on web
- `npm run format` - Format code with Prettier

---

## Key Components

### Chat Component (`components/chat/Chat.tsx`)
- Main chat interface using `useChat` hook
- Handles streaming responses
- Integrates with message parser
- Manages artifact/action display

### Project Screen (`screens/ProjectScreen.tsx`)
- Project detail view
- Chat interface integration
- File tree navigation
- Preview integration

### Workbench Store (`stores/workbench.ts`)
- Central state management
- Artifact lifecycle
- Action execution
- File synchronization

### Message Parser (`lib/message-parser.ts`)
- Streaming text parsing
- Artifact extraction
- Action extraction
- Callback system

---

## Differences from Web App

### Simplified Features
- **No Code Editor**: Mobile app focuses on chat and preview
- **No File Tree Editing**: Files managed through AI actions only
- **Simplified UI**: Mobile-optimized interface
- **Touch-First**: Optimized for touch interactions

### Mobile-Specific Features
- **Voice Input**: Native voice transcription
- **Camera Integration**: Image capture for prompts
- **Push Notifications**: Native push notifications
- **Deep Linking**: Handle app deep links
- **Offline Support**: Better offline handling (future)

### Navigation Differences
- **Tab Navigation**: Mobile-first tab navigation
- **Bottom Sheets**: Mobile UI patterns
- **Gesture Navigation**: Native gesture support

---

## Testing & Debugging

### Development Tools
- **React Native Debugger**: Debug React Native code
- **Flipper**: Advanced debugging (if configured)
- **Sentry**: Error tracking in production
- **Console Logging**: `logger.ts` for structured logging

### Common Issues
- **API Connection**: Check `EXPO_PUBLIC_API_BASE_URL`
- **Auth Token**: Verify Clerk token injection
- **Streaming**: Check network connectivity
- **Preview**: Verify Snack SDK integration

---

## Deployment

### Build Configuration
- **iOS**: Configured via `app.json` and `ios/` directory
- **Android**: Configured via `app.json` and `android/` directory
- **EAS**: Expo Application Services for builds

### Build Commands
```bash
# Build for iOS
eas build --platform ios

# Build for Android
eas build --platform android

# Submit to stores
eas submit --platform ios
eas submit --platform android
```

---

## Future Enhancements

Based on the codebase structure, potential enhancements:
- **Offline Mode**: Cache projects and work offline
- **Code Editor**: Add mobile code editor
- **Collaboration**: Real-time collaboration features
- **Templates**: Pre-built app templates
- **Export**: Export projects as standalone apps
- **Analytics**: Enhanced usage analytics

---

## Key Files Reference

### Entry Points
- `app/_layout.tsx` - Root layout with providers
- `app/index.tsx` - Root redirect
- `index.ts` - Expo entry point

### Core Logic
- `app/stores/workbench.ts` - Main state store
- `app/lib/message-parser.ts` - Message parsing
- `app/lib/action-runner.ts` - Action execution
- `app/components/chat/Chat.tsx` - Chat interface

### API Integration
- `app/utils/api.ts` - API URL generation
- `app/hooks/use-custom-fetch.ts` - Authenticated fetch
- `app/hooks/use-projects.ts` - Project management

### Navigation
- `app/(root)/_layout.tsx` - Root navigation layout
- `app/(root)/(home)/index.tsx` - Home screen
- `app/(root)/projects/[projectId].tsx` - Project screen

---

## Resources

- **Expo Docs**: https://docs.expo.dev
- **React Native Docs**: https://reactnative.dev
- **Expo Router Docs**: https://docs.expo.dev/router/introduction
- **NativeWind Docs**: https://www.nativewind.dev
- **Vercel AI SDK**: https://sdk.vercel.ai
- **Clerk Docs**: https://clerk.com/docs

---

- [Bfloat Go Local Codebase](file:///Users/v1b3m/Dev/bfloat/bfloat-go/)
- [About backend and web app](../bfloat-app-engineer/README.md)
