# RENDIMIENTO — Estrategia de eficiencia de tokens de SpecFounder v2

> Cómo hacer SpecFounder v2 más rápido y barato **sin comprometer el resultado** (igualdad semántica, grill-me, memoria persistente). Léelo antes de tocar `core/`, `agents/`, `skills/sf-checkpoint` o `integrations/`.

---

## 1. Dónde se van realmente los tokens

El tamaño de los prompts **no** es el cuello de botella: el monolito (~2.600 tokens) y el coordinador (~2.700) se envían una vez y, con *prompt caching*, son casi gratis al repetirse.

El costo real nace de la naturaleza **grill-me: una pregunta por turno × muchos turnos**. Una entrevista de software completa ≈ 40 turnos. Por cada turno se paga:

| Fuente | Por qué pesa | Prioridad |
|---|---|---|
| **Checkpoint de cada turno** | Reescribir `session.md` íntegro + drafts tras CADA respuesta. El output cuesta ~5× el input → 40 reescrituras completas son el mayor sumidero. | 🔴 Alta |
| **Modelo que procesa los 40 turnos** | Usar un modelo de alto desempeño en tareas mecánicas multiplica el gasto sin mejorar el spec. | 🔴 Alta |
| **Historial conversacional creciente** | En turnos tardíos se reprocesa todo el hilo. | 🟠 Media |
| **Carga de todos los dominios/metodologías** | Solo se usa uno de cada por sesión. | 🟡 Baja-media |
| **Tamaño de los prompts** | Estático y cacheable. | 🟢 Baja |

**Palanca nº1:** poner cada rol en el modelo correcto. **Palanca nº2:** abaratar el checkpoint.

---

## 2. Estrategia de modelos por rol

Principio: **el modelo de alto desempeño (Opus) solo donde se decide la calidad del spec** — entrevista, visión y arquitectura. Todo lo que sea *leer, resumir, rellenar plantillas o guardar estado* va en un modelo barato (Haiku) o de equilibrio (Sonnet).

| Rol / pieza | Modelo | Por qué |
|---|---|---|
| **Entrevistador** (grill-me, recomendaciones, desafiar lenguaje) | `opus` | Corazón del valor: igualdad semántica y calidad de recomendación. |
| **Generador de Visión** (3 alternativas + criterio) | `opus` | Redacción experta + juicio. |
| **Arquitecto/ADR** (trade-offs, "a decidir") | `opus` (o `sonnet`) | Juicio técnico real. |
| **Coordinador** (orquestación/ruteo) | `sonnet` | Decide a quién delegar; no necesita Opus. |
| **Explorador** (leer código/manuscrito) | `sonnet` | Contexto grande, lectura > razonamiento profundo. |
| **Glosarista** (extraer/canonizar términos) | `sonnet` (o `haiku`) | Patrón, poca creatividad. |
| **Adaptador/Emitter** (plantillas, transformación) | `haiku` (o `sonnet`) | Relleno de plantilla casi determinista. |
| **sf-checkpoint** (reescribir `session.md`) | `haiku` | Mecánico: volcar el turno al ledger. |
| **sf-resume** (resumen de retomada) | `haiku` | Leer y resumir. |
| **Comando `/ayuda`** | `haiku` | Q&A de solo lectura sobre `AYUDA.md`. |
| **Menús de selección** (dominio/metodología/modo) | `haiku` | Menús fijos, no razonamiento. |

> Regla mental: **Opus piensa, Sonnet lee/orquesta, Haiku guarda/resume/rellena.**

> ⚠️ **Matiz sobre las skills:** en Claude Code una *skill* (`sf-checkpoint`, `sf-resume`) se ejecuta en el modelo del agente que la invoca; no tiene `model:` propio. Para que corran realmente en `haiku` hay que exponerlas como **sub-agentes dedicados**. Si no, heredan el modelo del coordinador (`sonnet`), que ya es barato — y el grueso del ahorro viene del checkpoint incremental (§3), no del modelo. El `model:` por rol aplica de forma directa a **sub-agentes** y **slash-commands** (como `/ayuda`).

### Cómo se aplica según la herramienta

- **Claude Code (multi-agente):** los sub-agentes y los slash-commands aceptan `model:` en el frontmatter (`opus | sonnet | haiku | inherit`). Cada sub-agente corre en su propia ventana y modelo → la tabla se aplica al 100%. Ver `integrations/claude-code.md`.
- **Monolito (Codex, Cursor, OpenCode, chat puro):** ⚠️ **no se puede cambiar de modelo a mitad de conversación** — es un solo agente. Eliges **un modelo para toda la sesión**: por defecto **Sonnet** (equilibrio); sube a **Opus** solo si el proyecto es complejo y la calidad del razonamiento lo justifica. El tiering fino es un beneficio exclusivo de los harness multi-agente.

---

## 3. Optimizaciones independientes del modelo

Suman sobre el tiering, sin tocar la calidad:

1. **Checkpoint incremental, no reescritura total.** Editar (patch) el frontmatter y *append* de una línea al log, en vez de regenerar `session.md` completo. Es el mayor ahorro de tokens de salida. Implementado en `skills/sf-checkpoint/SKILL.md` y reflejado en `persistence/STATE-SCHEMA.md`.
2. **`session.md` magro.** Es un cursor, no un transcript. Log de **una línea por decisión**, con tope (últimas ~12 + todas las irreversibles). El contenido vive en los drafts, no en el ledger.
3. **Carga perezosa de dominios y metodologías.** Cargar solo el **perfil de dominio activo** (no los 5) y el **adaptador de metodología solo en la emisión** (no los 4). El Explorador solo en modo `existente`; el Generador de Visión solo en modo `nuevo`. *(Recomendación documentada; aplicar en `core/coordinator.md` y `monolith/` cuando se haga la siguiente pasada.)*
4. **Diseñar para prompt caching.** Prompt de sistema **estático**; estado volátil (`session.md`) leído por herramienta cada turno. Así el bloque grande se cachea y la repetición por turno es casi gratis.
5. **No re-imprimir el draft completo al usuario cada turno.** Mostrar solo el delta + la siguiente pregunta. El SPEC/BIBLIA completo, solo en el cierre.

---

## 4. Qué NO sacrificar (no negociable)

La eficiencia jamás puede romper estas reglas inviolables:

- **Una pregunta por turno** con recomendación (grill-me). No se "agrupan" preguntas de descubrimiento para ahorrar turnos.
- **Checkpoint antes de cada pregunta.** Incremental sí; omitirlo no.
- **Igualdad semántica** y glosario puro.
- La **normativa de Visión** (≤ 2 párrafos) y los **3 criterios de ADR**.

Si una optimización choca con cualquiera de estas, gana la regla.
