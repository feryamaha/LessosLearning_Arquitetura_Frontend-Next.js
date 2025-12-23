# Módulo 05: TypeScript Profissional

## 📘 Conteúdo

1. **Configuração Strict** - tsconfig.json profissional
2. **Tipagem de Componentes** - Props, State, Events
3. **Tipagem de API** - Requests e Responses
4. **Evitar `any`** - Boas práticas e alternativas
5. **Generics** - Tipos reutilizáveis

## 🎯 Objetivos

- ✅ TypeScript em modo strict
- ✅ Zero uso de `any`
- ✅ Tipagem completa de API
- ✅ Autocomplete perfeito na IDE

---

## 💡 Exemplo - Tipagem API

```typescript
// types/api.ts
export interface User {
  id: number
  name: string
  email: string
}

export interface ApiResponse<T> {
  data: T
  status: 'success' | 'error'
  message?: string
}

//  hooks/useApi.ts
function useApi<T>(url: string): ApiResponse<T> {
  // Tipagem automática!
}

// Uso
const { data } = useApi<User[]>('/api/users')
// data é User[] - autocomplete funciona!
```

⏱️ **Tempo:** 3-4 horas
