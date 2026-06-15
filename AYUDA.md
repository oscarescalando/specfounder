# ❓ AYUDA — Guía de SpecFounder v2 para usuarios

Esta es la guía general. Si algo no se entiende, escribe `/ayuda` en tu herramienta y el asistente te la explica y responde tus dudas. **No necesitas saber programar.**

---

## ¿Qué es esto, en simple?

SpecFounder es un asistente que **te entrevista** —una pregunta a la vez, siempre con una recomendación— y con tus respuestas arma la **especificación** (el "plano" o "spec") de lo que quieres crear. Sirve para dos grandes cosas:

- **Código:** un sistema, una app, una API, una página web… (cualquier tecnología).
- **Creativo:** una novela o libro, una serie de imágenes, un guion de video, o casi cualquier estructura.

Su objetivo es que **tú y la IA usen exactamente las mismas palabras con el mismo significado**, para que después, cuando generes el código o la obra, no haya malentendidos.

> Va **guardando tu avance** automáticamente. Si se cierra la sesión, puedes continuar donde quedaste.

---

## Los comandos disponibles

| Comando | Para qué sirve |
|---|---|
| **`/iniciar`** | Empezar una sesión nueva (o continuar si ya había una). Es el comando del día a día. |
| **`/retomar`** | Continuar una sesión que se interrumpió, sin repetir preguntas. |
| **`/ayuda`** | Mostrar esta guía y responder tus dudas. No empieza ni modifica nada. |

¿No tienes los comandos instalados? Ver [INICIAR.md](INICIAR.md) (instalación en 1 minuto) o usa [EMPEZAR-AQUI.md](EMPEZAR-AQUI.md) (copiar y pegar, sin instalar nada).

---

## Cómo funciona, paso a paso

Cuando escribes `/iniciar`, el asistente te lleva por estos pasos. **En cada paso te da una recomendación**: si no sabes qué responder, puedes seguirla.

1. **¿Para qué es el spec? (dominio)** — Eliges *Código* o *Creativo*, y el subtipo (software, novela, serie de imágenes, guion de video, u "otro"). Esto define el vocabulario de las preguntas y el resultado final.
2. **Metodología (solo si es código)** — Eliges cómo quieres el resultado: *OpenSpec*, *GitHub Spec-Kit* o *SDD genérico*. Si no conoces ninguna, elige **SDD genérico** (es portable y se puede cambiar después). *En proyectos creativos este paso no aparece: el resultado es una "Biblia".*
3. **¿Nuevo o existente?** — Si ya hay material (código, manuscrito, etc.), el asistente lo revisa antes de preguntarte, para no repetir lo que ya está claro.
4. **La Visión (solo proyectos nuevos)** — El "Norte" de tu proyecto. Puedes:
   - **Construirla:** cuentas tu idea con tus palabras y el asistente te propone **3 versiones** para que elijas (y puedes ajustarla o añadir algo).
   - **Aportarla:** si ya la tienes, la pegas.
   - Regla: una buena Visión cabe en **máximo 2 párrafos**.
5. **La entrevista (6 secciones)** — Te pregunta por las 6 partes de tu spec, una pregunta por turno. Los nombres cambian según el dominio (ver tabla abajo).
6. **Revisión** — Cuando termina, te muestra todo para que lo revises.
7. **Resultado (emisión)** — Genera los archivos finales (la metodología que elegiste, o la "Biblia" si es creativo) y un mensaje de "entrega" listo para usar en la siguiente herramienta.

---

## ¿Qué es cada cosa? (diccionario rápido)

- **Spec / especificación:** el documento que describe qué hay que construir, antes de construirlo.
- **Dominio:** el tipo de cosa que estás especificando (código o creativo).
- **Metodología (OpenSpec / Spec-Kit / SDD genérico):** el formato final del spec para proyectos de código. Son maneras distintas de organizar los mismos archivos. *SDD genérico* es la más simple.
- **Modo (nuevo / existente):** si partes de cero o de algo que ya existe.
- **Visión:** una o dos frases que dicen por qué existe el proyecto, qué problema resuelve y qué lo hace único.
- **Las 6 secciones:** las partes de tu spec. Según el dominio se llaman distinto:

  | # | Software | Novela | Serie de imágenes | Guion de video |
  |---|---|---|---|---|
  | 1 | Visión | Premisa y tema | Concepto visual | Logline |
  | 2 | Usuarios | Personajes y facciones | Sujetos consistentes | Personajes y voces |
  | 3 | Funcionalidades | Tramas y subtramas | Rasgos recurrentes | Beats / mensajes |
  | 4 | Flujos | Estructura narrativa | Línea de la serie | Fraccionamiento por partes |
  | 5 | Arquitectura | Mundo y escenarios | Guía de estilo | Formato y producción |
  | 6 | Requisitos no funcionales | Tono y estilo | Restricciones de producción | Restricciones |

- **Glosario / Canon:** la lista de términos importantes con su significado exacto. En obras creativas es la "biblia" (personajes, lugares, reglas) que evita tener que redefinir todo en cada capítulo o imagen.
- **Decisión irreversible (ADR):** una decisión difícil de cambiar después; el asistente la anota para no olvidarla.
- **Checkpoint:** el guardado automático que ocurre tras cada respuesta.
- **`.specfounder/`:** la carpeta donde se guarda tu avance (dentro de tu proyecto).
- **Biblia (`BIBLE.md` + `CANON.md`):** el resultado final de un proyecto creativo.
- **Handoff:** el mensaje final que le pasas a la siguiente IA/herramienta para que use tu spec como fuente de verdad.

---

## Preguntas frecuentes

**Se cerró la app / se cayó internet. ¿Perdí todo?**
No. Escribe `/retomar` (o vuelve a abrir el asistente): leerá tu avance y seguirá justo donde quedaste, sin repetir preguntas.

**No sé responder una pregunta.**
Cada pregunta trae una recomendación. Puedes aceptarla, o decir "no sé" y el asistente te propone una opción concreta.

**¿Tengo que elegir metodología si no soy técnico?**
Si dudas, elige **SDD genérico**. Después se puede convertir a otra sin reescribir nada.

**¿Esto escribe el código / la novela por mí?**
No directamente. Crea el **spec** (el plano). Luego ese spec se usa para generar el código u obra con la herramienta que prefieras, con mucha menos confusión.

**¿Dónde quedan mis archivos?**
En tu proyecto: el avance en `.specfounder/` y, al final, los archivos del resultado (spec o Biblia) en la raíz.

**¿Puedo usarlo para algo que no está en la lista (un curso, un podcast, un juego)?**
Sí. Elige "otro" y el asistente arma un perfil a tu medida.

---

## ¿Por dónde empiezo ahora?

- Para **arrancar**: escribe `/iniciar` (o sigue [INICIAR.md](INICIAR.md) para instalar el comando, o [EMPEZAR-AQUI.md](EMPEZAR-AQUI.md) para copiar y pegar).
- Para **continuar** algo a medias: escribe `/retomar`.
- Para **entender más**: sigue preguntando aquí con `/ayuda`.
- Documentación completa: [README.md](README.md).
