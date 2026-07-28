# Workflow — Status do Pedido

## Estado actual

```
Novo → Em Cotação → Aguardando Aprovação → Aguardando Documentos → Em Processamento → Concluído / Cancelado
```

Campo `Status do Pedido` é do tipo **status** (não select), com grupos nativos `to_do / in_progress / complete`.

## Workflow proposto (decisão tomada, aplicação PENDENTE)

```
Novo → Em Análise → Cotação → Aguardando Cliente → Confirmado → Em Processamento → Concluído → Arquivado
```

**Mapeamento acordado para migração:**
- `Em Cotação` → `Cotação`
- `Aguardando Aprovação` + `Aguardando Documentos` → `Aguardando Cliente` (a confirmar fusão exacta)
- `Arquivado` = novo estado final, depois de `Concluído`
- `Cancelado` mantém-se como estado terminal separado

## Por que ainda não foi aplicado

A API pública do Notion **não permite criar, renomear ou remover opções de um campo do tipo `status`**. Testado e confirmado — o comando é aceite sem erro mas as opções não mudam.

### Duas opções, decisão tomada: Opção A

**Opção A (escolhida):** editar manualmente as opções do campo na interface do Notion. Mantém o tipo `status` nativo (barra de progresso, agrupamento automático).

**Opção B (não escolhida):** converter o campo para tipo `select`. Ficaria 100% editável via API para sempre, mas perde a UI nativa de progresso do tipo status.

## Passo a passo para aplicar a Opção A (manual, no Notion)

1. Gestão de Pedidos → campo `Status do Pedido` → Editar propriedade
2. Renomear `Em Cotação` → `Cotação`
3. Renomear `Aguardando Aprovação` → `Aguardando Cliente`
4. Decidir o destino de `Aguardando Documentos` (fundir ou renomear para `Confirmado`)
5. Adicionar `Em Análise` (entre Novo e Cotação)
6. Adicionar `Arquivado` (grupo "Complete", depois de Concluído)
7. Reordenar visualmente

## Depois da alteração manual (via API/Claude)

- Migrar pedidos existentes para os novos estados
- Adicionar filtro de exclusão de Status às vistas FOLLOW-UP, URGENTE, Sem Actividade Recente
- Criar o indicador "Pedidos Activos" no dashboard
