# Outras Bases de Dados

## Tarefas da Agência

ID: `<id-base-tarefas>`

| Campo | Tipo |
|---|---|
| Nome da tarefa | title |
| Agente (executa) | person |
| Atribuído Por | person |
| Cliente | relation → Clientes |
| Pedido Relacionado | relation → Pedidos |
| Prazo | date (DD/MM/YYYY) |
| Prioridade | select |
| Status | select |
| Tipo de Pedido | select |
| Solicitante | text |
| Ref do Pedido | rollup ← Pedidos.Ref |
| Telefone do Cliente | rollup ← Clientes.Telefone |

## Registo de Comunicações

ID: `<id-base-comunicacoes>`

| Campo | Tipo |
|---|---|
| Assunto | title |
| Cliente | relation ↔ Gestão de Clientes |
| Pedido | relation ↔ Gestão de Pedidos |
| Data | date |
| Canal | select — WhatsApp, Telefone, Email, Presencial, Instagram, Outro |
| Tipo | select — Contacto Inicial, Follow-up, Cotação Enviada, Confirmação, Reclamação, Outro |
| Resumo | rich_text |
| Próxima Acção | rich_text |
| Responsável | people |

## Fornecedores & Parceiros

Companhias aéreas parceiras: TAAG, Turkish Airlines, TAP, Qatar Airways, Lufthansa, Air France, Ethiopian Airlines, Emirates, Air Moroc.

## Histórico de Tarifas

Preços de referência por rota/companhia (uso interno de cotação).
