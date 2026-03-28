# Manual de Construção de Skills para o Claude

## O que é uma Skill?

Uma skill é um conjunto de instruções empacotadas que o Claude consulta automaticamente quando detecta que o contexto da conversa é relevante. Em vez de você repetir suas boas práticas toda vez, a skill funciona como um "manual de referência permanente" que o Claude lê antes de agir.

Na prática, é uma pasta contendo um arquivo `SKILL.md` (obrigatório) e, opcionalmente, scripts, documentos de referência e assets auxiliares.

---

## Estrutura de uma Skill

```
nome-da-skill/
├── SKILL.md              ← Obrigatório. Instruções principais.
├── scripts/              ← Opcional. Código executável para tarefas repetitivas.
├── references/           ← Opcional. Documentação extra carregada sob demanda.
└── assets/               ← Opcional. Templates, ícones, fontes, imagens.
```

### Para que serve cada parte

- **SKILL.md**: O coração da skill. Contém o cabeçalho YAML (metadados) e o corpo em Markdown (instruções).
- **scripts/**: Scripts prontos que o Claude pode executar. Útil quando várias tarefas diferentes precisam do mesmo código auxiliar.
- **references/**: Documentação complementar que o Claude lê apenas quando necessário. Ideal para conteúdo extenso que não precisa estar sempre carregado.
- **assets/**: Arquivos estáticos usados na saída — templates de documentos, fontes customizadas, imagens padrão, etc.

---

## Anatomia do SKILL.md

O arquivo SKILL.md tem duas seções fundamentais:

### 1. Cabeçalho YAML (Frontmatter)

Fica no topo do arquivo, delimitado por `---`. Contém os metadados da skill.

```yaml
---
name: nome-da-skill
description: >
  Descrição clara do que a skill faz e QUANDO ela deve ser ativada.
  Inclua palavras-chave, contextos e exemplos de situações de uso.
---
```

**Campos obrigatórios:**

- `name`: Identificador da skill. Use kebab-case (palavras separadas por hífen, sem espaços).
- `description`: A descrição é o **mecanismo principal de ativação**. É com base nela que o Claude decide se deve ou não consultar a skill. Quanto mais completa e abrangente, melhor.

**Campos opcionais:**

- `license`: Informações de licença, se aplicável.
- `compatibility`: Ferramentas ou dependências necessárias.

### 2. Corpo em Markdown

O restante do arquivo contém as instruções propriamente ditas — suas boas práticas, fluxos de trabalho, formatos de saída, exemplos, e tudo mais que o Claude precisa saber para executar bem a tarefa.

---

## Como escrever uma boa Description

A description é o elemento mais importante para o funcionamento correto da skill. Ela determina **quando** o Claude vai ativá-la. Pense nela como o "sensor de ativação".

### Princípios para uma boa description

1. **Seja abrangente**: Liste não apenas o que a skill faz, mas todos os contextos em que ela deveria ser ativada. Inclua sinônimos, variações de fraseamento e situações adjacentes.

2. **Seja um pouco "insistente"**: O Claude tende a sub-ativar skills (não usá-las quando deveria). Para compensar, torne a description levemente mais agressiva nos gatilhos.

3. **Inclua exemplos concretos**: Mencione tipos de pedido, palavras-chave e formatos de arquivo que devem ativar a skill.

4. **Inclua exclusões quando necessário**: Se houver contextos similares em que a skill NÃO deve ser usada, mencione-os também.

### Exemplo ruim

```yaml
description: Ajuda a criar documentos Word.
```

### Exemplo bom

```yaml
description: >
  Use esta skill sempre que o usuário quiser criar, ler, editar ou manipular
  documentos Word (.docx). Inclui: menções a 'documento Word', '.docx',
  pedidos de relatórios profissionais com formatação como sumário, cabeçalhos,
  numeração de páginas ou papel timbrado. Também use ao extrair ou reorganizar
  conteúdo de arquivos .docx, inserir imagens, fazer busca-e-substituição,
  trabalhar com controle de alterações ou converter conteúdo em um documento
  Word polido. Se o usuário pedir um 'relatório', 'memorando', 'carta' ou
  'template' como arquivo Word ou .docx, use esta skill. NÃO use para PDFs,
  planilhas ou Google Docs.
```

---

## Como escrever boas instruções (corpo do SKILL.md)

### Princípio fundamental: Explique o "porquê"

O Claude é inteligente. Ele segue instruções muito melhor quando entende a razão por trás delas. Em vez de empilhar regras rígidas com "SEMPRE" e "NUNCA" em caixa alta, explique o motivo.

**Abordagem fraca:**
```markdown
SEMPRE use aspas duplas em strings. NUNCA use aspas simples.
```

**Abordagem forte:**
```markdown
Use aspas duplas em strings para manter consistência com o padrão do projeto
e evitar conflitos com apóstrofos em textos em português.
```

### Padrões de escrita

#### Definindo formatos de saída

Quando a skill precisa gerar saídas em um formato específico, forneça o template exato:

```markdown
## Estrutura do relatório

Use sempre este template:

# [Título]
## Resumo executivo
## Principais descobertas
## Recomendações
## Próximos passos
```

#### Usando exemplos

Exemplos concretos de entrada e saída são extremamente eficazes. Use-os sempre que possível:

```markdown
## Formato de mensagem de commit

**Exemplo 1:**
Entrada: Adicionou autenticação de usuário com tokens JWT
Saída: feat(auth): implementa autenticação baseada em JWT

**Exemplo 2:**
Entrada: Corrigiu bug no cálculo de desconto para clientes premium
Saída: fix(billing): corrige cálculo de desconto para clientes premium
```

#### Fluxos de trabalho passo a passo

Para processos sequenciais, numere os passos e seja específico:

```markdown
## Fluxo de criação de componente

1. Criar o arquivo do componente em `src/components/`
2. Definir as props com TypeScript (interfaces, não types)
3. Implementar o componente como função (não classe)
4. Adicionar export default no final do arquivo
5. Criar arquivo de teste em `__tests__/` com o mesmo nome
```

#### Tabelas de decisão

Para situações com múltiplas condições, tabelas são mais claras que parágrafos:

```markdown
## Qual biblioteca usar

| Situação                  | Biblioteca    | Motivo                              |
|---------------------------|---------------|-------------------------------------|
| Requisição simples        | fetch nativo  | Sem dependência extra               |
| Retry automático          | axios         | Interceptors facilitam              |
| Streaming de dados        | undici        | Melhor performance para streams     |
```

---

## Sistema de carregamento progressivo

Skills usam um sistema de três níveis para otimizar o que o Claude carrega na memória:

### Nível 1 — Metadados (sempre carregados)

O `name` e a `description` do cabeçalho YAML. São lidos em **toda** conversa para decidir se a skill é relevante. Mantenha a description informativa mas concisa (~100 palavras).

### Nível 2 — Corpo do SKILL.md (carregado quando a skill é ativada)

As instruções principais. Idealmente, mantenha este conteúdo em **até 500 linhas**. Se precisar de mais espaço, mova o conteúdo adicional para arquivos de referência.

### Nível 3 — Recursos adicionais (carregados sob demanda)

Arquivos em `scripts/`, `references/` e `assets/`. São lidos apenas quando o Claude precisa deles. Podem ser tão extensos quanto necessário.

### Como organizar conteúdo extenso

Se a skill cobre múltiplos domínios ou frameworks, organize por variante:

```
deploy-cloud/
├── SKILL.md                    ← Visão geral + critérios de seleção
└── references/
    ├── aws.md                  ← Instruções específicas para AWS
    ├── gcp.md                  ← Instruções específicas para GCP
    └── azure.md                ← Instruções específicas para Azure
```

No SKILL.md, inclua ponteiros claros sobre quando ler cada arquivo de referência:

```markdown
## Referências por provedor

- Se o deploy for na AWS, leia `references/aws.md`
- Se o deploy for no GCP, leia `references/gcp.md`
- Se o deploy for no Azure, leia `references/azure.md`
```

Para arquivos de referência longos (mais de 300 linhas), inclua um sumário no topo para facilitar a navegação.

---

## Incluindo scripts auxiliares

Se suas tarefas envolvem operações repetitivas ou determinísticas, inclua scripts prontos na pasta `scripts/`. Isso evita que o Claude reinvente a roda a cada execução.

### Quando incluir um script

- Quando a mesma sequência de código seria escrita em múltiplas tarefas diferentes.
- Quando a operação envolve lógica complexa que pode ter bugs se reescrita do zero.
- Quando há dependências específicas de bibliotecas ou configurações.

### Como referenciar scripts no SKILL.md

```markdown
## Gerando o relatório

Para gerar o relatório final, execute o script incluído:

```bash
python scripts/gerar_relatorio.py --entrada dados.csv --saida relatorio.pdf
```

O script aceita os seguintes parâmetros:
- `--entrada`: Caminho para o arquivo CSV com os dados
- `--saida`: Caminho de destino para o PDF gerado
- `--template`: (opcional) Template customizado. Padrão: `assets/template_padrao.html`
```

---

## Princípios de Design de Skills

### 1. Princípio da não-surpresa

O conteúdo e o comportamento de uma skill não devem surpreender o usuário. Se alguém lesse a description e depois visse o que a skill faz, deveria ser exatamente o esperado. Nada de funcionalidades ocultas ou comportamentos inesperados.

### 2. Generalize, não sobreajuste

Sua skill será usada em muitos contextos diferentes. Evite instruções extremamente específicas que só funcionam para um caso. Em vez de regras rígidas e super restritas, explique o raciocínio e deixe o Claude adaptar-se ao contexto.

**Sobreajustado:**
```markdown
Quando o usuário pedir um relatório de vendas do Q3, use a cor azul #1a73e8
para os gráficos e fonte Roboto 14px para o corpo.
```

**Generalizado:**
```markdown
Use cores do esquema da marca do usuário quando disponíveis. Para gráficos,
prefira cores com bom contraste e que sejam acessíveis para daltônicos.
A tipografia deve ser legível e profissional — priorize fontes sans-serif
para dados e serifadas para texto corrido.
```

### 3. Mantenha enxuto

Remova instruções que não estão contribuindo para o resultado. Se algo pode ser dito em uma frase, não use três. O Claude processa melhor instruções concisas e diretas.

### 4. Use o imperativo

Escreva instruções no modo imperativo — elas são mais claras e diretas:

- ✅ "Crie o arquivo no diretório `src/`"
- ✅ "Valide os dados antes de processar"
- ❌ "Você deveria criar o arquivo no diretório `src/`"
- ❌ "Seria bom validar os dados antes de processar"

---

## Exemplos de Skills Completas

### Exemplo 1: Skill simples (sem recursos extras)

```
padrao-commits/
└── SKILL.md
```

```markdown
---
name: padrao-commits
description: >
  Use esta skill sempre que o usuário pedir ajuda com mensagens de commit,
  histórico de git, ou organização de commits. Ativa quando mencionarem
  'commit', 'git log', 'conventional commits', 'changelog', ou pedirem
  para formatar/reescrever mensagens de commit.
---

# Padrão de Commits

Todas as mensagens de commit seguem o formato Conventional Commits
para manter o histórico legível e permitir geração automática de changelogs.

## Formato

```
<tipo>(<escopo>): <descrição curta>

<corpo opcional>

<rodapé opcional>
```

## Tipos permitidos

- `feat`: Nova funcionalidade
- `fix`: Correção de bug
- `docs`: Mudanças em documentação
- `style`: Formatação (sem mudança de lógica)
- `refactor`: Refatoração (sem feat nem fix)
- `test`: Adição ou correção de testes
- `chore`: Manutenção geral (deps, configs, CI)

## Regras

- A descrição curta é em português, minúsculas, sem ponto final, máximo 72 caracteres
- O escopo indica o módulo afetado (ex: `auth`, `api`, `ui`)
- O corpo explica o "porquê" da mudança, não o "o quê" (o diff já mostra o quê)
- Use `BREAKING CHANGE:` no rodapé para mudanças incompatíveis

## Exemplos

**Exemplo 1:**
Entrada: Adicionei paginação na listagem de produtos
Saída: feat(produtos): adiciona paginação na listagem

**Exemplo 2:**
Entrada: O login quebrava quando o email tinha caracteres especiais
Saída: fix(auth): corrige validação de email com caracteres especiais

**Exemplo 3:**
Entrada: Migrei o banco de MySQL para PostgreSQL
Saída:
```
refactor(db): migra de MySQL para PostgreSQL

A migração foi motivada pela necessidade de suporte a JSONB e
melhor performance em queries complexas com CTEs.

BREAKING CHANGE: variáveis de ambiente do banco mudaram de MYSQL_* para PG_*
```
```

---

### Exemplo 2: Skill com referências e scripts

```
api-rest/
├── SKILL.md
├── scripts/
│   └── gerar_rotas.py
├── references/
│   ├── autenticacao.md
│   └── tratamento-erros.md
└── assets/
    └── template_openapi.yaml
```

```markdown
---
name: api-rest
description: >
  Use esta skill sempre que o usuário estiver construindo, projetando ou
  modificando uma API REST. Ativa quando mencionarem 'API', 'endpoint',
  'rota', 'REST', 'CRUD', 'controller', 'middleware', ou pedirem ajuda
  com design de APIs, documentação OpenAPI/Swagger, ou padrões de resposta.
  Também ative ao discutir autenticação de APIs (JWT, OAuth, API keys),
  tratamento de erros em APIs, ou versionamento de endpoints. NÃO use para
  APIs GraphQL ou gRPC.
---

# Padrões de API REST

Esta skill define os padrões do projeto para construção de APIs REST
consistentes, seguras e bem documentadas.

## Convenções de URL

- Use substantivos no plural: `/usuarios`, `/produtos`, `/pedidos`
- Hierarquia para recursos aninhados: `/usuarios/{id}/pedidos`
- Kebab-case para nomes compostos: `/categorias-produto`
- Versionamento na URL: `/api/v1/usuarios`

## Estrutura de resposta padrão

Respostas de sucesso:
```json
{
  "data": { ... },
  "meta": {
    "page": 1,
    "per_page": 20,
    "total": 142
  }
}
```

Respostas de erro:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Descrição legível do erro",
    "details": [ ... ]
  }
}
```

## Códigos HTTP

| Operação         | Sucesso | Erros comuns          |
|------------------|---------|-----------------------|
| GET (lista)      | 200     | 401, 403              |
| GET (item)       | 200     | 401, 403, 404         |
| POST (criar)     | 201     | 400, 401, 409         |
| PUT (atualizar)  | 200     | 400, 401, 404         |
| DELETE (remover) | 204     | 401, 403, 404         |

## Referências detalhadas

- Para autenticação (JWT, OAuth, API keys), leia `references/autenticacao.md`
- Para tratamento de erros e validação, leia `references/tratamento-erros.md`

## Geração de rotas

Para gerar o boilerplate de novas rotas, use o script incluído:

```bash
python scripts/gerar_rotas.py --recurso usuarios --operacoes crud
```

## Documentação

Toda API nova deve incluir documentação OpenAPI 3.0. Use o template
em `assets/template_openapi.yaml` como ponto de partida.
```

---

## Testando sua Skill

Depois de criar uma skill, é importante verificar se ela funciona bem na prática.

### Crie casos de teste realistas

Escreva 2-3 prompts que um usuário real diria. Não faça prompts genéricos — simule pedidos concretos, com contexto, detalhes e o nível de formalidade que as pessoas realmente usam.

**Prompt de teste fraco:**
```
Formate este commit.
```

**Prompt de teste forte:**
```
Opa, fiz umas mudanças no módulo de pagamento. Basicamente troquei
o gateway de Stripe pra Mercado Pago e tive que mudar a interface
do checkout também. Me ajuda a escrever o commit?
```

### Avalie qualitativamente

Peça para o Claude executar a tarefa usando a skill e avalie:

- A skill foi ativada quando deveria?
- O resultado seguiu as instruções da skill?
- Algo ficou estranho ou fora do padrão esperado?
- As instruções são claras o suficiente ou algo ficou ambíguo?

### Itere e refine

Com base nos resultados, ajuste as instruções, adicione exemplos para cobrir casos que falharam, e rode os testes novamente. Repita até estar satisfeito.

---

## Empacotando e Instalando

Depois de finalizar a skill, você pode empacotá-la em um arquivo `.skill` para facilitar a instalação e o compartilhamento:

```bash
# Dentro do diretório da skill
python -m scripts.package_skill caminho/para/nome-da-skill/
```

Isso gera um arquivo `nome-da-skill.skill` que pode ser instalado diretamente.

Alternativamente, se você estiver criando a skill com a ajuda do Claude, simplesmente peça para ele empacotar e gerar o arquivo de download.

---

## Checklist de Qualidade

Antes de considerar uma skill pronta, verifique:

- [ ] O `name` está em kebab-case e é descritivo
- [ ] A `description` cobre todos os cenários de ativação relevantes
- [ ] A `description` inclui exclusões para evitar falsos positivos
- [ ] O SKILL.md tem menos de 500 linhas
- [ ] As instruções explicam o "porquê", não só o "o quê"
- [ ] Há exemplos concretos de entrada/saída
- [ ] Conteúdo extenso foi movido para `references/`
- [ ] Scripts repetitivos estão em `scripts/`
- [ ] A skill foi testada com prompts realistas
- [ ] As instruções são claras para alguém que nunca viu o projeto

---

## Dicas Finais

1. **Comece simples.** Uma skill com apenas um SKILL.md já funciona perfeitamente. Adicione scripts e referências só quando sentir necessidade.

2. **Observe os padrões.** Se você percebe que está corrigindo o Claude nas mesmas coisas repetidamente, isso é um sinal perfeito para criar uma skill.

3. **Peça ajuda ao Claude.** Você pode simplesmente dizer "crie uma skill para [o que você quer]" e ele vai guiar você pelo processo, fazendo perguntas, escrevendo o rascunho e ajudando a testar.

4. **Atualize conforme necessário.** Skills não são estáticas. Conforme suas práticas evoluem, atualize as instruções para refletir o estado atual.

5. **Uma skill por domínio.** Evite criar uma skill gigante que cobre tudo. Prefira skills focadas em domínios específicos — é mais fácil de manter e o Claude ativa com mais precisão.
