# Justificativa dos Workflows/Skills de Frontend

Data: 9 de maio de 2026

## Objetivo do documento

Este documento registra os motivos da criação dos workflows/skills de frontend adicionados em `.windsurf/workflows/`, para que a utilidade deles continue transparente em futuras sessões, revisões, PRs e incrementos do projeto P2P LOOT.

Os workflows foram criados para melhorar a estrutura de trabalho agentico, acelerar a revisão humana e aumentar a assertividade das iterações de código, especialmente durante a modularização incremental do frontend.

## Workflows criados

Os workflows adicionados foram:

- `.windsurf/workflows/frontend-increment-preflight.md`
- `.windsurf/workflows/frontend-extraction-playbook.md`
- `.windsurf/workflows/frontend-visual-evidence-pack.md`
- `.windsurf/workflows/frontend-review-bundle.md`

## Contexto que motivou a criação

O frontend do P2P LOOT passou a receber incrementos de modularização para reduzir arquivos grandes, preservar contratos e facilitar manutenção com agentes de IA.

A modularização recente em `Web3Games` mostrou que incrementos pequenos e bem documentados reduzem risco e tornam o código mais revisável. Porém, também evidenciou necessidades recorrentes:

- verificar estado Git antes de alterar arquivos;
- proteger mudanças locais e submodules;
- preservar contratos de API, props, rotas e exports;
- manter PRs pequenos e revisáveis;
- capturar evidências visuais antes/depois em desktop e mobile;
- documentar validações executadas;
- evitar perda de contexto entre sessões e agentes;
- padronizar como separar componentes, hooks e utils;
- entregar à revisão humana um resumo objetivo do que mudou e como validar.

Sem workflows explícitos, essas práticas dependem da memória da sessão ou da interpretação individual do agente. Isso aumenta risco de inconsistência, retrabalho, revisão lenta e regressões.

## Problemas que os workflows resolvem

### 1. Perda de contexto entre sessões

Agentes diferentes ou novas sessões podem não lembrar:

- qual branch deve ser usada;
- quais arquivos estavam modificados;
- qual build precisa de `VITE_API_URL`;
- onde registrar evidências;
- quais contratos não podem mudar;
- qual foi a estratégia de modularização já adotada.

Os workflows transformam esse conhecimento em instruções consultáveis e repetíveis.

### 2. Risco de alterações fora de escopo

Em um projeto com frontend, backend, submodule/gitlink, documentação e PRs paralelos, é fácil misturar mudanças não relacionadas.

O preflight reduz esse risco exigindo:

- `git status --short`;
- identificação de arquivos locais modificados;
- definição de arquivos-alvo;
- definição de arquivos a evitar;
- classificação de risco antes de editar.

### 3. Refatorações pouco revisáveis

Arquivos grandes como modais, páginas e seções complexas podem gerar diffs difíceis de revisar.

O extraction playbook orienta o agente a separar responsabilidades de forma previsível:

- UI em componentes;
- estado e efeitos em hooks;
- transformação pura em utils;
- arquivo original como shell/orquestrador quando necessário.

Isso reduz o custo cognitivo da revisão e diminui a chance de regressão.

### 4. Falta de evidência visual auditável

Mudanças visuais ou refatorações sem alteração esperada de UX precisam de prova comparativa para revisão posterior.

O visual evidence pack padroniza:

- antes/depois desktop;
- antes/depois mobile;
- estados relevantes como modal aberto, loading, erro, sucesso e empty state;
- pasta de armazenamento em `docs/evidencias/frontend/incremento-N-nome-curto/`;
- documentação do resultado visual como preservado ou mudança intencional.

Isso torna a revisão mais objetiva e protege o projeto contra regressões visuais silenciosas.

### 5. Revisão humana lenta ou incompleta

Sem um pacote final, a pessoa revisora precisa reconstruir contexto a partir do diff.

O review bundle cria uma entrega padronizada com:

- resumo do incremento;
- arquivos criados/modificados;
- contratos preservados;
- validação executada;
- evidências visuais;
- ordem recomendada de revisão;
- template de PR.

Isso acelera a revisão e melhora a tomada de decisão.

## Utilidade de cada workflow

### `frontend-increment-preflight`

Serve para preparar qualquer incremento antes de editar código.

Utilidade futura:

- evita começar em branch errada;
- protege arquivos modificados por outra pessoa;
- obriga definição de escopo;
- identifica risco inicial;
- confirma se haverá necessidade de evidência visual;
- alinha a tarefa às regras ativas do projeto.

Quando usar:

- antes de modularizações;
- antes de mudanças de UX;
- antes de mexer em modais, páginas, formulários ou fluxos sensíveis;
- antes de criar uma nova branch ou PR.

### `frontend-extraction-playbook`

Serve para orientar extrações de código no frontend.

Utilidade futura:

- reduz decisões improvisadas de arquitetura;
- mantém padrão entre agentes;
- favorece arquivos menores;
- preserva contratos públicos;
- evita misturar backend, API e UI em uma mesma mudança;
- facilita revisão por responsabilidade.

Quando usar:

- ao quebrar arquivos grandes;
- ao extrair hooks;
- ao criar componentes compartilhados;
- ao mover filtros, mappers, permissões ou formatadores para utils;
- ao transformar arquivo grande em shell/orquestrador.

### `frontend-visual-evidence-pack`

Serve para capturar e registrar evidências visuais de alterações frontend/UX.

Utilidade futura:

- comprova equivalência visual em refatorações;
- documenta mudanças intencionais de UX;
- melhora auditoria de incrementos;
- reduz discussões subjetivas sobre layout;
- cria histórico visual para futuras comparações.

Quando usar:

- qualquer alteração visual;
- modais;
- formulários;
- telas responsivas;
- estados de loading/erro/sucesso;
- refatorações sem mudança visual esperada, quando possível.

### `frontend-review-bundle`

Serve para preparar a entrega final de um incremento.

Utilidade futura:

- facilita revisão humana;
- padroniza PRs;
- registra validação;
- explicita contratos preservados;
- mostra onde revisar primeiro;
- reduz retrabalho após feedback.

Quando usar:

- ao final de cada incremento;
- antes de commit;
- antes de abrir PR;
- antes de pedir revisão;
- quando for necessário retomar uma tarefa em outra sessão.

## Benefícios esperados para o projeto

### Mais velocidade

Os workflows eliminam perguntas recorrentes e reduzem tempo de setup mental. O agente pode seguir uma sequência conhecida em vez de redescobrir o processo a cada tarefa.

### Mais assertividade

A definição prévia de escopo, contratos e validação reduz a chance de alterações indevidas e regressões.

### Melhor revisão

Com evidências visuais, resumo de contratos e ordem de leitura, a revisão deixa de depender apenas do diff bruto.

### Melhor continuidade agentica

As instruções ficam persistidas no repositório. Mesmo com troca de sessão, agente ou contexto, o processo continua reproduzível.

### Menor risco em refatorações

Extrações passam a seguir critérios consistentes, reduzindo o risco de quebrar fluxo, payload, props ou rotas.

### Mais transparência histórica

No futuro, será possível entender não apenas que os workflows existem, mas por que foram criados e qual problema resolvem.

## Relação com regras existentes

Os workflows operacionalizam regras já registradas em:

```text
regras/CONSULTA_REGRAS.md
```

Principalmente:

- incrementos frontend/UX devem ter evidência visual;
- incrementos devem continuar pequenos e validáveis;
- contratos e backend devem ser preservados;
- alterações locais devem ser protegidas;
- build frontend exige `VITE_API_URL`;
- commits devem seguir Conventional Commits;
- documentação incremental deve ser atualizada.

## Relação com a modularização incremental

Os workflows também apoiam o plano registrado em:

```text
MODULARIZACAO_INCREMENTAL_FRONTEND.md
```

Eles devem ser usados para manter a modularização previsível, auditável e revisável, especialmente nos próximos incrementos recomendados:

- filtros e seções de `Web3GamesHome.jsx`;
- comentários de `GameDetailsPage.jsx`;
- hero e dados técnicos da página de detalhes;
- blocos menores da página de blockchain;
- futuros P0 como `AdDetailsModal.jsx`, `GuildAuctionSection.jsx` e `UserProfilePage.jsx`.

## Como usar em conjunto

Fluxo recomendado para incrementos frontend:

1. Usar `frontend-increment-preflight` antes de qualquer edição.
2. Usar `frontend-extraction-playbook` durante modularizações.
3. Usar `frontend-visual-evidence-pack` quando houver impacto visual/UX.
4. Usar `frontend-review-bundle` ao finalizar o incremento ou preparar PR.

## Critério de sucesso dos workflows

Os workflows estarão cumprindo seu papel se:

- os PRs ficarem menores e mais fáceis de revisar;
- houver menos regressões visuais;
- o agente tocar menos arquivos fora de escopo;
- a documentação incremental permanecer atualizada;
- a revisão humana for mais rápida;
- novos agentes conseguirem continuar o trabalho sem reconstruir todo o histórico.

## Observação final

Esses workflows não substituem revisão humana nem decisão de produto. Eles existem para padronizar o processo, reduzir riscos operacionais e tornar a colaboração entre pessoa revisora e agentes de IA mais previsível, rastreável e eficiente.
