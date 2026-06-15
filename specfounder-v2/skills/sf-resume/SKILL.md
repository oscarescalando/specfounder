---
name: sf-resume
description: Recupera una sesión SpecFounder interrumpida. Úsala al activar el agente para detectar .specfounder/session.md, mostrar un resumen de lo realizado y retomar exactamente donde quedó, sin repetir preguntas.
---

# sf-resume — Retomar una sesión interrumpida

Ejecuta el **protocolo de resume** de `../../persistence/STATE-SCHEMA.md`.

## Pasos

1. **Detecta** si existe `.specfounder/session.md` en la raíz del proyecto.
   - **No existe** → no hay nada que retomar: arranca sesión nueva (fase de selección del coordinator).
   - **Existe** → continúa.
2. **Carga** `session.md`, `SPEC.draft.md`, `CONTEXT.draft.md` y `adr/`.
3. **Muestra el resumen de retomada** (sin re-preguntar nada):

```
🔄 Sesión recuperada — "<project_name>" (<methodology> · <project_mode>)

Progreso:
  ✅ / ⏳ / ⬜ por cada sección (s1..s6)
Glosario: <glossary_terms> términos · ADRs: <adr_count>

Última decisión: <último item del Log de decisiones>
Ramas abiertas: <lista o "ninguna">

▶️ Retomo aquí: <Siguiente acción>
   ¿Continuamos?
```

4. **Al confirmar**, formula exactamente la pregunta de **Siguiente acción**. No repitas ninguna pregunta ya presente en el Log de decisiones.

## Nota para entornos sin acceso a archivos
Si la sesión corrió en modo "chat puro" (sin escritura en disco), el usuario debió guardar el bloque de ledger. Pídele que lo pegue y reconstruye el estado a partir de él.
