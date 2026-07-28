# Limitações Conhecidas da API do Notion

Registo de limitações confirmadas por tentativa directa, relevantes para quem for manter este workspace via API/MCP no futuro.

## 1. Campos do tipo `status`

Não é possível adicionar, renomear ou remover opções de um campo `status` via API. O comando `ALTER COLUMN ... SET STATUS(...)` é rejeitado; `SET STATUS` sem opções é aceite mas não altera nada.

**Impacto:** qualquer reestruturação do fluxo de `Status do Pedido` exige edição manual na interface.

## 2. Filtros sobre campos `status` em vistas

Filtros do tipo "is not X" sobre campos status via DSL só aceitam `is empty` / `is not empty`. Não é possível filtrar por valor específico ou por grupo (`complete`/`in_progress`) de forma fiável via API nesta integração.

**Impacto:** vistas como FOLLOW-UP, URGENTE e Sem Actividade Recente não conseguem excluir "Concluído"/"Cancelado" via API — requer configuração manual do filtro na interface.

## 3. Toggle "Sub-items" em vistas tabela

A visibilidade de subitens expandidos numa vista tabela é controlada por um toggle nativo (`Sub-items → Always expand`) que não está exposto na API — não aparece no schema da vista nem é alterável via DSL.

## 4. Vistas do tipo `chart`

A API aceita o comando de criação de uma vista `chart` e devolve sucesso, mas a vista não persiste na base de dados (confirmado por reprodução dupla). Usar antes uma vista `board`/`table` agrupada com soma manual activada no rodapé.

## 5. Templates nativos de base de dados

Não é possível criar Templates de base de dados via API (os que aparecem em "+ Novo → escolher modelo"). Só são criáveis manualmente na interface. Workaround: páginas-modelo normais, feitas para duplicar.

## 6. Permissões e partilha

A API não expõe nem permite gerir permissões ao nível de página ou teamspace. `notion-get-users` e `notion-get-teams` mostram membros e teamspaces, mas não quem tem acesso a que página especificamente.

## 7. Criação de cenários Make via API

Cenários Make que usam apps de terceiros (ex: Notion) por vezes exigem `confirmed: true` e nomes de módulo exactos (ex: `notion:getADatabaseItem`, não `notion:getDatabaseItem`) — a lista de módulos disponíveis deve ser verificada via `app-modules_list` antes de montar o blueprint.

## 8. Activação de cenários Make via API

`scenarios_activate` falha de forma consistente com "No approval received", mesmo após criação bem-sucedida do cenário com `confirmed: true`. A activação de um cenário novo precisa de ser feita manualmente na interface do Make (toggle ON no canto superior direito do cenário) — não há confirmação equivalente disponível para esta chamada.

## 9. Módulo de envio de email (Gmail)

O módulo `google-email:ActionSendEmail` (v1) existe mas não é compatível com conexões OAuth Gmail padrão nesta conta — falha com "Provided account is not compatible". O módulo correcto e funcional é `google-email:sendAnEmail` (v4), confirmado por uso no cenário de Envio de Bilhetes já existente.
