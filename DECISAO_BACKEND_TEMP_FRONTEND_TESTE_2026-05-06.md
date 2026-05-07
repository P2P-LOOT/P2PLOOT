# Registro de Decisao Tecnica

- **ID:** DEC-2026-05-06-frontend-backend-freeze-local
- **Data:** 2026-05-06
- **Status:** Aplicada localmente
- **Solicitacao:** Testar apenas o frontend sem usar as alteracoes recentes do backend

## Contexto

Durante os testes, apareceu erro de backend relacionado a `Privy` e coluna ausente no banco:

- `The column talon.profiles.privy_id does not exist in the current database`

Ao revisar o historico da branch `origin/teste` do backend, foi identificado que existem commits recentes com alteracoes de Web3/Privy:

- `ea98923` - feat: add solana escrow baseline
- `330b423` - feat: add platform fee split to devnet escrow
- `372eb1a` - feat: add privy auth bridge
- `1dabf9b` - feat: add web3 readiness status

## Decisao

Para permitir validacao do frontend sem bloqueio de backend, foi decidido:

1. **Nao remover** os commits recentes do backend.
2. Criar uma branch local temporaria do backend em um commit estavel anterior (`f389d16`).
3. Executar os testes de frontend com esse backend local estavel.

## Implementacao Executada

Branch criada localmente:

- `temp/frontend-only-backend` apontando para `f389d16`

Comando aplicado:

```bash
git -C "/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-backend" switch -c temp/frontend-only-backend f389d16
```

Validacao realizada:

- A branch temporaria nao contem referencias a `privy` no `src` e no `prisma/schema.prisma`.
- `origin/teste` foi preservada em `1dabf9b` (sem rollback remoto e sem reescrita de historico).

## Escopo e Limites

- Esta decisao vale para **teste local** e foco em frontend.
- Nenhum commit remoto foi apagado, alterado ou revertido.
- Nenhuma forca de push foi utilizada.

## Riscos Conhecidos

1. Divergencia de comportamento entre backend local estavel e ambiente de teste remoto.
2. Endpoints/fluxos novos de Web3 podem nao estar disponiveis nessa branch local.
3. Ao voltar para `origin/teste`, o ambiente precisa de migracoes consistentes do banco.

## Plano de Retorno

Quando finalizar os testes de frontend, retornar para o backend mais recente:

```bash
git -C "/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-backend" switch main
```

Ou criar/usar branch local rastreando `origin/teste`:

```bash
git -C "/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-backend" switch -c teste --track origin/teste
```

## Evidencia para Auditoria

- Solicitacao registrada em chat para "testar somente com o Frontend" sem retirar alteracoes do backend.
- Branch temporaria criada localmente com hash base `f389d16`.
- Commits recentes de `origin/teste` preservados: `ea98923`, `330b423`, `372eb1a`, `1dabf9b`.
