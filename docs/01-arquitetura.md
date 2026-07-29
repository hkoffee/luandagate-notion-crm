# Arquitetura do Workspace

## Contexto

Agência de viagens Luandagate (Angola ↔ Europa). Workspace Notion no plano Plus, integrado com Make para automação de envio de bilhetes e gestão de CRM. Utilizadores: supervisor, Direção (chefe), 3 agentes.

## Bases de dados

| Base | ID | Descrição |
|---|---|---|
| Gestão de Pedidos | `<id-base-pedidos>` | Núcleo operacional — todos os pedidos de clientes |
| Gestão de Clientes | `<id-base-clientes>` | Perfis de cliente + CRM |
| Tarefas da Agência | `<id-base-tarefas>` | Tarefas atribuídas a agentes |
| Registo de Comunicações | `<id-base-comunicacoes>` | Log de contactos com clientes |
| Fornecedores & Parceiros | — | Companhias aéreas e parceiros |
| Histórico de Tarifas | — | Preços de referência |

## Páginas principais

- 🏠 Luandagate — Hub Central
- 👔 Painel do Supervisor
- 👤 Painel do Agente
- 📖 Wiki & Processos (contém toda a documentação operacional)
- ⚡ Automação Make — Envio de Bilhetes

## Relações entre bases

```
Gestão de Clientes ──┬── Gestão de Pedidos (relação Cliente ↔ Pedidos)
                      └── Registo de Comunicações (relação Cliente ↔ Comunicações)

Gestão de Pedidos ────┬── Registo de Comunicações (relação Pedido ↔ Comunicações)
                       ├── Gestão de Pedidos (self-relation: item principal / Subitem)
                       └── Tarefas da Agência (relação Pedido Relacionado)
```

## Integrações

- **Make** — 4 cenários (ver [07-automacoes-make.md](07-automacoes-make.md))
- **Claude (via MCP)** — usado para configurar e manter o workspace
