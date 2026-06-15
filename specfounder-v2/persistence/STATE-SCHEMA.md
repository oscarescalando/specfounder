# STATE-SCHEMA — Memoria persistente de SpecFounder v2

> Este documento define **cómo SpecFounder v2 sobrevive a caídas**. Es el cimiento que referencian el `coordinator`, el `monolith` y las skills `sf-checkpoint` / `sf-resume`. Léelo antes que cualquier otro archivo del sistema.

---

## Por qué existe

En v1 toda la entrevista vivía en el contexto de la conversación. Si el aplicativo se cerraba, la red fallaba o el contexto se truncaba, **se perdía todo el progreso**. v2 persiste el estado en disco tras cada respuesta, de modo que cualquier sesión puede retomarse exactamente donde quedó, sin re-preguntar lo ya respondido.

---

## El directorio `.specfounder/`

Se crea en la **raíz del proyecto objetivo** (no en este repo). Es la única fuente de verdad del progreso de una sesión.

```
.specfounder/
├── session.md            # Ledger de estado (cursor + metadatos). EL archivo crítico.
├── SPEC.draft.md         # Spec vivo (6 secciones), se actualiza en tiempo real
├── CONTEXT.draft.md      # Glosario vivo, se actualiza en tiempo real
└── adr/
    ├── 0001-slug.md      # ADRs borrador (solo si aplican los 3 criterios)
    └── ...
```

**Separación de responsabilidades:** el **contenido** vive en los `*.draft.md` y en `adr/`. El **cursor** (en qué pregunta vamos, qué falta, qué sigue) vive en `session.md`. Así el ledger se mantiene pequeño y barato de reescribir tras cada turno.

> Recomendación: añadir `.specfounder/` a `.gitignore` mientras la sesión está en curso. Los artefactos finales emitidos por el adaptador (ver `methodologies/`) sí se versionan.

---

## Esquema de `session.md`

Markdown con **frontmatter YAML** (parte machine-readable) + **cuerpo estructurado** (parte human-readable). Se eligió markdown sobre JSON por portabilidad entre herramientas, fiabilidad de los LLM al leer/escribir, y porque un humano puede abrirlo y entender el progreso de un vistazo.

```markdown
---
spec_founder_version: "2.0.0"
session_id: "2026-06-14-mi-proyecto"
created_at: "2026-06-14T10:00:00Z"
updated_at: "2026-06-14T10:42:00Z"
domain: "software"             # software | novela | serie-imagenes | guion-video | custom
methodology: "openspec"        # openspec | github-spec-kit | generic-sdd | creative-bible
project_mode: "nuevo"          # nuevo | existente | re-spec-parcial | glosario-urgente
project_name: "Mi Proyecto"
project_root: "/ruta/al/proyecto"
phase: "entrevista"            # seleccion | exploracion | vision | entrevista | cierre | emitido
vision_mode: "generada"        # generada | aportada | "" (vacío si aún no se definió)
current_section: 2             # 1..6  (0 = aún en selección)
current_question_id: "S2.Q3"   # id estable de la pregunta en curso
sections:                       # claves fijas s1..s6 (ranuras universales); su ETIQUETA depende del perfil de dominio
  s1_vision:        "completa"   # pendiente | en_curso | completa
  s2_actores:       "en_curso"   # software: usuarios · novela: personajes · etc.
  s3_elementos:     "pendiente"  # software: funcionalidades · novela: tramas · etc.
  s4_estructura:    "pendiente"  # software: flujos · novela: estructura narrativa · etc.
  s5_forma:         "pendiente"  # software: arquitectura · novela: mundo/escenarios · etc.
  s6_restricciones: "pendiente"  # software: no funcionales · creativo: tono/formato · etc.
glossary_terms: 7              # nº de términos del canon/glosario (detalle en CONTEXT.draft.md)
adr_count: 1
---

# Sesión: Mi Proyecto

## Siguiente acción (lo PRIMERO que se lee al retomar)
> Formular S2.Q3: "¿Existe un usuario anónimo no autenticado con acciones propias?
> Mi recomendación: sí, al menos lectura pública del catálogo."

## Ramas abiertas (deben cerrarse antes de avanzar de sección)
- [ ] S2: confirmar si "Operador" y "Supervisor" son roles distintos o el mismo con permisos.

## Log de decisiones (recomendaciones aceptadas / rechazadas)
- S1.Q1 ✅ Producto definido en una oración (ver SPEC.draft.md §1).
- S1.Q2 ✅ Usuario principal: empresa (B2B). Recomendación aceptada.
- S2.Q1 ✅ 3 tipos de usuario: Administrador, Operador, Cliente.
- S2.Q2 ⏳ En curso.

## Contradicciones resueltas
- "cuenta" se usó para Usuario y para Organización → canonizado como **Organización** (ver CONTEXT.draft.md).

## Notas de retomada
- (libre) cualquier contexto que el agente quiera dejarse a sí mismo para no perder hilo.
```

### Campos obligatorios del frontmatter
`domain`, `methodology` (si el dominio es de código), `project_mode`, `phase`, `current_section`, `current_question_id`, `sections.*`, `updated_at`. Sin estos, el resume no es fiable.

### IDs de pregunta estables
Formato `S{sección}.Q{n}` (p. ej. `S4.Q2`). Cada sub-agente Entrevistador usa estos IDs para que el cursor sea inequívoco. Las preguntas adaptativas (no del guion base) se numeran `S{sección}.Qa{n}` (`a` = ad-hoc).

---

## Protocolo de CHECKPOINT (regla inviolable del núcleo)

Tras **cada** respuesta del usuario, y **antes** de formular la siguiente pregunta, en este orden:

1. **Actualizar drafts** (incremental): editar la parte afectada de `SPEC.draft.md` y/o `CONTEXT.draft.md` y/o `adr/`.
2. **Actualizar `session.md`** (incremental — ver más abajo): `updated_at`, el estado de la sección, *append* de una línea al log de decisiones, las ramas abiertas y —lo más importante— el bloque **"Siguiente acción"** con la pregunta exacta que toca.
3. **Recién entonces** formular la siguiente pregunta al usuario.

> Si el agente formula una pregunta sin haber persistido el turno anterior, está violando el protocolo. El checkpoint es atómico respecto a la pregunta: primero se persiste, luego se pregunta.

### Escritura eficiente (rendimiento)
El checkpoint se ejecuta ~1 vez por turno durante decenas de turnos, así que es el mayor sumidero de tokens de salida del sistema (ver `../RENDIMIENTO.md`). Reglas:
- **Edición incremental, no reescritura total.** Modifica solo los campos del frontmatter que cambiaron, *append* de **una línea** al log de decisiones, y reemplaza el bloque "Siguiente acción". No regeneres el archivo entero.
- **Ledger magro.** El log es un resumen (una línea por decisión), no una transcripción. Tope sugerido: **últimas ~12 entradas + todas las decisiones irreversibles (ADR)**; el detalle completo vive en los drafts.
- **Excepción chat puro:** sin acceso a archivos no hay edición incremental — reimprime el ledger íntegro tras cada turno y pide guardarlo.

---

## Protocolo de RESUME (al activarse el agente)

1. **Detectar**: ¿existe `.specfounder/session.md` en la raíz del proyecto?
   - **No existe** → sesión nueva. Ir a la fase de selección del `coordinator`.
   - **Sí existe** → continuar abajo.
2. **Cargar estado**: leer `session.md`, `SPEC.draft.md`, `CONTEXT.draft.md`, `adr/`.
3. **Mostrar resumen de retomada** al usuario (sin re-preguntar nada):
   - Metodología y modo de la sesión.
   - Secciones completas vs pendientes.
   - Nº de términos del glosario y ADRs.
   - La última decisión registrada.
   - Las ramas abiertas que faltan cerrar.
4. **Confirmar y retomar**: "Retomo en **{Siguiente acción}**. ¿Continuamos?" — y al confirmar, formular esa pregunta exacta. No se repite ninguna pregunta ya marcada en el log de decisiones.

### Resumen de retomada — formato visible al usuario
```
🔄 Sesión recuperada — "Mi Proyecto" (OpenSpec · proyecto nuevo)

Progreso:
  ✅ Sección 1 · Visión
  ⏳ Sección 2 · Usuarios (en curso)
  ⬜ Secciones 3–6 pendientes
Glosario: 7 términos · ADRs: 1

Última decisión: 3 tipos de usuario (Administrador, Operador, Cliente).
Rama abierta: confirmar si "Operador" y "Supervisor" son el mismo rol.

▶️ Retomo aquí: S2.Q3 — ¿Existe un usuario anónimo con acciones propias?
   ¿Continuamos?
```

---

## Estados de fase (`phase`)

| Fase | Significado |
|------|-------------|
| `seleccion` | Eligiendo metodología y modo de proyecto (aún no empieza la entrevista). |
| `exploracion` | (Solo proyecto existente) el Explorador está leyendo código. |
| `vision` | (Solo proyecto nuevo, o re-spec con Visión rota) el Generador de Visión construye o valida la Visión (Sección 1) antes de la entrevista. `vision_mode` registra si fue `generada` o `aportada`. |
| `entrevista` | Recorriendo las 6 secciones con grill-me. |
| `cierre` | Todas las secciones completas; mostrando SPEC/CONTEXT/ADRs para revisión. |
| `emitido` | El adaptador de metodología ya generó los artefactos finales y el handoff. |

---

## Regla de oro

> **El `session.md` siempre debe poder responder, por sí solo, la pregunta: "si todo se cae ahora mismo, ¿qué pregunta exacta toca hacer al volver?"** Si no puede, el checkpoint está incompleto.
