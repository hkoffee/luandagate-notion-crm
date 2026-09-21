# Automações — Make

Conexão Notion no Make: `<id-conexao-notion>` ("Luandagate - Supervisor - LUANDAGATE's Space")
Team ID: `<id-equipa-make>`

## Cenários (após correção do bug de escrita — 03/08/2026)

| # | Nome | ID | Trigger | Estado |
|---|---|---|---|---|
| 1 | Integration Notion (Envio de Bilhetes) | 4139307 | update em Pedidos, checkbox `Enviar Bilhete` | activo — **corrigido e testado (16/08/2026)**, ver secção dedicada abaixo |
| 2 | CRM — Fase e Classificação ao Concluir Pedido | 4879246 | update em Pedidos | activo — **corrigido e testado com sucesso** |
| 3 | CRM — Novo Cliente → Fase Inicial | 4879256 | a cada 15 min | activo — **corrigido e testado com sucesso (06/08/2026)** |
| 4 | CRM — Manutenção Semanal de Clientes (Inactivos + Passaportes) | 4879253 | semanal | activo — **corrigido e testado com sucesso** |
| 5 | CRM — Alerta de Pedidos Parados (sem Próximo Passo) | 6737359 | diariamente 08:30 | activo |
| 6 | CRM — Checklists Automáticas por Tipo de Pedido | 6741903 | a cada 15 min | activo |
| 7 | CRM — Comunicação → Tarefa de Follow-up | 6726654 | a cada 15 min | activo — **corrigido e testado com sucesso** |

**Desactivados** (substituídos, mantidos por segurança):
- 6737356 — Alerta Semanal de Passaportes (fundido em #4)
- 6737366 — Checklist de Visto (fundido em #6)
- 6741957 — Checklist VIP (nunca funcionou, ver limitação)

---

## 🔴 Bug crítico encontrado e corrigido (03/08/2026)

Durante uma sessão de testes reais, descobri que **3 automações activas há meses nunca escreveram dados de facto**, apesar de correrem "com sucesso" (sem erro) regularmente. A causa raiz foi um erro de configuração presente desde a criação original destas automações.

### Causa raiz

O parâmetro `"select": "list"` nos módulos `notion:updateADatabaseItem` e `notion:createDataSourceItem` é um modo pensado **apenas para selecção manual de campos na interface visual do Make** — ao usá-lo via blueprint/API com um objecto `"fields": {"NomeDoCampo": {"value": "..."}}`, o Make aceita o pedido, devolve sucesso, mas **não escreve nada**. Não há erro, não há aviso — falha completamente silenciosa.

### Correcção

O modo correcto para escrita programática é `"select": "map"`, com `"fields"` como um **array** de objectos `{key, type, value}`:

```json
{
  "select": "map",
  "fields": [
    {"key": "Nome do Campo", "type": "select", "value": "Valor"},
    {"key": "Outro Campo", "type": "checkbox", "value": true}
  ]
}
```

### Automações afectadas e corrigidas

| Automação | Sintoma antes da correcção | Desde quando estava partida |
|---|---|---|
| CRM — Fase e Classificação ao Concluir Pedido | Nunca actualizava Fase/Classificação do cliente | Criação original (Março 2026) |
| CRM — Manutenção Semanal (Clientes Inactivos) | Nunca marcava clientes como Inactivo | Criação original (Março 2026) |
| CRM — Comunicação → Tarefa de Follow-up | Nunca criava a tarefa (campo `fields` vazio) | Criação original (28/07/2026) |

Todas as 3 foram corrigidas e **confirmadas a escrever dados reais** através de testes directos com registos de teste, verificados por consulta SQL ao resultado final em Notion.

### Lições adicionais descobertas durante a correcção

1. **IDs de base de dados vs. IDs de fonte de dados são diferentes no Notion** e não são intercambiáveis. O parâmetro `data_source` do módulo `searchObjects1` (modo `data_source_item`) exige especificamente o ID da *fonte de dados*, não o ID da página da base de dados — usar o errado dá erro `404 Could not find data_source`.
2. **Valores de relação (`relation`) devem ser extraídos com `map(...; "id")`** antes de serem enviados como `value` num campo do tipo `relation` — o array bruto de `{id: "..."}` não é aceite directamente, causa erro de validação da API do Notion (`should be a string, instead was {"id":"..."}`).
3. **O tipo correcto para campos de texto rico (`rich_text`) no array `fields` do modo `map` é `"rich_text"`, não `"text"`** — usar `"text"` causa erro de validação genérico da API do Notion.
4. **Campos de relação vindos do gatilho `watchDatabaseItems` já incluem objectos `{id: "..."}` com o ID no formato correcto (com hífens)** — não é preciso normalizar formato para comparar com o `id` de resultados de `searchObjects1`.

### Por que "Integration Notion" não foi consolidado com outro cenário

Apesar de partilhar o mesmo tipo de gatilho (update em Gestão de Pedidos) com o cenário #2, esta automação continua **num cenário separado deliberadamente** — é a mais crítica do negócio (envia bilhetes reais a clientes) e juntá-la a outra lógica aumentaria o risco de um erro afectar o envio de bilhetes. Mantém-se isolada mesmo depois de corrigida.

---

## 🟡 Proposta pendente — substituir dados manuais por leitura de bilhete via IA

O utilizador criou, de forma independente, um novo cenário chamado **"Leitura de Bilhete"** (ID 7460594), com o objectivo de eliminar a dependência de preenchimento manual dos campos do pedido no Notion (Rota, Companhia Aérea, PNR/Ref, Datas, Número do Bilhete) — extraindo esses dados directamente do PDF do bilhete via IA, em vez de depender de o agente os copiar à mão.

**Arquitectura observada (via listagem de cenários, sem acesso à configuração completa):**
- `google-drive:watchFilesInAFolder` — vigia uma pasta do Google Drive à espera de novos PDFs
- `google-drive:getAFile` — obtém o ficheiro
- `pdf-co:PDFToAnything` — converte o PDF (provavelmente para texto/imagem legível pela IA)
- `gemini-ai:createACompletionGeminiPro` — extrai os dados do bilhete via IA generativa

**Estado:** descoberto e documentado, **integração com "Integration Notion" ainda não iniciada**.

**Bloqueios antes de avançar:**
1. Falta de permissão ("View scenario details") para inspeccionar a configuração exacta deste cenário a partir desta integração — necessário resolver o acesso primeiro
2. O cenário já registou 3 erros em 6 execuções — causa por identificar antes de o considerar fiável
3. **Risco de negócio a não ignorar:** este email vai directamente para o cliente, com informação de voo real. Uma extracção por IA que erre uma data, hora ou código de reserva é um erro que o cliente vê e pode confiar, mesmo com o PDF certo em anexo — é diferente de um erro técnico invisível. Antes de substituir os campos do Notion pela extracção da IA, faz sentido definir uma estratégia de validação cruzada (ex: a automação só avança se os dados extraídos pela IA baterem certo com os campos já preenchidos no Notion, ou pelo menos regista um alerta em caso de divergência, em vez de confiar cegamente)

## 🔴 Bug crítico #2 — Envio de Bilhetes, checkbox nunca resetava (encontrado e corrigido em 16/08/2026)

Um alerta do Make sobre um cenário diferente (Novo Cliente → Fase Inicial, ver changelog 06/08) levou a uma auditoria completa de todos os cenários, que revelou que a **Integration Notion (Envio de Bilhetes)** — a automação mais crítica do negócio — estava **inválida e inactiva**.

### Dois problemas encontrados

**1. O mesmo bug do `"select": "list"`.** O último passo do cenário, que devia desmarcar a checkbox `Enviar Bilhete` depois do envio, usava o mesmo padrão quebrado documentado no bug crítico #1 acima — nunca escrevia nada. Isto significa que, depois de qualquer bilhete ser enviado, a checkbox **ficava presa em `true`**. Se o pedido fosse editado novamente por qualquer motivo (mesmo sem relação com o bilhete), a automação reenviaria o mesmo bilhete ao cliente. Não há evidência de que isto tenha causado reenvios reais (nenhum aparece nos ~6950 envios históricos verificados), mas era um risco activo.

**2. Extracção de URL do anexo não suportava ficheiros externos.** O passo de download do bilhete lia `Bilhetes[].file.url`, que só existe para ficheiros carregados directamente no Notion. Um anexo do tipo "link externo" (`external.url`) resultava em URL vazio e falha silenciosa de validação antes mesmo de chegar ao envio de email — foi isto que causou a falha nos primeiros testes desta correcção.

### Correcção aplicada

1. Passo de download agora usa `{{ifempty(Bilhetes[].file.url; Bilhetes[].external.url)}}`, suportando os dois tipos de anexo
2. Passo de reset da checkbox reescrito com `"select": "map"` e `fields` em array, com `"database"` a apontar para o **ID da página** da base (não o ID da fonte de dados — este cenário legado usa esse formato consistentemente com o módulo de gatilho, ao contrário de scenarios criados de raiz nesta sessão)
3. Testado de ponta a ponta com um pedido de teste real, ligado ao cliente de teste que corresponde ao próprio Supervisor (para o email de teste ir para si mesmo, não para um cliente real): download do anexo, envio do email (confirmado recebido), e reset automático da checkbox — todos os 5 passos confirmados a funcionar

### Metodologia de teste usada (relevante para o futuro)

Como a API do Notion não permite anexar ficheiros carregados directamente a uma propriedade `files` de uma base de dados (só aceita `external` URLs via API — ver limitação nº 22 abaixo), o teste desta automação usou um ficheiro de teste alojado temporariamente no próprio repositório GitHub (`raw.githubusercontent.com`, já na lista de domínios permitidos) como anexo externo. Isto expôs o bug da extracção de URL (só suportava ficheiros internos) — um problema real que só apareceu por causa do método de teste, mas que também protege contra o caso (menos comum, mas possível) de um agente colar um link em vez de carregar o ficheiro directamente.

### Bug encontrado e corrigido durante a fusão

O cenário de Clientes Inactivos (#4) tinha o campo `fields` do módulo de actualização **vazio** desde a criação original em Março de 2026 — nunca chegou a marcar nenhum cliente como Inactivo, apesar de aparecer como "activo" e correr semanalmente sem erros. Corrigido ao fundir com o de Passaportes.

### Conexões usadas

| Conexão | ID | Uso |
|---|---|---|
| Luandagate (Notion) | <id-conexao-notion> | Todos os cenários Notion |
| Geral (Gmail — geral@empresa.com) | <id-conexao-gmail-geral> | Envio de bilhetes e alertas por email (`google-email:sendAnEmail` v4) |
| My Gmail connection (supervisor@empresa.com) | <id-conexao-gmail-pessoal> | Não usada actualmente |

**Nota:** o módulo correcto para envio de email é `google-email:sendAnEmail` versão 4 — o módulo `google-email:ActionSendEmail` versão 1 existe mas não é compatível com as conexões OAuth desta conta.

## Cenário 5 — Comunicação → Tarefa de Follow-up

Quando uma comunicação é criada no Registo de Comunicações com `Próxima Ação` preenchida, cria automaticamente uma tarefa em Tarefas da Agência (Nome = "Follow-up: {Próxima Ação}", Cliente, Pedido Relacionado, Agente e Atribuído Por = Responsável da comunicação, Prazo = dia seguinte, Prioridade = Média, Status = Por Fazer). **Corrigido e testado com sucesso em 03/08/2026** — ver bug crítico acima.

## Cenário 6 — Alerta Semanal de Passaportes a Expirar

Toda segunda-feira às 08:00, procura clientes com `Validade do Passaporte` preenchida e ≤ 60 dias no futuro, e envia um email (um por cliente) para supervisor@empresa.com com nome, data de expiração, telefone e email do cliente.

## Cenário 7 — Alerta de Pedidos Parados

Diariamente às 08:30, procura pedidos mãe (`item principal` vazio) sem `Próximo Passo` definido e com `Data de Solicitação` há mais de 3 dias, e envia um email de alerta por pedido.

## Cenário 8 — Checklist Automática de Visto

Ao criar um pedido com `Serviço` contendo `Visto`, adiciona automaticamente ao corpo da página um cabeçalho "✅ Checklist do Visto" e 8 itens to-do: Passaporte, Fotografia, Seguro, Formulário, Carta, Reserva, Pagamento, Entrega.

## Cenário 9 — Checklist Automática por Tipo de Pedido (Bilhete/Hotel/Empresa)

Um único cenário com **router** (3 ramos), disparado à criação de qualquer pedido:
- **Ramo Bilhete**: `Serviço` contém "Passagem" → adiciona checklist de 7 itens
- **Ramo Hotel**: `Serviço` contém "Hotel" → adiciona checklist de 5 itens
- **Ramo Empresa**: `Tipo de Cliente` = "Corporativo" → adiciona checklist de 4 itens

Um pedido pode disparar mais de um ramo (ex: bilhete corporativo recebe as duas checklists). Testado com sucesso nos 3 ramos.

## Cenário 10 — Checklist Automática VIP (⚠️ desactivado, limitação não resolvida)

**Objectivo:** adicionar checklist de atendimento premium quando o `Cliente` associado ao pedido tem `Classificação = VIP` na Gestão de Clientes.

**Por que está desactivado:** ao contrário dos outros tipos (Bilhete, Hotel, Empresa), que dependem apenas de campos do próprio pedido, o VIP depende de um campo (`Classificação`) que vive na base **Clientes**, relacionada por `Cliente`. Foram tentadas 4 abordagens, todas mal-sucedidas:

1. `notion:getADatabaseItem` para buscar o cliente pelo ID da relação → erro `Invalid request URL` (parâmetros do módulo mal mapeados; corrigido depois, mas o output desse módulo é um **array** de propriedades `{id, type, label, ...}`, não o formato `properties_value.Campo` plano usado pelos módulos de trigger/pesquisa — não há forma simples de extrair um valor por nome sem conhecer o ID interno da propriedade)
2. Rollup `Classificação do Cliente` na Gestão de Pedidos, lido directamente no trigger → filtro nunca correspondeu (0 resultados), mesmo com dados confirmados correctos na origem
3. Mesmo rollup, mas em cenário de pesquisa agendada (`searchObjects1`) em vez de trigger → mesmo resultado, 0 correspondências
4. Fórmula Notion a converter o rollup para texto simples (`format(prop(...))`, `.join(",")`, `relation.map()`) → **todas rejeitadas pela API do Notion** com "Type error with formula"

**Estado actual:** o cenário existe (ID 6741957) mas está desactivado — não faz nada. A checklist VIP continua disponível via a página-modelo manual (`🧩 MODELO — VIP`, ver [10-modelos.md](10-modelos.md)).

**Possíveis caminhos futuros:** obter os IDs internos exactos das propriedades via RPC do Make (`listDataSourcePropertiesFieldsForFilter`, que devolveu "deprecated" nesta tentativa) para construir um filtro nativo Notion correcto; ou usar Zapier/n8n como alternativa para este caso específico; ou simplificar o modelo de dados (ex: duplicar `Classificação` como campo directo no pedido, preenchido manualmente pelo agente).

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
