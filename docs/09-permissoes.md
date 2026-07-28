# Proposta de Permissões

> Não aplicado ainda — requer configuração manual (API do Notion não gere permissões).

## Estado actual

Um único teamspace, "Luandagate", com todos os membros:

| Utilizador | Papel sugerido |
|---|---|
| Edmilson Fabio | Supervisor (owner actual) |
| José Labadie | Direcção / Chefe |
| Cleuzia Baptista | Agente |
| Jarelde Francisco | Agente |
| Paulo Matias | Agente |

Por omissão, todos veem tudo dentro do teamspace.

## Estrutura proposta

**Direcção (José + Edmilson):** acesso total — todas as bases, Dashboard financeiro, Receita por Agente, CRM completo, Automações Make.

**Agentes (Cleuzia, Jarelde, Paulo):**
- ✅ Gestão de Pedidos (ver todos, idealmente editar só os seus)
- ✅ Gestão de Clientes (acesso completo)
- ✅ Painel do Agente
- ✅ Registo de Comunicações
- ⚠️ `Preço de Venda`, `Total Gasto`, vista Receita por Agente — avaliar se deve ser restrito
- ❌ Painel do Supervisor
- ❌ Configuração do Make

## Como aplicar (manual)

**Opção A — Teamspaces separados (recomendado):** criar teamspace "Operações" para agentes, manter "Luandagate" para Direcção.

**Opção B — Permissões por página:** restringir individualmente páginas sensíveis via `⋯ → Connections/Share`.

**Limitação:** o Notion não tem permissões ao nível de campo dentro da mesma vista. Esconder `Preço de Venda` de agentes exige bases/páginas separadas, não apenas vistas diferentes.

## Checklist de aplicação

- [ ] Decidir Opção A ou B
- [ ] Confirmar papéis (Direcção vs Agente)
- [ ] Decidir visibilidade de `Preço de Venda`
- [ ] Restringir Painel do Supervisor
- [ ] Restringir configuração do Make
- [ ] Testar com login de agente
