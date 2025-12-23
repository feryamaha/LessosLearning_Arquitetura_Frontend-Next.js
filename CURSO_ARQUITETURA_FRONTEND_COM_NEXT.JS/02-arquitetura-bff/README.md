# Módulo 02: Arquitetura BFF (Backend-for-Frontend)

## 📘 Visão Geral

BFF (Backend-for-Frontend) é um padrão arquitetural onde você cria uma camada intermediária no servidor entre o frontend e APIs externas. No Next.js, isso é feito através de **Route Handlers** (`app/api/**/route.ts`).

---

## 🎯 Por Que Usar BFF?

### Problema: Chamada Direta à API Externa

```tsx
// ❌ PROBLEMA: Credenciais expostas no frontend
'use client'

const api =  axios.create({
  baseURL: 'https://api.externa.com',
  headers: {
    'client-id': 'meu-id',        // ⚠️ EXPOSTO NO BUNDLE
    'client-token': 'meu-token'   // ⚠️ EXPOSTO NO BUNDLE
  }
})

// Qualquer pessoa pode abrir DevTools e ver estas credenciais!
```

### Solução: BFF Layer

```
┌─────────────┐                ┌─────────────┐                ┌──────────────┐
│  Frontend   │  fetch()       │   BFF       │   axios        │ API Externa  │
│  (Browser)  │ ────────────>  │  (Next.js)  │ ────────────>  │              │
│             │  /api/*        │  Server     │  com headers   │              │
└─────────────┘                └─────────────┘                └──────────────┘
   ✅ Zero credenciais            ✅ Credenciais                 ✅ Recebe
      expostas                        server-side                   autenticação
```

---

## 📚 Conteúdo

1. [O que é BFF](./01-o-que-e-bff.md)
2. [Route Handlers](./02-route-handlers.md)
3. [Proteção de Credenciais](./03-protecao-credenciais.md)
4. [Casos Práticos](./04-casos-praticos.md)

---

## 🎯 Objetivos

- ✅ Entender arquitetura BFF
- ✅ Criar Route Handlers no Next.js
- ✅ Proteger credenciais API
- ✅ Implementar proxy seguro para APIs externas

---

## ✅ Checklist de Revisão

- [ ] Route Handlers em `app/api/**/route.ts` com métodos corretos (Next.js Route Handlers: https://nextjs.org/docs/app/building-your-application/routing/route-handlers)
- [ ] Validação de input (ex.: Zod) antes de chamar APIs externas
- [ ] Erros tratados com `NextResponse.json` e status adequado
- [ ] Cache e revalidação configurados (`cache`, `next: { revalidate }`, headers de cache)
- [ ] Sem credenciais no cliente: secrets somente em variáveis de ambiente server-side
- [ ] `.env.local` no `.gitignore` e `.env.example` commitado
- [ ] Logs mínimos de erro sem vazar dados sensíveis

---

## ⏱️ Tempo Estimado

**4-5 horas** (teor + prática)

---

**Próximo:** [O que é BFF →](./01-o-que-e-bff.md)
