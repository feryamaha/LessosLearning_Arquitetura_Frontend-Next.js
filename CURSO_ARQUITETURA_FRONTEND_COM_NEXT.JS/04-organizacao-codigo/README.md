# Módulo 04: Organização de Código

## 📘 Visão Geral

Padrões profissionais de organização de código para projetos Next.js escaláveis.

## 📚 Conteúdo

1. Estrutura de Pastas - Organização lógica
2. Separação UI/Lógica - Componentes vs Hooks
3. Server vs Client Components - Quando usar cada
4. Renderização Híbrida - SSR, SSG, ISR
5. Hooks Patterns - Custom hooks reutilizáveis
6. Context Patterns - Estado global
7. Schemas e Validação - Zod integration
8. Lib vs Utils - Diferenças e quando usar

## 🎯 Objetivos

- ✅ Estrutura de pastas escalável
- ✅ Separação clara de responsabilidades
- ✅ Reuso de código
- ✅ Manutenibilidade

---

## 📁 Estrutura Recomendada

```
src/
├── app/                      # App Router
│   ├── (grupo)/             # Route groups
│   │   ├── layout.tsx
│   │   └── page.tsx
│   └── api/                 # BFF Layer
│       └── **/route.ts
│
├── components/              # Componentes React
│   ├── ui/                 # Componentes base
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   └── Card.tsx
│   ├── shared/             # Componentes compartilhados
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── Navigation.tsx
│   └── sections/           # Sections específicas
│       ├── HeroSection.tsx
│       └── FeaturesSection.tsx
│
├── hooks/                   # Custom hooks
│   ├── useApi.ts
│   ├── useLocalStorage.ts
│   └── fetch-api/          # Hooks por feature
│       ├── useUsers.ts
│       └── usePost.ts
│
├── contexts/                # Context API
│   ├── ThemeContext.tsx
│   └── AuthContext.tsx
│
├── lib/                     # Configurações/Clients
│   ├── api-client.ts
│   ├── env.ts
│   └── db.ts
│
├── utils/                   # Funções utilitárias
│   ├── format.ts
│   ├── validators.ts
│   └── helpers.ts
│
├── schemas/                 # Zod schemas
│   ├── user.schema.ts
│   └── post.schema.ts
│
└── types/                   # TypeScript types
    ├── api.ts
    └── global.d.ts
```

---

## ✅ Checklist de Revisão

- [ ] Estrutura por feature/colocation alinhada ao App Router (Next.js Routing: https://nextjs.org/docs/app/building-your-application/routing)
- [ ] Server vs Client Components definidos; `"use client"` apenas quando necessário
- [ ] Lógica em hooks/contexts, componentes focados em renderização
- [ ] Pastas claras: app/, components/ui|shared|sections, hooks/, lib/, utils/, schemas/, types/
- [ ] Tamanho dos componentes sob controle (<150-200 linhas como guideline)
- [ ] Sem duplicação de componentes/arquivos “copy”
- [ ] Schemas (Zod) próximos dos casos de uso; types globais em `types/`
- [ ] Testabilidade: hooks e funções puras fáceis de isolar

---

⏱️ **Tempo:** 6-7 horas
