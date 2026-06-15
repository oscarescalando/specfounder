# Integración — Claude Code

Claude Code soporta sub-agentes y skills nativas, así que aprovecha la versión **multi-agente** completa.

## Instalación

1. **Sub-agentes** → copia cada archivo a `.claude/agents/` del proyecto (o `~/.claude/agents/` para uso global). Añade el frontmatter que Claude Code requiere, **incluyendo `model:`** para aplicar la estrategia de rendimiento (ver [`../RENDIMIENTO.md`](../RENDIMIENTO.md)):
   ```markdown
   ---
   name: specfounder-coordinator
   description: Orquestador SDD. Conduce la sesión de spec con memoria persistente.
   model: sonnet
   ---
   <contenido de specfounder-v2/core/coordinator.md>
   ```
   Repite para los demás, asignando el modelo según el rol. Cada sub-agente corre en su propia ventana de contexto y modelo, así que el ahorro es real y aislado:

   | Sub-agente | `model:` | Por qué |
   |---|---|---|
   | `specfounder-coordinator` | `sonnet` | Orquesta y rutea; no necesita Opus. |
   | `vision-generator` | `opus` | Redacta 3 alternativas con criterio. |
   | `interviewer` | `opus` | Grill-me: aquí vive la igualdad semántica. |
   | `architect-adr` | `opus` | Juicio sobre trade-offs irreversibles. |
   | `glossarist` | `sonnet` | Canoniza términos (patrón). |
   | `explorer` | `sonnet` | Lee código/manuscrito (contexto grande). |
   | `emitter` | `haiku` | Rellena plantillas de salida (casi determinista). |

   > Regla mental: **Opus piensa, Sonnet lee/orquesta, Haiku rellena/guarda.** Si tu plan no da acceso a los tres modelos, usa `inherit` y deja que el ahorro venga de las optimizaciones de checkpoint.

2. **Skills** → copia las carpetas de `specfounder-v2/skills/` a `.claude/skills/`:
   ```
   .claude/skills/sf-domain/SKILL.md
   .claude/skills/sf-vision/SKILL.md
   .claude/skills/sf-checkpoint/SKILL.md
   .claude/skills/sf-resume/SKILL.md
   .claude/skills/sf-glossary-sync/SKILL.md
   .claude/skills/sf-explore/SKILL.md
   .claude/skills/sf-emit/SKILL.md
   ```
   Ya traen el frontmatter `name`/`description` correcto.

   > **Modelo de las skills:** una skill se ejecuta en el modelo del agente que la invoca; no tiene `model:` propio. Por eso `sf-checkpoint` y `sf-resume` corren, por defecto, en el modelo del coordinador (`sonnet`) — ya barato. Si quieres forzar `haiku` en el checkpoint, expón esa operación como un sub-agente dedicado (`model: haiku`) en lugar de skill. Para la mayoría de los casos no hace falta: el grueso del ahorro viene del **checkpoint incremental** (ver `../RENDIMIENTO.md` §3).

3. **Datos de referencia** → copia `specfounder-v2/domains/` y `specfounder-v2/methodologies/` a un lugar accesible del proyecto (p. ej. `.claude/specfounder/`) y ajusta las rutas relativas en los prompts. El coordinador y `sf-domain`/`sf-emit` los leen para cargar el perfil de dominio y el mapeo de salida.

4. **CLAUDE.md** → añade un puntero para que el coordinador se active fácil:
   ```markdown
   ## SpecFounder v2
   Para crear/retomar el spec de un proyecto, invoca el agente `specfounder-coordinator`.
   El estado vive en `.specfounder/`. Para retomar tras una caída, usa la skill `sf-resume`.
   ```

## Comando `/iniciar` (recomendado)

Copia a `.claude/commands/` los tres cargadores: [`../comandos/iniciar.claude-code.md`](../comandos/iniciar.claude-code.md) → `iniciar.md`, [`../comandos/retomar.claude-code.md`](../comandos/retomar.claude-code.md) → `retomar.md`, [`../comandos/ayuda.claude-code.md`](../comandos/ayuda.claude-code.md) → `ayuda.md`. Luego `/iniciar` para arrancar, `/retomar` para continuar y `/ayuda` para la guía — sin pegar prompts.

## Uso

- **Iniciar:** `/iniciar` (o `@specfounder-coordinator "Crear el spec de <proyecto>"`). Al primer turno pregunta el dominio (código o creativo), luego —si es código— la metodología, y el modo.
- **Retomar tras caída:** `/retomar` (o vuelve a invocar al coordinador / usa la skill `sf-resume`); detecta `.specfounder/session.md` y continúa donde quedó.
- **Permisos:** el agente necesita escribir en `.specfounder/`. Acéptalo cuando lo pida.

## Notas
- `.specfounder/` debería ir en `.gitignore` mientras la sesión está en curso; los artefactos emitidos por `sf-emit` sí se versionan.
