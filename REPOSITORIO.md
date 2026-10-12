# Resumo do repositório

**Repositório no GitHub:** `teste0102/claude-economizar-token` (antes `teste0102/code-review-graph`)
**Projeto:** `code-review-graph` v2.3.9 · Python ≥ 3.10 · licença MIT
**Branch da sessão:** `claude/lucid-cray-emd6yq`

## O que é

Grafo de conhecimento local para revisão de código. Ele analisa o repositório com Tree-sitter (mais alguns parsers alternativos), grava nós e arestas em SQLite e se atualiza de forma incremental a partir das mudanças do Git. Serve contexto compacto a ferramentas de IA via MCP e CLI, para que o assistente leia só os arquivos que a mudança toca. Isso economiza tokens.

Plataformas suportadas: Claude Code, Codex, Cursor, Windsurf, Zed, Continue, OpenCode, Antigravity, Gemini CLI, Qwen Code, Kiro, Qoder, GitHub Copilot, GitHub Copilot CLI, Hermes Agent e CodeBuddy Code.

## Instalação rápida

```bash
pip install code-review-graph        # ou: pipx install code-review-graph
code-review-graph build              # build completo do grafo
code-review-graph update             # atualização incremental
code-review-graph status             # estatísticas
code-review-graph detect-changes     # análise de mudanças com pontuação de risco
code-review-graph serve              # servidor MCP (stdio; --http para localhost)
```

## Estrutura

| Caminho | Conteúdo |
| --- | --- |
| `code_review_graph/` | Pacote principal. Contém o servidor MCP (`main.py`, 30 tools e 5 prompts), a CLI (`cli.py`), o parser (`parser.py`), o armazenamento SQLite (`graph.py`) e as atualizações incrementais (`incremental.py`). Também tem fluxos, comunidades, busca FTS5/vetorial, exportações e o instalador por plataforma (`skills.py`). |
| `code_review_graph/tools/` | Implementação das tools MCP por domínio (build, query, review, context etc.). |
| `code-review-graph-vscode/` | Extensão VS Code em TypeScript. Lê `.code-review-graph/graph.db` diretamente. |
| `tests/` | Mais de 100 módulos pytest e fixtures por linguagem. |
| `docs/` | Documentação: uso, comandos, FAQ, GitHub Action, roadmap, linguagens customizadas. |
| `hooks/`, `skills/`, `scripts/`, `evaluate/`, `diagrams/` | Hooks, skills, scripts de apoio, benchmarks e diagramas. |
| `action.yml` | GitHub Action. |
| `CLAUDE.md`, `AGENTS.md`, `GEMINI.md` | Instruções para agentes de IA. |

O banco de dados fica em `.code-review-graph/graph.db` (SQLite, modo WAL). A versão atual do schema é 13.

## Desenvolvimento

```bash
uv run pytest tests/ --tb=short -q
uv run ruff check code_review_graph/
uv run mypy code_review_graph/ --ignore-missing-imports --no-strict-optional
```

Convenções: linha de 100 colunas (ruff), SQL sempre parametrizado, sem `eval`/`exec`/`pickle`, sem `shell=True`, e nomes de nós passam por `_sanitize_name()`.

## Fluxo de branches

`PR de feature` → `staging` (padrão) → `testing` → `main` → tag → PyPI.
Todo PR deve ser aberto contra `staging`. A promoção de `staging` para `testing` é automática e diária. A promoção para `main` é sempre manual.

## CI

Lint (ruff), type-check (mypy), segurança (bandit), sincronia do schema com a extensão VS Code, testes em Python 3.10–3.13 com cobertura mínima de 65%, e um subconjunto da suíte no Windows.
