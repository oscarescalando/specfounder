# Integración — Claude Code

Claude Code soporta sub-agentes y skills nativas, así que aprovecha la versión **multi-agente** completa.

## Instalación

1. **Sub-agentes** → copia cada archivo a `.claude/agents/` del proyecto (o `~/.claude/agents/` para uso global). Añade el frontmatter que Claude Code requiere:
   ```markdown
   ---
   name: specfounder-coordinator
   description: Orquestador SDD. Conduce la sesión de spec con memoria persistente.
   ---
   <contenido de specfounder-v2/core/coordinator.md>
   ```
   Repite para `vision-generator`, `interviewer`, `glossarist`, `explorer`, `architect-adr`, `emitter` (de `specfounder-v2/agents/`).

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
