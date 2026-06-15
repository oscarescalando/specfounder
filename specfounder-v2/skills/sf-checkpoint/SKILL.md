---
name: sf-checkpoint
description: Persiste el estado de la sesión SpecFounder tras cada respuesta. Úsala SIEMPRE antes de formular la siguiente pregunta, para que la sesión sobreviva a caídas. Actualiza .specfounder/session.md y los drafts.
---

# sf-checkpoint — Guardar el avance de la entrevista

Ejecuta el **protocolo de checkpoint** definido en `../../persistence/STATE-SCHEMA.md`. Invócala tras CADA respuesta del usuario y ANTES de formular la siguiente pregunta.

## Pasos

1. **Actualiza los drafts** con lo aprendido en este turno:
   - `.specfounder/SPEC.draft.md` (sección correspondiente).
   - `.specfounder/CONTEXT.draft.md` (si apareció un término nuevo).
   - `.specfounder/adr/NNNN-*.md` (si se confirmó una decisión que cumple los 3 criterios).
2. **Reescribe `.specfounder/session.md`** completo:
   - `updated_at` con la hora actual.
   - Estado de la sección (`pendiente|en_curso|completa`).
   - `current_section`, `current_question_id`.
   - Añade la decisión de este turno al **Log de decisiones**.
   - Actualiza **Ramas abiertas** (marca cerradas, añade nuevas).
   - Reescribe el bloque **Siguiente acción** con la pregunta EXACTA que toca.
   - Actualiza `glossary_terms` y `adr_count` si cambiaron.
3. **Verifica la regla de oro:** el `session.md` debe responder por sí solo "si todo se cae ahora, ¿qué pregunta toca al volver?". Si no puede, corrige antes de seguir.

## Resultado
Un `session.md` íntegro y un draft actualizado. Solo después de esto se formula la siguiente pregunta.
