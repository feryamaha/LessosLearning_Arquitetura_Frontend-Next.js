# Proteção XSS - Múltiplas Camadas

## 🛡️ Estratégia de Defesa em Profundidade

### Camada 1: CSP Level 3
Bloqueia execução de scripts não autorizados.

### Camada 2: Sanitização HTML
Remove tags/scripts perigosos antes de renderizar.

### Camada 3: Encoding
Escapar caracteres especiais.

### Camada 4: Validação Input
Validar dados na entrada.

## ✅ Stack Completo

```
User Input → Validation → BFF → Sanitização → CSP → Render
```

Múltiplas camadas = Se uma falhar, as outras protegem.

---

**Próximo:** [Cookies Seguros →](./05-cookies-seguros.md)
