# Automações — Make

Conexão Notion no Make: `4476229` ("Luandagate - Edmilson Fabio - LUANDAGATE's Space")
Team ID: `799265`

## Cenários

| # | Nome | ID | Trigger | Estado |
|---|---|---|---|---|
| 1 | Integration Notion (Envio de Bilhetes) | 4139307 | checkbox `Enviar Bilhete` | activo |
| 2 | CRM — Fase e Classificação ao Concluir Pedido | 4879246 | a cada 15 min | activo |
| 3 | CRM — Marcar Clientes Inativos (Semanal) | 4879253 | domingo 08:00 | activo |
| 4 | CRM — Novo Cliente → Fase Inicial | 4879256 | a cada 15 min | activo |
| 5 | CRM — Comunicação → Tarefa de Follow-up | 6726654 | a cada 15 min | criado, **activação manual pendente** |
| 6 | CRM — Alerta Semanal de Passaportes a Expirar | 6737356 | segunda-feira 08:00 | criado, **activação manual pendente** |
| 7 | CRM — Alerta de Pedidos Parados (sem Próximo Passo) | 6737359 | diariamente 08:30 | criado, **activação manual pendente** |
| 8 | CRM — Checklist Automática de Visto | 6737366 | a cada 15 min | criado, **activação manual pendente** |

### Conexões usadas

| Conexão | ID | Uso |
|---|---|---|
| Luandagate (Notion) | 4476229 | Todos os cenários Notion |
| Geral (Gmail — geral@luandagate.com) | 5488679 | Envio de bilhetes e alertas por email (`google-email:sendAnEmail` v4) |
| My Gmail connection (edmilson.fabio@luandagate.com) | 4610052 | Não usada actualmente |

**Nota:** o módulo correcto para envio de email é `google-email:sendAnEmail` versão 4 — o módulo `google-email:ActionSendEmail` versão 1 existe mas não é compatível com as conexões OAuth desta conta.

## Cenário 5 — Comunicação → Tarefa de Follow-up

Quando uma comunicação é criada no Registo de Comunicações com `Próxima Ação` preenchida, cria automaticamente uma tarefa em Tarefas da Agência (Nome = "Follow-up: {Próxima Ação}", Cliente, Pedido Relacionado, Agente e Atribuído Por = Responsável da comunicação, Prazo = dia seguinte, Prioridade = Média, Status = Por Fazer).

## Cenário 6 — Alerta Semanal de Passaportes a Expirar

Toda segunda-feira às 08:00, procura clientes com `Validade do Passaporte` preenchida e ≤ 60 dias no futuro, e envia um email (um por cliente) para edmilson.fabio@luandagate.com com nome, data de expiração, telefone e email do cliente.

## Cenário 7 — Alerta de Pedidos Parados

Diariamente às 08:30, procura pedidos mãe (`item principal` vazio) sem `Próximo Passo` definido e com `Data de Solicitação` há mais de 3 dias, e envia um email de alerta por pedido.

## Cenário 8 — Checklist Automática de Visto

Ao criar um pedido com `Serviço` contendo `Visto`, adiciona automaticamente ao corpo da página um cabeçalho "✅ Checklist do Visto" e 8 itens to-do: Passaporte, Fotografia, Seguro, Formulário, Carta, Reserva, Pagamento, Entrega. Substitui a necessidade de duplicar manualmente a página-modelo (ver [10-modelos.md](10-modelos.md), que continua válido como alternativa/backup).

## Cenário 1 — Envio de Bilhetes

Gatilho: campo `Enviar Bilhete` (checkbox) marcado.
Campos usados: `Bilhetes` (anexo), `Email do Cliente` (rollup, destinatário), `Titulo do Pedido` (assunto), `Nome do Passageiro` (corpo).

**Ordem obrigatória:** anexar bilhete → confirmar email preenchido → só depois marcar `Enviar Bilhete`.

Riscos: email vazio → falha silenciosa; bilhetes vazios → email sem anexo; marcar duas vezes → envio duplicado.

## Cenário 2 — Fase e Classificação ao Concluir Pedido

Quando `Status do Pedido = Concluído`, actualiza `Fase do Cliente` e `Classificação` do cliente associado com base em `Nº de Pedidos` e `Total Gasto (AOA)`.

## Cenário 3 — Marcar Clientes Inativos

Semanalmente, marca `Fase do Cliente = Inactivo` para clientes cujo `Último Pedido` foi há mais de 6 meses (excepto `Fase = Perdido`).

## Cenário 4 — Novo Cliente → Fase Inicial

Ao criar um cliente sem `Fase do Cliente` preenchida, define `Fase = Novo` e `Classificação = Novo`.

## Próximas automações propostas (não implementadas)

- Registo de comunicação com "Próxima Ação" → cria tarefa automática em Tarefas da Agência
- Alerta semanal de passaportes a expirar (email/WhatsApp ao supervisor)
- Alerta de pedidos sem `Data do Próximo Passo` há mais de 3 dias
- Checklist automática de Visto ao seleccionar `Serviço = Visto` (hoje resolvido via página-modelo, não via Make)
