# ChatAPI User Guide

## Table of Contents

1. [Getting Started](#getting-started)
2. [Storage Modes](#storage-modes)
3. [Managing API Keys](#managing-api-keys)
4. [Using the Chat Interface](#using-the-chat-interface)
5. [Understanding Costs](#understanding-costs)
6. [Troubleshooting](#troubleshooting)
7. [FAQ](#faq)

---

## Getting Started

### First Launch

When you first open ChatAPI, you'll see the unlock screen with two options:

#### Option 1: Session Only ⚡
- Keys stored in memory only
- Cleared when you close the tab
- No password required
- Best for: Shared computers, one-time use

#### Option 2: Persistent 💾
- Keys encrypted and saved
- Requires master password
- Survives browser restarts
- Best for: Personal devices, regular use

**Choose your mode and click "Continue"**

---

## Storage Modes

### Session-Only Mode

**Pros:**
- No password to remember
- Maximum security (nothing persisted)
- Perfect for public computers

**Cons:**
- Keys lost on tab close
- Must re-enter keys each session

**When to use:**
- Using a shared computer
- Testing the app
- Maximum paranoia mode

### Persistent Mode

**Pros:**
- Keys saved between sessions
- Convenient for daily use
- One-time setup

**Cons:**
- Requires strong password
- Keys stored on device

**When to use:**
- Personal computer
- Regular AI chat usage
- Want conversation history

---

## Managing API Keys

### Adding Your First Key

1. Click **"Add API Key"**
2. Paste your API key (e.g., `sk-...`)
3. Provider is auto-detected
4. (Optional) Add a nickname
5. Click **"Add Key"**

The app will validate your key before saving.

### Supported Providers

#### OpenAI
- **Key format**: `sk-...`
- **Get keys**: [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Models**: GPT-4, GPT-3.5 Turbo

#### Anthropic
- **Key format**: `sk-ant-...`
- **Get keys**: [console.anthropic.com](https://console.anthropic.com)
- **Models**: Claude 3 Opus, Sonnet, Haiku

### Managing Multiple Keys

You can add keys from multiple providers:

- Each provider can have one key
- Switch between providers in the header
- Keys are validated on addition
- Delete keys anytime from Key Manager

### Deleting a Key

1. Click **"Manage Keys"** in header
2. Find the key card
3. Click the 🗑️ delete button
4. Confirm deletion

**Warning**: This cannot be undone!

---

## Using the Chat Interface

### Starting a Conversation

1. Click **"New Chat"** in sidebar
2. Type your message in the input box
3. Press **Enter** to send (Shift+Enter for new line)
4. Watch the AI respond in real-time!

### Chat Features

#### Streaming Responses
- Responses appear word-by-word
- See the AI "thinking" in real-time
- Cancel anytime (coming soon)

#### Conversation History
- All conversations saved in sidebar
- Click to switch between chats
- Auto-titled from first message
- Delete conversations anytime

#### Token Tracking
- See token count per message
- Total tokens per conversation
- Estimated cost in real-time

### Switching Models

1. Click provider badge in header
2. Select different provider/model
3. New messages use new model
4. Existing conversations unchanged

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| Enter | Send message |
| Shift+Enter | New line |
| Ctrl+N | New chat (coming soon) |
| Ctrl+K | Manage keys (coming soon) |

---

## Understanding Costs

### How Costs Work

ChatAPI estimates costs based on:
1. **Tokens used** (input + output)
2. **Model pricing** (from provider)
3. **Official pricing tables**

**Example:**
```
GPT-4 Turbo:
- Input: 100 tokens × $0.01/1K = $0.001
- Output: 200 tokens × $0.03/1K = $0.006
- Total: $0.007
```

### Viewing Costs

**Per Message:**
- Token count shown below each message
- Hover for breakdown (coming soon)

**Per Conversation:**
- Total cost in sidebar
- Total tokens tracked
- Updated in real-time

**Overall:**
- No overall tracking (by design)
- Each conversation independent
- Export data for your own analysis

### Cost Optimization Tips

1. **Use cheaper models** for simple tasks
   - GPT-3.5 Turbo vs GPT-4
   - Claude Haiku vs Opus

2. **Shorter prompts** = lower costs
   - Be concise
   - Avoid unnecessary context

3. **Clear conversations** when done
   - Reduces context sent
   - Saves tokens

4. **Monitor usage** on provider dashboard
   - Set spending limits
   - Get alerts

---

## Troubleshooting

### "Invalid API Key"

**Causes:**
- Typo in key
- Key revoked by provider
- Insufficient permissions

**Solutions:**
1. Double-check the key
2. Generate new key from provider
3. Check provider dashboard

### "Rate Limit Exceeded"

**Causes:**
- Too many requests
- Provider tier limits

**Solutions:**
1. Wait a few minutes
2. Upgrade provider plan
3. Use different provider

### "Insufficient Credits"

**Causes:**
- No credits on account
- Spending limit reached

**Solutions:**
1. Add credits to provider account
2. Check billing settings
3. Increase spending limit

### "Context Length Exceeded"

**Causes:**
- Conversation too long
- Message too large

**Solutions:**
1. Start new conversation
2. Shorten your message
3. Use model with larger context

### App Won't Unlock

**Causes:**
- Wrong password
- Corrupted storage

**Solutions:**
1. Try password again
2. Clear browser data
3. Use session-only mode

---

## FAQ

### Is my data safe?

Yes! Your API keys are:
- Encrypted with AES-GCM-256
- Never sent to our servers (we don't have any!)
- Only transmitted to AI providers
- Stored locally on your device

### Can I use this offline?

Partially:
- Key management works offline
- Chat requires internet (to reach AI providers)
- No server needed

### Do you track my usage?

**Absolutely not!**
- No analytics
- No telemetry
- No logging
- No tracking

### Can I sync across devices?

Not currently. This is by design for security.

**Workarounds:**
- Export conversations (coming soon)
- Use same keys on multiple devices
- Cloud sync (optional, coming soon)

### What happens to my conversations?

- Stored locally in browser
- Never sent to us
- You can export/delete anytime
- Cleared if you clear browser data

### Can I export my data?

Coming soon! You'll be able to:
- Export conversations as JSON
- Export as Markdown
- Import previous conversations

### Is this free?

The app is free, but:
- You pay for API usage
- Billed by AI providers
- No markup from us

### Can I self-host?

Yes! It's just a static website:
1. Build: `npm run build`
2. Deploy `dist/` folder anywhere
3. Works on any static host

### How do I update?

1. Pull latest code
2. Run `npm install`
3. Run `npm run build`
4. Deploy

Or wait for auto-updates (if using hosted version)

### Can I contribute?

Yes! See [CONTRIBUTING.md](CONTRIBUTING.md)

### Who made this?

Built with ❤️ for privacy-conscious AI users.

### Where can I get help?

- 📖 [Documentation](README.md)
- 🐛 [Report Issues](https://github.com/yourusername/chatapi/issues)
- 💬 [Discussions](https://github.com/yourusername/chatapi/discussions)

---

## Tips & Tricks

### 1. Use System Messages

Add a system message to set context:
```
System: You are a helpful coding assistant.
```

### 2. Compare Models

Open multiple tabs to compare:
- Same prompt
- Different models
- See which is better

### 3. Save Good Prompts

Keep a note of prompts that work well:
- Reuse across conversations
- Build a prompt library
- Share with others

### 4. Monitor Costs

Check provider dashboards regularly:
- Set up billing alerts
- Track spending
- Optimize usage

### 5. Secure Your Keys

- Use strong passwords
- Rotate keys periodically
- Revoke unused keys
- Monitor for unusual activity

---

## Need More Help?

Can't find what you're looking for?

1. Check the [README](README.md)
2. Read the [Architecture docs](ARCHITECTURE.md)
3. Search [existing issues](https://github.com/yourusername/chatapi/issues)
4. Ask in [Discussions](https://github.com/yourusername/chatapi/discussions)
5. Open a [new issue](https://github.com/yourusername/chatapi/issues/new)

---

**Happy chatting! 🚀**
