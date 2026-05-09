# Plano lean de submissão para tracks Superteam

Data: 2026-05-09
Projeto: P2P LOOT
Escopo: **aplicar para tracks com o mínimo necessário, sem desviar o roadmap principal**

---

## 1. Premissa do plano

Este plano substitui a abordagem de integração profunda por uma estratégia de **submissão enxuta**.

O objetivo não é transformar cada tecnologia em parte estrutural do P2P LOOT agora. O objetivo é:

- aproveitar o que o P2P LOOT já tem;
- criar uma camada mínima, demonstrável e isolada;
- aplicar para tracks onde a afinidade é real;
- evitar parar o desenvolvimento principal;
- deixar qualquer integração experimental em branch separada;
- manter a solução em standby depois da submissão, sem obrigação de merge imediato.

---

## 2. Regra geral de implementação

Para cada track, só implementar se cumprir pelo menos 3 dos critérios abaixo:

- A feature aproveita fluxo já existente de marketplace, chat, escrow, wallet, privacidade ou analytics.
- A implementação cabe em **0,5 a 2 dias**.
- Não exige reescrever arquitetura.
- Não exige mudar banco em produção.
- Pode ficar atrás de feature flag.
- Pode ser demo/devnet/prototype sem afetar usuários reais.
- Gera uma narrativa clara para submissão.
- Pode ser revertida ou abandonada sem prejuízo ao roadmap.

Se não cumprir, a track deve receber apenas:

- documentação;
- mock/demo visual;
- branch experimental;
- ou não ser priorizada.

---

## 3. Estratégia de branch

### Branch principal de aplicação

Criar uma branch guarda-chuva:

```bash
hackathon/superteam-submissions-minimal
```

Essa branch pode conter:

- páginas ou painéis de demo;
- flags experimentais;
- pequenas integrações;
- documentação de submissão;
- ajustes de copy;
- configs `.env.example`;
- scripts auxiliares.

### Branches por track, se necessário

Se alguma track exigir uma mudança mais específica:

```bash
track/cloak-private-checkout-demo
track/tether-usdt-demo
track/dune-metrics-demo
track/jupiter-payment-demo
```

### Regra de merge

Nada deve ir para `teste` ou `main` sem reavaliação posterior.

A branch é para:

- aplicar;
- gravar vídeo;
- demonstrar;
- talvez reaproveitar depois.

Não é branch de roadmap.

---

## 4. Escopo menor recomendado

Se quisermos reduzir bastante o escopo, focar só nas tracks de **afinidade muito alta**:

1. **Cloak Track**
2. **Tether Frontier Hackathon Track**
3. **Side Track Superteam Brasil**
4. **Privacy Track — MagicBlock/ST MY/SNS**

Esse pacote usa a mesma narrativa:

> **P2P LOOT é um marketplace gamer Web3 com compra segura, escrow em stablecoin e opção de privacidade para compradores e vendedores.**

Com isso, uma única demo pode servir para várias submissões.

---

## 5. Plano mínimo para as 8 tracks recomendadas

## 5.1. Cloak Track

### Objetivo da submissão

Mostrar que o P2P LOOT resolve um problema real de marketplace P2P: compradores e vendedores de ativos digitais de jogos não querem expor wallet, identidade e dados sensíveis em negociações públicas.

### Implementação mínima aceitável

- Criar modo de demo: **Private Checkout**.
- Usar os campos/flags de privacidade que já existem ou criar apenas camada visual/controlada.
- Mostrar no anúncio: “vendedor com privacidade ativada”.
- Mostrar no checkout: “comprar com privacidade”.
- Exibir resumo do que fica privado:
  - wallet do vendedor;
  - dados sensíveis do comprador;
  - metadata pública reduzida.
- Registrar no escrow metadata simples como:
  - `cloakPrivacy.sellerRequested`
  - `cloakPrivacy.buyerRequested`
  - `privacyMode: demo`
- Se a integração Cloak real for simples, adicionar link/camada mínima.
- Se não for simples, deixar como **Cloak-ready prototype** com documentação do ponto de integração.

### O que não fazer agora

- Não reescrever o escrow.
- Não criar protocolo próprio de privacidade.
- Não bloquear roadmap para integração profunda.
- Não prometer produção se for apenas demo.

### Entregável para submissão

- Branch com demo privada.
- Vídeo mostrando compra segura privada.
- README curto explicando o problema e a integração pretendida com Cloak.

### Estimativa

1 a 2 dias.

### Afinidade

**Muito alta.** Deve ser prioridade se escolhermos poucas tracks.

---

## 5.2. Tether Frontier Hackathon Track

### Objetivo da submissão

Mostrar o P2P LOOT como caso de uso de USDT para comércio gamer P2P: preço estável, compra internacional e liquidação em dólar digital.

### Implementação mínima aceitável

- Adicionar `USDT` como opção de moeda nos pontos de demo.
- Permitir criar ou exibir anúncio com preço em USDT.
- No checkout, mostrar:
  - preço em USDT;
  - taxa da plataforma;
  - total;
  - status de escrow.
- Registrar escrow com `currencySymbol: USDT`.
- Se pagamento SPL real for rápido, usar devnet/testnet.
- Se não for rápido, fazer demo com status de pagamento simulado/devnet e deixar claramente documentado.

### O que não fazer agora

- Não implementar suporte completo a todos os mints/redes.
- Não mexer profundamente na carteira.
- Não construir infra definitiva de stablecoin.

### Entregável para submissão

- Demo: anúncio gamer vendido em USDT com fluxo de escrow.
- README: “USDT-powered safe trading for gaming assets”.
- Screenshot/video do checkout em USDT.

### Estimativa

0,5 a 1,5 dia.

### Afinidade

**Muito alta.** Deve ser prioridade junto com Cloak.

---

## 5.3. Side Track Superteam Brasil

### Objetivo da submissão

Apresentar o P2P LOOT como projeto brasileiro com produto funcional e problema claro: compra e venda segura de ativos digitais de jogos.

### Implementação mínima aceitável

- Não precisa de tecnologia nova.
- Polir a demo existente.
- Criar roteiro de apresentação em PT-BR.
- Criar seed/demo com:
  - comprador;
  - vendedor;
  - anúncio;
  - chat;
  - escrow;
  - reputação;
  - privacidade opcional.
- Criar uma página/README de hackathon com:
  - problema;
  - solução;
  - público;
  - diferencial;
  - próximos passos.

### O que não fazer agora

- Não adicionar integrações só para essa track.
- Não mudar produto principal.

### Entregável para submissão

- Vídeo de 2 a 3 minutos.
- README de submissão.
- Link de demo ou instruções locais.
- Screenshots.

### Estimativa

0,5 a 1 dia.

### Afinidade

**Muito alta.** É a submissão mais barata e com maior aderência regional.

---

## 5.4. Privacy Track — MagicBlock/ST MY/SNS

### Objetivo da submissão

Mostrar que o P2P LOOT tem um problema nativo de privacidade e uma abordagem de produto para reduzir exposição de identidade nas negociações.

### Implementação mínima aceitável

- Reaproveitar o mesmo demo da Cloak Track.
- Adicionar camada de identidade/alias:
  - exibir username/handle no lugar de wallet;
  - mascarar wallet;
  - indicar “privacy-first negotiation”.
- Se SNS for simples, permitir campo de alias `.sol` apenas como display/demo.
- Se MagicBlock exigir integração mais profunda, não implementar; apenas mencionar como possível evolução para experiência realtime/ephemeral.

### O que não fazer agora

- Não implementar MagicBlock se exigir arquitetura nova.
- Não criar sistema novo de identidade.
- Não mexer no auth principal.

### Entregável para submissão

- Demo de negociação com identidade protegida.
- README explicando como a privacidade se aplica ao marketplace gamer.
- Se possível, pequeno campo visual de alias/SNS.

### Estimativa

0,5 a 1 dia se reaproveitar Cloak.

### Afinidade

**Muito alta**, mas deve ser tratada como extensão da Cloak, não como projeto separado complexo.

---

## 5.5. Eitherway / Solflare / QuickNode / Birdeye

### Objetivo da submissão

Mostrar que o P2P LOOT é uma dApp live ou demo-live com wallet, RPC e dados de preço.

### Implementação mínima aceitável

Escolher apenas **uma ou duas** integrações, não todas.

Preferência:

1. **Solflare**: conexão de wallet ou instrução de compatibilidade.
2. **QuickNode/RPC**: variável de ambiente para RPC.
3. **Birdeye**: preço simples de SOL/token no checkout.

MVP recomendado:

- Configurar `VITE_SOLANA_RPC_URL` ou equivalente.
- Mostrar no painel técnico qual RPC está sendo usado.
- Mostrar link para Explorer.
- Se Birdeye for rápido, exibir cotação estimada.
- Se Eitherway exigir algo específico e demorado, não priorizar.

### O que não fazer agora

- Não integrar 5 parceiros ao mesmo tempo.
- Não trocar toda camada Web3.
- Não criar dependência crítica no fluxo principal.

### Entregável para submissão

- Demo com wallet/RPC/Explorer.
- README explicando qual parceiro foi usado e onde.
- Screenshot do checkout técnico.

### Estimativa

1 a 2 dias, dependendo do estado atual da wallet/RPC.

### Afinidade

**Alta**, mas só vale se for superficial e reaproveitar o que já existe.

---

## 5.6. Adevar Labs Audit Credits

### Objetivo da submissão

Não precisa implementar tecnologia nova. O objetivo é mostrar que o P2P LOOT tem uma superfície de segurança relevante e merece créditos de auditoria.

### Implementação mínima aceitável

- Criar pacote de auditoria em Markdown:
  - arquitetura do escrow;
  - fluxo de permissões;
  - status do escrow;
  - riscos conhecidos;
  - invariantes;
  - escopo que gostaríamos que fosse auditado.
- Criar checklist de segurança:
  - quem pode criar escrow;
  - quem pode liberar pagamento;
  - quem pode solicitar reembolso;
  - o que acontece em disputa;
  - como validar valor/moeda/transação.
- Opcional: adicionar 2 ou 3 testes mínimos se já houver estrutura pronta.

### O que não fazer agora

- Não criar contrato novo só para parecer auditável.
- Não reescrever backend.
- Não parar para cobertura de testes grande.

### Entregável para submissão

- Documento `AUDIT_SCOPE_P2PLOOT_ESCROW.md`.
- Diagrama simples do fluxo.
- Lista de riscos e mitigação.

### Estimativa

0,5 a 1 dia.

### Afinidade

**Alta**, com custo baixo. Boa track de documentação/credibilidade.

---

## 5.7. Jupiter — Not Your Regular Bounty

### Objetivo da submissão

Mostrar potencial de “pagar com qualquer token” no marketplace, convertendo para USDT/USDC no checkout.

### Implementação mínima aceitável

Somente implementar se a API/SDK estiver rápida de plugar.

MVP aceitável:

- Criar modo visual “Pay with any Solana token via Jupiter”.
- Buscar quote simples para converter token → USDT/USDC.
- Mostrar rota, slippage e valor recebido.
- Não precisa executar swap real se isso atrasar.
- Se executar swap real for simples, fazer em devnet/mainnet com valor pequeno.

### O que não fazer agora

- Não encadear swap + escrow em produção.
- Não mexer no fluxo principal de pagamento.
- Não suportar todos os tokens com UX completa.

### Entregável para submissão

- Protótipo de quote Jupiter no checkout.
- README explicando evolução para pagamento universal.
- Vídeo mostrando comprador escolhendo token alternativo.

### Estimativa

1 a 2 dias se a quote for simples. Se passar disso, descartar.

### Afinidade

**Média-alta**, mas não deve entrar no escopo menor inicial.

---

## 5.8. Dune Analytics

### Objetivo da submissão

Mostrar que o P2P LOOT consegue medir atividade do marketplace e do escrow.

### Implementação mínima aceitável

- Criar plano de dashboard com métricas:
  - GMV;
  - escrows criados;
  - escrows concluídos;
  - disputas;
  - volume por jogo;
  - stablecoins usadas;
  - tempo médio até conclusão.
- Se houver transações on-chain reais, criar query/dashboard Dune simples.
- Se não houver dados on-chain suficientes, criar especificação de eventos e dashboard mockado/off-chain, deixando claro o próximo passo.
- Criar uma pequena página ou markdown com amostra das métricas.

### O que não fazer agora

- Não redesenhar eventos on-chain só para Dune.
- Não criar indexador próprio.
- Não transformar analytics em projeto paralelo grande.

### Entregável para submissão

- Dashboard Dune simples ou plano de queries.
- Documento `DUNE_DASHBOARD_PLAN.md`.
- Screenshot/mock de métricas.

### Estimativa

0,5 a 1,5 dia.

### Afinidade

**Média-alta**, boa para credibilidade, mas depende do quanto há de dado on-chain.

---

## 6. Ranking ajustado para pouco tempo

## Se tivermos 2 a 3 dias

Focar em:

1. **Superteam Brasil**
2. **Tether**
3. **Cloak**

Motivo:

- reaproveitam o produto atual;
- têm narrativa forte;
- exigem menos integração profunda;
- podem compartilhar a mesma demo.

## Se tivermos 4 a 5 dias

Adicionar:

4. **Privacy Track**
5. **Adevar**

Motivo:

- Privacy reaproveita Cloak;
- Adevar é quase todo documentação técnica e escopo de auditoria.

## Se tivermos 1 semana ou mais

Adicionar, se não atrapalhar:

6. **Eitherway/Solflare/QuickNode/Birdeye**
7. **Dune**
8. **Jupiter**

Motivo:

- podem agregar, mas têm maior risco de virar desvio técnico.

---

## 7. Entregável mínimo comum para todas as tracks

Criar uma pasta de submissão:

```text
docs/hackathon-superteam/
  README.md
  DEMO_SCRIPT.md
  TRACK_MATRIX.md
  screenshots/
```

Conteúdo mínimo:

- descrição do P2P LOOT;
- problema que resolve;
- demo flow;
- matriz de tracks;
- tecnologia usada por track;
- limitações honestas;
- próximos passos;
- screenshots.

Isso permite aplicar para múltiplas tracks sem duplicar esforço.

---

## 8. Demo única recomendada

Criar uma única demo com o seguinte fluxo:

1. Vendedor cria anúncio de item/serviço gamer.
2. Vendedor ativa opção de privacidade.
3. Comprador abre o anúncio.
4. Comprador inicia conversa.
5. Comprador escolhe pagar em `USDT`.
6. Comprador ativa modo privado.
7. Sistema cria escrow.
8. UI mostra status protegido.
9. Vendedor confirma entrega.
10. Comprador libera pagamento.
11. Página final mostra timeline, reputação e explorer/status técnico.

Essa demo cobre:

- **Superteam Brasil:** produto completo.
- **Tether:** pagamento em USDT.
- **Cloak:** privacidade no pagamento.
- **Privacy:** identidade/wallet protegida.
- **Eitherway/RPC:** status técnico e explorer, se implementado.
- **Adevar:** fluxo auditável.
- **Dune:** eventos que podem virar métricas.
- **Jupiter:** opcional como botão de quote.

---

## 9. Critérios de corte

Cortar ou deixar apenas documentado se:

- exigir mais de 2 dias para um MVP;
- exigir mudança de arquitetura;
- exigir integração insegura com dinheiro real;
- quebrar o fluxo principal;
- precisar de API key difícil de obter;
- depender de documentação pouco clara;
- não gerar uma história de submissão convincente.

---

## 10. Plano de execução enxuto

### Dia 1

- Criar branch `hackathon/superteam-submissions-minimal`.
- Criar pasta `docs/hackathon-superteam`.
- Criar README de submissão.
- Preparar demo Superteam Brasil.
- Ajustar copy do checkout para USDT/privacidade, sem integração profunda.

### Dia 2

- Adicionar ou validar `USDT` no fluxo de demo.
- Adicionar painel de privacidade/Cloak-ready.
- Gravar screenshots.
- Criar demo script.

### Dia 3

- Criar pacote Adevar de segurança.
- Criar versão Privacy Track reaproveitando Cloak.
- Revisar vídeo e submissões.

### Dias extras, se houver

- RPC/Explorer/Solflare superficial.
- Dune plan/dashboard simples.
- Jupiter quote-only.

---

## 11. Recomendação final

Não tentar aplicar para todas com integração real.

Aplicar com força para:

1. **Superteam Brasil**
2. **Tether**
3. **Cloak**
4. **Privacy Track**
5. **Adevar**

E manter como opcionais:

6. **Eitherway/Solflare/QuickNode/Birdeye**
7. **Dune**
8. **Jupiter**

A melhor estratégia é uma **branch paralela de submissão**, com uma demo única e pequenas camadas de adaptação por track, sem compromisso de merge no produto principal.
