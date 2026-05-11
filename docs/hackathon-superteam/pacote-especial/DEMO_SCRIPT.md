# Demo script - Superteam Brasil

Duracao alvo: 2 a 3 minutos.

## Mensagem central

P2P LOOT transforma negociacoes gamer P2P em um fluxo seguro com chat, reputacao, pagamento em stablecoin, escrow Web3 e privacidade opcional.

## Roteiro

### 0:00 - Problema

"No Brasil, boa parte da compra e venda de ativos digitais de jogos acontece em chats, grupos e combinados manuais. Isso cria risco de golpe, disputa sem prova e exposicao desnecessaria de identidade."

Mostrar:

- marketplace com anuncios gamer;
- anuncio ativo em `USDT`;
- badge/indicacao de privacidade quando disponivel.

### 0:25 - Produto

"O P2P LOOT organiza esse fluxo em um marketplace: anuncio, conversa, comprador, vendedor, reputacao e checkout em um so lugar."

Mostrar:

- detalhe do anuncio;
- vendedor mascarado quando Cloak esta ativo;
- preco e moeda do anuncio.

### 0:55 - Checkout seguro

"Na compra, o pagamento pode ser criado em `USDT` e registrado como escrow. A ideia e tirar a negociacao do combinado informal e colocar status, valor e partes em um fluxo auditavel."

Mostrar:

- botao de criar escrow;
- detalhe tecnico do escrow;
- `VALOR 10 USDT` ou valor equivalente da seed;
- status `funded` ou estado validado.

### 1:35 - Privacidade opcional

"Nem todo usuario quer expor identidade publica em um marketplace gamer. Com o modo Cloak-ready, a demo mostra privacy intent e mascaramento de comprador/vendedor sem prometer settlement shielded completo ainda."

Mostrar:

- comprador vendo `Vendedor privado via Cloak`;
- vendedor vendo `Comprador privado via Cloak`;
- explicar o limite como MVP honesto de privacidade.

### 2:05 - Por que Superteam Brasil

"Essa e uma tese brasileira: gamers locais ja negociam assets digitais, mas precisam de uma camada de confianca. O P2P LOOT usa Solana, stablecoin e UX em PT-BR para tornar esse comercio mais seguro."

Mostrar:

- tela em PT-BR;
- lista de anuncios;
- evidencias ou status final.

### 2:35 - Fechamento

"O plano modular por tracks permite promover Tether e Cloak quando estiverem garantidos, ou manter qualquer parte em standby sem contaminar a branch de teste. Para a submissao Brasil, a entrega e o produto completo demonstravel."

Mostrar:

- matriz de tracks ou README;
- proximos passos: deploy demo, video, QA final.

## Checklist antes de gravar

- Backend local ou deploy responde em `/api/web3/status`.
- Frontend aponta para o backend correto via `VITE_API_URL`.
- Existe anuncio ativo de demo em `USDT`.
- O comprador consegue abrir o anuncio e criar escrow.
- O vendedor consegue ver a conversa.
- Evidencia de Cloak mostra comprador/vendedor mascarados quando aplicavel.
- Nenhum texto da demo promete settlement shielded completo.
- Screenshots finais batem com o roteiro.
