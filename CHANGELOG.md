# Changelog

Todas as alterações relevantes feitas ao workspace Notion/Make são registadas aqui, por ordem cronológica inversa.

## 2026-07-28

- Criado o repositório de documentação `luandagate-notion-crm`
- Documentado: arquitectura, schemas (Pedidos, Clientes, outras bases), vistas, CRM, automações Make, workflow (proposta pendente), permissões (proposta pendente), modelos de pedido, limitações da API do Notion
- Confirmadas 4 automações Make activas: Envio de Bilhetes, Fase/Classificação, Clientes Inactivos, Novo Cliente
- Criados 5 modelos de pedido (Visto, Bilhete, Hotel, Empresa, VIP) com checklists
- Criadas vistas de dashboard: 🎫 Bilhetes Emitidos, 💰 Receita por Agente, ⏸️ Sem Actividade Recente
- Criado campo formula `Tempo de Processamento (dias)` na Gestão de Pedidos
- Documentada proposta de reestruturação do workflow (Novo → Em Análise → Cotação → Aguardando Cliente → Confirmado → Em Processamento → Concluído → Arquivado) — **aplicação manual pendente**
- Documentada proposta de estrutura de permissões — **aplicação manual pendente**

## Anterior (histórico consolidado, sessões de Março 2026)

- Estrutura inicial do workspace: Gestão de Pedidos, Gestão de Clientes, Tarefas da Agência, Fornecedores
- Resolvido problema de subitens não visíveis (toggle nativo "Sub-items")
- Implementado CRM: campos de segmentação, Registo de Comunicações, Pipeline Kanban, vistas VIP/Inactivos
- Documentação inicial em Wiki (Procedimentos, Automação Make — Envio de Bilhetes)
