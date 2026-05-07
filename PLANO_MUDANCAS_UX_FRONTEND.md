# Plano de Correcoes UX do Frontend

## Objetivo

Aplicar correcoes de UX no frontend para melhorar clareza de feedback, reduzir friccao em modais e corrigir comportamento de notificacoes e hero section.

## Escopo

1. Notificacoes devem desaparecer corretamente apos interacao e nao reaparecer indevidamente no polling.
2. Fluxos de criacao de anuncio devem exibir status claro do processo e validacoes antes da finalizacao.
3. Scrollbars de modais devem ficar padronizadas e menos intrusivas visualmente.
4. Modal de novo anuncio deve ficar menos verticalizado em telas maiores.
5. Frase da hero section nao pode sofrer clipping/corte visual.

## Arquivos-Alvo

- `p2ploot-frontend/src/components/common/Navbar.jsx`
- `p2ploot-frontend/src/components/PlayerMarket/CreateAdModal.jsx`
- `p2ploot-frontend/src/components/Home/HomePage.jsx`
- `p2ploot-frontend/src/index.css`

## Checklist de Execucao

- [x] Ajustar dismiss de notificacao por clique e persistencia em `localStorage`
- [x] Evitar reexibicao de notificacoes no polling
- [x] Implementar auto-close do dropdown de notificacoes fora de hover/foco
- [x] Adicionar painel de status de processo e validacao no modal de anuncio
- [x] Reestruturar layout do modal de anuncio para reduzir verticalizacao
- [x] Padronizar estilo de scrollbar via utilitario global `.custom-scrollbar`
- [x] Ajustar tipografia/overflow da hero para eliminar corte visual do titulo
- [x] Validar comportamento em PT/EN, desktop/mobile

## Criterios de Aceite

### Notificacoes
- Clique em notificacao de usuario remove o card da lista atual.
- Notificacao clicada nao reaparece no proximo polling enquanto estiver marcada como dispensada.
- Dropdown desktop fecha automaticamente quando perde hover/foco por um intervalo curto.

### Feedback de Processo
- Usuario visualiza claramente o status atual (ocioso, enviando imagem, publicando anuncio, sincronizando perfil).
- Antes de submeter, pendencias obrigatorias ficam visiveis no proprio modal.
- Botao de envio evita acao duplicada durante processamento.

### Modais e Scrollbars
- Scrollbar interna usa estilo padronizado e discreto.
- Modal de anuncio apresenta menos rolagem vertical em viewport media/grande.

### Hero Section
- Texto principal nao apresenta clipping/corte em PT e EN.
- Efeitos visuais de fundo nao cortam o conteudo textual.

## Observacoes de Implementacao

- As mudancas devem ser retrocompativeis com o fluxo atual.
- Evitar alteracoes de comportamento fora do escopo acima.

## Atualizacao de Relatorio (Hero Section)

- **Problema reportado em teste visual:** conteudo da hero ainda aparecia cortado em viewport desktop.
- **Ajustes aplicados (iterativos) em `p2ploot-frontend/src/components/Home/HomePage.jsx` e `p2ploot-frontend/src/index.css`:**
  - compactacao da hero para telas com pouca altura (`@media (max-height: 900px)`);
  - reducao de tipografia e espacamentos verticais da hero em baixa altura;
  - remocao da scrollbar visual da home sem bloquear a rolagem de conteudo;
  - restauracao da logo da hero acima do titulo principal.
- **Validacao visual final (viewport 1024x550):**
  - titulo completo da hero visivel sem corte;
  - scrollbar visual removida;
  - rolagem funcional mantida;
  - logo visivel na posicao esperada acima do titulo.
- **Status:** corrigido e validado.

## Atualizacao de Relatorio (Ambiente)

- **Banco:** porta `3307` em `LISTEN`.
- **Backend:** `next dev` ativo em `http://localhost:6110` (branch `temp/frontend-only-backend`).
- **Frontend:** `vite` ativo em `http://localhost:6111`.
- **Smoke checks:** `/pt` = `200`, `/en` = `200`, `/api/games` = `200`.

## Checklist de Validacao Final (QA)

### Dispositivos

- [ ] Desktop (1366x768 ou maior): abrir e interagir com notificacoes, modal de anuncio e home.
- [ ] Tablet (768x1024): validar rolagem de modal e legibilidade da hero.
- [ ] Mobile (390x844): validar drawer de notificacoes e fluxo de criacao de anuncio.

### Idiomas

- [ ] PT-BR: conferir textos de status, pendencias e hero sem corte visual.
- [ ] EN: repetir validacao para garantir ausencia de clipping no titulo da hero.

### Fluxos Criticos

- [ ] Notificacao de usuario: clicar, remover card da lista e confirmar que nao reaparece no polling seguinte.
- [ ] Notificacao desktop: confirmar fechamento automatico ao sair de hover/foco.
- [ ] Criar anuncio: validar painel de status em upload, envio e sincronizacao de perfil.
- [ ] Criar anuncio com erro: confirmar exibicao de pendencias e bloqueio de submit invalido.
- [ ] Modais com rolagem: verificar scrollbar discreta e consistente.
- [ ] Hero section: conferir titulo completo sem corte em breakpoints principais.
