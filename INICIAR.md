# ▶️ INICIAR — SpecFounder v2 (modo automatizado)

Esta es la forma **automatizada** de arrancar SpecFounder v2: en vez de copiar y pegar un prompt largo (eso es [EMPEZAR-AQUI.md](EMPEZAR-AQUI.md)), instalas los comandos y luego solo escribes `/iniciar` (empezar/retomar), `/retomar` (forzar recuperación) o `/ayuda` (guía para usuarios) en tu herramienta.

Funciona porque `INICIAR.md` es un **cargador**: no contiene el prompt completo, sino la instrucción de leer el prompt canónico ([specfounder-v2/monolith/specfounder-v2.monolith.md](specfounder-v2/monolith/specfounder-v2.monolith.md)) y adoptarlo. Así no hay copias del prompt que mantener sincronizadas.

---

## El cargador (lo que el comando ejecuta)

```
Actúa como CARGADOR de SpecFounder v2. Tu única tarea es convertirte en ese agente:

1. Lee el archivo specfounder-v2/monolith/specfounder-v2.monolith.md de este proyecto y
   adopta ÍNTEGRAMENTE el rol definido dentro de su bloque de prompt.
   (Si tu entorno soporta sub-agentes y están instalados, adopta en su lugar
   specfounder-v2/core/coordinator.md junto con specfounder-v2/agents/.)
2. Antes de nada, comprueba si existe .specfounder/session.md:
   - Si existe → ejecuta el protocolo RESUME (muestra el resumen y retoma sin repetir).
   - Si no existe → ejecuta la sección <inicio>: preséntate como "SpecFounder v2 activado"
     y formula la primera pregunta (el dominio).
3. Si NO tienes acceso de lectura a archivos, pide al usuario el prompt de respaldo
   (está embebido en EMPEZAR-AQUI.md) o que use esa vía.

No expliques que eres un cargador: simplemente conviértete en SpecFounder v2 y comienza.
```

`/retomar` usa el mismo cargador pero **fuerza el protocolo RESUME**: si hay `.specfounder/session.md` lo retoma sin repetir preguntas; si no hay sesión, lo dice y sugiere `/iniciar`. En la práctica `/iniciar` ya retoma solo cuando detecta una sesión, así que `/retomar` es el atajo explícito para "sigue donde quedamos".

`/ayuda` es distinto: **no arranca ni modifica nada**. Lee [AYUDA.md](AYUDA.md) y actúa como guía para el usuario —explica los comandos, los pasos y qué es cada opción, y responde dudas en lenguaje sencillo—. Ideal para personas con pocos conocimientos.

---

## Instalación de los comandos por herramienta

Archivos listos para copiar en [`specfounder-v2/comandos/`](specfounder-v2/comandos/). Copia los que correspondan a la ruta indicada **dentro del proyecto donde vas a crear el spec** y reinicia/recarga la herramienta. Instala ambos (`iniciar.*` y `retomar.*`).

| Herramienta | Carpeta de comandos del proyecto | Comandos |
|---|---|---|
| **Claude Code** | `.claude/commands/` → `iniciar.md`, `retomar.md`, `ayuda.md` | `/iniciar` · `/retomar` · `/ayuda` |
| **Cursor** | `.cursor/commands/` → `iniciar.md`, `retomar.md`, `ayuda.md` | `/iniciar` · `/retomar` · `/ayuda` (o `@INICIAR.md`) |
| **Codex CLI** | `~/.codex/prompts/` → `iniciar.md`, `retomar.md`, `ayuda.md` | `/iniciar` · `/retomar` · `/ayuda` |
| **OpenCode** | `.opencode/command/` → `iniciar.md`, `retomar.md`, `ayuda.md` | `/iniciar` · `/retomar` · `/ayuda` |
| **VSCode + Copilot** | `.github/prompts/` → `iniciar.prompt.md`, `retomar.prompt.md`, `ayuda.prompt.md` | `/iniciar` · `/retomar` · `/ayuda` en Copilot Chat |

> Los nombres de carpeta de comandos varían entre versiones; si tu herramienta no reconoce el comando, revisa dónde guarda los comandos personalizados y coloca ahí el mismo contenido. En cualquier caso, **el texto del comando es siempre el cargador correspondiente** (`comandos/iniciar.*`, `comandos/retomar.*`, `comandos/ayuda.*`).

### Atajo aún más simple (sin instalar comando)
En cualquier agente que pueda leer archivos, basta decir:

> **"Lee INICIAR.md y haz lo que dice."** (empezar/retomar automático)
> **"Retoma la sesión de SpecFounder"** (recuperación explícita)
> **"Lee AYUDA.md y explícame cómo funciona"** (ayuda)

---

## ¿Cuál uso?

- **`/iniciar`:** empieza una sesión nueva (o retoma si ya hay una). Tu comando del día a día.
- **`/retomar`:** específico para continuar una sesión interrumpida sin repetir preguntas.
- **`/ayuda`:** guía y dudas; no inicia ni cambia nada. Pensado para usuarios nuevos.
- **[EMPEZAR-AQUI.md](EMPEZAR-AQUI.md):** copiar y pegar. Para chats sin acceso a archivos (Claude.ai, ChatGPT web) o si no quieres instalar nada.

Todas terminan en lo mismo: SpecFounder v2 conduciendo tu entrevista con memoria persistente.
