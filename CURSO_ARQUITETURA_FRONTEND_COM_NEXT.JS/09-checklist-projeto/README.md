# Módulo 09: Checklists de Projeto

## 📋 Checklists Prontos para Uso

### 1. Checklist de Arquitetura
- [ ] BFF implementado (Route Handlers)
- [ ] Separação Server/Client components
- [ ] Estrutura de pastas organizada
- [ ] Custom hooks reutilizáveis
- [ ] Context para estado global
- [ ] TypeScript strict mode

### 2. Checklist de Segurança
- [ ] CSP Level 3 com nonce
- [ ] 7 headers OWASP configurados
- [ ] Sanitização HTML (DOMPurify)
- [ ] Credenciais apenas server-side
- [ ] Cookies com flags seguros
- [ ] Score A+ em SecurityHeaders.com

### 3. Checklist de Performance
- [ ] Cache-Control configurado
- [ ] Imagens otimizadas (next/image)
- [ ] Bundle size otimizado
- [ ] Code splitting implementado
- [ ] Lazy loading onde apropriado
- [ ] Lighthouse score > 90

### 4. Checklist de Deploy
- [ ] Variáveis de ambiente configuradas
- [ ] Build sem erros/warnings
- [ ] Testes passando
- [ ] CI/CD configurado
- [ ] Monitoramento configurado
- [ ] Documentação atualizada

---

## ✅ Checklist de Revisão (como testar cada item)

- [ ] Arquitetura: conferir colocation no App Router (Next.js Routing: https://nextjs.org/docs/app/building-your-application/routing)
- [ ] Segurança: rodar SecurityHeaders.com e Observatory; CSP/headers em `next.config.js`; DOMPurify onde há HTML dinâmico (OWASP: https://owasp.org/www-project-secure-headers)
- [ ] Performance: Lighthouse > 90 (Chrome DevTools); `next/image` e cache configurados
- [ ] Deploy: `npm run build` sem warnings; CI (GitHub Actions) com estágios lint/type/test/build/scan; variáveis configuradas no ambiente de deploy

---

Use esses checklists em:
- ✅ Code reviews
- ✅ Onboarding de devs
- ✅ Auditorias internas
- ✅ Pré-deploy

⏱️ **Tempo:** 2-3 horas
