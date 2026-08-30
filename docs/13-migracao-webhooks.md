# Migração de Sondagem (Polling) para Webhooks

## Por que esta migração

Em Agosto de 2026, a conta Make (plano Core, 10.000 operações/mês) ficou pausada por excesso de operações. A causa raiz: todas as automações usavam **sondagem** — o Make perguntava ao Notion "mudou alguma coisa?" em intervalos fixos (60 segundos a 15 minutos), gastando operações constantemente mesmo quando nada mudava.

Em Março de 2026 o Notion lançou **webhooks nativos** (Database Automations → "Send webhook"), permitindo o oposto: o Notion avisa o Make apenas quando algo relevante muda. Isto reduz o consumo de operações em mais de 99% nas automações mais frequentes, sem perda de funcionalidade — na prática fica mais rápido, porque reage instantaneamente em vez de esperar pelo próximo ciclo de sondagem.

## Estado geral

| Automação | ID Cenário | Webhook Make | Estado |
|---|---|---|---|
| Envio de Bilhetes | 4139307 | `https://hook.eu1.make.com/4v2u63usj0tbobjvnb1vn3ha62wvu6io` | Make preparado; Notion pendente; payload real por confirmar |
| Fase e Classificação | 4879246 | `https://hook.eu1.make.com/zrigdaf8uvxgzbvbkx3brpjyc84ksmti` | Make preparado; Notion pendente |
| Novo Cliente → Fase Inicial | 4879256 | `https://hook.eu1.make.com/aotqs2stfwmirrmsir76nsc3kgata23i` | Make preparado; Notion pendente |
| Checklists por Tipo de Pedido | 6741903 | `https://hook.eu1.make.com/rd3y2g1xtmppvas5ycb3dgmve7v0fdz3` | Make preparado; Notion pendente |
| Comunicação → Tarefa | 6726654 | `https://hook.eu1.make.com/d2euk7mkjv5yi34eo6s55u1b66etxwwp` | Make preparado; Notion pendente |

**Não migradas (ficam em sondagem, propositadamente):**
- Manutenção Semanal de Clientes (Inactivos + Passaportes) — é uma auditoria em lote a todos os clientes, não um evento por registo; sondagem semanal já é barata
- Alerta de Pedidos Parados — mesma lógica, auditoria diária em lote

## ⚠️ Nota técnica importante — payload por confirmar

Todos os cenários acima foram reconstruídos com o módulo de gatilho trocado para `gateway:CustomWebHook`, mas a lógica a jusante (filtros, referências a campos) ficou marcada com `PLACEHOLDER` onde depende do formato exacto dos dados que o Notion envia no payload do webhook — **isto só se confirma com um teste real**, que não foi possível fazer porque a conta Make estava pausada no momento da preparação.

**Antes de activar qualquer uma destas em produção:** fazer um teste controlado (alterar um registo de teste que corresponda ao gatilho), inspeccionar o payload recebido no Make (a estrutura de dados fica visível no histórico de execuções do cenário), e só então substituir os `PLACEHOLDER` pelas referências de campo correctas.

## Passo a passo — configurar cada automação no Notion

Em cada base de dados: ícone **⚡ (raio)** no canto superior direito → **Novo automatismo** → definir gatilho → acção **Enviar webhook** → colar o URL da tabela acima → seleccionar as propriedades a incluir no payload → Guardar e activar.

### 1. Envio de Bilhetes (Gestão de Pedidos)
- **Gatilho:** propriedade `Enviar Bilhete` muda → fica marcada
- **Propriedades a incluir:** Enviar Bilhete, Bilhetes, Email do Cliente, PNR/Ref, Rota, Companhia Aérea, Data de Ida, Data de Regresso, Número do Bilhete, Nome do Passageiro

### 2. Fase e Classificação ao Concluir Pedido (Gestão de Pedidos)
- **Gatilho:** propriedade `Status do Pedido` muda → é `Concluído`
- **Propriedades a incluir:** Status do Pedido, Cliente
- **Nota:** esta automação continua a precisar de um passo de pesquisa no Make (já preparado) para ler `Nº de Pedidos` e `Total Gasto (AOA)` do cliente relacionado — são rollups do lado do Cliente, não vêm no payload do pedido.

### 3. Novo Cliente → Fase Inicial (Gestão de Clientes)
- **Gatilho:** "Página adicionada" (quando um cliente é criado)
- **Propriedades a incluir:** não é necessário nenhuma — a acção define sempre Fase/Classificação = Novo

### 4. Checklists Automáticas por Tipo de Pedido (Gestão de Pedidos)
- **Gatilho:** "Página adicionada" (quando um pedido é criado)
- **Propriedades a incluir:** Serviço, Tipo de Cliente

### 5. Comunicação → Tarefa de Follow-up (Registo de Comunicações)
- **Gatilho:** propriedade `Próxima Acção` muda → não está vazia
- **Propriedades a incluir:** Próxima Acção, Cliente, Pedido, Responsável, Assunto

## Depois de configurado no Notion

1. Fazer um teste real por automação (alterar/criar um registo de teste que corresponda ao gatilho)
2. No Make, verificar o histórico de execução do cenário — o payload recebido fica visível ali
3. Substituir os `PLACEHOLDER` pelas referências de campo reais (ex: `{{1.data.properties.NomeDoCampo}}`, a confirmar a estrutura exacta)
4. Confirmar que a lógica de negócio (filtros, valores calculados) continua correcta
5. Só depois considerar a automação "em produção"
