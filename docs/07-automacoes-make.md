# Automações — Make

Conexão Notion no Make: `4476229` ("Luandagate - Edmilson Fabio - LUANDAGATE's Space")
Team ID: `799265`

## Cenários

| # | Nome | ID | Trigger | Estado |
|---|---|---|---|---|
| 1 | Integration Notion (Envio de Bilhetes) | 4139307 | checkbox `Enviar Bilhete` | activo (confirmado pelo utilizador) |
| 2 | CRM — Fase e Classificação ao Concluir Pedido | 4879246 | a cada 15 min | activo |
| 3 | CRM — Marcar Clientes Inativos (Semanal) | 4879253 | domingo 08:00 | activo |
| 4 | CRM — Novo Cliente → Fase Inicial | 4879256 | a cada 15 min | activo |

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
