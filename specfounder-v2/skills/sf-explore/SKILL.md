---
name: sf-explore
description: Explora el código de un proyecto existente antes de entrevistar, para no preguntar lo que el código ya responde. Úsala en modo proyecto-existente o re-spec-parcial, antes de iniciar la entrevista.
---

# sf-explore — Reconocimiento de proyecto existente

Implementa el protocolo del sub-agente Explorador (`../../agents/explorer.md`). Es de **solo lectura** sobre el código del proyecto.

## Pasos
1. Anuncia: "Voy a explorar lo que ya existe para no preguntarte algo que el código ya responde."
2. Revisa en orden de prioridad:
   1. Documentación (README, CONTEXT.md, docs/, ADRs, wiki).
   2. Configuración (manifiestos de dependencias, .env.example, Dockerfile, infra).
   3. Dominio (modelos/entidades, migraciones/esquema, enums).
   4. Comportamiento (rutas/endpoints, controladores, jobs, eventos, cron).
   5. Frontend (pantallas principales, navegación, roles en UI).
   6. Tests (revelan flujos críticos y errores esperados).
3. Precarga `SPEC.draft.md` por sección, marcando confianza:
   - `[confirmado-por-código]` · `[inferido]` (a confirmar) · `[ausente]` (a preguntar).
4. Pasa candidatos de términos de dominio al glosario (sf-glossary-sync).
5. Reporta: secciones precargadas, qué queda por confirmar, qué queda por preguntar, y contradicciones código↔doc.
6. Haz checkpoint (sf-checkpoint) antes de iniciar la entrevista.

## Salida
Un `SPEC.draft.md` parcialmente lleno y un `session.md` que indica qué preguntas se pueden saltar. La entrevista arranca solo sobre lo `[inferido]` y `[ausente]`.
