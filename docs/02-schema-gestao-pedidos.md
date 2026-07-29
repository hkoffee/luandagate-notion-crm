# Schema — Gestão de Pedidos

ID da base: `<id-base-pedidos>`
Data source: `<id-fonte-dados-pedidos>`

## Campos

| Campo | Tipo | Notas |
|---|---|---|
| Titulo do Pedido | title | |
| Ref | auto_increment_id | formato LG-XXXX |
| Cliente | relation → Gestão de Clientes | |
| item principal | relation (self) | usado para estrutura mãe/subitem (pedidos corporativos em grupo) |
| Subitem | relation (self) | inverso de item principal |
| Serviço | multi_select | Seguro, Pacote, Viatura, Hotel, Visto, Passagem |
| Companhia Aérea | select | TAAG, Turkish Airlines, TAP, Qatar Airways, Lufthansa, Air France, Ethiopian Airlines, Emirates, Air Moroc |
| PNR/Ref | text | PNR do bilhete ou referência do visto |
| Número do Bilhete | number | |
| N° do Passaporte | text | operacional (existe também em Clientes, permanente) |
| Requerente | text | |
| Tipo de Cliente | select | Particular / Corporativo |
| Status do Pedido | status | Novo, Em Cotação, Aguardando Aprovação, Aguardando Documentos, Em Processamento, Concluído, Cancelado — **ver [08-workflow.md](08-workflow.md) para proposta de reestruturação, pendente** |
| Status de Pagamento | status | Não Pago, Parcial, Pago, Contrato Empresarial, Concluído |
| Data de Solicitação | date | |
| Data de Ida / Data de Regresso | date | |
| Data de Emissão | date | |
| Próximo Passo | select | Ligar cliente, Aguardar resposta, Enviar documento, Emitir, Enviar cotação, Outro |
| Data do Próximo Passo | date | |
| Responsável | person | agente responsável (limit 1) |
| Preço de Venda (AOA) | number | |
| Valor Pago (AOA) | number | |
| Bilhetes | file | |
| Faturas | file | |
| Enviar Bilhete | checkbox | gatilho do cenário Make de envio de bilhete |
| Check-in Feito ? | checkbox | |
| WhatsApp | formula | |
| Tempo de Processamento (dias) | formula | `dateBetween(Data de Emissão, Data de Solicitação, "days")` — indicador de dashboard |
| Comunicações | relation → Registo de Comunicações | |

### Rollups (a partir de Cliente)

| Campo | Origem |
|---|---|
| Email do Cliente | Clientes.Email |
| Telefone do Cliente | Clientes.Telefone |
| Nome do Passageiro | Clientes.título |
| Nº Cartão de Milhas | Clientes.Nº Cartão de Milhas |
| Validade do Passaporte | Clientes.Validade do Passaporte (earliest_date) |

## Nota sobre subitens (pedidos corporativos)

A visibilidade de subitens numa vista tabela depende do toggle nativo **"Sub-items → Always expand"**, que **não é controlável via API** — só na interface do Notion, por vista.
