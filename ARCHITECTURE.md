# ChatAPI - Multi-Provider AI Chat Application
## Architecture & Security Design

---

## 1. System Architecture

### 1.1 Architecture Type: **Client-Side First with Optional Local Backend**

**Primary Mode: Pure Frontend (Browser-Based)**
- All API calls made directly from browser to AI providers
- Keys stored in browser's encrypted storage (IndexedDB with Web Crypto API)
- Zero server-side components
- Works offline for key management, online for chat

**Optional Mode: Local Electron/Tauri App**
- For users wanting OS-level encryption
- Keys stored in OS keychain (Windows Credential Manager, macOS Keychain, Linux Secret Service)
- Still no remote servers

### 1.2 Component Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        User Interface                        │
│  (React/Vue + TypeScript)                                   │
│  - Chat Interface                                           │
│  - Key Management UI                                        │
│  - Provider Selection                                       │
│  - Usage Dashboard                                          │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                   Provider Abstraction Layer                │
│  - Unified Chat Interface                                   │
│  - Request/Response Normalization                           │
│  - Streaming Handler                                        │
│  - Error Standardization                                    │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────┬──────────────┬──────────────┬──────────────┐
│   OpenAI     │  Anthropic   │   Google     │   Mistral    │
│   Adapter    │   Adapter    │   Adapter    │   Adapter    │
└──────────────┴──────────────┴──────────────┴──────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                    Secure Key Storage                        │
│  - Web Crypto API (AES-GCM encryption)                      │
│  - IndexedDB (encrypted blobs)                              │
│  - Session-only option (memory only)                        │
│  - Master password derivation (PBKDF2)                      │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Security Model

### 2.1 Key Storage Strategy

#### **Encryption at Rest**
```typescript
// Master key derivation from user password
masterKey = PBKDF2(userPassword, salt, 100000 iterations, SHA-256)

// Per-provider key encryption
encryptedKey = AES-GCM-256(apiKey, masterKey, nonce)

// Storage format
{
  provider: "openai",
  encryptedKey: base64(encryptedKey),
  nonce: base64(nonce),
  salt: base64(salt),
  createdAt: timestamp
}
```

#### **Storage Options**

1. **Persistent (IndexedDB)**
   - Keys encrypted with master password
   - User must unlock on each session
   - Auto-lock after inactivity

2. **Session-Only (Memory)**
   - Keys stored in memory only
   - Cleared on tab close
   - No persistence

3. **Local-Only Mode**
   - Keys never leave device
   - No network calls except to AI providers
   - Can work offline for key management

### 2.2 Security Constraints (Non-Negotiable)

✅ **Implemented Safeguards:**
- Keys encrypted with Web Crypto API (FIPS 140-2 compliant)
- No server-side storage or transmission
- No analytics or telemetry
- No logging of keys, prompts, or responses
- CORS-safe: Direct API calls to providers
- Content Security Policy (CSP) headers
- No third-party scripts or CDNs (all bundled)

❌ **Prohibited:**
- Sending keys to any proxy server
- Logging prompts or responses
- Analytics tied to API usage
- Third-party integrations that access keys
- Unencrypted storage

### 2.3 Threat Model

| Threat | Mitigation |
|--------|-----------|
| Key theft from storage | AES-GCM encryption with user password |
| Man-in-the-middle | HTTPS only, certificate pinning (optional) |
| XSS attacks | CSP, input sanitization, no eval() |
| Memory dumps | Session-only mode, auto-lock |
| Malicious dependencies | Minimal deps, SRI hashes, audit |
| Browser extensions | Warn users, use isolated contexts |

---

## 3. API Abstraction Strategy

### 3.1 Provider Interface

```typescript
interface ChatProvider {
  name: string;
  validateKey(key: string): Promise<boolean>;
  listModels(key: string): Promise<Model[]>;
  sendMessage(request: ChatRequest): AsyncIterator<ChatChunk>;
  estimateCost(tokens: TokenUsage): number;
}

interface ChatRequest {
  model: string;
  messages: Message[];
  apiKey: string;
  temperature?: number;
  maxTokens?: number;
  stream: boolean;
}

interface ChatChunk {
  delta: string;
  tokens?: TokenUsage;
  finishReason?: string;
}
```

### 3.2 Supported Providers

| Provider | Authentication | Streaming | Cost Estimation |
|----------|---------------|-----------|-----------------|
| OpenAI | Bearer token | SSE | ✅ (official pricing) |
| Anthropic | x-api-key header | SSE | ✅ (official pricing) |
| Google Gemini | API key param | SSE | ✅ (official pricing) |
| Mistral | Bearer token | SSE | ✅ (official pricing) |
| OpenAI-Compatible | Configurable | SSE | ⚠️ (user-defined) |

### 3.3 OpenAI-Compatible Support

Users can add custom endpoints:
```typescript
{
  name: "Custom LLM",
  baseURL: "https://api.custom.ai/v1",
  authType: "bearer" | "header" | "query",
  authKey: "user-provided-key",
  models: ["model-1", "model-2"]
}
```

---

## 4. Key Management Flow

### 4.1 First-Time Setup

```
1. User opens app
2. Prompted to create master password (optional for session-only)
3. Choose storage mode:
   - Persistent (requires password)
   - Session-only (no password needed)
4. Add first API key
5. Auto-detect provider from key format
6. Validate key with test request
7. Store encrypted key
```

### 4.2 Key Auto-Detection

```typescript
function detectProvider(key: string): Provider {
  if (key.startsWith('sk-')) return 'openai';
  if (key.startsWith('sk-ant-')) return 'anthropic';
  if (key.match(/^[A-Za-z0-9_-]{39}$/)) return 'google';
  if (key.length === 32) return 'mistral';
  return 'unknown'; // Prompt user to select
}
```

### 4.3 Key Validation

Each provider adapter implements:
```typescript
async validateKey(key: string): Promise<boolean> {
  try {
    // Make minimal API call (e.g., list models)
    await this.listModels(key);
    return true;
  } catch (error) {
    return false;
  }
}
```

---

## 5. Chat Functionality

### 5.1 Message Flow

```
User Input → Provider Adapter → AI API → Stream Handler → UI Update
     ↓                                                          ↓
Token Counter ← Response Parser ← Chunk Processor ← SSE Reader
     ↓
Cost Estimator → Usage Display
```

### 5.2 Conversation Management

```typescript
interface Conversation {
  id: string;
  title: string;
  provider: string;
  model: string;
  messages: Message[];
  tokenUsage: TokenUsage;
  estimatedCost: number;
  createdAt: Date;
  updatedAt: Date;
}

interface Message {
  role: 'system' | 'user' | 'assistant';
  content: string;
  tokens?: number;
  timestamp: Date;
}
```

### 5.3 Token-Aware Truncation

```typescript
// Keep conversation within context window
function truncateConversation(
  messages: Message[],
  maxTokens: number
): Message[] {
  let totalTokens = 0;
  const result: Message[] = [];
  
  // Always keep system message
  if (messages[0]?.role === 'system') {
    result.push(messages[0]);
    totalTokens += messages[0].tokens || 0;
  }
  
  // Add messages from newest to oldest
  for (let i = messages.length - 1; i >= 0; i--) {
    const msg = messages[i];
    if (msg.role === 'system') continue;
    
    if (totalTokens + (msg.tokens || 0) > maxTokens) break;
    
    result.unshift(msg);
    totalTokens += msg.tokens || 0;
  }
  
  return result;
}
```

### 5.4 Streaming Implementation

```typescript
async function* streamChat(request: ChatRequest) {
  const response = await fetch(endpoint, {
    method: 'POST',
    headers: getHeaders(request.apiKey),
    body: JSON.stringify(request)
  });
  
  const reader = response.body!.getReader();
  const decoder = new TextDecoder();
  
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    
    const chunk = decoder.decode(value);
    const lines = chunk.split('\n');
    
    for (const line of lines) {
      if (line.startsWith('data: ')) {
        const data = JSON.parse(line.slice(6));
        yield parseChunk(data);
      }
    }
  }
}
```

---

## 6. Credit & Usage Handling

### 6.1 Token Tracking

```typescript
interface TokenUsage {
  promptTokens: number;
  completionTokens: number;
  totalTokens: number;
}

// Track per message and per conversation
class UsageTracker {
  private conversationUsage = new Map<string, TokenUsage>();
  
  addUsage(conversationId: string, usage: TokenUsage) {
    const current = this.conversationUsage.get(conversationId) || {
      promptTokens: 0,
      completionTokens: 0,
      totalTokens: 0
    };
    
    this.conversationUsage.set(conversationId, {
      promptTokens: current.promptTokens + usage.promptTokens,
      completionTokens: current.completionTokens + usage.completionTokens,
      totalTokens: current.totalTokens + usage.totalTokens
    });
  }
}
```

### 6.2 Cost Estimation

```typescript
// Provider pricing tables (updated periodically)
const PRICING = {
  openai: {
    'gpt-4': { prompt: 0.03, completion: 0.06 }, // per 1K tokens
    'gpt-3.5-turbo': { prompt: 0.0015, completion: 0.002 }
  },
  anthropic: {
    'claude-3-opus': { prompt: 0.015, completion: 0.075 },
    'claude-3-sonnet': { prompt: 0.003, completion: 0.015 }
  },
  google: {
    'gemini-pro': { prompt: 0.00025, completion: 0.0005 }
  }
};

function estimateCost(
  provider: string,
  model: string,
  usage: TokenUsage
): number {
  const pricing = PRICING[provider]?.[model];
  if (!pricing) return 0;
  
  return (
    (usage.promptTokens / 1000) * pricing.prompt +
    (usage.completionTokens / 1000) * pricing.completion
  );
}
```

### 6.3 Error Handling

```typescript
class APIError extends Error {
  constructor(
    public code: string,
    public message: string,
    public provider: string,
    public retryable: boolean
  ) {
    super(message);
  }
}

// Standardized error codes
const ERROR_CODES = {
  INVALID_KEY: 'invalid_api_key',
  RATE_LIMIT: 'rate_limit_exceeded',
  INSUFFICIENT_CREDITS: 'insufficient_quota',
  CONTEXT_LENGTH: 'context_length_exceeded',
  NETWORK: 'network_error',
  UNKNOWN: 'unknown_error'
};

// User-friendly error messages
function formatError(error: APIError): string {
  switch (error.code) {
    case ERROR_CODES.INVALID_KEY:
      return `Invalid API key for ${error.provider}. Please check your key.`;
    case ERROR_CODES.RATE_LIMIT:
      return `Rate limit exceeded for ${error.provider}. Please wait and try again.`;
    case ERROR_CODES.INSUFFICIENT_CREDITS:
      return `Insufficient credits on your ${error.provider} account.`;
    case ERROR_CODES.CONTEXT_LENGTH:
      return `Message too long. Try shortening your conversation.`;
    default:
      return `Error: ${error.message}`;
  }
}
```

---

## 7. UX Requirements

### 7.1 Key Management UI

**Add API Key Screen:**
- Provider selection (auto-detected or manual)
- Key input (masked by default)
- Validation indicator (real-time)
- Nickname/label for key
- Test connection button

**Key List:**
- Provider icon + nickname
- Status indicator (valid/invalid/untested)
- Last used timestamp
- Delete/edit actions

### 7.2 Chat Interface

**Layout:**
```
┌─────────────────────────────────────────────────────┐
│  [Provider] [Model ▼]              [⚙️] [🔑]       │
├─────────────────────────────────────────────────────┤
│                                                     │
│  💬 Previous conversations                          │
│  ├─ Conversation 1                                  │
│  └─ Conversation 2                                  │
│                                                     │
│  ┌───────────────────────────────────────────────┐ │
│  │ User: Hello!                                  │ │
│  │ 🕐 2:30 PM • 5 tokens                         │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
│  ┌───────────────────────────────────────────────┐ │
│  │ Assistant: Hi! How can I help?                │ │
│  │ 🕐 2:30 PM • 12 tokens • $0.0001              │ │
│  └───────────────────────────────────────────────┘ │
│                                                     │
├─────────────────────────────────────────────────────┤
│  [Type your message...]                    [Send]  │
│  Tokens: 0 • Est. cost: $0.00                      │
└─────────────────────────────────────────────────────┘
```

### 7.3 Active Provider Indicator

- Colored badge in header
- Provider logo
- Model name
- Quick-switch dropdown

---

## 8. Implementation Plan

### Phase 1: Core Infrastructure (Week 1)
- [ ] Project setup (Vite + React + TypeScript)
- [ ] Web Crypto API key encryption
- [ ] IndexedDB storage layer
- [ ] Provider abstraction interface

### Phase 2: Provider Adapters (Week 2)
- [ ] OpenAI adapter + streaming
- [ ] Anthropic adapter + streaming
- [ ] Google Gemini adapter + streaming
- [ ] Mistral adapter + streaming
- [ ] OpenAI-compatible custom endpoint

### Phase 3: UI Components (Week 3)
- [ ] Key management UI
- [ ] Chat interface
- [ ] Message rendering
- [ ] Streaming display
- [ ] Usage dashboard

### Phase 4: Features & Polish (Week 4)
- [ ] Conversation management
- [ ] Token counting
- [ ] Cost estimation
- [ ] Error handling
- [ ] Export/import conversations
- [ ] Dark mode
- [ ] Responsive design

### Phase 5: Security Audit & Testing
- [ ] Security review
- [ ] Penetration testing
- [ ] Dependency audit
- [ ] Performance optimization

---

## 9. Limitations & Risks

### 9.1 Known Limitations

1. **Browser Storage Limits**
   - IndexedDB typically limited to 50MB-1GB
   - Large conversation histories may need pruning

2. **CORS Restrictions**
   - Some providers may not allow direct browser calls
   - Workaround: Browser extension or local proxy mode

3. **Key Security**
   - Browser memory can be inspected by extensions
   - Recommend: Warn users about malicious extensions

4. **No Server-Side Features**
   - No conversation sync across devices
   - No cloud backup (by design)

### 9.2 Risk Assessment

| Risk | Severity | Mitigation |
|------|----------|------------|
| Key theft via XSS | High | CSP, input sanitization, code review |
| Browser extension access | Medium | User warning, isolated context |
| Phishing (fake app) | Medium | Domain verification, open source |
| Dependency vulnerabilities | Medium | Minimal deps, regular audits |
| Provider API changes | Low | Adapter pattern, version pinning |

### 9.3 User Responsibilities

Users must:
- Use strong master passwords
- Keep browser updated
- Avoid malicious extensions
- Understand that keys are stored locally
- Backup conversations manually if needed

---

## 10. Technology Stack

### Frontend
- **Framework:** React 18 + TypeScript
- **Build Tool:** Vite
- **Styling:** Vanilla CSS (modern, responsive)
- **State Management:** Zustand (lightweight)
- **Storage:** IndexedDB (via idb library)
- **Crypto:** Web Crypto API (native)

### Development
- **Linting:** ESLint + TypeScript ESLint
- **Formatting:** Prettier
- **Testing:** Vitest + React Testing Library
- **E2E:** Playwright

### Security
- **CSP:** Strict Content Security Policy
- **SRI:** Subresource Integrity for any CDN assets
- **HTTPS:** Required for production

---

## 11. Future Enhancements

### Optional Features
- [ ] Conversation export (JSON, Markdown)
- [ ] Prompt templates library
- [ ] Multi-model comparison (parallel requests)
- [ ] Voice input/output
- [ ] Image generation support (DALL-E, Midjourney)
- [ ] Plugin system for custom providers
- [ ] Electron/Tauri desktop app
- [ ] Mobile app (React Native)
- [ ] End-to-end encrypted cloud sync (optional)

### Advanced Security
- [ ] Hardware security key support (WebAuthn)
- [ ] Biometric unlock
- [ ] Secure enclave storage (desktop)
- [ ] Zero-knowledge architecture for cloud sync

---

## 12. Compliance & Privacy

### Data Handling
- **No data collection:** App collects zero user data
- **No analytics:** No tracking, no telemetry
- **No third parties:** Direct API calls only
- **User control:** Users own all data

### GDPR Compliance
- ✅ Data minimization (no data collected)
- ✅ Right to erasure (user controls storage)
- ✅ Data portability (export feature)
- ✅ Privacy by design (core principle)

### Terms of Service
- App is a tool, not a service
- Users responsible for API key security
- Users must comply with provider ToS
- No warranty or liability for API costs

---

## Conclusion

This architecture prioritizes:
1. **Security:** Military-grade encryption, zero trust
2. **Privacy:** No data collection, local-first
3. **Transparency:** Open source, auditable
4. **User Control:** Users own their keys and data
5. **Simplicity:** Clean UX, minimal dependencies

The app acts as a secure, transparent interface to AI providers, never touching user credits or data beyond what's necessary for the chat experience.
