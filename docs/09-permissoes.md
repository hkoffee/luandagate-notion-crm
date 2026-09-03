# Proposta de Permissões

> **✅ Decisão tomada:** Teamspaces separados. Preços e receita ficam visíveis a todos, incluindo agentes — decisão explícita, não omissão. Execução pendente (manual, na interface do Notion — a API não gere permissões).

## Estado anterior (histórico)

Um único teamspace, "Luandagate", com todos os membros:

| Utilizador | Papel |
|---|---|
| Supervisor | Owner |
| Direção | Direcção / Chefe |
| Agente 1 | Agente |
| Agente 2 | Agente |
| Agente 3 | Agente |

Por omissão, todos viam tudo dentro do teamspace.

## Plano final — 2 teamspaces

**"Luandagate" (Direcção — Direção + Supervisor):**
- Painel do Supervisor
- Automações CRM — Make
- Proposta de Permissões (esta página)
- Dados Bancários & Empresa — excepção à regra dos preços: é informação de segurança/fraude, não de visibilidade de negócio

**"Operações" (novo — todos, incluindo os 3 agentes):**
- Hub Central, Gestão de Pedidos, Gestão de Clientes, Tarefas da Agência, Registo de Comunicações
- Fornecedores & Parceiros, Histórico de Tarifas
- Painel do Agente
- Wiki & Processos (Procedimentos, Onboarding, Guia Rápido, Scripts de Vendas, Textos Padrão, Requisitos de Vistos)

Preços de venda e receita por agente ficam visíveis a todos — sem restrição de campo.

## Passo a passo para executar

1. `Settings & Members → Teamspaces → Criar novo teamspace` → "Operações"
2. Adicionar os 5 membros
3. Mover as páginas operacionais listadas acima para "Operações"
4. Isolar no "Luandagate": Painel do Supervisor, Automações Make, Dados Bancários
5. Remover os 3 agentes do teamspace "Luandagate"
6. Testar com um agente para confirmar visibilidade

## Limitação confirmada

O Notion não tem permissões ao nível de campo dentro da mesma página/vista. Esconder um campo específico (ex: Preço de Venda) de alguém que tem acesso à página exige bases/páginas separadas, não apenas vistas diferentes — por isso a decisão foi simplesmente deixar os preços visíveis a todos em vez de complicar a arquitectura por um único campo.

## Checklist

- [x] Decidir estrutura — Teamspaces
- [x] Confirmar níveis — Direcção vs Agentes
- [x] Decidir visibilidade de preços — Visível a todos
- [ ] Criar teamspace "Operações" e mover páginas
- [ ] Isolar Painel do Supervisor + Automações Make + Dados Bancários
- [ ] Remover agentes do teamspace "Luandagate"
- [ ] Testar com login de agente
