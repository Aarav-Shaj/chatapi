# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in ChatAPI, please report it responsibly:

1. **DO NOT** open a public issue
2. Email security details to: [shajapurkaraarav@gmail.com]
4. Include:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

We will respond within 48 hours and work with you to address the issue.

## Security Best Practices

### For Users

1. **Master Password**
   - Use a strong, unique password (12+ characters)
   - Include uppercase, lowercase, numbers, and symbols
   - Never reuse passwords from other services
   - Consider using a password manager

2. **Browser Security**
   - Keep your browser updated
   - Use a reputable browser (Chrome, Firefox, Edge, Safari)
   - Avoid malicious browser extensions
   - Clear browser data when using shared computers

3. **API Key Security**
   - Never share your API keys
   - Rotate keys periodically
   - Use provider dashboards to monitor usage
   - Revoke keys if compromised

4. **Session Management**
   - Use session-only mode on shared computers
   - Lock the app when stepping away
   - Close browser tab when done

### For Developers

1. **Code Review**
   - All PRs require security review
   - No hardcoded secrets
   - Validate all user inputs
   - Sanitize outputs

2. **Dependencies**
   - Minimal dependencies
   - Regular security audits (`npm audit`)
   - Pin dependency versions
   - Review dependency changes

3. **Crypto**
   - Use Web Crypto API only (no custom crypto)
   - Follow OWASP guidelines
   - Use secure random number generation
   - Proper key derivation (PBKDF2, 100k+ iterations)

4. **Testing**
   - Security test coverage
   - Penetration testing
   - Fuzzing critical functions
   - XSS prevention tests

## Security Features

### Implemented

- ✅ AES-GCM-256 encryption
- ✅ PBKDF2 key derivation (100k iterations)
- ✅ Web Crypto API (FIPS 140-2)
- ✅ Content Security Policy (CSP)
- ✅ No server-side storage
- ✅ No telemetry/analytics
- ✅ Input sanitization
- ✅ Auto-lock on inactivity

### Planned

- 🚧 Hardware security key support (WebAuthn)
- 🚧 Biometric unlock
- 🚧 Certificate pinning
- 🚧 Secure enclave (desktop app)

## Threat Model

### In Scope

- XSS attacks
- Key theft from storage
- Man-in-the-middle attacks
- Malicious browser extensions
- Memory dumps
- Dependency vulnerabilities

### Out of Scope

- Physical access to unlocked device
- Compromised browser/OS
- Social engineering
- Provider-side vulnerabilities

## Security Audits

| Date | Auditor | Findings | Status |
|------|---------|----------|--------|
| TBD | Internal | TBD | Pending |

## Responsible Disclosure

We follow responsible disclosure practices:

1. Report received → Acknowledged (48h)
2. Investigation → Assessment (1 week)
3. Fix developed → Testing (varies)
4. Patch released → Public disclosure (after fix)
5. Credit given → Hall of Fame

## Hall of Fame

Security researchers who have helped improve ChatAPI:

- *Your name could be here!*

## Contact

Security Team: [your-email@example.com]

