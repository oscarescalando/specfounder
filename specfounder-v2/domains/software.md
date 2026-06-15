# Perfil de dominio — Software (sistema · app · API · web)

> **domain:** `software`
> **Salida:** metodologías de código (`openspec` · `github-spec-kit` · `generic-sdd`).
> **Espina:** ver `_spine.md`. Este perfil es el original de SpecFounder; las 6 ranuras conservan su nombre clásico.

Aplica a cualquier stack/tecnología (no está casado con ninguno): sistemas backend, apps móviles/escritorio, APIs, sitios y aplicaciones web, CLIs, librerías, etc.

## Mapeo de ranuras → secciones

| Ranura | Sección (software) |
|---|---|
| 1 Visión | **Visión del Producto** |
| 2 Actores | **Usuarios y Casos de Uso** |
| 3 Elementos | **Funcionalidades por Módulo** |
| 4 Estructura/Flujo | **Flujos de Usuario** |
| 5 Forma | **Arquitectura** |
| 6 Restricciones | **Requisitos No Funcionales** |

## Preguntas guía
Son las del Entrevistador (`agents/interviewer.md`, guion base S1–S6). Este perfil usa ese guion tal cual.

## Canon / Glosario
Términos únicos del dominio del software (entidades, conceptos de negocio). No conceptos generales de programación.

## Decisiones irreversibles
ADRs clásicos: elección de base de datos, patrón de arquitectura, proveedor de auth, etc., cuando cumplen los 3 criterios.

## Notas
- En Sección 5 (Arquitectura) el agente pregunta el stack/tecnología que usa el equipo; **no asume ninguno**. Si el usuario dice "a decidir", el Arquitecto propone una opción concreta y justificada.
