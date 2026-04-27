---
name: Portfolio
description: Dashboard interativo dos projetos e arquitetura do ecossistema Pizani
---

# Portfolio Dashboard

> Dados dos repositórios GitHub + análise local dos projetos em `/apps/`
> Atualizado automaticamente via GitHub Actions CI

## Indicadores Globais

| Indicador | Valor |
|-----------|-------|
| Repositórios GitHub (não-forks) | 3 |
| Projetos locais (`/apps/`) | 9 |
| Total tecnologias | 20+ |
| Serviços rodando | 9 |

## Tech Stack Consolidada

<!-- skillicons -->

## Arquitetura do Ecossistema

<!-- architecture -->

## Repositórios GitHub

<!-- github-repos -->

## Projetos Locais (`/apps/`)

<!-- local-projects -->

## Roadmap de Desenvolvimento

<!-- kanban -->

## Health dos Serviços

<!-- health -->

## Como Funciona

Este dashboard é gerado automaticamente a partir de:
- **GitHub API** → dados dos repos não-forks via `curl` + `jq`
- **Análise local** → `/apps/` varrido por `cloc` + parse de README
- **Mermaid diagrams** → architecture + treemap + kanban
- **skillicons.dev** → badges de tecnologia

### Atualizar dados

```bash
# 1. Buscar dados GitHub
curl -s "https://api.github.com/users/pizanao/repos?per_page=100&type=owner" \
  -H "Accept: application/vnd.github.v3+json" \
  | jq '[.[] | select(.fork == false)]'

# 2. Contar linhas por projeto (requer cloc)
find /apps -maxdepth 2 -name "README.md" -execdirname {} \; \
  | xargs -I{} cloc --quiet --json {} 2>/dev/null

# 3. Verificar serviços rodando
for port in 5100 8000 5106 8006 5173 8002 3000 11434; do
  nc -z localhost $port 2>/dev/null && echo "🟢 :$port" || echo "🔴 :$port"
done
```

### Workflow GitHub Actions

```yaml
# .github/workflows/portfolio.yml
name: Update Portfolio Dashboard
on:
  schedule:
    - cron: '0 */6 * * *'  # a cada 6 horas
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Fetch GitHub data
        run: |
          curl -s "https://api.github.com/users/pizanao/repos?per_page=100&type=owner" \
            -H "Accept: application/vnd.github.v3+json" \
            | jq '[.[] | select(.fork == false)]' > .github/repos.json
      - name: Update PORTFOLIO.md
        run: python scripts/generate_portfolio_md.py
      - uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: "chore: auto-update PORTFOLIO.md"
          file_pattern: "PORTFOLIO.md"
```

---

*Gerado automaticamente · não editar manualmente — mudanças serão sobrescritas*
