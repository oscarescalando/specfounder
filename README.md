# Sistema de Prompts — SpecFounder

<!-- VERSION:START -->
**Versión actual:** `v2.0.0` (2026-06-15) — ver [CHANGELOG.md](CHANGELOG.md) · [RELEASE-NOTES.md](RELEASE-NOTES.md)
<!-- VERSION:END -->

Repositorio de **especificaciones de agentes** para Spec-Driven Development (SDD). Su objetivo es construir la **capa de fundación del spec** de cualquier proyecto —de **código** (cualquier tecnología) o **creativo** (novela, serie de imágenes, guion…)— con **igualdad semántica**: que los términos del usuario sean exactamente los que la IA asimila como propios.

> ### 🚀 ¿Cómo empiezo? Dos formas
> - **Automatizada — [INICIAR.md](INICIAR.md):** instalas los comandos una vez y luego escribes `/iniciar` (empezar/retomar), `/retomar` (continuar) o `/ayuda` (guía para usuarios) en tu herramienta (Claude Code, Cursor, Codex, OpenCode, VSCode). Sin pegar nada.
> - **Manual — [EMPEZAR-AQUI.md](EMPEZAR-AQUI.md):** copiar y pegar un prompt. Sirve en cualquier chat, incluso sin acceso a archivos (Claude.ai, ChatGPT web).
>
> ¿Dudas o pocos conocimientos? Escribe **`/ayuda`** o abre **[AYUDA.md](AYUDA.md)**: la guía paso a paso para usuarios.
>
> El resto de este README es la documentación completa.

---

## Obtener el proyecto

Clona el repositorio y entra en la carpeta:

```bash
git clone https://github.com/oscarescalando/specfounder.git
cd specfounder
```

> ¿Prefieres no usar Git? Descarga el proyecto como ZIP desde GitHub y descomprímelo. El sistema funciona igual: lo que importa es tener los archivos de `specfounder-v2/` y los comandos accesibles desde tu herramienta.

Después, instala los comandos (ver [INICIAR.md](INICIAR.md)) o usa el arranque manual ([EMPEZAR-AQUI.md](EMPEZAR-AQUI.md)).

Contiene dos generaciones del agente:

| | Qué es | Estado |
|---|---|---|
| **`SPEC-FOUNDER-AGENT.md`** | SpecFounder **v1.0** — prompt monolítico original. Atado a OpenSpec y a Claude Code. | Legacy / referencia |
| **`specfounder-v2/`** | SpecFounder **v2.0** — sistema multi-agente, multi-metodología, multi-herramienta, con **memoria persistente**. | Recomendado |

> Todo el contenido está en **español** por consistencia del repo.

---

## Qué resuelve la v2 frente a la v1

1. **Memoria persistente.** La sesión guarda su estado en `.specfounder/` tras cada respuesta. Si el aplicativo se cierra o la comunicación falla, se retoma exactamente donde quedó, con un resumen de lo realizado y **sin repetir preguntas**.
2. **Agnóstico de dominio.** No solo genera código. Eliges el propósito del spec: **software** (sistema, app, API, web) o **creativo** (novela/libro, serie de imágenes, guion de video, o cualquier otra estructura). Las 6 secciones se adaptan al dominio; el método SDD es el mismo.
3. **Agnóstico de tecnología.** En el lado código no está casado con ningún stack (no es "solo para Laravel"): sirve para cualquier lenguaje/framework.
4. **Multi-metodología.** En dominios de código eliges el destino: **OpenSpec**, **GitHub Spec-Kit** o **SDD genérico**. En dominios creativos la salida es una **Biblia** en Markdown. La entrevista es la misma; solo cambia cómo se emite el resultado.
5. **Nuevo vs existente.** Se declara explícitamente el modo; en proyectos existentes el agente **explora el material (código o manuscrito/biblia) antes de preguntar**.
6. **Multi-herramienta.** No está casado con Claude Code: hay guías para Codex, OpenCode, Cursor y Antigravity, además de un **monolito portable** que funciona en cualquier chat/CLI.
7. **Sistema de prompts coordinado.** Un orquestador delega en sub-agentes especializados que operan de forma simultánea (con un monolito de respaldo para herramientas sin sub-agentes).

---

## Arquitectura

```
Coordinator (orquestador, dueño del estado y del checkpoint)
│
├─ Generador de Visión → en proyectos nuevos, construye o valida la Visión (Sección 1)
├─ Entrevistador       → preguntas grill-me, una por turno, con recomendación
├─ Glosarista          → CONTEXT.md canónico, detecta contradicciones de términos
├─ Explorador          → lee el código en proyectos existentes (solo lectura)
├─ Arquitecto/ADR      → detecta ADRs (3 criterios) y propone arquitectura
└─ Adaptador           → emite los artefactos en la metodología elegida + handoff
```

Cuando la herramienta **no** soporta sub-agentes, todo se colapsa en `specfounder-v2/monolith/specfounder-v2.monolith.md` (mismo comportamiento).

### Estructura del repositorio

```
.
├── INICIAR.md                         ← ▶️ arranque automatizado (instalas /iniciar, /retomar, /ayuda)
├── EMPEZAR-AQUI.md                    ← 🚀 arranque manual (copiar y pegar, 3 pasos)
├── AYUDA.md                           ← ❓ guía para usuarios (la presenta /ayuda)
├── README.md                          ← este archivo
├── CLAUDE.md                          ← guía para Claude Code
├── SPEC-FOUNDER-AGENT.md              ← v1 (legacy)
└── specfounder-v2/
    ├── core/coordinator.md            ← orquestador
    ├── agents/                        ← vision-generator, interviewer, glossarist, explorer, architect-adr, emitter
    ├── domains/                       ← perfiles: _spine, software, novela, serie-imagenes, guion-video, _custom
    ├── monolith/                      ← prompt portable todo-en-uno (fuente canónica del prompt)
    ├── comandos/                      ← archivos /iniciar listos para copiar por herramienta
    ├── methodologies/                 ← openspec, github-spec-kit, generic-sdd, creative-bible (mapeos + handoff)
    ├── skills/                        ← sf-domain, sf-vision, sf-checkpoint, sf-resume, sf-glossary-sync, sf-explore, sf-emit
    ├── persistence/                   ← STATE-SCHEMA.md + templates (la memoria persistente)
    ├── integrations/                  ← claude-code, codex, opencode, cursor, antigravity
    └── RENDIMIENTO.md                 ← estrategia de eficiencia de tokens (modelo por rol + checkpoint incremental)
```

---

## Cómo funciona la memoria persistente (lo más importante)

En la raíz del **proyecto objetivo** (no en este repo) se crea:

```
.specfounder/
├── session.md          ← cursor de la sesión: en qué pregunta vamos y qué sigue
├── SPEC.draft.md       ← spec vivo (6 secciones)
├── CONTEXT.draft.md    ← glosario vivo
└── adr/                ← ADRs borrador
```

- **Checkpoint:** tras *cada* respuesta y *antes* de la siguiente pregunta, el agente actualiza los drafts y reescribe `session.md` con la **"Siguiente acción" exacta**.
- **Resume:** al reactivarse, detecta `session.md`, muestra un resumen del avance y retoma en la "Siguiente acción", sin re-preguntar.

Detalle completo del esquema y los protocolos: [`specfounder-v2/persistence/STATE-SCHEMA.md`](specfounder-v2/persistence/STATE-SCHEMA.md).

> Añade `.specfounder/` a `.gitignore` mientras la sesión está en curso. Los artefactos finales (los que emite el Adaptador) sí se versionan.

---

## Dominios: SDD para código y para obras creativas

SDD no es solo para programar. La metodología (entrevista grill-me, glosario/canon, igualdad semántica, checkpoint, Visión) es **universal**; lo que cambia entre dominios es **cómo se nombran las 6 secciones, qué es el glosario y cómo se emite el resultado**. SpecFounder lo resuelve con **perfiles de dominio** sobre una **espina universal** de 6 ranuras.

Lo **primero** que pregunta una sesión nueva es el dominio:

| Ranura universal | `software` | `novela` | `serie-imagenes` | `guion-video` |
|---|---|---|---|---|
| 1 Visión | Visión del producto | Premisa y tema | Concepto visual | Logline |
| 2 Actores | Usuarios | Personajes y facciones | Sujetos consistentes | Personajes y voces |
| 3 Elementos | Funcionalidades | Tramas y subtramas | Rasgos recurrentes | Beats / mensajes |
| 4 Estructura/Flujo | Flujos de usuario | Estructura narrativa | Línea de la serie | Fraccionamiento por partes |
| 5 Forma | Arquitectura | Mundo y escenarios | Guía de estilo visual | Formato y producción |
| 6 Restricciones | No funcionales | Tono, estilo, formato | Restricciones de producción | Restricciones |
| **Glosario = ** | términos del dominio | **biblia** (personajes, lugares, reglas) | model-sheet en palabras | términos recurrentes |
| **Salida** | OpenSpec / Spec-Kit / genérico | **Biblia** (`BIBLE.md`+`CANON.md`) | **Biblia** | **Biblia** |

¿Otro propósito (curso, podcast, juego, campaña…)? El perfil **`custom`** deriva las 6 ranuras al vuelo y las confirma contigo — son **infinitas posibilidades**. El glosario/canon es lo que evita **redefinir la estructura en cada pieza** (cada capítulo, imagen o parte hereda el marco fijo).

Detalle: [`specfounder-v2/domains/`](specfounder-v2/domains/) · espina universal en [`_spine.md`](specfounder-v2/domains/_spine.md) · salida creativa en [`methodologies/creative-bible.md`](specfounder-v2/methodologies/creative-bible.md) · skill [`sf-domain`](specfounder-v2/skills/sf-domain/SKILL.md).

---

## Generador de Visión (proyectos nuevos)

La Visión del producto es el **"Norte"** del proyecto. El problema: el usuario rara vez la tiene redactada con precisión y suele pedirle a una IA que actúe como experto para crearla. SpecFounder v2 integra ese paso al flujo.

Al iniciar un **proyecto nuevo**, antes de la entrevista, el agente ofrece dos caminos:

- **Construirla** — el usuario escribe libremente su idea; el agente redacta **mínimo 3 alternativas de Visión** (con ángulos distintos), explica el porqué de cada una, **recomienda una**, y deja elegir, fusionar o anexar algo extra.
- **Aportarla** — el usuario pega una Visión que ya tiene y el agente la valida para continuar.

**Lineamientos (normativa inviolable):** toda Visión responde *¿por qué existe?*, *¿qué problema resuelve?* y *¿cuál es su esencia única?*, en **máximo 2 párrafos**. Un texto más amplio no se considera una buena Visión. La Visión queda en `SPEC.draft.md` §1 y cubre la mayor parte de la Sección 1; la entrevista solo confirma lo que falte (p. ej. el usuario principal).

Detalle: [`agents/vision-generator.md`](specfounder-v2/agents/vision-generator.md) · skill [`sf-vision`](specfounder-v2/skills/sf-vision/SKILL.md).

---

## Instalación por herramienta

Cada guía indica exactamente dónde colocar los prompts:

| Herramienta | Modo | Guía |
|---|---|---|
| **Claude Code** | Multi-agente + skills nativas | [integrations/claude-code.md](specfounder-v2/integrations/claude-code.md) |
| **Codex** | Monolito vía `AGENTS.md` | [integrations/codex.md](specfounder-v2/integrations/codex.md) |
| **OpenCode** | Multi-agente o monolito | [integrations/opencode.md](specfounder-v2/integrations/opencode.md) |
| **Cursor** | Monolito vía `.cursor/rules/` | [integrations/cursor.md](specfounder-v2/integrations/cursor.md) |
| **Antigravity** | Monolito vía reglas del workspace | [integrations/antigravity.md](specfounder-v2/integrations/antigravity.md) |
| **Cualquier chat/CLI** | Pega el [monolito](specfounder-v2/monolith/specfounder-v2.monolith.md) como system prompt | — |

---

## Pasos de uso (flujo completo)

1. **Instala** SpecFounder v2 en tu herramienta (tabla de arriba). Atajos: [INICIAR.md](INICIAR.md) (comando `/iniciar`) o [EMPEZAR-AQUI.md](EMPEZAR-AQUI.md) (copiar y pegar).
2. **Activa** el agente en la raíz del proyecto a especificar (`/iniciar`, `@specfounder-coordinator`, o pegando el prompt).
3. **Responde la selección inicial** (una pregunta a la vez):
   - **Dominio:** código o creativo, y el subtipo (software · novela · serie-imagenes · guion-video · custom).
   - **Metodología** (solo si es código): OpenSpec · Spec-Kit · SDD genérico.
   - **Modo:** proyecto nuevo · existente · re-spec parcial · glosario urgente.
4. *(Proyecto existente)* el agente **explora el material** (código o manuscrito/biblia) y precarga lo que ya está respondido.
5. *(Proyecto nuevo)* el agente lanza el **Generador de Visión**: eliges entre construir la Visión (3+ alternativas + recomendación) o aportar la tuya. La Visión final (≤ 2 párrafos) fija la Sección 1.
6. **Entrevista grill-me** por las secciones restantes (con el vocabulario del dominio). Una pregunta por turno, siempre con recomendación. El glosario/canon y las decisiones irreversibles se construyen en paralelo.
7. **Si se corta la sesión:** vuelve a activar el agente (o usa la skill `sf-resume`). Verás el resumen y continuarás donde quedaste.
8. **Cierre:** revisas SPEC/Biblia + glosario + decisiones.
9. **Emisión:** el Adaptador genera los artefactos (metodología de código **o** Biblia) y entrega el **handoff** para el siguiente paso.

### Cómo retomar tras una caída (resumen)

```
Escribes /retomar  (o reactivas el agente / usas la skill sf-resume)
   → detecta .specfounder/session.md
   → muestra: progreso por sección, glosario, ADRs, última decisión, ramas abiertas
   → "Retomo aquí: S2.Q3 — ¿…? ¿Continuamos?"
   → continúas sin repetir nada
```

---

## Las 6 secciones del SPEC

1. **Visión** — qué es, para quién, qué problema resuelve.
2. **Usuarios** — roles concretos con acciones concretas.
3. **Funcionalidades** — comportamiento observable por módulo.
4. **Flujos** — pasos exactos, happy path + error path.
5. **Arquitectura** — estructura técnica (decisiones grandes → ADR).
6. **No Funcionales** — concurrencia, seguridad, offline, i18n, SLAs, hosting.

**CONTEXT.md** (glosario / canon) y las **decisiones irreversibles** (ADRs) se construyen en paralelo. Una decisión se registra solo si es *difícil de revertir*, *sorprendente sin contexto* y tiene un *trade-off real*. En dominios creativos, las 6 secciones cambian de nombre (ver tabla de [Dominios](#dominios-sdd-para-código-y-para-obras-creativas)).

---

## Ejemplo A — sesión del Generador de Visión

*Dominio: software · proyecto nuevo · ruta "construir la Visión".*

```
🧑 Usuario: Quiero una app para que equipos pequeños registren sus gastos
            compartidos y sepan quién le debe a quién.

🤖 SpecFounder (Generador de Visión):
Cuéntame tu idea con tus palabras: ¿qué te molesta hoy de cómo se hace,
y para quién es? No te preocupes por la forma, yo la redacto.

🧑 Usuario: Los grupos usan hojas de cálculo y se vuelve un lío; nadie
            sabe el saldo real y siempre hay discusiones al cerrar cuentas.

🤖 SpecFounder: Tengo 3 alternativas de Visión (máx. 2 párrafos cada una):

  Alternativa 1 — centrada en el problema
  "Saldar cuentas entre amigos hoy depende de hojas de cálculo frágiles y
  de la memoria de cada quien, lo que genera saldos opacos y discusiones.
  [Producto] reemplaza ese caos por un registro compartido en tiempo real
  donde cada gasto queda claro y el saldo de quién-debe-a-quién es siempre
  exacto y visible para todo el grupo."
  _Por qué esta_: ataca directo el dolor (opacidad y fricción social).

  Alternativa 2 — centrada en el usuario/impacto
  "[Producto] le da a los grupos pequeños la tranquilidad de gastar juntos
  sin llevar la cuenta mentalmente: registra el gasto en segundos y olvídate;
  el saldo se calcula solo. Menos planillas, menos discusiones, más confianza."
  _Por qué esta_: vende el beneficio emocional (tranquilidad), no la mecánica.

  Alternativa 3 — centrada en la esencia diferenciadora
  "A diferencia de las hojas de cálculo y las apps bancarias, [Producto] se
  enfoca en una sola cosa hecha impecable: el saldo neto de un grupo, siempre
  correcto y en tiempo real, con la mínima fricción para registrar un gasto."
  _Por qué esta_: posiciona el foco como ventaja frente a alternativas.

Mi recomendación: la 1 — nombra el problema y la esencia (saldo exacto y
visible), que es lo que más repetiste.

¿Cuál eliges (1, 2, 3), combino alguna, o quieres anexar algo extra?

🧑 Usuario: La 1, pero añade que funciona aunque no todos tengan la app.

🤖 SpecFounder: Visión final (guardada en SPEC.draft.md §1):
"Saldar cuentas entre amigos hoy depende de hojas de cálculo frágiles…
…exacto y visible para todo el grupo, incluso cuando no todos los miembros
usan la app (se les puede incluir como participantes sin cuenta)."
✅ Cumple la normativa (2 párrafos, responde por qué/problema/esencia).
Checkpoint hecho. Continúo con la Sección 2 (Usuarios). ¿Listo?
```

---

## Ejemplo B — sesión end-to-end (resumen)

*Dominio: software · OpenSpec · proyecto nuevo. Se muestran turnos clave; cada `↳` es un checkpoint en `.specfounder/`.*

```
1. Dominio        → "Código → software"                         ↳ domain=software
2. Metodología    → "OpenSpec"                                  ↳ methodology=openspec
3. Modo           → "Nuevo"                                     ↳ phase=vision
4. Visión         → (Ejemplo A) → Visión fijada                 ↳ s1=completa, vision_mode=generada

5. Sección 2 (Usuarios)
   🤖 ¿Cuántos tipos de usuario distintos hay?
      Mi recomendación: 2 — Miembro y Organizador del grupo.
   🧑 Sí, esos dos.                                             ↳ S2.Q1 ✅
   🤖 Capté el término "grupo". ¿Es lo mismo que "organización"?
   🧑 Es lo mismo. Usemos "grupo".                              ↳ CANON: Grupo (_Evitar_: organización)
   … (resto de S2) …                                           ↳ s2=completa

6. Sección 3 (Funcionalidades) … 4 (Flujos) … 5 (Arquitectura)
   🤖 ¿Backend propio o servicio externo?
      Mi recomendación: BaaS (p. ej. Supabase) para llegar a v1 rápido.
   🧑 De acuerdo.                                               ↳ ADR-0001 (difícil de revertir)
   … 6 (No Funcionales) …                                      ↳ s3..s6=completa

7. — se cae la sesión / cierro el editor —
   🔄 Reactivo el agente:
      "Sesión recuperada — App de gastos (OpenSpec · nuevo).
       ✅ Secciones 1–5 · ⏳ Sección 6 en curso · CANON: 6 términos · 1 ADR.
       ▶️ Retomo: S6.Q3 — ¿Debe funcionar offline? ¿Continuamos?"
   🧑 Sí, lectura offline.                                      ↳ s6=completa

8. Cierre → muestra SPEC + CANON + ADR para revisión.
9. Emisión (OpenSpec):
   openspec/project.md   (visión + glosario + stack)
   openspec/specs/…      (capabilities por módulo)
   openspec/changes/…    (primer cambio propuesto)
   + bloque de handoff.                                         ↳ phase=emitido
```

> Para un dominio creativo el flujo es idéntico, pero el paso 1 sería "Creativo → novela", no se pregunta metodología, las secciones usan vocabulario narrativo, y el paso 9 emite `BIBLE.md` + `CANON.md` en lugar de `openspec/`.

---

## Rendimiento y costo de tokens

SpecFounder es eficiente por diseño, con dos palancas (detalle en [`specfounder-v2/RENDIMIENTO.md`](specfounder-v2/RENDIMIENTO.md)):

- **Modelo por rol** — no todo necesita un modelo de alto desempeño. *Opus piensa* (Entrevistador, Generador de Visión, Arquitecto/ADR), *Sonnet lee y orquesta* (Coordinador, Explorador, Glosarista), *Haiku rellena y guarda* (Adaptador, comando `/ayuda`). En **Claude Code** se aplica con `model:` en cada sub-agente y slash-command; en el **monolito** (Codex, Cursor, OpenCode, chat) se elige un único modelo de sesión (Sonnet por defecto, Opus si el proyecto es complejo).
- **Checkpoint incremental** — `session.md` se actualiza por edición (no se regenera entero) y se mantiene magro; es la operación más repetida, así que ahí está el mayor ahorro.

Lo que **nunca** se sacrifica por eficiencia: una pregunta por turno, checkpoint antes de cada pregunta, igualdad semántica, Visión ≤ 2 párrafos y los 3 criterios de ADR.

---

## Reglas inviolables (heredadas de v1, ampliadas)

- Una sola pregunta por turno.
- No avanzar de sección con ramas abiertas.
- No inventar decisiones técnicas: siempre recomendación + confirmación.
- No usar lenguaje del usuario sin verificarlo contra el glosario.
- Ante contradicción, detenerse y resolverla.
- CONTEXT es un glosario **puro** (sin implementación ni decisiones técnicas).
- **Nunca formular una pregunta sin checkpoint del turno anterior.**
- **Nunca asumir dominio, metodología ni modo:** se eligen al inicio o se leen del `session.md` al retomar.

---

## Migración desde v1

El spec de v1 (`SPEC.md` + `CONTEXT.md`) equivale a la salida del adaptador **SDD genérico** de v2. Para migrarlo a OpenSpec o Spec-Kit, ejecuta una sesión en modo **re-spec parcial** eligiendo la nueva metodología; el Adaptador reestructura sin reescribir el contenido.

---

## Versionado y releases

El proyecto sigue [SemVer](https://semver.org/lang/es/) y documenta cada versión en [CHANGELOG.md](CHANGELOG.md) (histórico, más reciente arriba) y [RELEASE-NOTES.md](RELEASE-NOTES.md) (última versión). La versión vigente se muestra al inicio de este README.

Cada versión se etiqueta con un tag (`vX.Y.Z`) y agrupa los cambios por tipo de [Conventional Commit](https://www.conventionalcommits.org/) (`feat`, `fix`, `docs`, `refactor`, `perf`, `chore`…). Para que las entradas salgan limpias, escribe los commits en ese formato (`tipo: descripción`).

---

## Licencia

Publicado bajo la **Licencia MIT** — uso libre (usar, modificar, distribuir, incluso comercial) siempre que se **conserve el aviso de copyright y la atribución al autor**. Ver [LICENSE](LICENSE).

© 2026 Ing. Oscar Lobo
