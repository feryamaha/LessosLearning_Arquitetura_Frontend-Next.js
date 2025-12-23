# Proteção de Credenciais

## 🎯 O Problema

Aplicações precisam se comunicar com APIs externas, que geralmente exigem **autenticação** via API keys, tokens, ou client credentials.

### ❌ Abordagem Insegura (NUNCA FAÇA ISSO)

```typescript
// ❌ CRÍTICO: Credenciais expostas no frontend
'use client'

const API_KEY = 'sk_live_abc123_super_secret' // ⚠️ EXPOSTO NO BUNDLE

fetch('https://api.externa.com/data', {
  headers: {
    'Authorization': `Bearer ${API_KEY}` // ⚠️ VISÍVEL NO DEVTOOLS
  }
})
```

**Riscos:**
- 🔴 **Qualquer pessoa** pode abrir DevTools e copiar a chave
- 🔴 **Bundle JavaScript** contém a credencial em texto claro
- 🔴 **Ferramentas automatizadas** podem extrair essas chaves de sites públicos
- 🔴 **Multas LGPD** - Exposição de dados sensíveis

---

## ✅ Solução: BFF com Variáveis de Ambiente

### Arquitetura Segura

```
┌────────────────┐
│   Frontend     │  ✅ Zero credenciais
│   (Browser)    │  ✅ fetch('/api/data')
└────────┬───────┘
         │
         ↓
┌────────────────┐
│   BFF Layer    │  ✅ Credenciais server-side
│   (Next.js)    │  ✅ process.env.API_KEY
└────────┬───────┘
         │
         ↓
┌────────────────┐
│  API Externa   │  ✅ Recebe credenciais válidas
└────────────────┘
```

---

## 🔐 Variáveis de Ambiente

### Tipos de Variáveis

Next.js tem **dois tipos** de variáveis de ambiente:

#### 1. Server-Side Only (Seguras)

```bash
# .env.local
API_KEY=super_secret_key
DATABASE_URL=postgresql://...
CLIENT_ID=meu-cliente-id
CLIENT_TOKEN=token-ultra-secreto
```

**Características:**
- ✅ **Nunca** expostas no bundle do cliente
- ✅ **Apenas** acessíveis em Server Components e Route Handlers
- ✅ **Seguras** para credentials, tokens, database URLs

#### 2. Client-Side (Públicas)

```bash
# .env.local
NEXT_PUBLIC_API_URL=https://api.exemplo.com
NEXT_PUBLIC_GOOGLE_MAPS_KEY=AIzaSy...
```

**Características:**
- ⚠️ **Expostas** no bundle do cliente
- ⚠️ **Qualquer pessoa** pode ver
- ⚠️ Usar **apenas** para valores públicos/não sensíveis
- ⚠️ Google Maps keys são OK (domain restricted)

---

## 📝 Configuração Correta

### 1. Criar arquivo `.env.local`

```bash
# .env.local (NÃO commitar!)

# Server-side only (seguros)
CLIENT_ID=my-client-id
CLIENT_TOKEN=my-super-secret-token
API_BASE_URL=https://api.externa.com
DATABASE_URL=postgresql://user:pass@host:5432/db

# Client-side (públicos)
NEXT_PUBLIC_API_URL=https://api.meusite.com
```

### 2. Adicionar ao `.gitignore`

```bash
# .gitignore
.env*.local
.env
```

### 3. Criar `.env.example` (template)

```bash
# .env.example (PODE commitar)

# Server-side credentials
CLIENT_ID=your_client_id_here
CLIENT_TOKEN=your_client_token_here
API_BASE_URL=https://api.externa.com

# Client-side
NEXT_PUBLIC_API_URL=https://api.example.com
```

---

## 🛠️ Uso Correto

### lib/api-client.ts (Server-Side)

```typescript
// lib/api-client.ts
import axios from 'axios'

// ✅ Variáveis SERVER-SIDE ONLY
const CLIENT_ID = process.env.CLIENT_ID
const CLIENT_TOKEN = process.env.CLIENT_TOKEN
const API_BASE_URL = process.env.API_BASE_URL

// Validação (opcional mas recomendada)
if (!CLIENT_ID || !CLIENT_TOKEN) {
  throw new Error('Credenciais não configuradas! Verifique .env.local')
}

export const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
    'client-id': CLIENT_ID,        // ✅ Seguro
    'client-token': CLIENT_TOKEN    // ✅ Seguro
  },
  timeout: 30000
})

// Error interceptor
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    console.error('API Error:', {
      url: error.config?.url,
      status: error.response?.status,
      message: error.message
    })
    return Promise.reject(error)
  }
)
```

### Route Handler (BFF)

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function GET(request: NextRequest) {
  try {
    // ✅ apiClient usa credenciais server-side
    const response = await apiClient.get('/users')
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    // ❌ NUNCA exponha credenciais em erros!
    return NextResponse.json(
      { error: 'Erro ao buscar usuários' }, // Genérico
      { status: 500 }
    )
  }
}
```

### Frontend (Cliente)

```typescript
// components/UserList.tsx
'use client'

export function UserList() {
  const [users, setUsers] = useState([])
  
  useEffect(() => {
    // ✅ Chama BFF interno, sem credenciais
    fetch('/api/users')
      .then(res => res.json())
      .then(setUsers)
  }, [])
  
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  )
}
```

---

## 🔍 Validação de Variáveis

### lib/env.ts (Recomendado)

```typescript
// lib/env.ts

/**
 * Valida variáveis de ambiente obrigatórias
 * Falha em build time se alguma estiver faltando
 */
function validateEnv() {
  const required = [
    'CLIENT_ID',
    'CLIENT_TOKEN',
    'API_BASE_URL'
  ]
  
  const missing = required.filter(key => !process.env[key])
  
  if (missing.length > 0) {
    throw new Error(
      `❌ Variáveis de ambiente faltando: ${missing.join(', ')}\n` +
      `Configure no arquivo .env.local`
    )
  }
}

// Validar no startup (apenas server-side)
if (typeof window === 'undefined') {
  validateEnv()
}

// Exportar com type safety
export const env = {
  clientId: process.env.CLIENT_ID!,
  clientToken: process.env.CLIENT_TOKEN!,
  apiBaseUrl: process.env.API_BASE_URL!,
} as const
```

**Uso:**

```typescript
import { env } from '@/lib/env'

const apiClient = axios.create({
  baseURL: env.apiBaseUrl,
  headers: {
    'client-id': env.clientId,
    'client-token': env.clientToken
  }
})
```

---

## ⚙️ Deploy (Vercel)

### Configurar Variáveis no Vercel

1. Acesse o projeto no Vercel Dashboard
2. Settings → Environment Variables
3. Adicione cada variável:

```
CLIENT_ID = seu_valor_producao
CLIENT_TOKEN = seu_token_producao
API_BASE_URL = https://api.producao.com
```

4. Escolha o ambiente (Production / Preview / Development)
5. Save

**Importante:**
- ✅ Nunca commitar `.env.local`
- ✅ Sempre criar `.env.example` como template
- ✅ Configurar variáveis no Vercel antes do deploy
- ✅ Usar valores diferentes para dev/staging/prod

---

## 🧪 Testando Segurança

### Verificar se credenciais NÃO estão expostas

1. **Build o projeto:**
```bash
npm run build
```

2. **Inspecionar bundle:**
```bash
# Buscar por "client-token" no build
grep -r "client-token" .next/

# Buscar por CLIENT_TOKEN
grep -r "CLIENT_TOKEN" .next/
```

Se encontrar algo = ❌ **PROBLEMA!**

3. **DevTools (navegador):**
- Network tab → Verificar headers das requisições
- Sources tab → Buscar por credenciais
- Console → `localStorage`, `sessionStorage`

Se encontrar credenciais = ❌ **PROBLEMA!**

---

## ✅ Checklist de Segurança

### Desenvolvimento
- [ ] `.env.local` criado e configurado
- [ ] `.env.local` no `.gitignore`
- [ ] `.env.example` commitado
- [ ] Validação de env vars implementada

### Código
- [ ] Credenciais APENAS em `process.env`
- [ ] Sem `NEXT_PUBLIC_` para dados sensíveis
- [ ] API client em `lib/` (server-side)
- [ ] Route Handlers fazendo proxy

### Deploy
- [ ] Variáveis configuradas no Vercel
- [ ] Build sem erros de env vars
- [ ] Teste: credenciais não aparecem no DevTools

---

## 🚨 Nunca Exponha

❌ API Keys  
❌ Client Tokens  
❌ Database URLs  
❌ Private Keys  
❌ Senhas  
❌ Session Secrets  

✅ **Tudo isso deve estar em `process.env` (server-side)**

---

**Próximo:** [Casos Práticos →](./04-casos-praticos.md)
