# Dreamteam — Instruções do Projeto

Este projeto usa o **Dreamteam**, um framework de orquestração de agentes de IA construído para o Claude Code.

## Início Rápido

Digite `/dreamteam` para abrir o menu principal, ou use qualquer um desses comandos:
- `/dreamteam create` — Criar um novo time
- `/dreamteam run <nome>` — Executar um time
- `/dreamteam personas` — Gerenciar a biblioteca de personas
- `/dreamteam teams` — Ver todos os times
- `/dreamteam help` — Ver todos os comandos

## Estrutura de Diretórios

```
_core/          — Núcleo do framework (não editar manualmente)
_workspace/     — Contexto persistente do seu negócio/projeto
personas/       — Biblioteca global de personas reutilizáveis
teams/          — Times criados pelo usuário
teams/{nome}/
  team.yaml         — Definição do time e pipeline
  personas/         — Personas locais (exclusivas deste time)
  tasks/            — Arquivos de tarefas do pipeline
  output/           — Saídas geradas
  _memory/          — Memória persistente do time
```

## Como Funciona

1. O skill `/dreamteam` é o ponto de entrada para todas as interações
2. O **Arquiteto** cria e modifica times
3. Personas vivem em `personas/` (global) ou `teams/{nome}/personas/` (local)
4. O **Pipeline Runner** executa os times automaticamente
5. Checkpoints pausam a execução para input do usuário

## Personas

A grande diferença do Dreamteam: personas são **reutilizáveis entre times**. Crie uma persona uma vez em `personas/` e use ela em qualquer time. Times também podem ter personas locais exclusivas.

## Regras

- Use sempre os comandos `/dreamteam` para interagir com o sistema
- Não edite arquivos em `_core/` manualmente
- Arquivos `team.yaml` podem ser editados manualmente se necessário
- O contexto em `_workspace/context.md` é carregado em todas as execuções
