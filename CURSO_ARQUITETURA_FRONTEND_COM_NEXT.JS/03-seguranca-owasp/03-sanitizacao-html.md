# Sanitização HTML - Prevenção XSS

## 🎯 O Problema

Se sua aplicação renderiza HTML vindo de API ou usuários, há risco de XSS (Cross-Site Scripting).

```tsx
// ❌ PERIGO!
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// Se userInput = "<script>alert(document.cookie)</script>"
// O script será executado!
```

## ✅ Solução: DOMPurify

Biblioteca que remove scripts e tags perigosas de HTML.

## 📦 Instalação

```bash
npm install dompurify
npm install --save-dev @types/dompurify
```

## 🛠️ Implementação

### lib/sanitize-html.ts

```typescript
// lib/sanitize-html.ts

const allowedTags = [
  'p', 'br', 'strong', 'em', 'u', 'h1', 'h2', 'h3', 'h4', 'h5', 'h6',
  'ul', 'ol', 'li', 'a', 'span', 'div', 'img', 'table', 'thead', 'tbody',
  'tr', 'td', 'th', 'blockquote', 'code', 'pre'
]

const allowedAttributes = [
  'href', 'target', 'rel', 'src', 'alt', 'title', 'class', 'id'
]

export function sanitizeHtml(dirty: string): string {
  if (typeof window === 'undefined') return '' // Server-side: retornar vazio
  
  // Carregar DOMPurify dinamicamente (client-side only)
  const DOMPurify = require('dompurify')
  
  return DOMPurify.sanitize(dirty, {
    ALLOWED_TAGS: allowedTags,
    ALLOWED_ATTR: allowedAttributes,
    ALLOW_DATA_ATTR: false,
    RETURN_TRUSTED_TYPE: false,
  })
}
```

### components/SafeHTML.tsx

```typescript
// components/shared/SafeHTML.tsx
'use client'

import { useMemo } from 'react'
import { sanitizeHtml } from '@/lib/sanitize-html'

interface SafeHTMLProps {
  html: string
  className?: string
}

export function SafeHTML({ html, className }: SafeHTMLProps) {
  const cleanHTML = useMemo(() => sanitizeHtml(html), [html])
  
  return (
    <div 
      className={className}
      dangerouslySetInnerHTML={{ __html: cleanHTML }}
    />
  )
}
```

## 🎨 Uso

```tsx
// ANTES (INSEGURO)
<div dangerouslySetInnerHTML={{ __html: apiData.description }} />

// DEPOIS (SEGURO)
<SafeHTML html={apiData.description} />
```

## ✅ O que é removido

- ❌ `<script>` tags
- ❌ `<iframe>` tags
- ❌ Event handlers (`onclick`, `onerror`)
- ❌ `javascript:` URLs
- ❌ Tags não permitidas

## ✅ O que é mantido

- ✅ Formatação (strong, em, u)
- ✅ Listas (ul, ol, li)
- ✅ Links (com sanitização)
- ✅ Imagens (com sanitização)
- ✅ Tabelas
- ✅ Classes CSS

## 📊 Dupla Proteção

```
API Response
    ↓
1. Sanitização (DOMPurify)
    ↓
2. CSP Level 3
    ↓
Renderização Segura
```

---

**Próximo:** [Proteção XSS →](./04-protecao-xss.md)
