# O que é BFF (Backend-for-Frontend)?

## ��� Definição

**BFF** é um padrão arquitetural onde você cria uma camada de backend específica para atender as necessidades do frontend. No contexto Next.js, isso significa usar **Route Handlers** como proxy para APIs externas.

## ��� Arquitetura

```
┌──────────────────────────────────────────────────┐
│ FRONTEND (Browser - Client Side)                 │
│ ┌──────────────────────────────────────────────┐ │
│ │ React Components                              │ │
│ │ - fetch() nativo                              │ │
│ │ - Zero credenciais no código                  │ │
│ │ - Bundle leve                                 │ │
│ └──────────────────────────────────────────────┘ │
└────────────────┬─────────────────────────────────┘
                 │ HTTP Request
                 │ fetch('/api/...')
                 ↓
┌──────────────────────────────────────────────────┐
│ BFF LAYER (Next.js Server - Server Side)         │
│ ┌──────────────────────────────────────────────┐ │
│ │ Route Handlers (app/api/**/route.ts)         │ │
│ │ - Validação de requisições                   │ │
│ │ - Autenticação/Autorização                   │ │
│ │ - Transformação de dados                     │ │
│ │ - Cache-Control                              │ │
│ │ - Error handling                             │ │
│ └──────────────────────────────────────────────┘ │
│ ┌──────────────────────────────────────────────┐ │
│ │ API Client (lib/api-client.ts)               │ │
│ │ - Axios com credenciais server-side         │ │
│ │ - CLIENT_ID + CLIENT_TOKEN                   │ │
│ │ - Timeouts e retries                         │ │
│ └──────────────────────────────────────────────┘ │
└────────────────┬─────────────────────────────────┘
                 │ HTTP Request  
                 │ axios.get/post()
                 │ Headers: auth
                 ↓
┌──────────────────────────────────────────────────┐
│ API EXTERNA                                       │
│ - Recebe credenciais válidas                     │
│ - Processa requisição                            │
│ - Retorna dados                                  │
└──────────────────────────────────────────────────┘
```

## ✅ Benefícios

### 1. Segurança
-  **Credenciais protegidas** - API keys nunca expostas no frontend
- ✅ **Validação centralizada** - Input validation no servidor
- ✅ **Rate limiting** - Controle de requisições
- ✅ **Autenticação** - Verificar permissões antes de proxy

### 2. Performance
- ✅ **Cache server-side** - Dados podem ser cacheados
- ✅ **Bundle menor** - Axios fica no servidor, não no cliente
- ✅ **Agregação de dados** - Combinar múltiplas APIs em uma chamada

### 3. Manutenibilidade
- ✅ **Versionamento** - Mudar API externa sem afetar frontend
- ✅ **Transformação** - Adaptar response para formato esperado
- ✅ **Logs centralizados** - Monitoramento de todas as chamadas

## ��� Problema Sem BFF

```tsx
// ❌ Frontend chamando API diretamente
'use client'

import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.externa.com',
  headers: {
    'client-id': process.env.NEXT_PUBLIC_CLIENT_ID,     // ⚠️ EXPOSTO
    'client-token': process.env.NEXT_PUBLIC_CLIENT_TOKEN // ⚠️ EXPOSTO
  }
})

export function UserList() {
  const [users, setUsers] = useState([])
  
  useEffect(() => {
    api.get('/users').then(res => setUsers(res.data))
  }, [])
  
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>
}
```

**Riscos:**
- ��� Credenciais no bundle JavaScript (inspecionáveis)
- ��� Qualquer pessoa pode roubar e usar as credenciais
- ��� CORS pode bloquear requisições
- ��� Sem controle de rate limiting
- ��� Axios no bundle (~13KB extra)

## ✅ Solução Com BFF

### Frontend (Client)
```tsx
'use client'

export function UserList() {
  const [users, setUsers] = useState([])
  
  useEffect(() => {
    // ✅ Chama BFF interno, sem credenciais
    fetch('/api/users')
      .then(res => res.json())
      .then(setUsers)
  }, [])
  
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>
}
```

### BFF (Server)
```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function GET(request: NextRequest) {
  try {
    // ✅ apiClient tem credenciais server-side
    const response = await apiClient.get('/users')
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Erro ao buscar usuários' },
      { status: 500 }
    )
  }
}
```

### API Client (Server-side)
```typescript
// lib/api-client.ts
import axios from 'axios'

// ✅ Variáveis de ambiente SERVER-SIDE (nunca expostas)
const CLIENT_ID = process.env.CLIENT_ID
const CLIENT_TOKEN = process.env.CLIENT_TOKEN

export const apiClient = axios.create({
  baseURL: 'https://api.externa.com',
  headers: {
    'client-id': CLIENT_ID,      // ✅ Seguro
    'client-token': CLIENT_TOKEN  // ✅  Seguro
  },
  timeout: 30000
})
```

**Benefícios:**
- ✅ Credenciais 100% server-side
- ✅ Frontend leve (fetch nativo)
- ✅ Controle total sobre requisições
- ✅ Fácil adicionar validação/cache

## ��� Exemplo Real

Projeto com **51 Route Handlers** protegendo credenciais:

```
app/api/
├── users/
│   ├── route.ts              # GET /api/users
│   └── [id]/route.ts         # GET /api/users/123
├── posts/
│   ├── route.ts              # GET/POST /api/posts
│   └── [slug]/route.ts       # GET /api/posts/hello-world
└── auth/
    ├── login/route.ts        # POST /api/auth/login
    └── logout/route.ts       # POST /api/auth/logout
```

Cada endpoint é um proxy seguro para a API externa!

---

**Próximo:** [Route Handlers →](./02-route-handlers.md)
