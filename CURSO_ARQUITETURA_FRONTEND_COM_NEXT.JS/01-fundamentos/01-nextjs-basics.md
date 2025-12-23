# Next.js 15 - Fundamentos

## 🎯 O que é Next.js?

Next.js é um **framework React full-stack** que permite criar aplicações web modernas com renderização híbrida (server-side, client-side, estática).

### Por que Next.js?

✅ **Server-Side Rendering (SSR)** - SEO otimizado  
✅ **Static Site Generation (SSG)** - Performance máxima  
✅ **API Routes** - Backend integrado  
✅ **File-based Routing** - Rotas automáticas  
✅ **Otimizações automáticas** - Imagens, fonts, código  

---

## 📁 App Router (Next.js 13+)

O App Router é a nova arquitetura do Next.js baseada em React Server Components.

### Estrutura de Pastas

```
app/
├── layout.tsx          # Layout raiz (obrigatório)
├── page.tsx            # Página inicial (/)
├── loading.tsx         # Loading UI
├── error.tsx           # Error boundary
├── not-found.tsx       # 404 page
│
├── (grupo)/            # Route Group (não afeta URL)
│   ├── page.tsx
│   └── layout.tsx
│
├── dashboard/
│   ├── page.tsx        # /dashboard
│   └── [id]/
│       └── page.tsx    # /dashboard/123 (dynamic route)
│
└── api/                # API Routes (Backend)
    └── users/
        └── route.ts    # /api/users
```

---

## 🔄 Server Components vs Client Components

### Server Components (Padrão)

Componentes que rodam **apenas no servidor**.

```tsx
// app/page.tsx (Server Component por padrão)
export default async function HomePage() {
  // Pode fazer fetch direto, sem useEffect
  const data = await fetch('https://api.example.com/data')
  const json = await data.json()

  return <div>{json.title}</div>
}
```

**Vantagens:**
- ✅ Acesso direto a banco de dados
- ✅ Credenciais seguras (server-side)
- ✅ Menor bundle JavaScript
- ✅ SEO automaticamente

**Limitações:**
- ❌ Sem hooks (`useState`, `useEffect`)
- ❌ Sem event handlers (`onClick`, `onChange`)
- ❌ Sem browser APIs (`window`, `localStorage`)

---

### Client Components

Componentes que rodam **no navegador**.

```tsx
'use client' // ← Diretiva obrigatória

import { useState } from 'react'

export default function Counter() {
  const [count, setCount] = useState(0)

  return (
    <button onClick={() => setCount(count + 1)}>
      Clicks: {count}
    </button>
  )
}
```

**Quando usar:**
- ✅ Interatividade (clicks, forms)
- ✅ Hooks do React
- ✅ Browser APIs
- ✅ Event listeners

**Regra de ouro:** Use Server Components por padrão, Client Components apenas quando necessário.

---

## 🛣️ Routing

### Rotas Estáticas

```
app/
├── about/
│   └── page.tsx        → /about
├── blog/
│   └── page.tsx        → /blog
└── contact/
    └── page.tsx        → /contact
```

### Rotas Dinâmicas

```tsx
// app/blog/[slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>Post: {params.slug}</h1>
}

// URLs válidas:
// /blog/hello-world
// /blog/nextjs-tutorial
```

### Catch-all Routes

```tsx
// app/docs/[...slug]/page.tsx
export default function Docs({ params }: { params: { slug: string[] } }) {
  return <div>Caminho: {params.slug.join('/')}</div>
}

// URLs válidas:
// /docs/getting-started
// /docs/api/authentication
// /docs/guides/deployment/vercel
```

---

## 📄 Arquivos Especiais

### layout.tsx

Layout compartilhado entre páginas.

```tsx
// app/layout.tsx (Root Layout - obrigatório)
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="pt-BR">
      <body>
        <header>Header Global</header>
        {children}
        <footer>Footer Global</footer>
      </body>
    </html>
  )
}
```

**Características:**
- ✅ Layout raiz (`app/layout.tsx`) é obrigatório
- ✅ Pode ser aninhado (layout dentro de layout)
- ✅ Preserva estado ao navegar
- ✅ É um Server Component por padrão

### loading.tsx

UI de loading automática.

```tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return <div>Carregando dashboard...</div>
}
```

### error.tsx

Error boundary automático.

```tsx
'use client' // Error boundaries devem ser Client Components

export default function Error({
  error,
  reset,
}: {
  error: Error
  reset: () => void
}) {
  return (
    <div>
      <h2>Algo deu errado!</h2>
      <button onClick={reset}>Tentar novamente</button>
    </div>
  )
}
```

---

## 🔧 Renderização

### SSG (Static Site Generation)

Gerado em **build time**.

```tsx
// app/blog/page.tsx
export default async function BlogPage() {
  const posts = await fetch('https://api.example.com/posts')
  return <PostsList posts={posts} />
}
```

### SSR (Server-Side Rendering)

Gerado em **request time**.

```tsx
// Força revalidação a cada request
export const dynamic = 'force-dynamic'

export default async function DashboardPage() {
  const data = await fetch('https://api.example.com/dashboard', {
    cache: 'no-store' // Não cachear
  })
  return <Dashboard data={data} />
}
```

### ISR (Incremental Static Regeneration)

Revalida a cada X segundos.

```tsx
export const revalidate = 3600 // Revalida a cada 1 hora

export default async function NewsPage() {
  const news = await fetch('https://api.example.com/news')
  return <NewsList news={news} />
}
```

---

## 🎨 Metadata

SEO configurado de forma declarativa.

```tsx
// app/page.tsx
import { Metadata } from 'next'

export const metadata: Metadata = {
  title: 'Minha Aplicação',
  description: 'Descrição para SEO',
  keywords: ['nextjs', 'react', 'typescript'],
  openGraph: {
    title: 'Minha App',
    description: 'Compartilhe nas redes sociais',
    images: ['/og-image.png'],
  },
}

export default function Page() {
  return <h1>Home</h1>
}
```

---

## 📚 Recursos

- [Documentação Oficial do Next.js](https://nextjs.org/docs)
- [App Router Guide](https://nextjs.org/docs/app)
- [Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components)

---

## ✅ Checklist

- [ ] Entendi a diferença entre App Router e Pages Router
- [ ] Sei quando usar Server vs Client Components
- [ ] Conheço os arquivos especiais (layout, loading, error)
- [ ] Entendo os tipos de renderização (SSG, SSR, ISR)

---

**Próximo:** [React Patterns →](./02-react-patterns.md)
