# Changelog

Todas as alterações relevantes feitas ao workspace Notion/Make são registadas aqui, por ordem cronológica inversa.

## 2026-07-28 (testes)

- 4 cenários activados manualmente no Make pelo utilizador
- Testados os 4 com `scenarios_run`: 3 confirmados a funcionar (Alerta de Passaportes, Alerta de Pedidos Parados, Checklist de Visto); 1 bloqueado (Comunicação → Tarefa) por falta de partilha da base Registo de Comunicações com a integração Make
- Corrigidos 2 bugs encontrados durante o teste: formato de filtro inválido em `notion:searchObjects1` (movido para filtro pós-pesquisa) e formato de blocos incompleto em `notion:appendADatabaseItemContent`
- Confirmado achado operacional real: 6 pedidos estão parados sem Próximo Passo há mais de 3 dias — vale a pena rever
- Documentadas 4 novas limitações da API (10, 11, 12 em `11-limitacoes-api-notion.md`, mais partilha de bases)

## 2026-07-28 (continuação)

- Criadas 4 novas automações Make: Comunicação → Tarefa de Follow-up (6726654), Alerta Semanal de Passaportes a Expirar (6737356), Alerta de Pedidos Parados (6737359), Checklist Automática de Visto (6737366)
- Descoberta e documentada limitação: `scenarios_activate` falha via API — activação requer acção manual na interface do Make
- Descoberto e documentado o módulo correcto de envio de email: `google-email:sendAnEmail` v4 (não `ActionSendEmail` v1)

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
