# Schema — Gestão de Clientes

ID da base: `<id-base-clientes>`
Data source: `<id-fonte-dados-clientes>`

## Campos

| Campo | Tipo | Notas |
|---|---|---|
| Nome do Cliente | title | |
| Telefone | phone_number | |
| Email | email | |
| Empresa | text | para clientes corporativos |
| Tipo de Cliente | select | Particular / Corporativo |
| N° do Passaporte | text | permanente (existe também em Pedidos, operacional) |
| Validade do Passaporte | date | |
| Nº Cartão de Milhas | text | |
| Observações | text | |
| Documentos | file | |
| Favoritar | checkbox | |
| Pedidos | relation → Gestão de Pedidos | |
| Comunicações | relation → Registo de Comunicações | |

### Campos CRM

| Campo | Tipo | Opções |
|---|---|---|
| Canal de Origem | select | WhatsApp, Referência, Instagram, Facebook, Balcão, Email, Outro |
| Fase do Cliente | select | Novo, Activo, Fidelizado, Inactivo, Perdido |
| Segmento | select | Viajante Frequente, Família, Executivo, Estudante, Empresa, Outro |
| Classificação | select | VIP, Frequente, Ocasional, Novo |

### Rollups

| Campo | Origem / Função |
|---|---|
| Total Gasto (AOA) | soma de Pedidos.Preço de Venda |
| Nº de Pedidos | contagem de Pedidos |
| Último Pedido | data mais recente de Pedidos |
| Nº Comunicações | contagem de Comunicações |

## Regras de negócio (documentadas, aplicação manual/Make)

**Fase do Cliente**
| Fase | Critério |
|---|---|
| Novo | Sem pedidos |
| Activo | 1–2 pedidos concluídos |
| Fidelizado | 3+ pedidos concluídos |
| Inactivo | Último pedido há mais de 6 meses |
| Perdido | Definido manualmente |

**Classificação**
| Classificação | Critério |
|---|---|
| VIP | Total Gasto ≥ 500.000 AOA |
| Frequente | 3+ pedidos concluídos |
| Ocasional | 1–2 pedidos concluídos |
| Novo | Nenhum pedido |
