---
name: sf-checkpoint
description: Persiste el estado de la sesión SpecFounder tras cada respuesta. Úsala SIEMPRE antes de formular la siguiente pregunta, para que la sesión sobreviva a caídas. Actualiza .specfounder/session.md de forma INCREMENTAL y los drafts.
---

# sf-checkpoint — Guardar el avance de la entrevista

Ejecuta el **protocolo de checkpoint** definido en `../../persistence/STATE-SCHEMA.md`. Invócala tras CADA respuesta del usuario y ANTES de formular la siguiente pregunta.

> **Rendimiento (ver ../../RENDIMIENTO.md):** el checkpoint es la operación más repetida de toda la sesión (~1 por turno × decenas de turnos). Hazlo **incremental** —editar solo lo que cambió, no regenerar el archivo entero— y mantén `session.md` **magro** (es un cursor, no un transcript). Ahí está el mayor ahorro de tokens.

## Pasos

1. **Actualiza los drafts** (siempre incremental: edita la parte afectada, no reescribas el archivo entero):
   - `.specfounder/SPEC.draft.md` (sección correspondiente).
   - `.specfounder/CONTEXT.draft.md` (si apareció un término nuevo).
   - `.specfounder/adr/NNNN-*.md` (si se confirmó una decisión que cumple los 3 criterios).
2. **Actualiza `.specfounder/session.md` de forma INCREMENTAL** (edita estos puntos, no regeneres todo el archivo):
   - **Frontmatter:** `updated_at`, el estado de la sección actual, `current_section`, `current_question_id`, y `glossary_terms`/`adr_count` solo si cambiaron.
   - **Siguiente acción:** reemplaza ese bloque por la pregunta EXACTA que toca (es el campo crítico para el resume).
   - **Log de decisiones:** *append* de **una sola línea** por la decisión de este turno (`S2.Q3 ✅ …`). No reescribas las líneas previas.
   - **Ramas abiertas:** marca las cerradas y añade las nuevas; no reordenes el resto.
3. **Mantén el ledger magro:** el Log de decisiones es un resumen, no una transcripción. Si supera ~12 entradas, condensa o conserva solo las **últimas ~12 + todas las decisiones irreversibles (ADR)**; el detalle completo ya vive en los drafts.
4. **Verifica la regla de oro:** el `session.md` debe responder por sí solo "si todo se cae ahora, ¿qué pregunta toca al volver?". Si no puede, corrige antes de seguir.

## Resultado
Un `session.md` actualizado al mínimo necesario y un draft al día. Solo después de esto se formula la siguiente pregunta.

> **Excepción — sin acceso a archivos (chat puro):** si no puedes editar archivos, reimprime el ledger íntegro como bloque de texto tras cada turno (no hay edición incremental posible en ese modo) y pide al usuario que lo guarde.
