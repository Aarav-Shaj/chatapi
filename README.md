# ChatAPI - Secure Multi-Provider AI Chat

A secure, privacy-first AI chat application that uses **your own API keys** instead of a central billing account. All usage is billed directly to your AI provider accounts.

![ChatAPI](https://img.shields.io/badge/Security-AES--GCM--256-green)
![ChatAPI](https://img.shields.io/badge/Privacy-Local--First-blue)
![ChatAPI](https://img.shields.io/badge/License-MIT-yellow)

## 🎯 Core Concept

- **Your Keys, Your Control**: Paste in your own AI API keys
- **Direct Routing**: Requests go directly to AI providers using your key
- **Zero Central Billing**: The app never consumes its own credits
- **Maximum Privacy**: Keys encrypted locally, never transmitted to third parties

## ✨ Features

### 🔐 Security First
- **AES-GCM-256 encryption** for API keys at rest
- **PBKDF2 key derivation** with 100,000 iterations
- **Web Crypto API** (FIPS 140-2 compliant)
- **No server-side storage** - everything stays on your device
- **Session-only mode** - keys stored in memory only
- **Auto-lock** after inactivity

### 🤖 Multi-Provider Support
- ✅ **OpenAI** (GPT-4, GPT-3.5)
- ✅ **Anthropic** (Claude 3 Opus, Sonnet, Haiku)
- 🚧 **Google Gemini** (coming soon)
- 🚧 **Mistral AI** (coming soon)
- 🚧 **Custom OpenAI-compatible endpoints**

### 💬 Chat Features
- **Real-time streaming** responses
- **Conversation history** with token tracking
- **Cost estimation** based on provider pricing
- **Multi-model support** - switch models per conversation
- **Token-aware truncation** to stay within context limits

### 📊 Usage Tracking
- **Token count** per message and conversation
- **Cost estimation** using official pricing tables
- **Rate limit handling** with clear error messages
- **Quota warnings** when credits are low

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ and npm
- API keys from your chosen providers

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/chatapi.git
cd chatapi

# Install dependencies
npm install

# Start development server
npm run dev
```

The app will open at `http://localhost:3000`

### First-Time Setup

1. **Choose Storage Mode**
   - **Session Only**: Keys stored in memory, cleared on tab close
   - **Persistent**: Keys encrypted and saved to IndexedDB

2. **Add Your API Keys**
   - Click "Add API Key"
   - Paste your API key (e.g., `sk-...` for OpenAI)
   - Provider is auto-detected
   - Key is validated before saving

3. **Start Chatting**
   - Select a provider and model
   - Type your message
   - Watch the streaming response!

## 🔑 Getting API Keys

### OpenAI
1. Go to [platform.openai.com](https://platform.openai.com)
2. Sign up or log in
3. Navigate to API Keys
4. Create a new secret key
5. Copy and paste into ChatAPI

### Anthropic
1. Go to [console.anthropic.com](https://console.anthropic.com)
2. Sign up or log in
3. Navigate to API Keys
4. Create a new key
5. Copy and paste into ChatAPI

## 🏗️ Architecture

```
┌─────────────────────────────────────────┐
│          User Interface (React)         │
│  - Chat Interface                       │
│  - Key Management                       │
│  - Provider Selection                   │
└─────────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────┐
│      Provider Abstraction Layer         │
│  - Unified Chat Interface               │
│  - Streaming Handler                    │
│  - Error Standardization                │
└─────────────────────────────────────────┐
                  ↓
┌──────────┬──────────┬──────────┬────────┐
│  OpenAI  │ Anthropic│  Google  │ Mistral│
│  Adapter │  Adapter │  Adapter │ Adapter│
└──────────┴──────────┴──────────┴────────┘
                  ↓
┌─────────────────────────────────────────┐
│        Secure Key Storage               │
│  - Web Crypto API (AES-GCM)             │
│  - IndexedDB (encrypted)                │
│  - Session-only option                  │
└─────────────────────────────────────────┘
```

## 🔒 Security Model

### Encryption
- **Algorithm**: AES-GCM-256
- **Key Derivation**: PBKDF2 with 100,000 iterations
- **Random Nonces**: 96-bit nonces for each encryption
- **Salt**: 128-bit random salt per master key

### Storage Options
1. **Persistent Mode**
   - Keys encrypted with master password
   - Stored in IndexedDB
   - Auto-lock after 30 minutes

2. **Session-Only Mode**
   - Keys stored in memory only
   - Cleared on tab close
   - No persistence

### What We DON'T Do
- ❌ No server-side storage
- ❌ No telemetry or analytics
- ❌ No logging of prompts/responses
- ❌ No third-party integrations
- ❌ No proxy servers

## 💰 Cost Transparency

ChatAPI estimates costs using official provider pricing:

| Provider | Model | Input (per 1K tokens) | Output (per 1K tokens) |
|----------|-------|----------------------|------------------------|
| OpenAI | GPT-4 Turbo | $0.01 | $0.03 |
| OpenAI | GPT-3.5 Turbo | $0.0015 | $0.002 |
| Anthropic | Claude 3 Opus | $0.015 | $0.075 |
| Anthropic | Claude 3 Sonnet | $0.003 | $0.015 |
| Anthropic | Claude 3 Haiku | $0.00025 | $0.00125 |

*Prices as of December 2025. Check provider websites for current rates.*

## 🛠️ Development

### Tech Stack
- **Frontend**: React 18 + TypeScript
- **Build Tool**: Vite
- **State Management**: Zustand
- **Storage**: IndexedDB (via idb)
- **Crypto**: Web Crypto API
- **Styling**: Vanilla CSS

### Project Structure
```
src/
├── components/          # React components
│   ├── ChatInterface.tsx
│   ├── KeyManager.tsx
│   ├── UnlockScreen.tsx
│   └── Header.tsx
├── lib/
│   ├── providers/       # AI provider adapters
│   │   ├── types.ts
│   │   ├── openai.ts
│   │   ├── anthropic.ts
│   │   └── index.ts
│   ├── keyStorage.ts    # Secure key storage
│   └── store.ts         # Global state
├── App.tsx
├── main.tsx
└── index.css
```

### Building for Production

```bash
npm run build
```

Output will be in `dist/` directory. Deploy to any static hosting service.

## 🚨 Limitations & Risks

### Known Limitations
1. **Browser Storage**: IndexedDB typically limited to 50MB-1GB
2. **CORS**: Some providers may not allow direct browser calls
3. **No Sync**: Conversations don't sync across devices
4. **Browser Extensions**: Malicious extensions could access memory

### Risk Mitigation
- Use strong master passwords
- Keep browser updated
- Avoid untrusted browser extensions
- Understand keys are stored locally
- Backup conversations manually if needed

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

### Adding a New Provider

1. Create adapter in `src/lib/providers/your-provider.ts`
2. Implement `ChatProvider` interface
3. Add to `src/lib/providers/index.ts`
4. Update pricing tables
5. Add documentation

## 📄 License

MIT License - see [LICENSE](LICENSE) file

## ⚠️ Disclaimer

This app is a tool, not a service. Users are responsible for:
- Securing their own API keys
- Complying with provider Terms of Service
- Managing their own API costs
- Understanding the security model

No warranty or liability for API costs or data loss.

## 🙏 Acknowledgments

- Built with [React](https://react.dev)
- Powered by [Vite](https://vitejs.dev)
- Inspired by privacy-first principles

## 📞 Support

- 📖 [Documentation](ARCHITECTURE.md)
- 🐛 [Report Issues](https://github.com/yourusername/chatapi/issues)
- 💬 [Discussions](https://github.com/yourusername/chatapi/discussions)

---

**Made with ❤️ for privacy-conscious AI users**
