# 02. SeparaÃ§Ã£o UI vs LÃ³gica - PrincÃ­pios Profissionais

## ðŸŽ¯ Objetivo

Separar claramente a **lÃ³gica de negÃ³cio** dos **componentes de interface**, criando cÃ³digo mais testÃ¡vel, reutilizÃ¡vel e manutenÃ­vel.

---

## âŒ Problema: Tudo Misturado

### Anti-Pattern - God Component

```typescript
// âŒ ERRADO - 323 linhas de God Component (exemplo real!)
'use client'

export function ProfessionalsSearchPage() {
  // Estados (20+ useState)
  const [Professionals, setProfessionals] = useState([])
  const [especialidades, setEspecialidades] = useState([])
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)
  const [filters, setFilters] = useState({})
  const [page, setPage] = useState(1)
  const [hasMore, setHasMore] = useState(true)
  // ... +15 estados

  // LÃ³gica de API misturada
  useEffect(() => {
    fetch('/api/Professionals/especialidades')
      .then(res => res.json())
      .then(setEspecialidades)
  }, [])

  useEffect(() => {
    fetch(`/api/Professionals/pesquisa?page=${page}`, {
      method: 'POST',
      body: JSON.stringify(filters)
    })
      .then(res => res.json())
      .then(data => {
        setProfessionals(prev => [...prev, ...data.Professionals])
        setHasMore(data.hasMore)
      })
  }, [page, filters])

  // FunÃ§Ãµes complexas inline (100+ linhas)
  const handleSearch = () => { /* 50 linhas */ }
  const handleLoadMore = () => { /* 30 linhas */ }
  const resetFilters = () => { /* 20 linhas */ }

  // UI misturada com lÃ³gica (100+ linhas de JSX)
  return (
    <div>
      {/* 100+ linhas de UI complexa */}
    </div>
  )
}

// Total: 323 linhas em 1 arquivo! ðŸ˜±
```

**Problemas:**
- ðŸ”´ ImpossÃ­vel testar lÃ³gica separadamente
- ðŸ”´ DifÃ­cil reutilizar em outras pÃ¡ginas
- ðŸ”´ Hard to debug
- ðŸ”´ ViolaÃ§Ã£o do Single Responsibility Principle

---

## âœ… SoluÃ§Ã£o: SeparaÃ§Ã£o em Camadas

### Arquitetura em 3 Camadas

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  UI Layer (Componentes)             â”‚  â† Apenas renderizaÃ§Ã£o
â”‚  - ApresentaÃ§Ã£o                     â”‚
â”‚  - Eventos de usuÃ¡rio               â”‚
â”‚  - Styling                          â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
               â”‚
               â†“ usa
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  Logic Layer (Hooks/Context)        â”‚  â† LÃ³gica de negÃ³cio
â”‚  - Estado gerenciado                â”‚
â”‚  - Efeitos colaterais               â”‚
â”‚  - TransformaÃ§Ãµes                   â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
               â”‚
               â†“ usa
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  Data Layer (API/Services)          â”‚  â† Dados
â”‚  - fetch() calls                    â”‚
â”‚  - ValidaÃ§Ãµes                       â”‚
â”‚  - TransformaÃ§Ãµes de dados          â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

---

## ðŸŽ¨ ImplementaÃ§Ã£o Correta

### 1. Data Layer (fetch-api)

```typescript
// hooks/fetch-api/useProfessionals.ts
'use client'

import { useState, useEffect } from 'react'

interface Professional {
  id: number
  nome: string
  crm: string
  especialidade: string
}

interface useProfessionalsResult {
  Professionals: Professional[]
  loading: boolean
  error: string | null
  loadMore: () => void
  hasMore: boolean
}

export function useProfessionals(filters: any): useProfessionalsResult {
  const [Professionals, setProfessionals] = useState<Professional[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)
  const [page, setPage] = useState(1)
  const [hasMore, setHasMore] = useState(true)

  useEffect(() => {
    let isMounted = true
    setLoading(true)

    fetch(`/api/Professionals/pesquisa?page=${page}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(filters)
    })
      .then(res => {
        if (!res.ok) throw new Error(`Erro ${res.status}`)
        return res.json()
      })
      .then(data => {
        if (isMounted) {
          setProfessionals(prev => page === 1 ? data.Professionals : [...prev, ...data.Professionals])
          setHasMore(data.hasMore)
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
  }, [page, filters])

  const loadMore = () => {
    if (hasMore && !loading) {
      setPage(prev => prev + 1)
    }
  }

  return { Professionals, loading, error, loadMore, hasMore }
}
```

### 2. Logic Layer (Context)

```typescript
// contexts/ProfessionalsContext.tsx
'use client'

import { createContext, useContext, useState, ReactNode } from 'react'

interface Filters {
  cidade: string
  especialidade: string
  urgencia: boolean
}

interface ProfessionalsContextType {
  filters: Filters
  updateFilters: (newFilters: Partial<Filters>) => void
  resetFilters: () => void
  executeSearch: () => void
}

const ProfessionalsContext = createContext<ProfessionalsContextType | undefined>(undefined)

const initialFilters: Filters = {
  cidade: '',
  especialidade: '',
  urgencia: false
}

export function DentistsProvider({ children }: { children: ReactNode }) {
  const [filters, setFilters] = useState<Filters>(initialFilters)

  const updateFilters = (newFilters: Partial<Filters>) => {
    setFilters(prev => ({ ...prev, ...newFilters }))
  }

  const resetFilters = () => {
    setFilters(initialFilters)
  }

  const executeSearch = () => {
    // Trigger de busca (pode disparar evento ou atualizar estado)
    console.log('Buscando com filtros:', filters)
  }

  return (
    <ProfessionalsContext.Provider value={{ filters, updateFilters, resetFilters, executeSearch }}>
      {children}
    </ProfessionalsContext.Provider>
  )
}

export function useProfessionalsFilters() {
  const context = useContext(ProfessionalsContext)
  if (!context) {
    throw new Error('useProfessionalsFilters must be used within DentistsProvider')
  }
  return context
}
```

### 3. UI Layer (Componente)

```typescript
// components/sections/ProfessionalSearch.tsx
'use client'

import { useProfessionals } from '@/hooks/fetch-api/useProfessionals'
import { useProfessionalsFilters } from '@/contexts/ProfessionalsContext'
import { Button } from '@/components/ui/Button'
import { ProfessionalCard } from '@/components/shared/ProfessionalCard'

export function ProfessionalSearch() {
  // LÃ³gica vem dos hooks/contexts
  const { filters, updateFilters, resetFilters } = useProfessionalsFilters()
  const { Professionals, loading, error, loadMore, hasMore } = useProfessionals(filters)

  // Componente sÃ³ lida com apresentaÃ§Ã£o
  if (error) return <div className="error">Erro: {error}</div>
  
  return (
    <div className="container">
      {/* Filtros */}
      <div className="filters">
        <input
          value={filters.cidade}
          onChange={e => updateFilters({ cidade: e.target.value })}
          placeholder="Cidade"
        />
        <input
          value={filters.especialidade}
          onChange={e => updateFilters({ especialidade: e.target.value })}
          placeholder="Especialidade"
        />
        <Button onClick={resetFilters}>Limpar</Button>
      </div>

      {/* Resultados */}
      <div className="results grid grid-cols-3 gap-4">
        {Professionals.map(Professional => (
          <ProfessionalCard key={Professional.id} Professional={Professional} />
        ))}
      </div>

      {/* Loading / LoadMore */}
      {loading && <div>Carregando...</div>}
      {hasMore && !loading && (
        <Button onClick={loadMore}>Carregar Mais</Button>
      )}
    </div>
  )
}

// Total: ~60 linhas limpas e focadas!
```

---

## ðŸ“Š ComparaÃ§Ã£o: Antes vs Depois

### Antes (God Component)
```
ProfessionalsSearchPage.tsx
â”œâ”€â”€ Estados: 20+ useState
â”œâ”€â”€ Efeitos: 5+ useEffect
â”œâ”€â”€ FunÃ§Ãµes: 10+ handlers
â”œâ”€â”€ API calls: Inline
â”œâ”€â”€ UI: 100+ linhas JSX
â””â”€â”€ Total: 323 linhas ðŸ˜±
```

### Depois (Separado)
```
hooks/fetch-api/useProfessionals.ts      â† 80 linhas (Data)
contexts/ProfessionalsContext.tsx         â† 60 linhas (Logic)
components/ProfessionalSearch.tsx         â† 60 linhas (UI)
components/ProfessionalCard.tsx           â† 30 linhas (UI)
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
Total: 230 linhas (28% menor)
Mas muito mais organizado e testÃ¡vel!
```

**BenefÃ­cios:**
- âœ… Cada arquivo <100 linhas
- âœ… TestÃ¡vel separadamente
- âœ… ReutilizÃ¡vel
- âœ… FÃ¡cil de debugar

---

## ðŸ§ª Testabilidade

### Sem SeparaÃ§Ã£o (DifÃ­cil testar)

```typescript
// âŒ Como testar este componente?
export function Component() {
  const [data, setData] = useState([])
  
  useEffect(() => {
    fetch('/api/data').then(setData)
  }, [])
  
  return <div>{data.map(...)}</div>
}

// Precisaria mockar: useState, useEffect, fetch, DOM...
```

### Com SeparaÃ§Ã£o (FÃ¡cil testar)

```typescript
// âœ… Testar hook isoladamente
describe('useProfessionals', () => {
  it('deve carregar Professionals', async () => {
    const { result } = renderHook(() => useProfessionals({}))
    
    await waitFor(() => {
      expect(result.current.loading).toBe(false)
    })
    
    expect(result.current.Professionals).toHaveLength(10)
  })
})

// âœ… Testar componente com hook mockado
describe('ProfessionalSearch', () => {
  it('deve renderizar lista', () => {
    jest.mock('@/hooks/fetch-api/useProfessionals', () => ({
      useProfessionals: () => ({
        Professionals: [{ id: 1, nome: 'Dr. JoÃ£o' }],
        loading: false
      })
    }))
    
    render(<ProfessionalSearch />)
    expect(screen.getByText('Dr. JoÃ£o')).toBeInTheDocument()
  })
})
```

---

## ðŸŽ¯ Quando Usar Cada PadrÃ£o

### Custom Hook (quando)
- âœ… LÃ³gica reutilizÃ¡vel em mÃºltiplos componentes
- âœ… Gerenciar estado complexo
- âœ… Encapsular efeitos colaterais (fetch, timers)

```typescript
// Exemplo: useLocalStorage
export function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key)
    return stored ? JSON.parse(stored) : initialValue
  })

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])

  return [value, setValue] as const
}
```

### Context (quando)
- âœ… Estado compartilhado entre MUITOS componentes
- âœ… Evitar prop drilling
- âœ… Temas, autenticaÃ§Ã£o, idioma

```typescript
// Exemplo: ThemeContext
export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light')
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light')
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}
```

### Props (quando)
- âœ… ComunicaÃ§Ã£o DIRETA pai â†’ filho
- âœ… Estado local simples
- âœ… Componentes pequenos e isolados

```typescript
// Exemplo: Props simples
interface ButtonProps {
  children: ReactNode
  onClick: () => void
  variant?: 'primary' | 'secondary'
}

export function Button({ children, onClick, variant = 'primary' }: ButtonProps) {
  return (
    <button className={`btn btn-${variant}`} onClick={onClick}>
      {children}
    </button>
  )
}
```

---

## âœ… Checklist

**SeparaÃ§Ã£o UI/LÃ³gica:**
- [ ] Componentes < 100 linhas (guideline)
- [ ] LÃ³gica complexa em hooks
- [ ] Estado global em Context
- [ ] API calls em hooks dedicados
- [ ] Componentes apenas renderizam

**Testabilidade:**
- [ ] Hooks testÃ¡veis isoladamente
- [ ] Componentes podem ser mockados
- [ ] Sem efeitos colaterais diretos em componentes

**ReutilizaÃ§Ã£o:**
- [ ] Hooks genÃ©ricos e reutilizÃ¡veis
- [ ] Componentes UI sem lÃ³gica acoplada
- [ ] Context para estado compartilhado

---

**PrÃ³ximo:** [Server vs Client Components â†’](./03-server-client-components.md)

