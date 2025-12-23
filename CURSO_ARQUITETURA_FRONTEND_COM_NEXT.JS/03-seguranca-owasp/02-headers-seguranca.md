# 7 Headers de Segurança OWASP

Implementação completa dos headers de segurança recomendados pela OWASP para proteção A+.

## 📋 Headers Implementados

1. Content-Security-Policy (CSP Level 3)
2. Strict-Transport-Security (HSTS)
3. X-Frame-Options
4. X-Content-Type-Options
5. Referrer-Policy
6. Permissions-Policy
7. X-XSS-Protection (legacy)

## ⚙️ Implementação

### next.config.js

```javascript
const securityHeaders = [
  // 1. CSP (implementado via middleware com nonce)
  
  // 2. HSTS - Force HTTPS por 2 anos
  {
    key: 'Strict-Transport-Security',
    value: 'max-age=63072000; includeSubDomains; preload'
  },
  
  // 3. X-Frame-Options - Anti-clickjacking
  {
    key: 'X-Frame-Options',
    value: 'DENY'
  },
  
  // 4. X-Content-Type-Options - Anti-MIME sniffing
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff'
  },
  
  // 5. Referrer-Policy - Controle de vazamento de URLs
  {
    key: 'Referrer-Policy',
    value: 'strict-origin-when-cross-origin'
  },
  
  // 6. Permissions-Policy - Desabilitar features não utilizadas
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=(), payment=(), usb=()'
  },
  
  // 7. X-XSS-Protection - Proteção XSS legada
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
]

module.exports = {
  poweredByHeader: false, // Remove X-Powered-By
  
  async headers() {
    return [
      {
        source: '/:path*',
        headers: securityHeaders,
      },
    ]
  },
}
```

## 📝 Explicação

### 1. CSP - Ver módulo anterior

### 2. HSTS
**Função:** Força HTTPS por período definido  
**Valor:** `max-age=63072000` (2 anos)  
**Flags:** `includeSubDomains` + `preload`

Após primeira visita, browser SEMPRE usará HTTPS.

### 3. X-Frame-Options  
**Função:** Previne clickjacking  
**Valor:** `DENY` = nunca carregar em iframe

### 4. X-Content-Type-Options
**Função:** Previne MIME-type sniffing  
**Valor:** `nosniff`

Browser confia no Content-Type declarado.

### 5. Referrer-Policy
**Função:** Controla quando enviar Referer header  
**Valor:** `strict-origin-when-cross-origin`

- Same-origin: envia URL completa
- Cross-origin HTTPS: envia apenas origin
- Downgrade HTTP: não envia nada

### 6. Permissions-Policy
**Função:** Desabilita APIs sensíveis  
**Valor:** Bloqueia camera, mic, geolocation, etc

### 7. X-XSS-Protection (legacy)
**Função:** Ativa filtro XSS do browser (antigo)  
**Valor:** `1; mode=block`

Mantido para browsers antigos. CSP é superior.

## ✅ Checklist
- [ ] Headers em next.config.js
- [ ] poweredByHeader: false
- [ ] Testado em securityheaders.com (A+)
- [ ] Testado em observatory.mozilla.org (A+)

---

**Próximo:** [Sanitização HTML →](./03-sanitizacao-html.md)
