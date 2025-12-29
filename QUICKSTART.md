# Quick Start Guide

## For Users

### 1. Install Node.js
Download and install from [nodejs.org](https://nodejs.org) (LTS version recommended)

### 2. Install Dependencies
```bash
cd C:\Users\aarav\OneDrive\Desktop\ChatAPI
npm install
```

### 3. Start the App
```bash
npm run dev
```

### 4. Open Browser
Navigate to `http://localhost:3000`

### 5. Setup
1. Choose storage mode (Session or Persistent)
2. Click "Add API Key"
3. Paste your OpenAI or Anthropic API key
4. Start chatting!

---

## For Developers

### Project Commands

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Lint code
npm run lint
```

### Adding a New Provider

1. **Create adapter file**: `src/lib/providers/your-provider.ts`

```typescript
import type { ChatProvider, ChatRequest, ChatChunk, Model } from './types';

export class YourProvider implements ChatProvider {
  name = 'your-provider';
  displayName = 'Your Provider';
  icon = '🤖';

  detectKey(key: string): boolean {
    // Implement key detection logic
    return key.startsWith('your-prefix-');
  }

  async validateKey(key: string): Promise<boolean> {
    // Implement key validation
  }

  async listModels(key: string): Promise<Model[]> {
    // Return available models
  }

  async *sendMessage(request: ChatRequest): AsyncGenerator<ChatChunk> {
    // Implement streaming chat
  }

  estimateCost(model: string, usage: TokenUsage): number {
    // Calculate cost
  }
}
```

2. **Register provider**: Add to `src/lib/providers/index.ts`

```typescript
import { YourProvider } from './your-provider';

export const providers: ChatProvider[] = [
  new OpenAIProvider(),
  new AnthropicProvider(),
  new YourProvider(), // Add here
];
```

3. **Test**: Add your API key and verify it works!

### Project Structure

```
src/
├── components/       # React components
├── lib/
│   ├── providers/   # AI provider adapters
│   ├── keyStorage.ts # Encryption & storage
│   └── store.ts     # Global state
├── App.tsx          # Main app
└── index.css        # Design system
```

### Key Technologies

- **React 18**: UI framework
- **TypeScript**: Type safety
- **Zustand**: State management
- **Web Crypto API**: Encryption
- **IndexedDB**: Local storage
- **Vite**: Build tool

### Development Tips

1. **Hot Reload**: Changes auto-refresh
2. **TypeScript**: Strict mode enabled
3. **Console**: Check for errors
4. **Network Tab**: Monitor API calls
5. **React DevTools**: Debug state

### Deployment

```bash
# Build
npm run build

# Output in dist/
# Deploy to:
# - Vercel: vercel deploy
# - Netlify: netlify deploy
# - GitHub Pages: gh-pages -d dist
```

---

## Troubleshooting

### "npm: command not found"
→ Install Node.js first

### "Module not found"
→ Run `npm install`

### "Port 3000 already in use"
→ Change port in `vite.config.ts` or kill the process

### Build errors
→ Delete `node_modules` and `package-lock.json`, then `npm install`

---

## Need Help?

- 📖 [README](README.md)
- 📚 [User Guide](USER_GUIDE.md)
- 🏗️ [Architecture](ARCHITECTURE.md)
- 🔒 [Security](SECURITY.md)

---

**Happy coding! 🚀**
