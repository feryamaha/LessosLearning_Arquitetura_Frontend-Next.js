# Route Handlers - Next.js API Routes

## 🎯 O que são Route Handlers?

Route Handlers são funções **server-side** que permitem criar endpoints de API no Next.js. São o equivalente às API Routes do Pages Router, mas para o App Router.

## 📁 Estrutura de Arquivos

```
app/api/
├── users/
│   └── route.ts              # GET /api/users
├── posts/
│   ├── route.ts              # GET/POST /api/posts
│   └── [id]/
│       └── route.ts          # GET/PUT/DELETE /api/posts/123
└── auth/
    ├── login/
    │   └── route.ts          # POST /api/auth/login
    └── register/
        └── route.ts          # POST /api/auth/register
```

**Regra:** Arquivo `route.ts` (ou `.js`) dentro de `app/api/` cria um endpoint.

---

## ✅ Métodos HTTP Suportados

```typescript
export async function GET(request: NextRequest) { }
export async function POST(request: NextRequest) { }
export async function PUT(request: NextRequest) { }
export async function PATCH(request: NextRequest) { }
export async function DELETE(request: NextRequest) { }
export async function HEAD(request: NextRequest) { }
export async function OPTIONS(request: NextRequest) { }
```

---

## 📋 Pattern 1: GET Simples

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function GET(request: NextRequest) {
  try {
    // Proxy para API externa
    const response = await apiClient.get('/users')
    
    return NextResponse.json(response.data, {
      status: 200,
      headers: {
        'Cache-Control': 'public, s-maxage=3600' // Cache 1 hora
      }
    })
  } catch (error: any) {
    console.error('Erro ao buscar usuários:', error.message)
    
    return NextResponse.json(
      { error: 'Erro ao buscar usuários' },
      { status: error.response?.status || 500 }
    )
  }
}
```

**Uso no frontend:**
```typescript
const response = await fetch('/api/users')
const users = await response.json()
```

---

## 📋 Pattern 2: GET com Query Parameters

```typescript
// app/api/posts/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function GET(request: NextRequest) {
  try {
    // Extrair query parameters
    const { searchParams } = new URL(request.url)
    const page = searchParams.get('page') || '1'
    const limit = searchParams.get('limit') || '10'
    
    // Montar query string
    const queryString = searchParams.toString()
    const endpoint = queryString ? `/posts?${queryString}` : '/posts'
    
    const response = await apiClient.get(endpoint)
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Erro ao buscar posts' },
      { status: 500 }
    )
  }
}
```

**Uso:**
```typescript
fetch('/api/posts?page=2&limit=20')
```

---

## 📋 Pattern 3: POST com Body JSON

```typescript
// app/api/auth/login/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function POST(request: NextRequest) {
  try {
    // Ler body da requisição
    const body = await request.json()
    
    // Validação básica
    if (!body.email || !body.password) {
      return NextResponse.json(
        { error: 'Email e senha são obrigatórios' },
        { status: 400 }
      )
    }
    
    // Enviar para API externa
    const response = await apiClient.post('/auth/login', body)
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    return NextResponse.json(
      { 
        error: 'Erro no login',
        message: error.response?.data?.message 
      },
      { status: error.response?.status || 500 }
    )
  }
}
```

**Uso:**
```typescript
fetch('/api/auth/login', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ email, password })
})
```

---

## 📋 Pattern 4: POST com FormData (Upload)

```typescript
// app/api/upload/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function POST(request: NextRequest) {
  try {
    // Ler FormData
    const formData = await request.formData()
    
    // Enviar para API externa
    const response = await apiClient.post('/upload', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    })
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Erro no upload' },
      { status: 500 }
    )
  }
}
```

**Uso:**
```typescript
const formData = new FormData()
formData.append('file', fileInput.files[0])
formData.append('title', 'Meu arquivo')

fetch('/api/upload', {
  method: 'POST',
  body: formData // Não definir Content-Type!
})
```

---

## 📋 Pattern 5: Dynamic Routes

### Parâmetros na URL

```typescript
// app/api/posts/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const { id } = params
    
    // Validar ID
    if (!id || isNaN(Number(id))) {
      return NextResponse.json(
        { error: 'ID inválido' },
        { status: 400 }
      )
    }
    
    const response = await apiClient.get(`/posts/${id}`)
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    if (error.response?.status === 404) {
      return NextResponse.json(
        { error: 'Post não encontrado' },
        { status: 404 }
      )
    }
    
    return NextResponse.json(
      { error: 'Erro ao buscar post' },
      { status: 500 }
    )
  }
}

export async function DELETE(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const { id } = params
    
    await apiClient.delete(`/posts/${id}`)
    
    return NextResponse.json(
      { message: 'Post deletado com sucesso' },
      { status: 200 }
    )
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Erro ao deletar post' },
      { status: 500 }
    )
  }
}
```

**Uso:**
```typescript
// GET
fetch('/api/posts/123')

// DELETE
fetch('/api/posts/123', { method: 'DELETE' })
```

---

## 🔧 Request Object

### Métodos Úteis

```typescript
export async function POST(request: NextRequest) {
  // URL e query params
  const url = new URL(request.url)
  const searchParams = url.searchParams
  const page = searchParams.get('page')
  
  // Headers
  const auth = request.headers.get('authorization')
  const contentType = request.headers.get('content-type')
  
  // Cookies
  const token = request.cookies.get('token')
  
  // Body
  const json = await request.json()        // JSON
  const formData = await request.formData() // FormData
  const text = await request.text()        // Texto
  
  // ...
}
```

---

## 🔧 Response Object

### Criando Respostas

```typescript
// JSON Response
return NextResponse.json({ message: 'OK' }, { status: 200 })

// JSON com Headers
return NextResponse.json(
  { data: [] },
  {
    status: 200,
    headers: {
      'Cache-Control': 'public, s-maxage=3600',
      'X-Custom-Header': 'valor'
    }
  }
)

// Redirect
return NextResponse.redirect(new URL('/login', request.url))

// Rewrite (interno)
return NextResponse.rewrite(new URL('/api/v2/users', request.url))

// Set Cookie
const response = NextResponse.json({ success: true })
response.cookies.set('token', 'abc123', {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 60 * 60 * 24 // 1 dia
})
return response
```

---

## ⚡ Otimizações

### Cache-Control

```typescript
export async function GET() {
  const response = await apiClient.get('/data')
  
  return NextResponse.json(response.data, {
    headers: {
      // Cache por 1 hora, revalida em background por 1 dia
      'Cache-Control': 'public, s-maxage=3600, stale-while-revalidate=86400'
    }
  })
}
```

### Edge Runtime

Para responses ultra-rápidas:

```typescript
export const runtime = 'edge' // Roda no Edge, não no Node.js

export async function GET() {
  return NextResponse.json({ message: 'Super rápido!' })
}
```

### Streaming

Para responses grandes:

```typescript
export async function GET() {
  const encoder = new TextEncoder()
  
  const stream = new ReadableStream({
    async start(controller) {
      for (let i = 0; i < 100; i++) {
        controller.enqueue(encoder.encode(`Chunk ${i}\n`))
        await new Promise(resolve => setTimeout(resolve, 100))
      }
      controller.close()
    }
  })
  
  return new Response(stream, {
    headers: { 'Content-Type': 'text/plain' }
  })
}
```

---

## ✅ Checklist

- [ ] Route Handlers em `app/api/**/route.ts`
- [ ] Métodos HTTP corretos (GET, POST, etc.)
- [ ] Tratamento de erros com try/catch
- [ ] Validação de inputs
- [ ] Tipagem TypeScript
- [ ] Cache-Control configurado

---

**Próximo:** [Proteção de Credenciais →](./03-protecao-credenciais.md)
