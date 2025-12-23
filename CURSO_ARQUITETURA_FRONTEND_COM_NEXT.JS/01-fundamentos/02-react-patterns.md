# React 19 - Padrões Modernos

## 🎯 Hooks Fundamentais

### useState

Gerencia estado  local em componentes.

```tsx
'use client'

import { useState } from 'react'

export function Counter() {
  const [count, setCount] = useState(0)
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Contagem: {count}
    </button>
  )
}
```

### useEffect

Executa efeitos colaterais.

```tsx
'use client'

import { useState, useEffect } from 'react'

export function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    let isMounted = true

    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        if (isMounted) {
          setUser(data)
          setLoading(false)
        }
      })

    // Cleanup
    return () => {
      isMounted = false
    }
  }, [userId]) // Dependência: reexecuta se userId mudar

  if (loading) return <div>Carregando...</div>
  return <div>Usuário: {user?.name}</div>
}
```

**Regras importantes:**
- ✅ Sempre limpar recursos (cleanup function)
- ✅ Especificar dependências corretas
- ❌ Nunca chamar hooks dentro de condições/loops

---

## 🎨 Composição de Componentes

### Props Tipadas

```tsx
interface ButtonProps {
  children: React.ReactNode
  variant?: 'primary' | 'secondary'
  onClick?: () => void
  disabled?: boolean
}

export function Button({ 
  children, 
  variant = 'primary',
  onClick,
  disabled = false 
}: ButtonProps) {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  )
}

// Uso
<Button variant="secondary" onClick={() => alert('Clicou!')}>
  Clique Aqui
</Button>
```

### Children Pattern

```tsx
interface CardProps {
  children: React.ReactNode
  title?: string
}

export function Card({ children, title }: CardProps) {
  return (
    <div className="card">
      {title && <h2>{title}</h2>}
      <div className="card-body">
        {children}
      </div>
    </div>
  )
}

// Uso
<Card title="Meu Card">
  <p>Conteúdo aqui</p>
  <Button>Ação</Button>
</Card>
```

---

## 🔄 Custom Hooks

Reutilização de lógica.

```tsx
// hooks/useApi.ts
'use client'

import { useState, useEffect } from 'react'

interface UseApiResult<T> {
  data: T | null
  loading: boolean
  error: string | null
}

export function useApi<T>(url: string): UseApiResult<T> {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    let isMounted = true

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`Erro ${res.status}`)
        return res.json()
      })
      .then(data => {
        if (isMounted) {
          setData(data)
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
  }, [url])

  return { data, loading, error }
}

// Uso
function UsersPage() {
  const { data, loading, error } = useApi<User[]>('/api/users')

  if (loading) return <div>Carregando...</div>
  if (error) return <div>Erro: {error}</div>
  
  return (
    <ul>
      {data?.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  )
}
```

---

## 🎯 Context API

Compartilhar estado global.

```tsx
// contexts/ThemeContext.tsx
'use client'

import { createContext, useContext, useState, ReactNode } from 'react'

type Theme = 'light' | 'dark'

interface ThemeContextType {
  theme: Theme
  toggleTheme: () => void
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined)

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light')

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light')
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}

export function useTheme() {
  const context = useContext(ThemeContext)
  if (!context) {
    throw new Error('useTheme deve ser usado dentro de ThemeProvider')
  }
  return context
}

// Uso no layout
export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html>
      <body>
        <ThemeProvider>
          {children}
        </ThemeProvider>
      </body>
    </html>
  )
}

// Uso em componentes
function ThemeToggle() {
  const { theme, toggleTheme } = useTheme()
  
  return (
    <button onClick={toggleTheme}>
      Tema atual: {theme}
    </button>
  )
}
```

---

## ⚡ Performance

### React.memo

Evita re-renders desnecessários.

```tsx
import { memo } from 'react'

interface UserCardProps {
  user: {
    id: string
    name: string
    email: string
  }
}

// Só re-renderiza se props mudarem
export const UserCard = memo(function UserCard({ user }: UserCardProps) {
  console.log('Renderizando UserCard', user.id)
  
  return (
    <div>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  )
})
```

### useCallback

Memoriza funções.

```tsx
'use client'

import { useState, useCallback } from 'react'

export function TodoList() {
  const [todos, setTodos] = useState([])

  // Função memorizada - mesma referência entre renders
  const addTodo = useCallback((text: string) => {
    setTodos(prev => [...prev, { id: Date.now(), text }])
  }, []) // Sem dependências = nunca muda

  return (
    <div>
      <TodoInput onAdd={addTodo} />
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </div>
  )
}
```

---

## 🎨 Padrões de Componentes

### Compound Components

```tsx
interface TabsProps {
  children: ReactNode
  defaultValue: string
}

interface TabListProps {
  children: ReactNode
}

interface TabProps {
  value: string
  children: ReactNode
}

export function Tabs({ children, defaultValue }: TabsProps) {
  const [activeTab, setActiveTab] = useState(defaultValue)

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  )
}

export function TabList({ children }: TabListProps) {
  return <div className="tab-list">{children}</div>
}

export function Tab({ value, children }: TabProps) {
  const { activeTab, setActiveTab } = useTabsContext()
  
  return (
    <button
      className={activeTab === value ? 'active' : ''}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  )
}

// Uso
<Tabs defaultValue="tab1">
  <TabList>
    <Tab value="tab1">Tab 1</Tab>
    <Tab value="tab2">Tab 2</Tab>
  </TabList>
  <TabPanel value="tab1">Conteúdo 1</TabPanel>
  <TabPanel value="tab2">Conteúdo 2</TabPanel>
</Tabs>
```

---

## ✅ Boas Práticas

✅ **Hooks no topo** - Nunca dentro de condições/loops  
✅ **Nomenclatura** - `use` prefix para custom hooks  
✅ **Dependências** - Sempre declarar no array de deps  
✅ **Cleanup** - Limpar timers, subscriptions, etc  
✅ **Tipagem** - Props e state sempre tipados  
✅ **Composição** - Preferir composição sobre herança  

---

**Próximo:** [TypeScript Config →](./03-typescript-config.md)
