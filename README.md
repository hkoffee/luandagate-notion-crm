# Luandagate — Notion CRM

Documentação viva do workspace Notion da **Luandagate**, agência de viagens (Angola ↔ Europa). Este repositório acompanha a evolução do workspace: schema das bases de dados, vistas, automações Make, workflow operacional e estrutura de permissões.

> Este repositório documenta um sistema construído no Notion + Make. Não contém código executável do próprio CRM (o CRM vive no Notion); contém a documentação de arquitetura, decisões e configuração.

## Índice

- [`docs/01-arquitetura.md`](docs/01-arquitetura.md) — visão geral do workspace, bases de dados e páginas principais
- [`docs/02-schema-gestao-pedidos.md`](docs/02-schema-gestao-pedidos.md) — schema completo da base Gestão de Pedidos
- [`docs/03-schema-gestao-clientes.md`](docs/03-schema-gestao-clientes.md) — schema completo da base Gestão de Clientes
- [`docs/04-outras-bases.md`](docs/04-outras-bases.md) — Tarefas da Agência, Registo de Comunicações, Fornecedores
- [`docs/05-vistas.md`](docs/05-vistas.md) — todas as vistas configuradas por base de dados
- [`docs/06-crm.md`](docs/06-crm.md) — funcionalidades e procedimentos do CRM
- [`docs/07-automacoes-make.md`](docs/07-automacoes-make.md) — cenários Make, activos e pendentes
- [`docs/08-workflow.md`](docs/08-workflow.md) — workflow actual e proposta de reestruturação (pendente)
- [`docs/09-permissoes.md`](docs/09-permissoes.md) — proposta de estrutura de permissões
- [`docs/10-modelos.md`](docs/10-modelos.md) — modelos/templates de pedido
- [`docs/11-limitacoes-api-notion.md`](docs/11-limitacoes-api-notion.md) — limitações conhecidas da API do Notion, relevantes para manutenção futura
- [`docs/12-guia-onboarding.md`](docs/12-guia-onboarding.md) — guia rápido de uso do sistema para a equipa
- [`CHANGELOG.md`](CHANGELOG.md) — histórico de alterações ao workspace

## Stack

- **Notion** (plano Plus) — base de dados operacional
- **Make** — automações (envio de bilhetes, CRM)
- **Claude** — assistente usado para configurar e manter o workspace via MCP (Notion + Make)

## Como este repositório é mantido

Cada alteração feita no workspace Notion (nova base, campo, vista, automação) é também reflectida aqui, para manter um histórico auditável fora do Notion.

---
*Luandagate · Angola*
