# Módulo 08: Casos Completos

## 📘 Exemplos End-to-End

Implementações completas de casos reais:

1. **Formulário de Cadastro** - Com validação Zod
2. **Integração API** - BFF + Hook + Component
3. **Autenticação** - Login/Logout flow
4. **Upload de Arquivos** - FormData + Progress
5. **Paginação Infinita** - Infinite scroll

## 🎯 Estrutura de Cada Caso

Cada caso inclui:
- ✅ Frontend completo (Component + Hook)
- ✅ BFF (Route Handler)
- ✅ Validação (Zod schema)
- ✅ Tipagem TypeScript
- ✅ Tratamento de erros
- ✅ Loading states

---

## ✅ Checklist de Revisão

- [ ] Formulários com React Hook Form + Zod; mensagens de erro acessíveis
- [ ] BFF (Route Handler) com validação e códigos de status corretos
- [ ] Segredos somente no server; nada sensível em `NEXT_PUBLIC`
- [ ] Upload com limites de tamanho, validação de MIME e resposta de erro clara
- [ ] Integrações com timeout/retry/backoff e logs mínimos
- [ ] Teste de fluxo (ex.: Playwright) cobrindo caminho feliz + erros

---

## 💡 Preview - Formulário

```tsx
// Zod Schema
const userSchema = z.object({
  name: z.string().min(3),
  email: z.string().email()
})

// Hook
function useUserForm() {
  const { register, handleSubmit } = useForm({
    resolver: zodResolver(userSchema)
  })
  // ...
}

// Component
<form onSubmit={handleSubmit(onSubmit)}>
  <input {...register('name')} />
  <input {...register('email')} />
  <button>Enviar</button>
</form>
```

⏱️ **Tempo:** 6-8 horas
