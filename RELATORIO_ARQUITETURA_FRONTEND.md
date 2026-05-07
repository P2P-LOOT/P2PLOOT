# Relatório de Arquitetura e Manutenibilidade do Frontend

Data da investigação: 7 de maio de 2026

Escopo analisado: `p2ploot-frontend`

## Capturas da visualização

As imagens abaixo são exports estáticos em SVG baseados na visualização em canvas do Cursor. Elas servem para leitura fora do Cursor; a versão interativa do canvas continua disponível no ambiente do Cursor.

Visão geral do relatório de arquitetura frontend

Achados priorizados e plano recomendado

## Conclusão executiva

A hipótese se confirmou: o frontend não parece difícil de alterar por um problema pontual de ferramenta, mas por concentração de responsabilidade e falta de proteção de tooling.

O sistema compila, mas alterações básicas tendem a atravessar arquivos grandes, estado espalhado, chamadas de API dentro de componentes e padrões inconsistentes. Isso aumenta o custo de entendimento, revisão e validação manual.

## Métricas levantadas

- `73` arquivos analisados em `p2ploot-frontend/src`.
- `24.949` linhas em `src`.
- `13` arquivos com mais de `500` linhas.
- `7` arquivos com mais de `1000` linhas.
- `463` ocorrências de `useState`.
- `100` ocorrências de `useEffect`.
- `181` chamadas `api.`.
- `41` usos de `localStorage`.
- `3` usos de `sessionStorage`.

## Arquivos mais críticos por tamanho e concentração


| Arquivo                                                 | Linhas | `useState` | `useEffect` | `api.` | `localStorage` |
| ------------------------------------------------------- | ------ | ---------- | ----------- | ------ | -------------- |
| `src/components/Guild/GuildAuctionSection.jsx`          | 2403   | 34         | 9           | 16     | 14             |
| `src/components/Web3Games/Web3GamesPage.jsx`            | 1736   | 29         | 6           | 12     | 6              |
| `src/components/Guild/GuildManagementPage.jsx`          | 1479   | 36         | 7           | 15     | 1              |
| `src/components/PlayerMarket/AdDetailsModal.jsx`        | 1476   | 27         | 8           | 20     | 0              |
| `src/UserProfilePage.jsx`                               | 1442   | 27         | 6           | 10     | 0              |
| `src/components/Guild/GuildDkpModals.jsx`               | 1347   | 28         | 4           | 5      | 0              |
| `src/components/Admin/AdminDashboard.jsx`               | 1289   | 43         | 4           | 17     | 0              |
| `src/lib/api.js`                                        | 851    | 0          | 0           | 0      | 1              |
| `src/components/common/Navbar.jsx`                      | 757    | 7          | 5           | 3      | 6              |
| `src/components/Guild/modals/MemberManagementModal.jsx` | 756    | 12         | 3           | 6      | 0              |


## Achados priorizados

### Crítico: componentes monolíticos concentram fluxo inteiro

Há vários arquivos de página/componente com mais de mil linhas. O caso mais forte é `GuildAuctionSection.jsx`, com UI, estado, filtros, persistência local, chamadas de API, importação de itens, modais e renderização no mesmo módulo.

Impacto: uma mudança pequena exige entender muitas responsabilidades ao mesmo tempo, aumentando risco de regressão e conflito.

### Alto: `api.js` virou uma fachada única grande demais

`src/lib/api.js` tem cerca de `851` linhas e reúne métodos de autenticação, perfil, mensagens, anúncios, guildas, DKP, leilões, jogos, tickets, eventos e admin no mesmo objeto `api`.

Impacto: mudanças de contrato ou domínio passam pelo mesmo arquivo, dificultando ownership, revisão e testes por feature.

### Alto: UI, estado, persistência e API estão misturados

Arquivos como `UserProfilePage.jsx`, `AdminDashboard.jsx`, `AdDetailsModal.jsx`, `GuildManagementPage.jsx` e `Web3GamesPage.jsx` fazem renderização e orquestração de dados no mesmo nível.

Impacto: é difícil alterar layout sem tocar em fluxo de dados, ou alterar fluxo de dados sem atravessar JSX extenso.

### Alto: há bug real que lint deveria capturar

Em `Web3GamesPage.jsx`, o componente `GameCard` usa `const { t } = useTranslation()`, mas navega com `i18n.language`. O identificador `i18n` não existe nesse escopo.

Impacto: clique no card ou na blockchain pode quebrar em runtime. Um ESLint funcional pegaria `no-undef`.

### Médio: padrões de rede e importação são inconsistentes

`TokensPage.jsx` e `MercadoPage.jsx` usam `fetch` direto para CoinGecko, enquanto a maior parte do app usa `api`. O alias `@` existe em `vite.config.js`, mas não é usado em `src`.

Impacto: o mantenedor precisa descobrir exceções caso a caso; tratamento de erro, cache e mocks ficam dispersos.

### Médio: há sinais de migração incompleta e código morto

`@supabase/supabase-js` segue instalado, e imports Supabase aparecem comentados em módulos de Guild. `MercadoPage.jsx` exporta uma página, mas não aparece nas rotas em `App.jsx`.

Impacto: a base comunica histórias antigas que não são mais verdade, aumentando o tempo para entender o estado atual.

### Médio: i18n centralizado demais e política de idioma desalinhada

`i18n.js` define `fallbackLng: 'en'`, enquanto `App.jsx` redireciona a raiz para `/pt`. Cada arquivo de tradução tem cerca de `1450` linhas.

Impacto: features novas tendem a gerar conflitos em arquivos de tradução grandes, e a regra de idioma padrão fica espalhada.

### Processo: tooling não protege alterações futuras

Não foram encontrados arquivos `*.test.*` ou `*.spec.*`. `npm run lint` falha porque não existe configuração ESLint versionada. O build passa com `VITE_API_URL`, mas emite muitos avisos de dependências.

Impacto: sem lint e testes mínimos, o custo de confiança recai em revisão manual e clique manual no navegador.

## Validações executadas

### Lint

Comando executado:

```bash
npm run lint
```

Resultado: falhou porque o ESLint não encontrou arquivo de configuração.

Mensagem principal:

```text
ESLint couldn't find a configuration file.
```

### Build

Comando executado:

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

Resultado: passou.

Observações:

- Build levou cerca de `1m51s`.
- O Vite transformou `7921` módulos.
- Houve muitos avisos vindos de dependências, especialmente `@privy-io/react-auth`.
- Bundle final indicou chunks grandes, incluindo `GuildManagementPage` com `225.26 kB` e `index` com `396.33 kB`.

## Plano recomendado

### 0. Estabilizar antes de refatorar

- Adicionar configuração ESLint versionada.
- Corrigir o bug de `i18n` em `GameCard`.
- Decidir o destino de `MercadoPage.jsx`: conectar a uma rota ou remover/arquivar.
- Remover dependência e comentários Supabase se a migração já foi concluída.
- Alinhar idioma padrão entre `App.jsx` e `i18n.js`.

### 1. Separar API por domínio

Quebrar `src/lib/api.js` em módulos por domínio, mantendo um `request()` compartilhado:

- `api/auth.js`
- `api/profile.js`
- `api/guild.js`
- `api/market.js`
- `api/events.js`
- `api/admin.js`

Manter compatibilidade temporária via barrel export, se necessário:

```js
export const api = {
  ...authApi,
  ...profileApi,
  ...guildApi,
  ...marketApi,
  ...eventsApi,
  ...adminApi,
};
```

### 2. Extrair hooks de dados

Criar hooks por feature para tirar loading, erro, fetch e transformação de dados dos componentes:

- `useGuildAuctions`
- `useProfileAds`
- `useProfileGuilds`
- `useAdminModerators`
- `useGameDetails`
- `useGameVotes`

Objetivo: componentes renderizam; hooks orquestram dados.

### 3. Fatiar páginas grandes

Começar pelos arquivos acima de `1000` linhas, priorizando os que mais mudam:

1. `GuildAuctionSection.jsx`
2. `Web3GamesPage.jsx`
3. `GuildManagementPage.jsx`
4. `AdDetailsModal.jsx`
5. `UserProfilePage.jsx`
6. `GuildDkpModals.jsx`
7. `AdminDashboard.jsx`

Extrair primeiro:

- filtros;
- cards/listas;
- modais;
- reducers de estado;
- helpers de formatação;
- hooks de carregamento.

Evitar fazer redesign visual junto com a refatoração estrutural.

### 4. Melhorar rotas e i18n

- Mover `GameDetailsPage` e `BlockchainDetailsPage` para arquivos próprios.
- Evitar lazy load de named exports a partir de um arquivo monolítico.
- Considerar uma página 404 estável para rotas inválidas.
- Dividir traduções por namespace/feature quando houver mudança grande em i18n.

### 5. Adicionar testes mínimos

Começar com cobertura de baixo custo e alto impacto:

- testes unitários para `request()`;
- testes de helpers de permissão;
- testes de formatadores;
- testes de reducers novos;
- smoke tests das rotas críticas.

## Ordem prática sugerida

1. Configurar ESLint e corrigir erros óbvios.
2. Corrigir `GameCard` e revisar rotas fallback.
3. Remover ou formalizar código legado Supabase.
4. Separar `api.js` por domínio sem alterar comportamento.
5. Extrair hooks dos arquivos maiores.
6. Quebrar páginas grandes em componentes menores.
7. Adicionar testes conforme os fluxos forem extraídos.

## Conclusão

O frontend está funcional, mas a arquitetura atual torna mudanças simples mais caras do que deveriam ser. O principal problema é a concentração de responsabilidades: páginas grandes demais, API centralizada, estado granular espalhado e baixo apoio de lint/testes.

A recomendação é estabilizar tooling primeiro e refatorar incrementalmente por domínio, começando pelos arquivos que concentram maior volume de estado, chamadas de API e persistência local.