# Plano de Correção de Bugs - P2PLOOT Frontend

## Visão Geral

Este documento detalha os bugs identificados através de análise de código no frontend da P2PLOOT e o plano para corrigi-los em PRs separados.

**Status Atual:**

- ✅ PR-1, PR-2 e PR-3: Concluídos e mergeados
- ✅ PR-4 e PR-5: Implementados localmente (aguardando commit/PR)
- 🟡 PR-6: Parcialmente implementado localmente (BUG-13)
- ⏳ Pendências atuais: BUG-20 (i18n), BUG-24 (qualidade ampla)

---

## Bugs Identificados

### BUG-01: Dropdown de notificações não fecha ao clicar fora

**Arquivo:** `src/components/common/Navbar.jsx`
**Descrição:** O dropdown de notificações (sino) permanece aberto quando o usuário clica fora dele.
**Prioridade:** Alta
**Status:** ✅ Corrigido no PR-1

### BUG-02: Dropdown do menu de usuário não fecha ao clicar fora

**Arquivo:** `src/components/common/Navbar.jsx`
**Descrição:** O dropdown do menu de usuário permanece aberto quando o usuário clica fora dele.
**Prioridade:** Alta
**Status:** ✅ Corrigido no PR-1

### BUG-03: Dropdown de idioma não fecha ao clicar fora

**Arquivo:** `src/components/common/Navbar.jsx`
**Descrição:** O dropdown de idioma permanece aberto quando o usuário clica fora dele.
**Prioridade:** Alta
**Status:** ✅ Corrigido no PR-1

### BUG-04: Notificações mobile não renderizam

**Arquivo:** `src/components/common/Navbar.jsx`
**Descrição:** O ícone de notificações e dropdown estão dentro de `hidden md:flex`, portanto não aparecem em telas móveis.
**Prioridade:** Alta
**Status:** ✅ Corrigido no PR-2

### BUG-05: Uso excessivo de alert() em vez de toasts

**Arquivos:** Múltiplos (~255 ocorrências)
**Descrição:** O app usa `alert()` e `confirm()` nativos em vez do wrapper `react-hot-toast` já existente em `utils/toast.jsx`.
**Prioridade:** Alta
**Status:** 🟡 Parcialmente corrigido no PR-3 + PR-4 local (fluxos críticos)

### BUG-08: Guild image upload desativado mas parece funcional

**Arquivo:** `src/components/Web3Games/CreateGuildWizard.jsx`
**Descrição:** O upload de imagem da guilda está desativado no código mas a UI sugere que funciona.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente (upload reativado no wizard)

### BUG-11: Filtro de jogos no PlayerMarket não marca seleção visualmente

**Arquivo:** `src/components/PlayerMarket/PlayerMarketPage.jsx`
**Descrição:** O componente usa `selectedItem` (singular) em vez de `selectedItems` (plural), causando bug na seleção visual.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente

### BUG-12: Detecção de créditos insuficientes falha silenciosamente em eventos

**Arquivo:** `src/components/Events/CreateEventModal.jsx` + `src/lib/api.js`
**Descrição:** Código testa `error.response?.status` mas a fetch API não popula `.response`, causando falha silenciosa.
**Prioridade:** Alta
**Status:** ✅ Corrigido localmente

### BUG-13: Erros de rede no AuthContext silenciados com console.warn

**Arquivo:** `src/contexts/AuthContext.jsx`
**Descrição:** Erros de rede são apenas logados com `console.warn` sem feedback visual ao usuário.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente (toast de conectividade)

### BUG-15: Falta feedback de loading em Guild

**Arquivo:** `src/components/Guild/GuildsPage.jsx`
**Descrição:** Operações na guilda não mostram indicadores de carregamento.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente

### BUG-16: Falta feedback de loading em Events

**Arquivo:** `src/components/Events/CreateEventModal.jsx` + `src/components/Events/EventDetailsModal.jsx`
**Descrição:** Operações em eventos não mostram indicadores de carregamento.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente

### BUG-17: Falta feedback de toast em Guild

**Arquivo:** `src/components/Guild/GuildsPage.jsx`
**Descrição:** Operações na guilda não mostram mensagens de sucesso/erro via toast.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente

### BUG-18: Falta feedback de toast em Events

**Arquivo:** `src/components/Events/EventsPage.jsx` + `src/components/Events/CreateEventModal.jsx` + `src/components/Events/EventDetailsModal.jsx`
**Descrição:** Operações em eventos não mostram mensagens de sucesso/erro via toast.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente

### BUG-19: Guild image upload desativado

**Arquivo:** `src/components/Web3Games/CreateGuildWizard.jsx`
**Descrição:** Upload de imagem da guilda está desativado no código.
**Prioridade:** Baixa
**Status:** ✅ Corrigido localmente

### BUG-20: Problemas de i18n em componentes

**Arquivos:** Múltiplos
**Descrição:** Alguns componentes não usam as chaves de tradução corretamente ou falta tradução.
**Prioridade:** Baixa
**Status:** ⏳ Pendente (não concluído nesta rodada)

### BUG-22: Uso de confirm() em vez de modal customizado

**Arquivos:** Múltiplos
**Descrição:** Diálogos de confirmação usam `confirm()` nativo em vez de modal customizado.
**Prioridade:** Média
**Status:** 🟡 Parcialmente corrigido no PR-3

### BUG-23: Falta feedback de loading em componentes de anúncios

**Arquivo:** `src/components/PlayerMarket/CreateAdModal.jsx`
**Descrição:** Criação de anúncios não mostra indicador de carregamento.
**Prioridade:** Média
**Status:** ✅ Corrigido localmente

### BUG-24: Problemas de qualidade de código

**Arquivos:** Múltiplos
**Descrição:** Diversos problemas de qualidade: código duplicado, funções não utilizadas, etc.
**Prioridade:** Baixa
**Status:** ⏳ Pendente (escopo amplo)

### BUG-25: Múltiplos dropdowns podem ficar abertos simultaneamente (mobile)

**Arquivo:** `src/components/common/Navbar.jsx`
**Descrição:** Em mobile, não há lógica para fechar outros dropdowns ao abrir um novo.
**Prioridade:** Média
**Status:** ✅ Corrigido no PR-2

---

## Plano de PRs

### ✅ PR-1: Corrigir dropdowns da Navbar (COMPLETO)

**Link:** [https://github.com/Bianca-campolongo/p2ploot-frontend/pull/13](https://github.com/Bianca-campolongo/p2ploot-frontend/pull/13)
**Commit:** `e3d98ea`
**Bugs:** BUG-01, BUG-02, BUG-03
**Alterações:**

- Adicionou refs: `notifRef`, `userMenuRef`, `langMenuRef`
- Adicionou `useEffect` com listener `mousedown` para fechar dropdowns ao clicar fora
- Modificou funções toggle para fechar outros dropdowns ao abrir um novo

---

### ✅ PR-2: Notificações mobile e multi-dropdowns (COMPLETO)

**Link:** [https://github.com/Bianca-campolongo/p2ploot-frontend/pull/14](https://github.com/Bianca-campolongo/p2ploot-frontend/pull/14)
**Commit:** `be8a682`
**Bugs:** BUG-04, BUG-25
**Arquivos:** `src/components/common/Navbar.jsx`
**Alterações:**

- Adicionou dropdown de notificações específico para mobile (drawer lateral)
- Escondeu dropdown de desktop em mobile (`md:block hidden`)
- Fecha menu mobile drawer ao abrir notificações (BUG-25)
- Permite visualização de notificações em telas pequenas (BUG-04)

---

### ✅ PR-3: Migrar alert() críticos para toasts (COMPLETO)

**Link:** [https://github.com/Bianca-campolongo/p2ploot-frontend/pull/15](https://github.com/Bianca-campolongo/p2ploot-frontend/pull/15)
**Commit:** `79569cc`
**Bugs:** BUG-05 (parcial), BUG-22
**Arquivos:**

- `src/components/PlayerMarket/CreateAdModal.jsx`
- `src/components/PlayerMarket/AdDetailsModal.jsx`
- `src/components/UserProfile/CreditRequestModal.jsx`
- `src/components/common/LoginModal.jsx`
**Alterações:**
- Substituiu `alert()` por `showToast.error()` em todos os arquivos
- Substituiu `window.confirm()` por toast informativo ou remoção de confirmação
- Adicionou `showToast.success()` para feedback de sucesso em operações críticas
- Melhorou UX com feedback visual integrado ao design do sistema

---

### ✅ PR-4: Toasts + loading em Guild/Events (IMPLEMENTADO LOCALMENTE)

**Status:** Aguardando commit/PR dedicado
**Bugs:** BUG-15, BUG-16, BUG-17, BUG-18, BUG-19, BUG-23
**Arquivos alterados:**

- `src/components/Guild/GuildsPage.jsx`
- `src/components/Events/EventsPage.jsx`
- `src/components/Events/CreateEventModal.jsx`
- `src/components/Events/EventDetailsModal.jsx`
- `src/components/PlayerMarket/CreateAdModal.jsx`
- `src/components/Web3Games/CreateGuildWizard.jsx`
**Alterações implementadas:**
- Adicionados indicadores de loading (`showToast.loading()`) em operações assíncronas
- Adicionado feedback de sucesso/erro via toast nos fluxos ajustados
- Removido uso de `alert()` nos fluxos críticos atualizados
- Reativada funcionalidade de upload de imagem da guilda no wizard

---

### ✅ PR-5: Bugs de lógica e estado (IMPLEMENTADO LOCALMENTE)

**Status:** Aguardando commit/PR dedicado
**Bugs:** BUG-08, BUG-11, BUG-12
**Arquivos alterados:**

- `src/components/PlayerMarket/PlayerMarketPage.jsx`
- `src/lib/api.js`
- `src/components/Events/CreateEventModal.jsx`
- `src/components/Web3Games/CreateGuildWizard.jsx`
**Alterações implementadas:**
- Corrigido `selectedItem` → `selectedItems` no PlayerMarket
- Corrigida detecção de erros em Events preservando `error.status`/`error.data` no client API
- Reativado upload de imagem da guilda no fluxo de criação

---

### 🟡 PR-6: Auth feedback + i18n + qualidade (PARCIALMENTE IMPLEMENTADO)

**Status:** Implementado localmente para BUG-13; pendente para BUG-20 e BUG-24
**Bugs:** BUG-13 (✅), BUG-20 (⏳), BUG-24 (⏳)
**Arquivos alterados:**

- `src/contexts/AuthContext.jsx`
**Alterações implementadas:**
- Adicionado feedback visual para erros de rede no AuthContext com toast
- Adicionado controle para evitar spam de notificações repetidas de conectividade
**Pendências:**
- Melhorar i18n em componentes que ainda não usam tradução
- Refatorar código duplicado e remover funções não utilizadas em escopo mais amplo

---

## Infraestrutura

**Status Atual:**

- Frontend (Vite): Rodando na porta 6111 ✅
- Backend (Next.js): Rodando na porta 6110 ✅
- MySQL: Rodando na porta 3306 (backend espera 3307) ⚠️

**Ação necessária:**

- Configurar MySQL para usar porta 3307 OU atualizar backend para usar 3306
- Criar banco de dados `talon` se não existir

---

## Notas

- O wrapper de toast já existe em `src/utils/toast.jsx` e está importado em `src/main.jsx`
- O `ToastContainer` já está configurado em `src/main.jsx`
- A maioria dos bugs são de UX/feedback ao usuário, não de funcionalidade crítica
- A clusterização em PRs separados facilita review e rollback