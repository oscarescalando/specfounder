# Adaptador de metodología — OpenSpec

> **Para:** equipos que usan OpenSpec (flujo basado en propuestas/cambios sobre un directorio `openspec/`).
> ⚠️ **Verifica contra tu versión instalada:** los nombres exactos de carpetas y archivos pueden variar entre versiones de OpenSpec. Este mapeo refleja el modelo conceptual estándar (capabilities estables en `specs/`, trabajo propuesto en `changes/`). Ajusta si tu CLI genera otra estructura.

---

## Modelo mental de OpenSpec

OpenSpec separa **lo que el sistema YA es** (capacidades estables, fuente de verdad) de **lo que se PROPONE cambiar** (cambios con su propio ciclo propuesta → aplicar → archivar). SpecFounder produce la fundación: el contexto del proyecto, las capacidades base y, si el proyecto es nuevo, el primer cambio.

## Mapeo del spec neutral → artefactos

| Sección del spec neutral | Destino en `openspec/` |
|---|---|
| Sección 1 (Visión) + `CONTEXT.draft.md` | `openspec/project.md` — propósito, contexto y glosario del proyecto |
| Sección 2 (Usuarios) | `openspec/project.md` (roles) y como actores en las capabilities |
| Sección 3 (Funcionalidades) + Sección 4 (Flujos) | `openspec/specs/<capability>/spec.md` — una capability por módulo, con sus requisitos/escenarios |
| Sección 5 (Arquitectura) + `adr/` | `openspec/project.md` (stack/convenciones) + decisiones de los ADR como restricciones |
| Sección 6 (No Funcionales) | restricciones transversales en `openspec/project.md` y en cada capability afectada |

## Estructura resultante (orientativa)

```
proyecto/
└── openspec/
    ├── project.md                 ← contexto + glosario + stack + NFR
    ├── specs/
    │   └── <capability>/
    │       └── spec.md            ← requisitos y escenarios por módulo
    └── changes/                    ← (proyecto nuevo) primer cambio propuesto
        └── <slug-del-cambio>/
            ├── proposal.md
            ├── tasks.md
            └── specs/             ← deltas sobre specs/
```

## Reglas de emisión

- **El glosario manda.** Copia los términos de CONTEXT a `project.md` y úsalos como vocabulario único; nunca uses sinónimos de la lista _Evitar_.
- **Una capability por módulo de la Sección 3.** Los flujos de la Sección 4 se vuelven escenarios dentro de su capability.
- **Proyecto nuevo:** además de `specs/`, genera un primer `changes/<slug>/proposal.md` que describa el alcance inicial, para que el equipo arranque con el flujo de cambios de OpenSpec.
- **Proyecto existente:** solo precarga `specs/` con lo confirmado por el Explorador; deja `changes/` vacío.

## Handoff

```
### Handoff — SpecFounder v2 → OpenSpec

openspec/project.md es el contexto base; openspec/specs/ son las capacidades actuales.
El glosario de project.md define los únicos términos válidos del dominio.
Al proponer un cambio (openspec/changes/), respeta SPEC, CONTEXT y los ADR.
Si una propuesta contradice una capability o un término canónico, señálalo antes de aplicar.
```
