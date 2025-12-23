# Casos PrÃ¡ticos - BFF em AÃ§Ã£o

## ðŸŽ¯ Objetivo

Ver exemplos reais de implementaÃ§Ã£o BFF baseados em projetos em produÃ§Ã£o.

---

## Caso 1: Buscar Lista de NotÃ­cias

### Frontend (Hook Customizado)

```typescript
// hooks/fetch-api/useNoticias.ts
'use client'

import { useState, useEffect } from 'react'

interface Noticia {
  id: number
  title: string
  description: string
  image: string
  date: string
}

export function useNoticias() {
  const [noticias, setNoticias] = useState<Noticia[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    let isMounted = true

    fetch('/api/noticias')
      .then(res => {
        if (!res.ok) throw new Error(`Erro ${res.status}`)
        return res.json()
      })
      .then(data => {
        if (isMounted) {
          setNoticias(data.news || [])
          setError(null)
        }
      })
      .catch(err => {
        if (isMounted) {
          setError(err.message)
        }
      })
      .finally(() => {
        if (isMounted) setLoading(false)
      })

    return () => {
      isMounted = false
    }
  }, [])

  return { noticias, loading, error }
}
```

### BFF (Route Handler)

```typescript
// app/api/noticias/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function GET(request: NextRequest) {
  try {
    const response = await apiClient.get('/noticias/home')
    
    return NextResponse.json(response.data, {
      status: 200,
      headers: {
        'Cache-Control': 'public, s-maxage=1800, stale-while-revalidate=3600'
      }
    })
  } catch (error: any) {
    console.error('Erro ao buscar notÃ­cias:', error.message)
    
    return NextResponse.json(
      { error: 'Erro ao buscar notÃ­cias' },
      { status: error.response?.status || 500 }
    )
  }
}
```

### Uso no Componente

```typescript
// components/NoticiasList.tsx
'use client'

import { useNoticias } from '@/hooks/fetch-api/useNoticias'

export function NoticiasList() {
  const { noticias, loading, error } = useNoticias()

  if (loading) return <div>Carregando...</div>
  if (error) return <div>Erro: {error}</div>

  return (
    <div className="grid grid-cols-3 gap-4">
      {noticias.map(noticia => (
        <article key={noticia.id} className="card">
          <img src={noticia.image} alt={noticia.title} />
          <h2>{noticia.title}</h2>
          <p>{noticia.description}</p>
        </article>
      ))}
    </div>
  )
}
```

---

## Caso 2: Cadastro com FormData

### Frontend (FormulÃ¡rio)

```typescript
// app/cadastro/page.tsx
'use client'

import { useState } from 'react'

export default function CadastroPage() {
  const [loading, setLoading] = useState(false)
  const [message, setMessage] = useState('')

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    setLoading(true)

    const formData = new FormData(e.currentTarget)

    try {
      const response = await fetch('/api/registration/professional', {
        method: 'POST',
        body: formData // NÃ£o definir Content-Type!
      })

      const data = await response.json()

      if (response.ok) {
        setMessage('Cadastro realizado com sucesso!')
      } else {
        setMessage(`Erro: ${data.error}`)
      }
    } catch (error) {
      setMessage('Erro ao enviar formulÃ¡rio')
    } finally {
      setLoading(false)
    }
  }

  return (
    <form onSubmit={handleSubmit} className="max-w-md mx-auto">
      <input 
        type="text" 
        name="nome" 
        placeholder="Nome completo"
        required
        className="input"
      />
      
      <input 
        type="text" 
        name="crm" 
        placeholder="CRM"
        required
        className="input"
      />
      
      <input 
        type="file" 
        name="documento"
        required
        className="input"
      />

      <button 
        type="submit" 
        disabled={loading}
        className="btn-primary"
      >
        {loading ? 'Enviando...' : 'Cadastrar'}
      </button>

      {message && <p className="mt-4">{message}</p>}
    </form>
  )
}
```

### BFF (Route Handler)

```typescript
// app/api/registration/professional/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function POST(request: NextRequest) {
  try {
    const formData = await request.formData()
    
    // ValidaÃ§Ã£o opcional
    const nome = formData.get('nome')
    const crm = formData.get('crm')
    
    if (!nome || !crm) {
      return NextResponse.json(
        { error: 'Nome e CRM sÃ£o obrigatÃ³rios' },
        { status: 400 }
      )
    }
    
    // Enviar para API externa
    const response = await apiClient.post('/registration/professional', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    })
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    console.error('Erro ao cadastrar professional:', error.message)
    
    return NextResponse.json(
      { error: 'Erro ao cadastrar professional' },
      { status: error.response?.status || 500 }
    )
  }
}
```

---

## Caso 3: Pesquisa com POST

### Frontend (Contexto + Hook)

```typescript
// contexts/SearchContext.tsx
'use client'

import { createContext, useContext, useState } from 'react'

interface SearchContextType {
  search: (filters: any) => Promise<void>
  results: any[]
  loading: boolean
}

const SearchContext = createContext<SearchContextType | undefined>(undefined)

export function SearchProvider({ children }: { children: React.ReactNode }) {
  const [results, setResults] = useState([])
  const [loading, setLoading] = useState(false)

  const search = async (filters: any) => {
    setLoading(true)

    try {
      const response = await fetch('/api/professionals/search', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(filters)
      })

      const data = await response.json()
      setResults(data.professionals || [])
    } catch (error) {
      console.error('Erro na pesquisa:', error)
      setResults([])
    } finally {
      setLoading(false)
    }
  }

  return (
    <SearchContext.Provider value={{ search, results, loading }}>
      {children}
    </SearchContext.Provider>
  )
}

export const useSearch = () => {
  const context = useContext(SearchContext)
  if (!context) throw new Error('useSearch deve ser usado dentro de SearchProvider')
  return context
}
```

### BFF (Route Handler)

```typescript
// app/api/professionals/search/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { apiClient } from '@/lib/api-client'

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    
    // Log em desenvolvimento
    if (process.env.NODE_ENV !== 'production') {
      console.log('Pesquisa professionals:', body)
    }
    
    const response = await apiClient.post('/professionals/search', body)
    
    return NextResponse.json(response.data, { status: 200 })
  } catch (error: any) {
    console.error('Erro ao pesquisar professionals:', error.message)
    
    return NextResponse.json(
      { error: 'Erro ao pesquisar professionals' },
      { status: error.response?.status || 500 }
    )
  }
}
```

### Uso no Componente

```typescript
// components/SearchForm.tsx
'use client'

import { useSearch } from '@/contexts/SearchContext'

export function SearchForm() {
  const { search, results, loading } = useSearch()

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    
    const formData = new FormData(e.currentTarget)
    
    await search({
      cidade: formData.get('cidade'),
      especialidade: formData.get('especialidade'),
    })
  }

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input name="cidade" placeholder="Cidade" />
        <input name="especialidade" placeholder="Especialidade" />
        <button type="submit" disabled={loading}>
          {loading ? 'Buscando...' : 'Buscar'}
        </button>
      </form>

      {results.length > 0 && (
        <ul>
          {results.map(professional => (
            <li key={professional.id}>{professional.nome}</li>
          ))}
        </ul>
      )}
    </div>
  )
}
```

---

## Caso 4: API Client Completo

```typescript
// lib/api-client.ts
import axios, { AxiosInstance, AxiosError } from 'axios'

// VariÃ¡veis de ambiente server-side
const CLIENT_ID = process.env.CLIENT_ID
const CLIENT_TOKEN = process.env.CLIENT_TOKEN
const API_BASE_URL = process.env.API_BASE_URL || 'https://api.externa.com'

// ValidaÃ§Ã£o
if (!CLIENT_ID || !CLIENT_TOKEN) {
  if (process.env.NODE_ENV !== 'production' || typeof window === 'undefined') {
    console.warn('âš ï¸ CLIENT_ID e CLIENT_TOKEN nÃ£o configurados')
  }
}

// Cliente para /site
export const apiClient: AxiosInstance = axios.create({
  baseURL: `${API_BASE_URL}/site`,
  headers: {
    'Content-Type': 'application/json',
    'client-id': CLIENT_ID,
    'client-token': CLIENT_TOKEN
  },
  timeout: 30000
})

// Cliente para raiz
export const apiClient2: AxiosInstance = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
    'client-id': CLIENT_ID,
    'client-token': CLIENT_TOKEN
  },
  timeout: 30000
})

// Interceptor de erro
const errorInterceptor = (error: AxiosError) => {
  console.error('API Error:', {
    url: error.config?.url,
    method: error.config?.method,
    status: error.response?.status,
    message: error.message
  })
  return Promise.reject(error)
}

apiClient.interceptors.response.use((response) => response, errorInterceptor)
apiClient2.interceptors.response.use((response) => response, errorInterceptor)
```

---

## âœ… PadrÃµes Observados

### Frontend
- âœ… Custom hooks para cada API
- âœ… Context para estado compartilhado
- âœ… Loading e error states sempre
- âœ… Cleanup em useEffect
- âœ… Tipagem TypeScript

### BFF
- âœ… Try/catch em todos os handlers
- âœ… ValidaÃ§Ã£o de inputs
- âœ… Logs apenas em desenvolvimento
- âœ… Status codes apropriados
- âœ… Mensagens de erro genÃ©ricas (sem expor detalhes)

### SeguranÃ§a
- âœ… Credenciais apenas server-side
- âœ… Nenhum `NEXT_PUBLIC_` para dados sensÃ­veis
- âœ… ValidaÃ§Ã£o antes de enviar para API
- âœ… Cache-Control configurado

---

**MÃ³dulo concluÃ­do!** â†’ [PrÃ³ximo MÃ³dulo: SeguranÃ§a OWASP](../03-seguranca-owasp/README.md)

