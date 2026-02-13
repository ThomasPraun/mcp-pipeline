# mcp-pipeline

Skill orquestador para [Claude Code](https://docs.anthropic.com/en/docs/claude-code) que guía paso a paso la creación de MCP servers de alta calidad para cualquier API o servicio.

## Qué hace

Coordina 15+ skills a través de 7 fases — desde la investigación del API hasta la publicación del package — siguiendo el patrón "coordinate, never execute": el pipeline dirige, los skills especializados ejecutan.

```
Phase 1: Discovery       → Investigar API, mapear tools, elegir lenguaje
Phase 2: Architecture    → Diseñar schemas, estructura, plan de implementación
Phase 3: Scaffolding     → Inicializar proyecto, dependencias, server base
Phase 4: Implementation  → TDD por tool, code review, verificación
Phase 5: Testing         → MCP Inspector, transport, auth
Phase 6: Evaluation      → 10 QA pairs + harness automático (accuracy ≥ 70%)
Phase 7: Distribution    → README, packaging, publicación
```

## Instalación

```bash
npx skills add ThomasPraun/mcp-pipeline@mcp-pipeline -g -y
```

## Uso

En Claude Code:

```
> mcp pipeline
> build mcp
> create mcp server
> mcp status
> next phase
```

## Características

- **Soporte TypeScript y Python** — selección automática basada en el SDK del API target y el stack del usuario
- **Orquesta `mcp-builder`** — inyecta las guías de MCP correctas en cada fase
- **State management** — tracking de progreso con `.claude/mcp-project/status.json`
- **Dashboard visual** — progreso por fase con porcentaje de completitud
- **Agent Teams** — 4 ventanas de paralelismo para acelerar el desarrollo
- **Navegación no-lineal** — skip ahead, go back, resume desde skill parcial
- **Auto-install** — detecta skills faltantes y ofrece instalación

## Dependencias

Requiere el skill [`mcp-builder`](https://www.skills.ai/s/mcp-builder) instalado. El pipeline verifica automáticamente y ofrece instalar skills faltantes.

## Estructura

```
mcp-pipeline/
├── SKILL.md                              # Orquestador principal
└── references/
    ├── phase-1-discovery.md              # API research + language decision
    ├── phase-2-architecture.md           # Tool schemas + project structure
    ├── phase-3-scaffolding.md            # Project setup + dependencies
    ├── phase-4-implementation.md         # TDD + code review
    ├── phase-5-testing.md                # MCP Inspector + transport
    ├── phase-6-evaluation.md             # QA pairs + evaluation harness
    ├── phase-7-distribution.md           # README + packaging
    ├── install-commands.md               # Skill install commands
    ├── skill-injection-protocol.md       # Subagent delegation protocol
    └── language-decision-matrix.md       # TypeScript vs Python decision tree
```

## Licencia

MIT
