# Cookies Seguros

## ⚙️ Configuração

```typescript
response.cookies.set('token', 'value', {
  httpOnly: true,      // Não acessível via JS
  secure: true,        // Apenas HTTPS
  sameSite: 'strict',  // Proteção CSRF
  maxAge: 86400,       // 24 horas
  path: '/'
})
```

## 🛡️ Flags de Segurança

- **httpOnly**: Previne acesso via `document.cookie`  
- **secure**: Apenas em HTTPS
- **sameSite**: Previne CSRF attacks

---

**Módulo concluído!** → [Próximo: Organização de Código](../04-organizacao-codigo/README.md)
