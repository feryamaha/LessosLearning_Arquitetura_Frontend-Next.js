# TypeScript - Configuração Profissional

## 🎯 tsconfig.json

Configuração recomendada para projetos Next.js profissionais.

```json
{
  "compilerOptions": {
    // Strict Mode (recomendado)
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    
    // Verificações adicionais
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    
    // Module Resolution
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    
    // JSX
    "jsx": "preserve",
    "lib": ["dom", "dom.iterable", "esnext"],
    
    // Paths
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/hooks/*": ["./src/hooks/*"]
    },
    
    // Next.js
    "target": "es5",
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "incremental": true,
    
    "plugins": [
      {
        "name": "next"
      }
    ]
  },
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

---

## 🔍 Strict Mode Explicado

### strict: true

Habilita TODAS as verificações strictas. Equivale a:

```json
{
  "noImplicitAny": true,
  "strictNullChecks": true,
  "strictFunctionTypes": true,
  "strictBindCallApply": true,
  "strictPropertyInitialization": true,
  "noImplicitThis": true,
  "alwaysStrict": true
}
```

### Por que usar?

✅ **Detecta bugs** em tempo de compilação  
✅ **Autocomplete melhor** na IDE  
✅ **Refatoração segura** - erros aparecem imediatamente  
✅ **Código mais confiável** - menos surpresas em runtime  

---

## 🛠️ Configurações Importantes

### baseUrl e paths

Permite imports absolutos ao invés de relativos.

```tsx
// ❌ Sem paths (import relativo)
import { Button } from '../../../components/ui/Button'

// ✅ Com paths (import absoluto)
import { Button } from '@/components/ui/Button'
```

**Configuração:**

```json
{
  "baseUrl": ".",
  "paths": {
    "@/*": ["./src/*"]
  }
}
```

### noUnusedLocals e noUnusedParameters

Detecta variáveis e parâmetros não utilizados.

```tsx
// ❌ Erro: 'unused' nunca é utilizado
function calculate(a: number, b: number, unused: number) {
  return a + b
}

// ✅ OK: Prefixo '_' ignora o erro
function calculate(a: number, b: number, _unused: number) {
  return a + b
}
```

### noImplicitReturns

Garante que todas as branches retornam valor.

```tsx
// ❌ Erro: nem todos os caminhos retornam
function getStatus(code: number): string {
  if (code === 200) {
    return 'OK'
  }
  // Falta return para outros casos!
}

// ✅ OK
function getStatus(code: number): string {
  if (code === 200) {
    return 'OK'
  }
  return 'Error'
}
```

---

## 📦 Tipos Globais

### next-env.d.ts

Gerado automaticamente pelo Next.js. **Não editar!**

```typescript
/// <reference types="next" />
/// <reference types="next/image-types/global" />

//  NOTE: This file should not be edited
// see https://nextjs.org/docs/basic-features/typescript for more information.
```

### Tipos Customizados

Criar arquivo `types/global.d.ts`:

```typescript
// types/global.d.ts

// Variáveis de ambiente
declare namespace NodeJS {
  interface ProcessEnv {
    NEXT_PUBLIC_API_URL: string
    DATABASE_URL: string
    SECRET_KEY: string
  }
}

// Módulos sem tipos
declare module '*.svg' {
  const content: any
  export default content
}

// Extensões globais
declare global {
  interface Window {
    gtag?: (...args: any[]) => void
  }
}

export {}
```

---

## ✅ Checklist

- [ ] `strict: true` habilitado
- [ ] `noUnusedLocals` e `noUnusedParameters` ativos
- [ ] Paths configurados (`@/*`)
- [ ] Tipos customizados em `types/`

---

**Próximo:** [Tailwind Setup →](./04-tailwind-setup.md)
