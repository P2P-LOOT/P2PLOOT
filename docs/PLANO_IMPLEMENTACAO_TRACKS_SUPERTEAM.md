# Plano de ataque para implementação das features por track Superteam

Data: 2026-05-09
Projeto: P2P LOOT

## 1. Tese principal

A implementação deve reaproveitar uma mesma base de produto para várias tracks:

> **P2P LOOT é um marketplace gamer Web3 com compra segura, escrow em stablecoin, privacidade opcional e ferramentas de reputação para compradores e vendedores de ativos digitais de jogos.**

Essa tese permite aplicar o mesmo fluxo central em várias submissões:

- **Cloak / Privacy:** pagamento privado, identidade protegida e metadata mínima.
- **Tether:** liquidação em USDT.
- **Superteam Brasil:** produto brasileiro com caso real para gamers.
- **Eitherway / Solflare / QuickNode / Birdeye:** dApp live com wallet, RPC e dados de mercado.
- **RPC Fast:** infraestrutura confiável para leitura e confirmação on-chain.
- **Adevar:** pacote auditável de segurança para escrow e pagamentos.
- **Dune:** dashboard de dados on-chain e métricas de marketplace.
- **Jupiter:** pagamento com qualquer token via swap para stablecoin.
- **KIRAPAY:** checkout/link de pagamento alternativo.
- **SagaPad / Zerion:** agentes para growth, monitoramento e automação.

---

## 2. Estratégia de implementação

### 2.1. Construir primeiro um core reutilizável

Antes de criar features isoladas para cada track, implementar um núcleo comum:

1. **Checkout Web3 unificado**
  - Seleção de moeda: `USDC`, `USDT`, `SOL` e, depois, “qualquer token via Jupiter”.
  - Conexão com wallet Solana.
  - Cotação, taxa da plataforma, taxa de privacidade e total final.
  - Criação de transação e confirmação on-chain.
2. **Escrow de marketplace**
  - Estados claros: `draft`, `initialized`, `funded`, `seller_confirmed`, `released`, `refund_requested`, `refunded`, `cancelled`, `disputed`.
  - Registro de eventos por transação.
  - Links para explorer.
  - Fluxo de liberação, reembolso e disputa.
3. **Camada de privacidade**
  - Toggle de privacidade do vendedor no anúncio.
  - Toggle de privacidade do comprador no checkout.
  - Mascaramento de wallet em UI pública.
  - Metadata mínima no escrow.
  - Integração Cloak ou equivalente para pagamento privado.
4. **Infraestrutura on-chain confiável**
  - RPC configurável por ambiente.
  - Healthcheck de RPC.
  - Confirmação de transação com retry.
  - Registro de falhas e status técnico.
5. **Analytics e evidência para submissão**
  - Eventos on-chain ou indexáveis.
  - Métricas: volume, número de escrows, taxa de conclusão, tempo médio de entrega, disputas e wallets ativas.
  - Dashboard ou relatório público.

### 2.2. Não concentrar tudo no modal de anúncio

O fluxo atual de negociação/escrow está muito concentrado no `AdDetailsModal.jsx`. Para implementar rápido sem aumentar risco:

- Extrair componentes de checkout, painel de escrow, painel de privacidade e timeline de eventos.
- Criar hooks para carregar wallet/status/escrow.
- Manter o modal como orquestrador visual, não como dono de toda regra de negócio.

Sugestão de módulos futuros:

- `PlayerMarket/components/escrow/EscrowPanel.jsx`
- `PlayerMarket/components/escrow/EscrowTimeline.jsx`
- `PlayerMarket/components/checkout/Web3CheckoutPanel.jsx`
- `PlayerMarket/components/privacy/PrivacyOptionsPanel.jsx`
- `PlayerMarket/hooks/useEscrowDeal.js`
- `PlayerMarket/hooks/useWeb3Checkout.js`
- `PlayerMarket/utils/escrowFormatting.js`

---

## 3. Fases de implementação

## Fase 0 — Preparação de submissão e baseline

**Objetivo:** estabilizar o projeto para implementar e demonstrar as tracks.

### Features necessárias

- Criar branch principal de hackathon.
- Garantir build limpo com `VITE_API_URL` configurado.
- Criar `.env.example` com variáveis Web3 necessárias.
- Documentar fluxo atual de marketplace, chat, escrow e privacidade.
- Preparar seed/demo com comprador, vendedor, anúncio e conversa.

### Entregáveis

- Documento de arquitetura curta.
- Demo local reproduzível.
- Checklist de variáveis de ambiente.
- Roteiro de vídeo de 2 a 3 minutos.

### Tracks beneficiadas

- Todas.

---

## Fase 1 — Checkout Web3 + escrow funcional

**Objetivo:** ter um fluxo demonstrável de compra segura de ponta a ponta.

### Features necessárias

- Conectar wallet Solana no frontend.
- Mostrar wallets vinculadas ao perfil.
- Permitir iniciar escrow a partir de uma conversa de anúncio.
- Calcular valor, quantidade, taxa da plataforma e total.
- Criar transação de pagamento para vault/escrow.
- Confirmar transação via RPC.
- Atualizar status do escrow para `funded`.
- Permitir vendedor marcar entrega como concluída.
- Permitir comprador liberar pagamento.
- Permitir abrir disputa.
- Exibir timeline de eventos.
- Exibir link da transação no Solana Explorer.

### Entregáveis

- Compra segura funcionando em devnet ou mainnet controlada.
- Evento de pagamento confirmado.
- UI clara para comprador e vendedor.
- Histórico de eventos do escrow.

### Tracks beneficiadas

- Tether.
- Cloak.
- Privacy Track.
- Eitherway/Solflare/QuickNode/Birdeye.
- RPC Fast.
- 100xDevs.
- Superteam Brasil.
- Adevar.

---

## Fase 2 — USDT e stablecoin-first checkout

**Objetivo:** transformar o checkout em uma experiência de dólar digital para gamers.

### Features necessárias

- Adicionar `USDT` como moeda suportada no cadastro de anúncio.
- Mapear mint de `USDT` por rede.
- Validar decimals e conversão `amountUi` → `amountRaw`.
- Criar pagamento SPL Token para USDT.
- Mostrar saldo de USDT da wallet.
- Tratar falta de associated token account quando aplicável.
- Atualizar filtros/labels de marketplace para stablecoins.
- Criar copy de produto: “compra segura com dólar digital”.

### Entregáveis

- Anúncio com preço em USDT.
- Checkout pagando em USDT.
- Escrow com status confirmado.
- Demonstração de compra internacional com preço estável.

### Tracks beneficiadas

- Tether Frontier Hackathon Track.
- Superteam Brasil.
- 100xDevs.
- Adevar.
- Dune.

---

## Fase 3 — Privacidade com Cloak e metadata mínima

**Objetivo:** criar o principal diferencial do P2P LOOT: compra segura privada.

### Features necessárias

- Ativar opção “proteger identidade do vendedor” no anúncio.
- Ativar opção “pagamento privado” para comprador no checkout.
- Ocultar ou mascarar wallet do vendedor em anúncios e negociação.
- Guardar no escrow apenas metadata necessária.
- Incluir cálculo da taxa de privacidade.
- Integrar Cloak para pagamento privado, se a API/SDK permitir no prazo.
- Caso a integração completa não caiba no prazo, entregar um MVP com:
  - flags de privacidade;
  - metadata reduzida;
  - UI final;
  - fluxo de pagamento preparado;
  - documentação clara do ponto de integração.
- Criar recibo privado com prova operacional, sem expor dados sensíveis.

### Entregáveis

- Fluxo “Comprar com privacidade”.
- Anúncio com vendedor protegido.
- Escrow com metadata de privacidade.
- Tela de detalhes técnicos mostrando o que é público e o que é privado.
- Demonstração do risco resolvido: vendedor/comprador não precisam expor wallet publicamente.

### Tracks beneficiadas

- Cloak Track.
- Privacy Track.
- Superteam Brasil.
- 100xDevs.
- Adevar.

---

## Fase 4 — Solflare, QuickNode/RPC Fast e Birdeye

**Objetivo:** deixar a dApp live, confiável e com dados de mercado.

### Features necessárias

- Validar conexão com Solflare.
- Configurar RPC provider por ambiente.
- Criar healthcheck de RPC no backend.
- Criar fallback ou mensagem clara quando RPC falhar.
- Confirmar transação com retry/backoff.
- Mostrar status técnico da rede no checkout.
- Integrar Birdeye para cotação de tokens, quando útil.
- Exibir preço estimado em USD para SOL/outros tokens.

### Entregáveis

- DApp live com wallet Solflare.
- RPC dedicado configurado.
- Confirmação de pagamento mais confiável.
- Preços de token exibidos no checkout.

### Tracks beneficiadas

- Eitherway/Solflare/QuickNode/Birdeye.
- RPC Fast.
- 100xDevs.
- Superteam Brasil.

---

## Fase 5 — Jupiter: pagar com qualquer token

**Objetivo:** melhorar conversão de compra permitindo que o usuário pague com o token que já tem.

### Features necessárias

- Criar opção “Pagar com outro token”.
- Listar tokens da wallet.
- Buscar quote no Jupiter.
- Mostrar rota, slippage, taxa e valor final.
- Executar swap para `USDC` ou `USDT`.
- Encadear swap + depósito no escrow, se tecnicamente viável.
- Caso não caiba em uma transação, dividir em duas etapas claras:
  1. converter token;
  2. pagar escrow.
- Salvar assinatura da transação de swap no escrow/eventos.

### Entregáveis

- Usuário paga com token alternativo.
- Vendedor recebe stablecoin esperada.
- UI transparente sobre cotação e slippage.

### Tracks beneficiadas

- Jupiter.
- Tether.
- Eitherway/Birdeye.
- Superteam Brasil.
- 100xDevs.

---

## Fase 6 — Analytics e Dune

**Objetivo:** provar tração, transparência e saúde do marketplace.

### Features necessárias

- Definir eventos indexáveis:
  - escrow criado;
  - escrow fundado;
  - entrega confirmada;
  - pagamento liberado;
  - reembolso;
  - disputa;
  - taxa da plataforma.
- Garantir que transações relevantes estejam em endereços/programas rastreáveis.
- Criar tabela de referência entre `escrowDeal` e assinatura on-chain.
- Criar dashboard com:
  - GMV;
  - volume por jogo;
  - stablecoin mais usada;
  - taxa de conclusão;
  - tempo médio de entrega;
  - disputas por categoria;
  - compradores/vendedores ativos.
- Criar uma página interna simples com métricas operacionais off-chain, se Dune não cobrir tudo.

### Entregáveis

- Dashboard Dune público ou plano detalhado com queries.
- Métricas para pitch.
- Evidência de atividade do marketplace.

### Tracks beneficiadas

- Dune Analytics.
- Superteam Brasil.
- 100xDevs.
- Adevar.
- Tether.

---

## Fase 7 — Segurança, auditoria e pacote Adevar

**Objetivo:** transformar o fluxo de escrow em algo auditável.

### Features necessárias

- Criar threat model curto:
  - comprador malicioso;
  - vendedor malicioso;
  - disputa falsa;
  - pagamento não confirmado;
  - manipulação de preço/quantidade;
  - exposição de wallet;
  - replay de transação;
  - abuso de permissões admin.
- Definir invariantes:
  - apenas participantes da conversa podem criar/ler escrow;
  - apenas comprador pode fundar/liberar;
  - apenas vendedor pode confirmar entrega;
  - status terminal não pode voltar para status ativo;
  - valor confirmado on-chain precisa bater com valor do deal;
  - disputa bloqueia liberação automática.
- Criar testes de backend para rotas de escrow.
- Criar testes para normalização de rede, valores e permissões.
- Documentar contratos/programas, se existirem.
- Criar runbook de incidentes.

### Entregáveis

- Pacote de auditoria.
- Lista de riscos e mitigação.
- Testes mínimos.
- Escopo claro para Adevar.

### Tracks beneficiadas

- Adevar Labs.
- Tether.
- Cloak.
- Privacy Track.
- Superteam Brasil.
- 100xDevs.

---

## Fase 8 — KIRAPAY checkout alternativo

**Objetivo:** testar uma via de pagamento simplificada ou complementar.

### Features necessárias

- Validar documentação/API do KIRAPAY.
- Criar opção “Pagar com KIRAPAY” no checkout.
- Criar payment link ou sessão de pagamento.
- Receber callback/webhook de pagamento.
- Mapear status do KIRAPAY para status do escrow.
- Salvar referência externa no deal.
- Exibir comprovante no chat/escrow.

### Entregáveis

- Checkout com KIRAPAY.
- Pagamento externo refletindo no escrow.
- Demonstração de onboarding mais simples para usuários não técnicos.

### Tracks beneficiadas

- KIRAPAY.
- Superteam Brasil.
- 100xDevs.

---

## Fase 9 — Benefícios in-wallet / theMiracle

**Objetivo:** criar campanha de ativação e benefício dentro de wallet.

### Features necessárias

- Criar benefício de primeira compra:
  - taxa zero;
  - desconto em escrow;
  - proteção privada gratuita;
  - badge de vendedor verificado;
  - cashback simbólico.
- Criar regra de elegibilidade.
- Criar página/estado de benefício no perfil.
- Registrar uso do benefício em evento.
- Criar copy de campanha.

### Entregáveis

- Benefício ativável por wallet.
- Demonstração de campanha de aquisição.
- Métrica de conversão.

### Risco

- A track indica restrição regional para Espanha. Só priorizar se o time for elegível ou se houver parceiro elegível.

### Tracks beneficiadas

- theMiracle.
- Superteam Brasil, como inspiração de growth.

---

## Fase 10 — Agentes: Zerion e SagaPad

**Objetivo:** criar automações auxiliares para operação e crescimento.

### 10.1. Agente on-chain para escrow

#### Features necessárias

- Monitorar escrows pendentes.
- Detectar pagamento não confirmado.
- Alertar comprador/vendedor sobre prazos.
- Sugerir liberação, reembolso ou abertura de disputa.
- Gerar resumo de eventos para suporte/moderação.

#### Entregáveis

- Bot/agente de monitoramento.
- Relatório automático por negociação.
- Alertas operacionais.

#### Risco

- Zerion pode ter restrições de ecossistema/região. Validar elegibilidade antes de priorizar.

### 10.2. Agente de growth para X

#### Features necessárias

- Gerar posts com destaques de marketplace.
- Criar threads educativas sobre compra segura.
- Gerar conteúdo por jogo/categoria.
- Criar calendário de publicação.
- Sugerir respostas para comunidade.
- Preparar assets curtos para pitch.

#### Entregáveis

- Skill/agente de conteúdo.
- Calendário de posts.
- Exemplos de posts para lançamento.

### Tracks beneficiadas

- Zerion.
- SagaPad.
- Superteam Brasil.

---

## 4. Plano por track


| Track                                    | MVP mínimo para aplicar                              | Features prioritárias                                                                                  | Demo ideal                                                         | Prioridade |
| ---------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ | ---------- |
| **Cloak Track**                          | Compra segura com privacidade no pagamento.          | Toggle de privacidade, metadata mínima, mascaramento de wallet, taxa de privacidade, integração Cloak. | Comprar item gamer sem expor wallet pública do vendedor/comprador. | **P0**     |
| **Tether Frontier Hackathon Track**      | Checkout/escrow em USDT.                             | Suporte a USDT, SPL Token, saldo, mint por rede, confirmação on-chain.                                 | Comprar item com USDT e liberar pagamento ao vendedor.             | **P0**     |
| **Side Track Superteam Brasil**          | Produto full-stack demonstrável com caso brasileiro. | Fluxo completo, PT-BR, seed/demo, pitch, vídeo, estabilidade.                                          | Marketplace gamer brasileiro com compra segura em stablecoin.      | **P0**     |
| **Privacy Track — MagicBlock/ST MY/SNS** | Privacidade aplicada a identidade/pagamento.         | Alias, wallet mascarada, metadata mínima, UX privada, possível SNS/MagicBlock após validar docs.       | Negociação privada com compra segura.                              | **P1**     |
| **Eitherway/Solflare/QuickNode/Birdeye** | DApp live usando wallet/RPC/dados.                   | Solflare, RPC configurável, Birdeye quotes, status técnico.                                            | Checkout live com Solflare e confirmação via RPC.                  | **P1**     |
| **RPC Fast**                             | Uso claro de RPC para confirmar transações.          | RPC provider, healthcheck, retry, explorer links, logs de confirmação.                                 | Pagamento confirmado de forma confiável na devnet/mainnet.         | **P1**     |
| **100xDevs**                             | Produto full-stack completo e polido.                | Marketplace, auth, chat, escrow, disputa, UX, documentação.                                            | Fluxo ponta a ponta com comprador e vendedor.                      | **P1**     |
| **Adevar Labs**                          | Projeto auditável com escrow/pagamento.              | Threat model, invariantes, testes, escopo, documentação técnica.                                       | Pacote de auditoria do escrow Web3.                                | **P1**     |
| **Jupiter**                              | Pagar com qualquer token e liquidar em stablecoin.   | Quote, swap, slippage, token list, swap + escrow.                                                      | Comprador paga com token alternativo; vendedor recebe USDT/USDC.   | **P2**     |
| **Dune Analytics**                       | Dashboard de métricas on-chain.                      | Eventos indexáveis, queries, GMV, escrows, disputas, volume por jogo.                                  | Dashboard público mostrando saúde do marketplace.                  | **P2**     |
| **KIRAPAY**                              | Checkout alternativo por KIRAPAY.                    | Payment link, callback, status mapping, comprovante.                                                   | Pagamento por KIRAPAY vira escrow funded.                          | **P2**     |
| **theMiracle**                           | Benefício in-wallet.                                 | Cupom/taxa zero, regra de elegibilidade, campanha.                                                     | Wallet recebe/ativa benefício para primeira compra.                | **P3**     |
| **Zerion CLI**                           | Agente on-chain de monitoramento.                    | Monitor de escrow, alertas, resumo de disputa.                                                         | Agente avisa sobre pagamento, prazo e próximos passos.             | **P3**     |
| **SagaPad**                              | Agente de growth para X.                             | Conteúdo automático, threads, calendário, respostas.                                                   | Agente gera campanha pública do P2P LOOT.                          | **P3**     |


---

## 5. Ordem recomendada de execução

### Semana 1 — Core competitivo

1. Separar o fluxo de escrow/checkout em componentes menores.
2. Finalizar checkout Web3 básico.
3. Implementar USDT no anúncio e no escrow.
4. Configurar RPC confiável.
5. Preparar demo de compra segura.

### Semana 2 — Diferenciação

1. Implementar privacidade/Cloak.
2. Polir UX de compra privada.
3. Criar pacote Superteam Brasil.
4. Criar material para Tether e Cloak.
5. Criar threat model e checklist de segurança.

### Semana 3 — Expansões de track

1. Integrar Birdeye/Jupiter se houver tempo.
2. Criar dashboard Dune ou plano de indexação.
3. Preparar KIRAPAY se a API for simples.
4. Criar agente SagaPad/growth.
5. Revisar vídeos e submissões.

---

## 6. Critérios de pronto para submissão

Uma track só deve ser enviada quando tiver:

- Fluxo demonstrável em vídeo.
- README curto com problema, solução e como rodar.
- Link de deploy ou instrução local simples.
- Dados de demo reproduzíveis.
- Explicação clara de qual tecnologia da track foi usada.
- Evidência visual: screenshots, explorer links, dashboard ou logs.
- Limitações conhecidas documentadas honestamente.

---

## 7. Submissões mais fortes

### 1. Cloak Track

Submissão recomendada:

> “Private escrow checkout for gamer-to-gamer asset trading.”

Implementar primeiro porque diferencia o P2P LOOT e reaproveita para Privacy, Superteam Brasil e Adevar.

### 2. Tether Track

Submissão recomendada:

> “USDT-powered safe trading for gaming assets.”

Implementar junto do checkout porque stablecoin é central para marketplace P2P.

### 3. Superteam Brasil

Submissão recomendada:

> “Marketplace brasileiro para compra e venda segura de ativos digitais de games usando Web3.”

Usar como track guarda-chuva para mostrar o produto completo.

### 4. Eitherway / Solflare / QuickNode / Birdeye

Submissão recomendada:

> “Live Solana dApp for protected gaming commerce.”

Usar Solflare + RPC + dados de preço para provar que é uma dApp real.

### 5. Adevar

Submissão recomendada:

> “Security audit package for P2P marketplace escrow.”

Boa para credibilidade, mesmo que não seja a feature mais visível para usuário final.

---

## 8. Backlog técnico resumido

### Backend

- Validar permissões de escrow por conversa.
- Adicionar USDT e mints por rede.
- Registrar assinaturas de transação em eventos.
- Criar endpoints de quote/status quando necessário.
- Criar healthcheck RPC.
- Criar testes de status e permissões.
- Criar webhook/callback para KIRAPAY, se priorizado.

### Frontend

- Extrair painel de escrow do modal.
- Extrair checkout Web3.
- Exibir saldo, moeda, taxa e total.
- Mostrar status técnico da transação.
- Mostrar explorer links.
- Melhorar UX de privacidade.
- Criar timeline de eventos.
- Criar telas de erro/retentativa.

### Web3

- Definir provider RPC.
- Definir mints e rede.
- Definir estratégia de vault/escrow.
- Implementar confirmação de transação.
- Avaliar Cloak, Jupiter, Birdeye, KIRAPAY e MagicBlock conforme documentação.

### Produto e submissão

- Roteiro de demo.
- Pitch deck curto.
- Screenshots.
- Vídeo.
- README de hackathon.
- Dashboard ou métricas.

---

## 9. Decisão recomendada

Para maximizar chance e reduzir retrabalho, implementar nesta ordem:

1. **USDT + escrow funcional.**
2. **Privacidade/Cloak no checkout.**
3. **RPC confiável + Solflare + explorer links.**
4. **Polimento da demo Superteam Brasil.**
5. **Pacote de segurança Adevar.**
6. **Jupiter/Birdeye/Dune/KIRAPAY/agentes como extensões se houver tempo.**

Com isso, o P2P LOOT fica forte para as tracks de maior afinidade sem dispersar esforço em features isoladas.