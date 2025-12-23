# Módulo 06: Tailwind Profissional

## 📘 Conteúdo

1. **Configuração** - tailwind.config.ts completo
2. **Globals vs Config** - Quando usar cada
3. **Estilos Customizados** - Extend do theme
4. **Responsividade** - Breakpoints e mobile-first
5. **Dark Mode** - Implementação opcional

## 🎯 Objetivos

- ✅ Tailwind configurado profissionalmente
- ✅ Sistema de design consistente
- ✅ Utility-first approach
- ✅ Responsivo e performático

---

## ✅ Checklist de Revisão

- [ ] `content` paths corretos no `tailwind.config.ts` (Tailwind Config: https://tailwindcss.com/docs/configuration)
- [ ] Tokens definidos (cores, spacing, fonte, radius) no `theme.extend`
- [ ] globals.css com resets básicos e variáveis que fizerem sentido
- [ ] Padrão de variantes (clsx/cva) para estados e tamanhos
- [ ] Responsividade mobile-first usando breakpoints do Tailwind
- [ ] Foco visível e `prefers-reduced-motion` respeitado
- [ ] Estratégia de dark mode definida (class ou media) se aplicável

---

## 💡 Exemplo - Custom Theme

```typescript
// tailwind.config.ts
export default {
  theme: {
    extend: {
      colors: {
        brand:  {
          50: '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a8a',
        }
      }
    }
  }
}
```

```tsx
// Uso
<div className="bg-brand-500 hover:bg-brand-600">
  Cor da marca
</div>
```

⏱️ **Tempo:** 3-4 horas
