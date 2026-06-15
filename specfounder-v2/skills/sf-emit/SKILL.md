---
name: sf-emit
description: Emite los artefactos finales del spec en la metodología elegida (OpenSpec, GitHub Spec-Kit o SDD genérico) y genera el handoff. Úsala al cierre, cuando las 6 secciones estén completas y sin ramas abiertas.
---

# sf-emit — Compilar el spec a la metodología destino

Implementa el sub-agente Adaptador (`../../agents/emitter.md`) aplicando el mapeo de `../../methodologies/`.

## Precondiciones (no emitir si fallan)
- Las 6 secciones en estado `completa` (salvo modo glosario-urgente).
- Sin ramas abiertas ni contradicciones sin resolver en `session.md`.

Si algo falta, devuelve el control e indica qué resolver.

## Pasos
1. Lee `methodology` de `.specfounder/session.md`.
2. Carga el mapeo de `../../methodologies/<methodology>.md`:
   - `generic-sdd` → `SPEC.md` + `CONTEXT.md` + `docs/adr/`.
   - `openspec` → `openspec/project.md` + `openspec/specs/` + `openspec/changes/`.
   - `github-spec-kit` → `.specify/memory/constitution.md` + `specs/NNN-<feature>/spec.md`.
3. Genera los archivos destino en la estructura real del proyecto (fuera de `.specfounder/`).
4. Respeta los términos canónicos de CONTEXT; nunca introduzcas sinónimos de _Evitar_.
5. Produce el bloque de handoff de la metodología.
6. Actualiza `session.md` → `phase: emitido` y registra las rutas emitidas en "Notas de retomada" (vía sf-checkpoint).

## Verificación
Confirma los nombres de archivo/comandos contra la versión instalada de la herramienta destino (los adaptadores lo advierten).
