# Vistas por Base de Dados

## Gestão de Pedidos

| Vista | Tipo | Filtro principal |
|---|---|---|
| GERAL | table | sem filtro — 21 campos |
| CORPORATIVO | table | Tipo de Cliente = Corporativo |
| VISTOS | board | Serviço contém Visto + item principal vazio, agrupado por Status |
| CHECK-IN | table | viagens hoje/amanhã (ida ou regresso) |
| RELATÓRIO | board | Status = Concluído, agrupado por mês de Data de Emissão |
| 📅 Calendário de Viagens | calendar | por Data de Ida |
| 🔔 FOLLOW-UP | table | Status ≠ Complete (grupo), ordenado por Data do Próximo Passo |
| 🚨 URGENTE | table | Data do Próximo Passo ≤ hoje *(filtro de exclusão de Status ainda pendente — ver limitações da API)* |
| 🏆 PIPELINE | board | item principal vazio, agrupado por Status do Pedido |
| ⏸️ Sem Actividade Recente | table | item principal vazio + Próximo Passo vazio *(filtro de exclusão de Status pendente)* |
| 🎫 Bilhetes Emitidos | table | item principal vazio + Número do Bilhete preenchido |
| 💰 Receita por Agente | table | item principal vazio, agrupado por Responsável |

## Gestão de Clientes

| Vista | Tipo | Filtro |
|---|---|---|
| Geral | table | sem filtro |
| Favoritos | table | Favoritar = true |
| ⚠️ Passaportes a Expirar | table | Validade do Passaporte ≤ 60 dias |
| 📊 Vista CRM | table | todos, ordenado por Total Gasto desc |
| 😴 Clientes Inactivos | table | Fase do Cliente = Inactivo |
| ⭐ Clientes VIP | table | Classificação = VIP |

## Tarefas da Agência

Todas as tarefas, Por status, 👤 Minhas Tarefas, 🔴 Por Prioridade, 👤 Por Agente, 📅 Para Hoje.

## Limitação conhecida

Filtros sobre campos do tipo **status** (ex: excluir "Concluído"/"Cancelado") não são aplicáveis via API do Notion — apenas manualmente na interface. Ver [11-limitacoes-api-notion.md](11-limitacoes-api-notion.md).
