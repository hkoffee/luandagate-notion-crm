# Workflow — Status do Pedido

## ✅ Estado actual (aplicado em 03/08/2026)

```
Novo → Em Análise → Cotação → Aguardando Cliente → Confirmado → Em Processamento → Concluído → Arquivado
```

Aplicado manualmente pelo utilizador na interface do Notion (a API não permite alterar opções de um campo `status`). Confirmado por consulta directa aos dados: todos os pedidos existentes (22) têm um status válido do novo fluxo — o Notion preservou os valores automaticamente ao renomear as opções em vez de apagar e recriar, evitando a necessidade de migração manual.

Grupos:
- **To-do:** Novo
- **In progress:** Em Análise, Cotação, Aguardando Cliente, Confirmado, Em Processamento
- **Complete:** Concluído, Arquivado

## ⚠️ Pendência residual — filtros de exclusão de Status

Continua confirmado que a API do Notion **não permite filtrar vistas por valores ou grupos de um campo `status`** (nem por "is not X", nem por "in (...)" — ambos falham silenciosamente, sem erro, devolvendo grupo de filtro vazio). Isto afecta:

- **⏸️ Sem Actividade Recente** — falta excluir Concluído/Arquivado
- **📊 Pedidos Activos** (vista nova, criada em 03/08/2026) — precisa do mesmo filtro para mostrar só pedidos activos

**Acção manual necessária (1 minuto por vista):**
1. Abrir a vista → Filtro → adicionar `Status do Pedido` **não é** `Concluído` **e não é** `Arquivado`
2. Repetir na outra vista

A vista 🔔 FOLLOW-UP já tem este filtro aplicado (feito manualmente antes desta sessão).
