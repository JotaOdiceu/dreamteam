# Dreamteam

**Framework de orquestación de agentes de IA para Claude Code.**

Crea equipos de agentes con personalidades definidas, pipelines automatizados y memoria persistente, todo dentro de tu terminal, sin servidores, sin APIs externas y sin configuración compleja.

> Otras versiones de este documento: [Português](README.md) . [English](README.en.md)

---

## Que es Dreamteam?

Dreamteam es un framework basado en archivos Markdown que enseña a Claude (vía Claude Code) a orquestar múltiples agentes con personas distintas, ejecutar pipelines paso a paso y acumular memoria entre ejecuciones.

Tú defines **quiénes** son tus agentes (personas), **qué** hacen (tareas) y **en qué orden** (pipeline). Dreamteam se encarga del resto: carga de contexto, ejecución secuencial, checkpoints de revisión, versionado de outputs y memoria de aprendizaje.

### Qué puedes construir con Dreamteam

- Un consejo estratégico de VPs que debate las decisiones de tu empresa
- Un equipo de contenido con redactor, revisor y estratega que produce posts, hilos y artículos
- Un equipo de análisis que investiga, sintetiza y entrega informes estructurados
- Cualquier proceso que hoy harías usando múltiples "sombreros" a la vez

---

## Prerrequisitos

- **[Claude Code](https://claude.ai/code)**: CLI o aplicación de escritorio de Claude (Anthropic)
- Sin dependencias adicionales. Sin npm, pip, docker ni configuración de servidor.

---

## Instalación

### 1. Clona o descarga el repositorio

```bash
git clone https://github.com/JotaOdiceu/dreamteam.git
cd dreamteam
```

O descarga el ZIP y extráelo en la carpeta que prefieras.

### 2. Abre el proyecto en Claude Code

Lanza Claude Code y navega a la carpeta del proyecto:

```bash
claude /ruta/hacia/dreamteam
```

O ábrelo desde la aplicación de escritorio apuntando al directorio.

### 3. Ejecuta el onboarding

La primera vez, Dreamteam detecta que no está configurado e inicia el onboarding automáticamente:

```bash
/dreamteam
```

El asistente pedirá:

- Tu nombre y preferencias
- Idioma de los outputs (por defecto: Portugués, configurable a cualquier idioma)
- Contexto de tu proyecto o empresa

Esta información se guarda en `_workspace/context.md` y se carga en cada ejecución.

---

## Conceptos Fundamentales

### Personas

Una **persona** define a un agente: su identidad, tono de voz, principios y antipatrones. Es lo que hace que cada agente sea distinto y consistente.

El gran diferencial de Dreamteam es que **las personas son reutilizables entre equipos**. Defínelas una vez en la biblioteca global (`personas/`) y úsalas en tantos equipos como quieras. Los equipos también pueden tener personas locales exclusivas (`teams/{nombre}/personas/`).

```markdown
personas/
└── estratega.persona.md    ← disponible para cualquier equipo

teams/mi-equipo/
└── personas/
    └── especialista.persona.md  ← exclusiva de este equipo
```

**Formato de una persona (`*.persona.md`):**

```markdown
---
id: estratega
name: Estratega
title: VP de Estrategia y Posicionamiento
icon: 📈
tags: [estrategia, marketing, posicionamiento]
version: 1.0.0
---

## Identidad
Enfocado en "Dónde Jugar y Cómo Ganar". Analítico y directo.

## Tono de Voz
Preciso y orientado a resultados. Usa términos como "Ventaja Competitiva".
Nunca usa lenguaje de hype.

## Principios
- Diferénciate o muere
- La estrategia comienza en el problema del cliente, no en la tecnología
- Estrategia es decidir qué NO hacer

## Especialidades
Análisis competitivo, posicionamiento de mercado, propuesta de valor.

## Antipatrones
Nunca aprueba "hacer más de lo mismo". Nunca acepta metas sin métricas.
```

---

### Equipos

Un **equipo** es un grupo de agentes con un pipeline definido. Vive en `teams/{nombre}/`.

```markdown
teams/mi-equipo/
├── team.yaml          ← definición del equipo y pipeline
├── roster.csv         ← lista de miembros y sus personas
├── personas/          ← personas locales (opcionales)
├── tasks/             ← archivos de tareas del pipeline
│   ├── tarea-1.md
│   └── tarea-2.md
├── output/            ← outputs generados (gitignored)
└── _memory/
    ├── memories.md    ← preferencias y aprendizajes del equipo
    └── runs.md        ← historial de ejecuciones
```

**Formato de `team.yaml`:**

```yaml
name: "Consejo Estratégico"
description: "Mesa de consejeros para análisis de decisiones ejecutivas."
icon: "🏛️"
version: "1.0.0"

personas:
  - id: estratega
    source: global          # usa persona de la biblioteca global
  - id: moderador
    source: local           # usa persona local del equipo

pipeline:
  - id: briefing
    name: "Recolección del Desafío"
    persona: moderador
    task: tasks/briefing.md
    execution: inline
    output: output/briefing.md

  - id: revision
    type: checkpoint
    name: "Revisión del Briefing"
    message: "Revisa el briefing anterior. Se ve correcto?"

  - id: debate
    name: "Debate Estratégico"
    persona: estratega
    task: tasks/debate.md
    execution: inline
    input: output/briefing.md
    output: output/debate.md
```

---

### Tareas

Cada paso del pipeline apunta a un archivo de tarea. Las tareas definen qué hace el agente, cómo lo hace y qué produce.

**Formato de una tarea (`tasks/*.md`):**

```markdown
---
id: briefing
name: "Recolección del Desafío"
persona: moderador
---

## Objetivo
Recopilar y estructurar el desafío o pregunta planteado por el usuario.

## Contexto de Input
El usuario describe la situación o decisión que necesita ser analizada.

## Proceso
1. Lee el input del usuario
2. Identifica el núcleo de la decisión: que está en juego?
3. Estructura en: Contexto, Pregunta Central, Restricciones
4. Presenta para validación antes de avanzar

## Formato de Output
Documento Markdown con secciones: Contexto, Pregunta Central, Restricciones.

## Condiciones de Veto
- Output sin Pregunta Central definida: rechazar
- Output con más de 500 palabras: rechazar y resumir
```

---

### Pipeline

El pipeline es la secuencia de pasos definida en `team.yaml`. Cada paso puede ser:

| Tipo | Que hace |
| --------------------- | ---------------------------------------------------- |
| `execution: inline` | El agente ejecuta y presenta el resultado en el chat |
| `execution: subagent` | El agente trabaja en segundo plano |
| `type: checkpoint` | Pausa la ejecución y espera aprobación del usuario |

El Runner valida automáticamente:

- Que el input de un paso existe antes de ejecutar
- Que el output fue generado después de la ejecución
- Que no se violaron condiciones de veto (y solicita corrección)

---

### Memoria

Cada equipo acumula memoria entre ejecuciones:

- **`memories.md`**: preferencias y patrones extraídos de feedback explícito del usuario (lo que aprobó, rechazó o pidió directamente)
- **`runs.md`**: log cronológico de todas las ejecuciones con tema, output y resultado

La memoria se carga al inicio de cada ejecución e influye en el comportamiento de los agentes.

---

## Comandos

### Menu interactivo

```bash
/dreamteam
```

Abre el menú principal con opciones para crear, ejecutar, editar equipos y gestionar personas.

---

### Comandos de equipos

| Comando | Que hace |
| ---------------------------- | --------------------------------------------- |
| `/dreamteam create` | Lanza el asistente para crear un nuevo equipo |
| `/dreamteam teams` | Lista todos los equipos en `teams/` |
| `/dreamteam run <nombre>` | Ejecuta el pipeline de un equipo |
| `/dreamteam edit <nombre>` | Edita un equipo existente |
| `/dreamteam delete <nombre>` | Elimina un equipo (con confirmación) |

### Comandos de personas

| Comando | Que hace |
| ------------------------------------- | ------------------------------ |
| `/dreamteam personas` | Abre la biblioteca de personas |
| `/dreamteam personas new` | Crea una nueva persona global |
| `/dreamteam personas edit <nombre>` | Edita una persona existente |
| `/dreamteam personas delete <nombre>` | Elimina una persona |

### Workspace y configuración

| Comando | Que hace |
| -------------------- | ------------------------------------------------ |
| `/dreamteam context` | Ver o editar el contexto del workspace |
| `/dreamteam help` | Muestra el texto de ayuda completo |
| `/dreamteam reset` | Resetea toda la configuración (con confirmación) |

---

## Estructura de Directorios

```markdown
dreamteam/
│
├── CLAUDE.md                    ← Instrucciones del proyecto para Claude Code
│
├── .claude/
│   └── skills/
│       └── dreamteam/
│           └── SKILL.md         ← Skill principal (punto de entrada de /dreamteam)
│
├── _core/                       ← Núcleo del framework
│   ├── architect.md             ← Instrucciones del Arquitecto (crea/edita equipos)
│   └── runner.md                ← Instrucciones del Pipeline Runner (ejecuta equipos)
│
├── _workspace/
│   └── context.md               ← Contexto de tu proyecto/empresa (configurado en onboarding)
│
├── personas/                    ← Biblioteca global de personas reutilizables
│   └── estratega.persona.md     ← Ejemplo incluido
│
└── teams/                       ← Tus equipos viven aquí
    └── mi-equipo/
        ├── team.yaml
        ├── roster.csv
        ├── personas/
        ├── tasks/
        ├── output/
        └── _memory/
```

> **No modifiques manualmente** los archivos en `_core/` a menos que sepas lo que haces. Los archivos `CLAUDE.md`, `_workspace/` y `personas/` pueden editarse libremente.

---

## Ejemplo paso a paso

### 1. Crear un equipo de análisis estratégico

```bash
/dreamteam create "Un consejo con moderador y estratega para analizar decisiones de negocio"
```

El Arquitecto:

1. Propone la estructura del equipo y el pipeline
2. Verifica si hay personas compatibles en la biblioteca global
3. Crea o reutiliza personas
4. Construye todos los archivos necesarios

### 2. Ejecutar el equipo

```bash
/dreamteam run consejo-estrategico
```

El Runner:

1. Carga todas las personas
2. Ejecuta el paso 1: recopilar el desafío
3. Checkpoint: tú revisas el briefing
4. Ejecuta el paso 2: análisis estratégico
5. Guarda el output en `teams/consejo-estrategico/output/{run_id}/v1/`
6. Actualiza la memoria del equipo

### 3. Agregar una nueva persona a la biblioteca

```bash
/dreamteam personas new
```

El asistente recopila los datos de la persona y crea `personas/{id}.persona.md` lista para usar en cualquier equipo.

---

## Versionado de Outputs

Cada ejecución crea una carpeta única con timestamp:

```markdown
teams/mi-equipo/output/
└── 2026-04-16-143022/
    └── v1/
        ├── briefing.md
        └── analisis.md
```

Si el mismo paso se ejecuta más de una vez en el mismo run (por rechazo o reintento), el versionado avanza automáticamente a `v2/`, `v3/`, etc.

---

## Mención Honorífica

Dreamteam es una **reimplementación ligera y resumida** de [**Opensquad**](https://github.com/renatoasse/opensquad), un framework completo de orquestación multi-agente con soporte para skills conectables, investigación de perfiles en redes sociales (Sherlock), motor de publicación y mucho más.

Si Dreamteam cubre lo que necesitas, perfecto. Si necesitas algo más robusto, con catálogo de skills, integración con Playwright y un ecosistema más amplio, Opensquad es el camino correcto.

Dreamteam existe porque no siempre necesitas todo. A veces solo necesitas personas bien definidas, pipelines claros y memoria que funcione.

---

## Licencia

MIT. Usa, modifica y distribuye libremente.

---

## Powered by

jotaodiceu.dev
