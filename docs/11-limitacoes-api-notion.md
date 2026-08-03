# Limitações Conhecidas da API do Notion

Registo de limitações confirmadas por tentativa directa, relevantes para quem for manter este workspace via API/MCP no futuro.

## 1. Campos do tipo `status`

Não é possível adicionar, renomear ou remover opções de um campo `status` via API. O comando `ALTER COLUMN ... SET STATUS(...)` é rejeitado; `SET STATUS` sem opções é aceite mas não altera nada.

**Impacto:** qualquer reestruturação do fluxo de `Status do Pedido` exige edição manual na interface.

## 2. Filtros sobre campos `status` em vistas

Filtros do tipo "is not X" sobre campos status via DSL só aceitam `is empty` / `is not empty`. Não é possível filtrar por valor específico ou por grupo (`complete`/`in_progress`) de forma fiável via API nesta integração.

**Impacto:** vistas como FOLLOW-UP, URGENTE e Sem Actividade Recente não conseguem excluir "Concluído"/"Cancelado" via API — requer configuração manual do filtro na interface.

## 3. Toggle "Sub-items" em vistas tabela

A visibilidade de subitens expandidos numa vista tabela é controlada por um toggle nativo (`Sub-items → Always expand`) que não está exposto na API — não aparece no schema da vista nem é alterável via DSL.

## 4. Vistas do tipo `chart`

A API aceita o comando de criação de uma vista `chart` e devolve sucesso, mas a vista não persiste na base de dados (confirmado por reprodução dupla). Usar antes uma vista `board`/`table` agrupada com soma manual activada no rodapé.

## 5. Templates nativos de base de dados

Não é possível criar Templates de base de dados via API (os que aparecem em "+ Novo → escolher modelo"). Só são criáveis manualmente na interface. Workaround: páginas-modelo normais, feitas para duplicar.

## 6. Permissões e partilha

A API não expõe nem permite gerir permissões ao nível de página ou teamspace. `notion-get-users` e `notion-get-teams` mostram membros e teamspaces, mas não quem tem acesso a que página especificamente.

## 7. Criação de cenários Make via API

Cenários Make que usam apps de terceiros (ex: Notion) por vezes exigem `confirmed: true` e nomes de módulo exactos (ex: `notion:getADatabaseItem`, não `notion:getDatabaseItem`) — a lista de módulos disponíveis deve ser verificada via `app-modules_list` antes de montar o blueprint.

## 8. Activação de cenários Make via API

`scenarios_activate` falha de forma consistente com "No approval received", mesmo após criação bem-sucedida do cenário com `confirmed: true`. A activação de um cenário novo precisa de ser feita manualmente na interface do Make (toggle ON no canto superior direito do cenário) — não há confirmação equivalente disponível para esta chamada.

## 9. Módulo de envio de email (Gmail)

O módulo `google-email:ActionSendEmail` (v1) existe mas não é compatível com conexões OAuth Gmail padrão nesta conta — falha com "Provided account is not compatible". O módulo correcto e funcional é `google-email:sendAnEmail` (v4), confirmado por uso no cenário de Envio de Bilhetes já existente.

## 10. Filtro embutido no módulo `notion:searchObjects1`

O parâmetro `filter` deste módulo espera IDs internos de propriedade (obtidos via RPC `listDataSourcePropertiesFieldsForFilter`), não o nome legível da propriedade. Usar o nome directamente (ex: `"a": "Validade do Passaporte"`) falha com erro de validação genérico listando todos os tipos de filtro possíveis. **Solução:** não usar o `filter` embutido do módulo de pesquisa — trazer todos os registos e aplicar a lógica de filtro no `filter` do módulo seguinte no fluxo (mesmo padrão usado com sucesso nos outros cenários).

## 11. Formato de blocos no módulo `notion:appendADatabaseItemContent`

Cada objecto do array `objects` precisa da estrutura completa do bloco Notion, com o tipo como chave de nível superior contendo `text` (array de rich text simplificado): 
```json
{"type": "to_do", "to_do": {"text": [{"type": "text", "text": {"content": "Item"}}], "checked": false}}
```
Um formato simplificado como `{"type": "to_do", "text": "Item", "checked": false}` falha com erro de validação listando todos os tipos de bloco possíveis.

## 12. Partilha de base de dados com a integração Make

Cada base de dados precisa de ser **explicitamente partilhada** com a integração Make no Notion (`⋯ → Connections → Make`), mesmo que a conexão OAuth já exista e funcione noutras bases. Um cenário que tenta aceder a uma base não partilhada falha com `404 Could not find database`, e o Make marca o cenário como `isinvalid: true` até a partilha ser feita.

## 13. Agrupamento AND vs OR no filtro entre módulos

**Erro real cometido e corrigido em produção:** no filtro `conditions` do blueprint, cada sub-array representa um grupo, e os grupos entre si são combinados com **OR**; condições dentro do mesmo sub-array são combinadas com **AND**. Escrever `"conditions": [[condA], [condB], [condC]]` cria 3 condições em OR (basta uma ser verdadeira), não em AND como seria intuitivo. Isto causou uma automação de alerta a disparar para 8 registos em vez de 1 (bastava "Próximo Passo vazio" ser verdadeiro para passar, ignorando as restantes condições). **Correcto:** `"conditions": [[condA, condB, condC]]` — todas dentro do mesmo sub-array para AND.

## 14. Título e campos `title` devolvem um array de rich text, não uma string

Um campo do tipo `title` (ex: `Titulo do Pedido`, `Nome do Cliente`, `Assunto`) referenciado directamente como `{{1.properties_value.Campo}}` insere o array JSON bruto de rich text no output (visível como `{"type":"text","text":{...},"plain_text":"...",...}` no email/conteúdo gerado), não o texto legível. **Correcto:** extrair o texto com `{{join(map(1.properties_value.Campo; "plain_text"); "")}}`.

## 15. Router (`builtin:BasicRouter`) funciona bem para múltiplos tipos de conteúdo condicional

Um único cenário com `builtin:BasicRouter` e vários `routes`, cada uma com o seu próprio `filter` + módulo(s), é a forma correcta de implementar "se X então A, se Y então B" dentro do mesmo trigger — evita criar um cenário por combinação. Testado com sucesso (checklist por tipo de pedido, 3 ramos).

## 16. `notion:getADatabaseItem` devolve um formato de propriedades completamente diferente de `watchDatabaseItems`/`searchObjects1`

Os módulos de trigger e pesquisa (`watchDatabaseItems`, `searchObjects1`) expõem `properties_value.NomeDoCampo` — um objecto plano, fácil de referenciar. O módulo `notion:getADatabaseItem` devolve `properties` como um **array** de objectos `{id, type, label, ...valor tipado}` — não há forma simples de extrair um valor por nome do campo sem construir lógica de pesquisa no array (não testado com sucesso). Evitar este módulo quando possível; preferir refazer a pesquisa com `searchObjects1` scoped, ou usar rollups/fórmulas na própria base para trazer o valor necessário sem uma segunda chamada.

## 17. Rollups cross-database não são fiáveis como filtro no Make (limitação não resolvida)

Um rollup na Gestão de Pedidos que traz um valor da base Clientes relacionada (ex: `Classificação do Cliente`, rollup de `Cliente.Classificação`) **não correspondeu a nenhum filtro testado** em `watchDatabaseItems` nem em `searchObjects1`, mesmo com dados confirmados correctos na origem (testado com `text:equal` em string simples e em array `[].name`). Tentativas de contornar com uma fórmula Notion que achata o rollup em texto (`format()`, `.join(",")`, `relation.map()`) foram todas rejeitadas pela API do Notion com "Type error with formula". **Não foi encontrada uma solução funcional.** Ver Cenário 10 em `07-automacoes-make.md` para o caso real e caminhos futuros possíveis.

## 18. `"select": "list"` em módulos de escrita do Notion não funciona via API (CRÍTICO)

O modo `"select": "list"` nos módulos `notion:updateADatabaseItem` e `notion:createDataSourceItem` é destinado **apenas a selecção manual de campos na interface visual do Make**. Usado via blueprint/API com `"fields": {"Campo": {"value": "..."}}`, o pedido é aceite, o Make devolve sucesso, mas **nenhum dado é escrito** — falha completamente silenciosa, sem erro nem aviso. Isto passou despercebido durante meses em 3 automações de produção.

**Correcto:** usar `"select": "map"`, com `"fields"` como **array** de `{key, type, value}`:
```json
{"select": "map", "fields": [{"key": "Campo", "type": "select", "value": "Valor"}]}
```

## 19. ID de base de dados ≠ ID de fonte de dados (data source)

No modelo actual do Notion, cada base de dados tem um ID de página e um ID de fonte de dados (*data source*) distintos. O parâmetro `data_source` do módulo `searchObjects1` (modo `data_source_item`) exige o ID da fonte de dados — usar o ID da página da base de dados dá `404 Could not find data_source`. Confundir os dois é um erro fácil de cometer e difícil de detectar sem o erro explícito.

## 20. Valores de relação precisam de extracção explícita do ID

Ao escrever um campo `relation` via modo `map`, o valor deve ser um array de strings ID, não o array bruto de objectos `{id: "..."}` devolvido pelos módulos de leitura. Extrair com `{{map(propriedade; "id")}}` antes de atribuir a `value`.

## 21. Tipo correcto para texto rico no modo `map` é `rich_text`, não `text`

Ao declarar o `type` de um campo no array `fields` do modo `map`, campos de texto (Notion `rich_text`) devem usar `"type": "rich_text"`. Usar `"text"` causa erro de validação da API do Notion.
