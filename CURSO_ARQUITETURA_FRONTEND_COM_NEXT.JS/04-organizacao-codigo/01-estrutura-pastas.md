# 01. Estrutura de Pastas - Organização Profissional

## 🎯 Objetivo

Aprender a organizar um projeto Next.js de forma escalável, seguindo padrões profissionais baseados em projetos reais em produção.

---

## 📂 Estrutura Completa Recomendada

Baseado em projeto real com **187 arquivos** em produção:

```
src/
├── app/                          # App Router (Next.js 13+)
│   ├── (grupos)/                 # Route Groups
│   │   ├── (app-main)/          # Grupo principal
│   │   ├── (auth)/              # Rotas de autenticação
│   │   └── (dashboard)/         # Admin/Dashboard
│   │
│   ├── api/                     # BFF Layer (49 endpoints)
│   │   ├── users/
│   │   │   ├── route.ts        # GET/POST /api/users
│   │   │   └── [id]/
│   │   │       └── route.ts    # GET/PUT/DELETE /api/users/:id
│   │   ├── posts/
│   │   ├── auth/
│   │   ├── registration/
│   │   ├── feedback/
│   │   └── ...                  # +40 endpoints
│   │
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx
│
├── components/                   # Componentes React (90+ componentes)
│   ├── ui/                      # Componentes base (25 componentes)
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Card.tsx
│   │   ├── Badge.tsx
│   │   ├── Modal.tsx
│   │   ├── Select.tsx
│   │   ├── Table.tsx
│   │   ├── Checkbox.tsx
│   │   └── ...
│   │
│   ├── shared/                  # Compartilhados (34 componentes)
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── Navigation.tsx
│   │   ├── SearchBar.tsx
│   │   ├── BreadCrumbs.tsx
│   │   ├── GoogleMapa.tsx
│   │   └── ...
│   │
│   ├── sections/                # Seções de página (20 componentes)
│   │   ├── HeroSection.tsx
│   │   ├── FeaturesSection.tsx
│   │   ├── BlogSection.tsx
│   │   ├── FaqSection.tsx
│   │   ├── FastAccessSection.tsx
│   │   └── ...
│   │
│   ├── registration/            # Feature específica (24 componentes)
│   │   ├── IdentificationForm.tsx
│   │   ├── AddressForm.tsx
│   │   ├── BankInfoForm.tsx
│   │   ├── Upload.tsx
│   │   └── ...
│   │
│   └── ombudsman/              # Feature específica (12 componentes)
│       ├── Badge.tsx
│       ├── CircularProgress.tsx
│       ├── Input.tsx
│       └── ...
│
├── hooks/                       # Custom hooks (8 hooks)
│   ├── fetch-api/              # Hooks de API por feature
│   │   ├── useUsers.ts
│   │   ├── usePosts.ts
│   │   ├── useNoticiasHome.ts
│   │   └── useProfessionalsData.ts
│   │
│   ├── use-cnpj-validation.ts
│   ├── use-formatted-date.ts
│   ├── useMaskValidation.ts
│   ├── useFaqManager.ts
│   └── useCheckIsMobile.ts
│
├── contexts/                    # Context API (3 contexts)
│   ├── DentistsContext.tsx
│   ├── FaqContext.tsx
│   └── BreadCrumbsContext.tsx
│
├── lib/                        # Configurações e clients
│   ├── api-client.ts          # Axios configurado (BFF)
│   ├── env.ts                 # Validação de env vars
│   ├── db.ts                  # Prisma client (se usar DB)
│   └── utils.ts               # Funções auxiliares
│
├── schemas/                    # Zod schemas (2 schemas)
│   ├── user.schema.ts
│   ├── post.schema.ts
│   ├── cnpjSchema.ts
│   └── registrationSchema.ts
│
├── types/                      # TypeScript types (4 arquivos)
│   ├── api.ts                 # Tipos de API
│   ├── models.ts              # Modelos de dados
│   ├── global.d.ts            # Tipos globais
│   └── registration.ts        # Tipos de formulários
│
├── utils/                      # Funções utilitárias
│   ├── format.ts              # Formatação (datas, moeda)
│   ├── validators.ts          # Validações customizadas
│   ├── sanitize-html.ts       # Sanitização HTML
│   └── helpers.ts             # Helpers gerais
│
├── constants/                  # Constantes (4 arquivos)
│   ├── enums.ts               # Enums
│   ├── interfaces.ts          # Interfaces compartilhadas
│   ├── config.ts              # Configurações
│   └── routes.ts              # Rotas da aplicação
│
├── data/                       # Dados estáticos/mocks
│   ├── menuMock.ts
│   └── mockFaq.ts
│
├── assets/                    # Assets estáticos
│   ├── icons/
│   └── images/
│
└── middleware.ts              # Middleware global
```

---

## 📊 Estatísticas Real Project

**Projeto Analisado:**
- Total de arquivos: 187
- Components (`.tsx`): 90 arquivos
- TypeScript (`.ts`): 23 arquivos
- API Routes: 49 endpoints
- Hooks: 8 custom hooks
- Contexts: 3 contexts
- Schemas: 2 Zod schemas

---

## 🎯 Princípios de Organização

### 1. **Separação por Responsabilidade**

```
❌ ERRADO - Tudo misturado:
src/
├── Button.tsx
├── Header.tsx
├── useApi.ts
├── UserPage.tsx
└── api-client.ts

✅ CORRETO - Separado por tipo:
src/
├── components/
│   ├── ui/Button.tsx
│   └── shared/Header.tsx
├── hooks/useApi.ts
├── lib/api-client.ts
└── app/users/page.tsx
```

### 2. **Colocation (Colocar junto o que é usado junto)**

```
✅ Feature Registration:
components/registration/
├── IdentificationForm.tsx
├── AddressForm.tsx
├── BankInfoForm.tsx
├── StepNavigation.tsx
└── Upload.tsx

✅ Feature Ombudsman:
components/ombudsman/
├── Badge.tsx
├── Input.tsx
├── EvaluationModal.tsx
└── OmbudsmanModal.tsx
```

### 3. **Hierarquia Clara**

```
components/
├── ui/              ← Mais genérico (reutilizável em todo projeto)
├── shared/          ← Compartilhado entre páginas
├── sections/        ← Seções específicas de páginas
└── [feature]/       ← Componentes de feature específica
```

---

## 🚫 Anti-Patterns (O que NÃO fazer)

### Anti-Pattern 1: Arquivos "copy"

```
❌ NUNCA FAÇA ISSO:
components/
├── Upload.tsx
├── Upload copy.tsx          # 306 linhas IDÊNTICAS!
├── SearchBar.tsx
└── SearchBar copy.tsx       # Versão quebrada

Real project tinha 7 arquivos lixo assim!
```

**Solução:** Use Git para versionamento, não cópias manuais.

### Anti-Pattern 2: Componentes Duplicados

```
❌ 4 VERSÕES DE INPUT:
components/
├── ui/Input.tsx              # 40 linhas
├── ombudsman/Input.tsx       # 59 linhas
├── registration/Input.tsx    # 391 linhas
└── FloatingLabelInput.tsx    # 227 linhas

Total: ~700 linhas duplicadas!
```

**Solução:** Consolidar em 1-2 componentes com props opcionais.

### Anti-Pattern 3: Funções Espalhadas

```
❌ FORMATAÇÃO EM 3 LUGARES:
hooks/useMaskValidation.ts → formatCPF()
schemas/cnpjSchema.ts → formatCnpj()
components/LGPDModal.tsx → formatCPF()

Mesma lógica, 3 implementações!
```

**Solução:** Centralizar em `utils/format.ts` ou hook reutilizável.

---

## ✅ Boas Práticas

### 1. **Nomes Descritivos**

```typescript
// ❌ Genérico demais
components/Form.tsx
components/Card.tsx

// ✅ Específico e claro
components/registration/IdentificationForm.tsx
components/shared/BlogCard.tsx
```

### 2. **Agrupamento por Feature**

```
// ✅ Registration feature completa
components/registration/
├── IdentificationForm.tsx
├── AddressForm.tsx
├── BankInfoForm.tsx
├── StepNavigation.tsx
├── TermsOfConsent.tsx
└── corretores/               # Sub-feature
    ├── CnpjStep.tsx
    ├── CompanyDataStep.tsx
    └── Upload.tsx
```

### 3. **Separação UI vs Lógica**

```typescript
// ❌ Lógica misturada no componente
export function UserList() {
  const [users, setUsers] = useState([])
  
  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(setUsers)
  }, [])
  
  return <ul>{users.map(...)}</ul>
}

// ✅ Lógica no hook, UI no componente
// hooks/fetch-api/useUsers.ts
export function useUsers() {
  const [users, setUsers] = useState([])
  const [loading, setLoading] = useState(true)
  
  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(setUsers)
      .finally(() => setLoading(false))
  }, [])
  
  return { users, loading }
}

// components/UserList.tsx
export function UserList() {
  const { users, loading } = useUsers()
  
  if (loading) return <div>Loading...</div>
  return <ul>{users.map(...)}</ul>
}
```

---

## 📁 Convenções de Nomenclatura

### Arquivos

```
PascalCase:
- Componentes: Button.tsx, UserCard.tsx
- Contexts: ThemeContext.tsx
- Types: User.ts (se exportar type/interface)

camelCase:
- Hooks: useAuth.ts, useFetch.ts
- Utils: format.ts, validate.ts
- Schemas: userSchema.ts

kebab-case:
- Pages: user-profile/page.tsx (Next.js recomenda)
- API Routes: user-posts/route.ts
```

### Exports

```typescript
// ✅ Named exports (preferível para tree-shaking)
export function Button() {}
export function Input() {}

// ✅ Default export para páginas Next.js
export default function HomePage() {}

// ❌ Misturar sem critério
export default function Button() {}  // Confuso!
export function Input() {}
```

---

## 🔄 Refatoração de Projeto Real

### Antes (Problemas Reais)

```
components/
├── Upload.tsx
├── Upload copy.tsx                    ← Duplicata 100%
├── SearchBarAdvanced.tsx
├── SearchBarAdvanced copy.tsx         ← Versão quebrada
├── ui/Input.tsx                       ← 40 linhas
├── ombudsman/Input.tsx                ← 59 linhas
├── registration/Input.tsx             ← 391 linhas
└── FloatingLabelInput.tsx             ← 227 linhas

Total: 7 inputs, ~700 linhas duplicadas!
```

### Depois (Consolidado)

```
components/
├── ui/
│   ├── Input.tsx                      ← Versão base (50 linhas)
│   └── FormInput.tsx                  ← Com React Hook Form (120 linhas)
└── shared/
    └── SearchBarAdvanced.tsx          ← Única versão funcional

Total: 3 componentes, ~220 linhas
Redução: 68% menos código!
```

---

## ✅ Checklist de Organização

### Estrutura
- [ ] Pastas separadas por tipo (components, hooks, lib)
- [ ] Features organizadas por colocation
- [ ] UI components genéricos
- [ ] Sem arquivos "copy" ou "backup"

### Nomenclatura
- [ ] PascalCase para componentes
- [ ] camelCase para hooks/utils
- [ ] Nomes descritivos e específicos

### Separação
- [ ] Lógica em hooks, não em componentes
- [ ] Configurações em lib/
- [ ] Tipos em types/
- [ ] Utils em utils/

### Manutenibilidade
- [ ] Sem duplicação de código
- [ ] Componentes < 200 linhas (guideline)
- [ ] Funções reutilizáveis centralizadas

---

## 📚 Exemplo Completo

Ver estrutura real de projeto em produção:
- 187 arquivos organizados
- 90 componentes bem estruturados
- 49 API routes no BFF
- Zero duplicações críticas (após refatoração)

---

**Próximo:** [Separação UI/Lógica →](./02-separacao-ui-logica.md)
