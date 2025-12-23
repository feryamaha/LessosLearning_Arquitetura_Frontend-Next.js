# Módulo 07: DevSecOps

## 📘 Pipeline de 3 Camadas

### 1. Auditoria Local (Pre-commit)
Scripts que rodam antes de cada commit:
- Verificação de dependências
- Build sem erros
- Scan de vulnerabilidades (OSV Scanner)

### 2. CI/CD (GitHub Actions)
Pipeline automatizado:
- Testes automatizados
- Build de produção
- Análise de segurança
- Deploy automático (Vercel)

### 3. Review Manual
- Code review de PRs
- Análise de arquitetura
- Pixel perfect UI

## 📚 Conteúdo

1. **Auditoria Local** - Scripts pre-commit
2. **CI/CD Workflow** - GitHub Actions
3. **PR Review** - Guidelines e checklist
4. **Pixel Perfect** - Visual testing

---

## 💡 Script Pre-commit

```javascript
// scripts/precommit.js
const { execSync } = require('child_process')

// 1. Build
console.log('🔨 Building...')
exec Sync('npm run build')

// 2. Audit
console.log('🔍 Auditing dependencies...')
execSync('npm audit')

// 3. CVE Scan
console.log('🛡️ Scanning for vulnerabilities...')
execSync('osv-scanner scan --lockfile package-lock.json')

console.log('✅ Pre-commit checks passed!')
```

⏱️ **Tempo:** 4-5 horas
