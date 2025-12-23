# Tailwind CSS - Setup Profissional

## 🎯 Instalação

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

---

## ⚙️ tailwind.config.ts

Configuração completa e profissional.

```typescript
import type { Config } from 'tailwindcss'

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        // Cores customizadas do projeto
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          900: '#1e3a8a',
        },
        secondary: {
          500: '#8b5cf6',
          600: '#7c3aed',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
      spacing: {
        '128': '32rem',
        '144': '36rem',
      },
      borderRadius: {
        '4xl': '2rem',
      },
      boxShadow: {
        'custom': '0 4px 6px -1px rgba(0, 0, 0, 0.1)',
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}

export default config
```

---

## 🎨 globals.css

Arquivo de estilos globais.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

/* === BASE CUSTOMIZATIONS === */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
  }
  
  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
  }
  
  * {
    @apply border-border;
  }
  
  body {
    @apply bg-background text-foreground;
  }
  
  h1, h2, h3, h4, h5, h6 {
    @apply font-bold;
  }
}

/* === COMPONENTS === */
@layer components {
  .btn {
    @apply px-4 py-2 rounded-lg font-medium transition-colors;
  }
  
  .btn-primary {
    @apply bg-primary-600 text-white hover:bg-primary-700;
  }
  
  .btn-secondary {
    @apply bg-secondary-600 text-white hover:bg-secondary-700;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md p-6;
  }
  
  .input {
    @apply w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary-500 focus:border-transparent;
  }
}

/* === UTILITIES === */
@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
  
  .container-custom {
    @apply max-w-7xl mx-auto px-4 sm:px-6 lg:px-8;
  }
}
```

---

## 📐 Quando Usar Cada Abordagem

### globals.css (@layer components)

Use para componentes reutilizáveis que você criará classes para:

```css
@layer components {
  .btn-primary {
    @apply bg-blue-600 text-white px-4 py-2 rounded;
  }
}
```

```tsx
<button className="btn-primary">Clique</button>
```

**Quando usar:**
- ✅ Componentes muito usados (botões, cards, inputs)
- ✅ Combinações complexas de utilities
- ✅ Estilos base  (h1, h2, body)

### Inline com Utilities

Use utilities diretamente no JSX:

```tsx
<button className="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">
  Clique
</button>
```

**Quando usar:**
- ✅ Componentes únicos ou específicos
- ✅ Layouts e grids
- ✅ Espaçamento e alinhamento
- ✅ Maioria dos casos!

### tailwind.config.ts (extend)

Use para valores customizados do sistema de design:

```typescript
theme: {
  extend: {
    colors: {
      brand: '#FF6B6B'
    }
  }
}
```

```tsx
<div className="bg-brand text-white">
  Cor da marca
</div>
```

**Quando usar:**
- ✅ Cores do sistema de design
- ✅ Fontes customizadas
- ✅ Breakpoints específicos
- ✅ Spacing/border radius customizados

---

## 🎯 Boas Práticas

### ✅ DO

```tsx
// Utility-first approach
<div className="flex items-center justify-between p-4 bg-white rounded-lg shadow">
  <h2 className="text-xl font-bold">Título</h2>
  <button className="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700">
    Ação
  </button>
</div>

// Componente reutilizável
function Button({ children, variant = 'primary' }: ButtonProps) {
  const variants = {
    primary: 'bg-blue-600 hover:bg-blue-700 text-white',
    secondary: 'bg-gray-200 hover:bg-gray-300 text-gray-900'
  }
  
  return (
    <button className={`px-4 py-2 rounded ${variants[variant]}`}>
      {children}
    </button>
  )
}
```

### ❌ DON'T

```tsx
// ❌ Evitar CSS inline direto
<div style={{ padding: '16px', backgroundColor: 'white' }}>
  Conteúdo
</div>

// ❌ Evitar criar classes CSS para tudo
// styles.module.css
.myButton {
  padding: 1rem;
  background: blue;
  color: white;
}

// ❌ Evitar duplicar estilos
<button className="px-4 py-2 bg-blue-600 text-white rounded">Ação 1</button>
<button className="px-4 py-2 bg-blue-600 text-white rounded">Ação 2</button>
// Melhor: criar componente Button reutilizável
```

---

## 🚀 Plugins Úteis

```bash
npm install -D @tailwindcss/forms
npm install -D @tailwindcss/typography
npm install -D @tailwindcss/aspect-ratio
npm install -D tailwind-scrollbar
```

```typescript
// tailwind.config.ts
plugins: [
  require('@tailwindcss/forms'),        // Estilos para forms
  require('@tailwindcss/typography'),   // Prose classes
  require('@tailwindcss/aspect-ratio'), // Aspect ratio utilities
  require('tailwind-scrollbar'),        // Scrollbar customizado
]
```

---

## ✅ Checklist

- [ ] Tailwind instalado e configurado
- [ ] `content` paths corretos no config
- [ ] Cores customizadas definidas
- [ ] globals.css importado no layout
- [ ] Utility-first approach sendo seguido

---

**Módulo concluído!** → [Próximo Módulo: Arquitetura BFF](../02-arquitetura-bff/README.md)
