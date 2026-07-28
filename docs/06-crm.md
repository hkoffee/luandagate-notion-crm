# CRM — Funcionalidades e Procedimentos

## O que existe

- Perfil de cliente enriquecido (Canal de Origem, Fase, Segmento, Classificação)
- Base de Registo de Comunicações ligada a Clientes e Pedidos
- Pipeline visual (Kanban) na Gestão de Pedidos
- Vistas dedicadas: Vista CRM, Clientes Inactivos, Clientes VIP

## Procedimento — criar novo cliente

1. Nome, Telefone/Email, Tipo de Cliente
2. Canal de Origem (como chegou)
3. Fase do Cliente = Novo
4. Segmento

## Procedimento — registar comunicação

Após qualquer contacto (chamada, WhatsApp, email, presencial): registar no Registo de Comunicações com Assunto, Cliente, Pedido (se aplicável), Data, Canal, Tipo, Resumo, Próxima Ação, Responsável.

## Rotina semanal do supervisor

- Rever Clientes Inactivos → contactar os mais antigos
- Rever Clientes VIP → confirmar sem pedidos recentes esquecidos
- Actualizar Fase/Classificação dos clientes tratados na semana
- Confirmar que a equipa está a registar comunicações

## Dashboard — indicadores

| Indicador | Fonte |
|---|---|
| Pedidos concluídos | vista RELATÓRIO |
| Bilhetes emitidos | vista 🎫 Bilhetes Emitidos |
| Vistos | vista VISTOS |
| Tempo médio por pedido | campo formula `Tempo de Processamento (dias)` |
| Follow-ups vencidos | vista 🚨 URGENTE |
| Clientes VIP | vista ⭐ Clientes VIP |
| Clientes inactivos | vista 😴 Clientes Inactivos |
| Receita por agente | vista 💰 Receita por Agente |
| Pedidos activos | **pendente** — depende da reestruturação do Status (ver [08-workflow.md](08-workflow.md)) |
