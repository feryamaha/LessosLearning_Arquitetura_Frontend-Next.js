# CSP Level 3 - Content Security Policy

## 🎯 O que é CSP?

**Content Security Policy (CSP)** é um header HTTP que controla quais recursos (scripts, estilos, imagens) podem ser carregados na página, prevenindo ataques XSS (Cross-Site Scripting).

## 🔴 Problema: XSS Attack

```html
<!-- Atacante injeta script malicioso -->
<div>${userInput}</div>

<!-- Se userInput = "<script>alert(document.cookie)</script>" -->
<!-- O script será executado! -->
```

## ✅ Solução: CSP com Nonce

CSP Level 3 com nonce permite apenas scripts com um token criptográfico único gerado a cada requisição.

---

## 📝 Implementação Completa

### 1. Middleware (Geração de Nonce)

```typescript
// src/middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Gera nonce usando Web Crypto API (Edge Runtime compatível)
  const nonceArray = new Uint8Array(16)
  crypto.getRandomValues(nonceArray)
  const nonce = Buffer.from(nonceArray).toString('base64')
  
  const response = NextResponse.next()
  
  // Remove header Server (OWASP: não vaze stack)
  response.headers.delete('Server')
  
  // Passa nonce para layout via header
  response.headers.set('X-Nonce', nonce)

  // CSP Level 3 com strict-dynamic
  const isDev = process.env.NODE_ENV === 'development'
  
  const csp = [
    "default-src 'self'",
    // strict-dynamic: permite scripts injetados por scripts com nonce
    `script-src 'nonce-${nonce}' 'strict-dynamic' 'self'${isDev ? " 'unsafe-eval'" : ''}`,
    "style-src 'self' 'unsafe-inline' https://fonts.googleapis.com",
    "img-src 'self' blob: data: https:",
    "font-src 'self' https://fonts.gstatic.com data:",
    "connect-src 'self' https:",
    "frame-ancestors 'none'",
    "base-uri 'self'",
    "form-action 'self'",
    "upgrade-insecure-requests",
  ].join('; ')

  response.headers.set('Content-Security-Policy', csp)

  return response
}

export const config = {
  matcher: [
    '/((?!_next/static|_next/image|favicon.ico).*)',
  ],
}
```

### 2. Layout (Injeção de Nonce)

```tsx
// app/layout.tsx
import { headers } from 'next/headers'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  // Ler nonce do header
  const nonce = headers().get('X-Nonce') || ''

  return (
    <html lang="pt-BR">
      <head>
        {/* Scripts externos com nonce */}
        <script
          nonce={nonce}
          src="https://cdn.exemplo.com/script.js"
        />
      </head>
      <body>
        {children}
        
        {/* Script inline com nonce */}
        <script
          nonce={nonce}
          dangerouslySetInnerHTML={{
            __html: `
              console.log('Script permitido com nonce');
            `
          }}
        />
      </body>
    </html>
  )
}
```

---

## 🔒 Diretivas CSP

### default-src

Fallback para todas as diretivas.

```
default-src 'self'
```
- `'self'` = Apenas recursos do mesmo domínio

### script-src (CRÍTICO)

Controla scripts JavaScript.

```
script-src 'nonce-ABC123' 'strict-dynamic' 'self'
```

**Opções:**
- `'nonce-ABC123'` = Apenas scripts com este nonce
- `'strict-dynamic'` = Scripts carregados por scripts com nonce são permitidos
- `'self'` = Fallback para browsers antigos
- ❌ `'unsafe-inline'` = NUNCA usar em produção!
- ❌ `'unsafe-eval'` = NUNCA usar em produção!

**Exceção Development:**
```typescript
// Next.js precisa de unsafe-eval para HMR em dev
${isDev ? " 'unsafe-eval'" : ''}
```

### style-src

Controla estilos CSS.

``

`
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com
```

**Por que `'unsafe-inline'` aqui é OK:**
- Tailwind gera classes dinâmicas
- Next.js injeta estilos inline
- Combinado com CSP forte em scripts, risco é baixo

### img-src

Controla imagens.

```
img-src 'self' blob: data: https:
```
- `blob:` = Imagens de File API
- `data:` = Data URIs (base64)
- `https:` = Qualquer HTTPS (para CDNs)

### connect-src

Controla  fetch(), XHR, WebSockets.

```
connect-src 'self' https://api.exemplo.com
```

### frame-ancestors

Proteção contra clickjacking (complementa X-Frame-Options).

```
frame-ancestors 'none'
```
- Impede que o site seja embedado em iframes

---

## 🧪 Testando CSP

### 1. Verificar no DevTools

```
Console → Erros CSP aparecem como:
"Refused to load the script 'https://evil.com/hack.js' 
because it violates the following Content Security Policy directive..."
```

### 2. Ferramentas Online

- **SecurityHeaders.com** - https://securityheaders.com/
- **Mozilla Observatory** - https://observatory.mozilla.org/

Target: **Score A+**

### 3. Teste Manual

```html
<!-- Este script será BLOQUEADO (sem nonce) -->
<script>alert('Blocked!')</script>

<!-- Este será PERMITIDO (com nonce) -->
<script nonce="ABC123">console.log('Allowed')</script>
```

---

## ⚠️ Problemas Comuns

### Problema 1: Scripts de terceiros bloqueados

**Solução:** Adicionar nonce ou whitelist de domínio

```typescript
// Adicionar domínio confiável
`script-src 'nonce-${nonce}' 'strict-dynamic' https://cdn.confiavel.com`
```

### Problema 2: Estilos inline bloqueados

**Solução:** Permitir `'unsafe-inline'` em `style-src` (aceitável com CSP forte em scripts)

### Problema 3: Google Analytics/Clarity bloqueados

```typescript
const csp = [
  // ...
  `script-src 'nonce-${nonce}' 'strict-dynamic' https://www.googletagmanager.com https://www.clarity.ms`,
  "connect-src 'self' https://www.google-analytics.com https://www.clarity.ms wss:",
]
```

---

## ✅ Checklist

- [ ] Middleware gerando nonce com crypto.getRandomValues()
- [ ] Nonce passado via header X-Nonce
- [ ] Layout lendo nonce de headers()
- [ ] Scripts externos com nonce
- [ ] CSP com 'strict-dynamic'
- [ ] Sem 'unsafe-inline' ou 'unsafe-eval' em produção
- [ ] Testado em SecurityHeaders.com (Score A+)

---

**Próximo:** [Headers de Segurança →](./02-headers-seguranca.md)
