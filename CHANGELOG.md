# Changelog

Todas as alterações relevantes feitas ao workspace Notion/Make são registadas aqui, por ordem cronológica inversa.

## 2026-08-30 (início da migração para webhooks)

- Conta Make (plano Core, 10.000 op/mês) ficou pausada por excesso de operações — causa raiz: todas as automações usavam sondagem (polling) a cada 60s–15min
- Descoberto que o Notion lançou webhooks nativos em Março de 2026 (Database Automations → "Send webhook"), permitindo trigger por evento em vez de sondagem constante
- Criados 5 webhooks receptores no Make (IDs 3576431, 3576433, 3576434, 3576436, 3576437)
- Reconstruídos os 5 cenários mais frequentes para usar gatilho `gateway:CustomWebHook` em vez de `watchDatabaseItems` — lógica a jusante marcada com `PLACEHOLDER` onde depende do payload exacto, por confirmar em teste real (conta ainda pausada no momento desta preparação)
- Automações de auditoria em lote (Manutenção Semanal, Pedidos Parados) mantidas em sondagem deliberadamente — frequência já baixa (diária/semanal), não há ganho relevante em migrar
- Documentados os passos exactos de configuração do lado do Notion para as 5 automações em `13-migracao-webhooks.md`
- **Pendente:** configuração do lado do Notion (utilizador), teste real de cada webhook, e finalização das referências de campo após confirmar o payload

## 2026-08-16 (bug crítico #2 — Envio de Bilhetes corrigido)

- Auditoria completa a todos os cenários Make (despoletada por um alerta sobre outro cenário) revelou que **"Integration Notion" (Envio de Bilhetes) — a automação mais crítica do negócio — estava inválida e inactiva**
- Encontrados 2 problemas: (1) o mesmo bug `"select": "list"` no passo que desmarca a checkbox `Enviar Bilhete` após o envio — nunca funcionou, criando risco de reenvio duplicado a clientes reais se o pedido fosse editado de novo; (2) o passo de download do anexo só suportava ficheiros carregados directamente no Notion (`file.url`), não links externos (`external.url`)
- Ambos corrigidos: reset da checkbox reescrito em modo `map`; download do anexo agora suporta os dois tipos de ficheiro (`ifempty(...)`)
- Testado de ponta a ponta com um pedido de teste real ligado ao cliente Supervisor (para o email de teste não ir para um cliente real) — confirmado: download do anexo, envio do email (recebido e confirmado pelo utilizador), e reset automático da checkbox, todos a funcionar
- Descoberta durante o teste: a API do Notion não aceita upload interno de ficheiros em propriedades `files` de bases de dados — só URLs externos. Documentada como limitação nº 22
- Ficheiro de teste temporário alojado no próprio repositório GitHub (via `raw.githubusercontent.com`) para servir de anexo externo no teste, depois removido
- **Com esta correcção, todas as automações de escrita conhecidas (5 de 5) estão auditadas, corrigidas e confirmadas a funcionar**

## 2026-08-03 (bug crítico de escrita — corrigido)

- **Descoberta:** o modo `"select": "list"` usado nos módulos de escrita do Notion (`updateADatabaseItem`, `createDataSourceItem`) nunca funcionou via blueprint/API — falha silenciosa, sem erro. Afectava 3 automações activas há meses:
  - CRM — Fase e Classificação ao Concluir Pedido (partida desde a criação original, Março 2026)
  - CRM — Manutenção Semanal de Clientes / Inactivos (partida desde a criação original, Março 2026)
  - CRM — Comunicação → Tarefa de Follow-up (partida desde a criação, Julho 2026)
- **Corrigidas as 3**, usando o modo correcto `"select": "map"` com `fields` como array `{key, type, value}`. Todas testadas e confirmadas a escrever dados reais via verificação directa em Notion.
- Corrigidos bugs secundários descobertos no processo: confusão entre ID de base de dados e ID de fonte de dados; formato de extracção de IDs em campos de relação; tipo correcto (`rich_text` vs `text`) no modo `map`
- Documentadas 4 novas limitações críticas da API (18–21) em `11-limitacoes-api-notion.md`
- Bases Registo de Comunicações e Tarefas da Agência partilhadas com a integração Make (acção manual do utilizador)
- Limpeza de ~10 registos de teste criados durante o diagnóstico

## 2026-07-29 (fechar lacunas — formação e regras manuais)

- Criado guia de onboarding "👋 Guia Rápido — Como Usar o Sistema" no Wiki, linkado nos Painéis do Supervisor e do Agente
- Documentada explicitamente nos Procedimentos CRM a regra manual do VIP (checklist não é automática, precisa de duplicação do modelo)
- Análise de sistema entregue: pontos fortes, lacunas e recomendação de não aumentar complexidade — focar em (1) as 2 decisões pendentes do utilizador (workflow, permissões), (2) partilhar base Comunicações com Make, (3) adopção pela equipa em vez de mais automação

## 2026-07-29 (consolidação de cenários)

- Consolidados 9 cenários activos em 6, fundindo pares que partilhavam gatilho/cadência:
  - **Checklist de Visto** + **Checklist Bilhete/Hotel/Empresa** → 1 cenário só, router com 4 ramos (ID 6741903)
  - **Clientes Inactivos** + **Alerta de Passaportes** → 1 cenário só, router com 2 ramos (ID 4879253)
- **Bug histórico corrigido**: o cenário de Clientes Inactivos tinha o campo de actualização vazio desde Março de 2026 — nunca marcou nenhum cliente como Inactivo apesar de "activo" há meses. Corrigido na fusão.
- Deliberadamente **não consolidado**: "Integration Notion" (Envio de Bilhetes) — automação crítica com campos internos ofuscados, risco desnecessário de mexer
- 4 cenários antigos desactivados (não eliminados, por segurança): Alerta Passaportes standalone, Checklist Visto standalone, Checklist VIP, Comunicação→Tarefa

## 2026-07-29 (checklists por tipo de pedido)

- Criado cenário 9 "CRM — Checklist Automática por Tipo de Pedido" com router (3 ramos): Bilhete, Hotel, Empresa — **testado com sucesso nos 3 ramos**
- Adicionados campos `Classificação do Cliente` (rollup) e `Checklist VIP Aplicada` (checkbox) à Gestão de Pedidos
- Tentativa de automatizar a checklist VIP falhou após 4 abordagens diferentes — cenário 10 criado mas **desactivado**; limitação documentada em `11-limitacoes-api-notion.md` (itens 16, 17)
- Checklist VIP continua disponível apenas via página-modelo manual (🧩 MODELO — VIP)
- Limpeza de 7 registos de teste (Bilhete, Hotel, Empresa, VIP ×4)

## 2026-07-29 (correcção de bug crítico)

- **Bug encontrado em produção:** a automação "Alerta de Pedidos Parados" enviou 8 emails em vez de 1, devido a uma estrutura de filtro incorrecta (condições combinadas em OR em vez de AND). Corrigido.
- **Bug relacionado:** título/nome dos registos apareciam como JSON bruto nos emails (campos `title` referenciados sem extrair `plain_text`). Corrigido nas automações de Pedidos Parados, Passaportes e Comunicação → Tarefa.
- Verificado por SQL directo: existe apenas **1 pedido genuinamente parado** (Ref 45, "Pedido de print"), não 6 como reportado inicialmente — os outros 5 "matches" eram falsos positivos vindos das páginas-modelo (🧩 MODELO) e do registo de teste, com `Data de Solicitação` vazia
- Re-testadas as automações de Pedidos Parados e Passaportes após correcção — ambas confirmadas a devolver apenas resultados reais
- Documentadas 2 novas limitações/lições (13, 14 em `11-limitacoes-api-notion.md`): agrupamento AND/OR em filtros Make, e extracção de texto de campos `title`

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

## 2026-08-03 (workflow aplicado)

- Utilizador aplicou manualmente a reestruturação do `Status do Pedido` no Notion: Novo → Em Análise → Cotação → Aguardando Cliente → Confirmado → Em Processamento → Concluído → Arquivado
- Confirmado por consulta directa: todos os 22 pedidos existentes têm status válido, sem migração manual necessária (Notion preservou valores ao renomear opções)
- Criada vista 📊 Pedidos Activos (indicador de dashboard)
- Reconfirmado: filtros de exclusão de Status em vistas continuam impossíveis via API (testado com "is not X" e "in (...)", ambos falham silenciosamente) — pendência manual documentada para as vistas Sem Actividade Recente e Pedidos Activos

## 2026-08-06 (4º bug de escrita encontrado e corrigido)

- Alerta do Make (3 avisos ECONNRESET em "CRM — Novo Cliente → Fase Inicial") levou a inspeccionar o cenário — o ECONNRESET foi confirmado como falha temporária de rede da API do Notion (não relacionado com a lógica da automação; Make já reagia com retry automático via backoff)
- Ao inspeccionar, descoberto que este cenário tinha o **mesmo bug crítico** documentado em 03/08 (`"select": "list"` com `fields` vazio) — nunca escreveu `Fase do Cliente`/`Classificação` desde a criação em Março 2026
- Corrigido com `"select": "map"` e `fields` como array; testado com cliente de teste real, confirmado `Fase do Cliente = Novo` e `Classificação = Novo` a escrever correctamente
- Confirma-se: **todas as automações de escrita conhecidas foram agora auditadas e corrigidas** (4 de 4)
