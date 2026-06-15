# Adaptador de metodología — GitHub Spec-Kit

> **Para:** equipos que usan GitHub Spec-Kit (la `specify` CLI) y su flujo `/constitution` → `/specify` → `/plan` → `/tasks` → `/implement`.
> ⚠️ **Verifica contra tu versión instalada:** Spec-Kit evoluciona rápido y soporta múltiples agentes (Claude, Copilot, Cursor, Gemini, etc.). Los nombres de carpetas/comandos pueden variar. Este mapeo refleja el modelo estándar; ajústalo a lo que genere tu `specify init`.

---

## Modelo mental de Spec-Kit

Spec-Kit ancla el desarrollo en una **constitution** (principios no negociables del proyecto) y luego, por feature, produce un **`spec.md`** (el qué y el porqué, sin tecnología), del que derivan `plan.md` (el cómo) y `tasks.md` (la ejecución). SpecFounder produce justo las dos primeras piezas: la constitution y el `spec.md` inicial, ricos y desambiguados.

## Mapeo del spec neutral → artefactos

| Sección del spec neutral | Destino en Spec-Kit |
|---|---|
| Sección 6 (No Funcionales) + `adr/` + principios de Sección 5 | `.specify/memory/constitution.md` — principios y restricciones no negociables |
| Sección 1 (Visión) | encabezado de `specs/NNN-<feature>/spec.md` (objetivo y problema) |
| Sección 2 (Usuarios) | actores / user stories del `spec.md` |
| Sección 3 (Funcionalidades) | requisitos funcionales del `spec.md` |
| Sección 4 (Flujos) | acceptance scenarios / criterios de aceptación del `spec.md` |
| `CONTEXT.draft.md` | sección de "Key terms / glossary" del `spec.md` (y/o referenciado desde la constitution) |
| Sección 5 (Arquitectura) | **NO** va al `spec.md` (Spec-Kit mantiene el spec libre de tecnología); va a la constitution como restricción y luego al `/plan` |

> Clave de Spec-Kit: el `spec.md` describe el QUÉ y el PORQUÉ, nunca el stack. Las decisiones técnicas de la Sección 5 alimentan la constitution y el posterior `/plan`, no el `spec.md`.

## Estructura resultante (orientativa)

```
proyecto/
├── .specify/
│   └── memory/
│       └── constitution.md       ← principios + NFR + restricciones (de Secciones 5-6 + ADR)
└── specs/
    └── 001-<feature>/
        └── spec.md               ← Visión + Usuarios + Funcionalidades + Flujos + glosario
```

## Reglas de emisión

- **Mantén `spec.md` libre de tecnología.** Si una funcionalidad arrastra una decisión de stack, esa decisión va a la constitution, no al spec.
- **Marca lo no resuelto.** Spec-Kit usa marcadores tipo `[NEEDS CLARIFICATION]`; pero como SpecFounder no avanza con ramas abiertas, idealmente el `spec.md` sale sin ellos. Si quedara algo (modo glosario-urgente), márcalo explícitamente.
- **Glosario como anclaje.** Inserta los términos de CONTEXT en el `spec.md` para que `/plan` y `/tasks` hereden el vocabulario canónico.
- **Listo para `/plan`.** El objetivo es que el usuario pueda correr `/plan` inmediatamente sobre el `spec.md` emitido.

## Handoff

```
### Handoff — SpecFounder v2 → GitHub Spec-Kit

.specify/memory/constitution.md fija los principios y restricciones no negociables.
specs/NNN-<feature>/spec.md es el QUÉ y el PORQUÉ (sin tecnología), con el glosario canónico.
Siguiente paso sugerido: ejecutar /plan sobre el spec.md, luego /tasks.
Los términos del glosario son los únicos válidos. Si /plan o /tasks contradicen la constitution o el spec, detente y señálalo.
```
