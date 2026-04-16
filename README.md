# Dreamteam

> 🌎 Languages: Português • [English](README.en.md) • [Español](README.es.md)

**Framework de orquestração de agentes de IA.**

Crie times de agentes com personalidades definidas, pipelines automatizados e memória persistente, tudo dentro do seu terminal, sem servidores, sem APIs externas e sem configuração complexa.

---

## O que é o Dreamteam?

O Dreamteam é um framework baseado em arquivos que ensina agentes de IA a orquestrar múltiplos agentes com personas distintas, executar pipelines passo a passo e acumular memória entre execuções.

Compativel com **Claude Code** (Anthropic) e **Antigravity** (Google DeepMind). Basta abrir o projeto no seu assistente de preferido e digitar `/dreamteam`.

Você define **quem** são seus agentes (personas), **o que** eles fazem (tarefas) e **em que ordem** (pipeline). O Dreamteam cuida do resto: carregamento de contexto, execução sequencial, checkpoints de revisão, versionamento de output e memória de aprendizado.

### O que você pode fazer com o Dreamteam

- Montar um conselho estratégico de VPs que debate decisões da sua empresa
- Criar um time de conteúdo com redator, revisor e estrategista que produz posts, threads e artigos
- Construir um time de análise que pesquisa, sintetiza e entrega relatórios estruturados
- Automatizar qualquer processo que hoje você faria com múltiplos "chapéus" diferentes

---

## Pré-requisitos

Escolha uma das plataformas suportadas:

- **[Claude Code](https://claude.ai/code)** (Anthropic) — via CLAUDE.md + habilidade `.claude/skills/`
- **[Antigravity](https://antigravity.dev)** (Google DeepMind) — via `.agent/rules/` + `.agent/workflows/`

Nenhuma dependência adicional. Sem npm, pip, docker ou configuração de servidor.

---

## Instalação

### 1. Clone ou baixe o repositório

```bash
git clone https://github.com/JotaOdiceu/dreamteam.git
cd dreamteam
```

Ou baixe o ZIP e extraia na pasta de sua preferência.

### 2. Abra o projeto no Claude Code

Abra o Claude Code e navegue até a pasta do projeto:

```bash
claude /caminho/para/dreamteam
```

Ou abra pelo aplicativo desktop apontando para o diretório.

### 3. Execute o onboarding

Na primeira vez, o Dreamteam vai detectar que não está configurado e iniciar o onboarding automaticamente:

```bash
/dreamteam
```

O assistente vai pedir:

- Seu nome e preferências
- Linguagem dos outputs (padrão: Português)
- Contexto do seu projeto ou empresa

Essas informações ficam salvas em `_workspace/context.json` e são carregadas em toda execução.

---

## Conceitos Fundamentais

### Personas

Uma **persona** é a definição de um agente: sua identidade, tom de voz, princípios e anti-padrões. É o que torna cada agente distinto e consistente.

O grande diferencial do Dreamteam é que **personas são reutilizáveis entre times**. Crie uma vez na biblioteca global (`personas/`) e use em quantos times quiser. Times também podem ter personas locais exclusivas (`teams/{nome}/personas/`).

```markdown
personas/
└── estrategista.persona.md    ← disponível para qualquer time

teams/meu-time/
└── personas/
    └── especialista.persona.md  ← exclusiva deste time
```

**Formato de uma persona (`*.persona.md`):**

```markdown
---
id: estrategista
name: Estrategista
title: VP de Estratégia e Posicionamento
icon: 📈
tags: [estratégia, marketing, posicionamento]
version: 1.0.0
---

## Identidade
Focado em "Onde Jogar e Como Ganhar". Analítico e direto.

## Tom de Voz
Preciso e orientado a resultados. Usa termos como "Vantagem Competitiva".
Nunca usa linguagem de hype.

## Princípios
- Diferencie-se ou morra
- A estratégia começa no problema do cliente, não na tecnologia
- Estratégia é decidir o que NÃO fazer

## Especialidades
Análise competitiva, posicionamento de mercado, proposta de valor.

## Anti-padrões
Nunca aprova "fazer mais do mesmo". Nunca aceita metas sem métricas.
```

---

### Times

Um **time** é um grupo de agentes com um pipeline definido. Fica em `teams/{nome}/`.

```markdown
teams/meu-time/
├── settings.json      ← fonte única: personas + pipeline do time
├── _memory/
│   ├── memories.json  ← preferências e aprendizados do time
│   └── runs.json      ← histórico de execuções
├── output/            ← outputs gerados (gitignored)
├── personas/          ← personas locais (opcionais)
└── tasks/             ← arquivos de tarefas do pipeline
    ├── tarefa-1.md
    └── tarefa-2.md
```

**Formato do `settings.json`:**

```json
{
  "icon": "🏛️",
  "name": "Conselho Estratégico",
  "description": "Mesa de conselheiros para análise de decisões executivas.",

  "personas": [
    {
      "id": "estrategista",
      "name": "Estrategista",
      "title": "VP de Estratégia",
      "icon": "📈",
      "source": "global",
      "file": "personas/strategist.persona.md"
    },
    {
      "id": "moderador",
      "name": "Moderador",
      "title": "Secretário do Conselho",
      "icon": "✍️",
      "source": "local",
      "file": "teams/conselho/personas/moderador.persona.md"
    }
  ],

  "pipeline": [
    {
      "id": "briefing",
      "name": "Coleta do Desafio",
      "persona": "moderador",
      "task": "tasks/briefing.md",
      "execution": "inline",
      "output": "output/briefing.md"
    },
    {
      "id": "revisao",
      "type": "checkpoint",
      "name": "Revisão do Briefing",
      "message": "Revise o briefing acima. Está correto?"
    },
    {
      "id": "debate",
      "name": "Debate Estratégico",
      "persona": "estrategista",
      "task": "tasks/debate.md",
      "execution": "inline",
      "input": "output/briefing.md",
      "output": "output/debate.md"
    }
  ]
}
```

---

### Tarefas

Cada passo do pipeline aponta para um arquivo de tarefa. As tarefas definem o que o agente faz, como faz e o que produz.

**Formato de uma tarefa (`tasks/*.md`):**

```markdown
---
id: briefing
name: "Coleta do Desafio"
persona: moderador
---

## Objetivo
Coletar e estruturar o desafio ou pergunta trazido pelo usuário.

## Contexto de Input
O usuário descreve a situação ou decisão que precisa ser analisada.

## Processo
1. Leia o input do usuário
2. Identifique o núcleo da decisão: o que está em jogo?
3. Estruture em: Contexto, Pergunta Central, Restrições
4. Apresente para validação antes de avançar

## Formato de Output
Documento Markdown com seções: Contexto, Pergunta Central, Restrições.

## Condições de Veto
- Output sem Pergunta Central definida: reprovar
- Output com mais de 500 palavras: reprovar e resumir
```

---

### Pipeline

O pipeline é o array de passos definido no `settings.json`. Cada passo pode ser:

| Tipo                      | O que faz                                      |
|---------------------------|------------------------------------------------|
| `"execution": "inline"`   | Agente executa e apresenta o resultado no chat |
| `"execution": "subagent"` | Agente trabalha em segundo plano (background)  |
| `"type": "checkpoint"`    | Pausa a execução e espera aprovação do usuário |

O Runner valida automaticamente:

- Se o input do passo existe antes de executar
- Se o output foi gerado após a execução
- Se condições de veto foram violadas (e pede correção)

---

### Memória

Cada time acumula memória entre execuções:

- **`memories.json`**: preferências e padrões extraídos de feedback explícito do usuário (o que aprovou, rejeitou ou pediu diretamente)
- **`runs.json`**: log cronológico de todas as execuções com tema, output e resultado

A memória é carregada no início de cada execução e influencia o comportamento dos agentes.

---

## Comandos

### Menu interativo

```bash
/dreamteam
```

Abre o menu principal com as opções de criar, executar, editar times e gerenciar personas.

---

### Comandos de times

| Comando                    | O que faz                                   |
|----------------------------|---------------------------------------------|
| `/dreamteam create`        | Inicia o assistente para criar um novo time |
| `/dreamteam teams`         | Lista todos os times em `teams/`            |
| `/dreamteam run <nome>`    | Executa o pipeline do time                  |
| `/dreamteam edit <nome>`   | Edita um time existente                     |
| `/dreamteam delete <nome>` | Deleta um time (com confirmação)            |

### Comandos de personas

| Comando                             | O que faz                     |
|-------------------------------------|-------------------------------|
| `/dreamteam personas`               | Abre a biblioteca de personas |
| `/dreamteam personas new`           | Cria uma nova persona global  |
| `/dreamteam personas edit <nome>`   | Edita uma persona existente   |
| `/dreamteam personas delete <nome>` | Remove uma persona            |

### Workspace e configurações

| Comando              | O que faz                                       |
|----------------------|-------------------------------------------------|
| `/dreamteam context` | Ver ou editar o contexto do workspace           |
| `/dreamteam help`    | Exibe o texto de ajuda completo                 |
| `/dreamteam reset`   | Reseta todas as configurações (com confirmação) |

---

## Estrutura de Diretórios

```markdown
dreamteam/
│
├── CLAUDE.md                    ← Instruções do projeto para o Claude Code
│
├── .claude/
│   └── skills/
│       └── dreamteam/
│           └── SKILL.md         ← Skill principal (ponto de entrada do /dreamteam)
│
├── _core/                       ← Núcleo do framework
│   ├── architect.md             ← Instruções do Arquiteto (cria/edita times)
│   └── runner.md                ← Instruções do Pipeline Runner (executa times)
│
├── _workspace/
│   ├── context.example.json ← template de contexto (copie e renomeie)
│   └── context.json         ← contexto do seu negócio/projeto (gitignored)
│
├── personas/                    ← Biblioteca global de personas reutilizáveis
│   └── estrategista.persona.md  ← Exemplo incluído
│
└── teams/                       ← Seus times ficam aqui
    └── meu-time/
        ├── settings.json
        ├── personas/
        ├── tasks/
        ├── output/
        └── _memory/
```

> **Não modifique** os arquivos em `_core/` manualmente, a não ser que saiba o que está fazendo. O `CLAUDE.md` e os arquivos em `_workspace/` e `personas/` podem ser editados livremente.

---

## Exemplo passo a passo

### 1. Criar um time de análise estratégica

```bash
/dreamteam create "Um conselho com moderador e estrategista para analisar decisões de negócio"
```

O Arquiteto vai:

1. Propor a estrutura do time e o pipeline
2. Verificar se há personas compatíveis na biblioteca global
3. Criar ou reutilizar personas
4. Montar todos os arquivos necessários

### 2. Executar o time

```bash
/dreamteam run conselho-estrategico
```

O Runner vai:

1. Carregar todas as personas
2. Executar o passo 1: coletar o desafio
3. Fazer checkpoint: você revisa o briefing
4. Executar o passo 2: análise estratégica
5. Salvar o output em `teams/conselho-estrategico/output/{run_id}/v1/`
6. Atualizar a memória do time

### 3. Adicionar uma nova persona à biblioteca

```bash
/dreamteam personas new
```

O assistente coleta os dados da persona e cria `personas/{id}.persona.md` pronta para ser usada em qualquer time.

---

## Versionamento de Outputs

Cada execução cria uma pasta única com timestamp:

```markdown
teams/meu-time/output/
└── 2026-04-16-143022/
    └── v1/
        ├── briefing.md
        └── analise.md
```

Se o mesmo passo for executado mais de uma vez no mesmo run (por rejeição ou retry), o versionamento avança automaticamente para `v2/`, `v3/`, etc.

---

## Menção Honrosa

O Dreamteam é uma **reimplementação leve e resumida** do [**Opensquad**](https://github.com/renatoasse/opensquad), um framework de orquestração multi-agente completo, com suporte a skills plugáveis, investigação de perfis em redes sociais (Sherlock), engine de publicação e muito mais.

Se o Dreamteam atende ao que você precisa, ótimo. Se você precisar de um sistema mais robusto, com catálogo de skills, integração com Playwright e um ecossistema mais amplo, o Opensquad é o caminho certo.

O Dreamteam existe porque nem sempre você precisa de tudo. Às vezes você só precisa de personas bem definidas, pipelines claros e memória que funciona.

---

## Licença

MIT. Use, modifique e distribua à vontade.

---

## Powered by

jotaodiceu.dev
