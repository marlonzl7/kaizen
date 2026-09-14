# Endpoints da API

Checklist de recursos e endpoints planejados, baseado nas anotações da entrevista inicial e no doc de páginas do projeto (ainda não definitivo, endpoints podem ser ajustados conforme as telas evoluem).

> Este documento lista **rotas e métodos HTTP**, não schemas de request/response, isso é gerado automaticamente pelo Swagger a partir dos DTOs e fica disponível em `/swagger-ui.html` assim que cada endpoint for implementado.

## Convenções gerais

- Prefixo: `/api`
- Formato de resposta de sucesso: envelope `{ "mensagem": "...", "dados": {...} }`
- Formato de resposta de erro: [RFC 7807 Problem Details](https://datatracker.ietf.org/doc/html/rfc7807)
- Listagens: paginadas via `Pageable` (`?page=0&size=20&sort=campo,asc`), resultado da paginação vai dentro de `dados`
- Datas: ISO 8601
- Autenticação: JWT, implementação completa a definir na branch `feat/autenticacao-jwt`. Rotas já reservadas abaixo (ver observação).

---

## Auth

> Rotas reservadas com base no fluxo padrão JWT (login / refresh / logout). Lógica de invalidação de token (stateless vs. blacklist) ainda não decidida, fica para a branch `feat/autenticacao-jwt`.

- [ ] `POST /api/auth/entrar` → recebe credenciais, retorna token (+ refresh token, se usado)
- [ ] `POST /api/auth/atualizar-token` → renova token a partir do refresh token
- [ ] `POST /api/auth/sair` → encerra sessão (efeito no backend depende da estratégia escolhida)
- [ ] `POST /api/auth/esqueci-senha`
- [ ] `POST /api/auth/nova-senha`

## Usuários

- [ ] `GET    /api/usuarios` → listar (paginado, com filtro por status/cargo)
- [ ] `GET    /api/usuarios/{id}` → detalhar
- [ ] `POST   /api/usuarios` → cadastrar (nome, email, cargo)
- [ ] `PUT    /api/usuarios/{id}` → editar
- [ ] `DELETE /api/usuarios/{id}` → remover
- [ ] `POST   /api/usuarios/{id}/reenviar-ativacao` → reenviar link de ativação de conta

## Cargos

- [ ] `GET    /api/cargos` → listar (nome + permissões)
- [ ] `GET    /api/cargos/{id}` → detalhar
- [ ] `POST   /api/cargos` → criar (nome + permissões)
- [ ] `PUT    /api/cargos/{id}` → editar

> Modelagem exata de "permissões" ainda em aberto , depende de como o controle de acesso vai ser estruturado.

## Eventos

- [ ] `GET    /api/eventos` → listar (paginado)
- [ ] `GET    /api/eventos/{id}` → detalhar (inclui lucro bruto/líquido, período)
- [ ] `POST   /api/eventos` → criar
- [ ] `PUT    /api/eventos/{id}` → editar
- [ ] `DELETE /api/eventos/{id}` → remover
- [ ] `GET    /api/eventos/{id}/pedidos` → histórico de pedidos do evento

> Endpoint de "lucro líquido" pode virar cálculo embutido no `GET /eventos/{id}` ou rota própria (`/eventos/{id}/resultado`) , decidir quando a regra de cálculo (receita − despesas) estiver fechada.

## Produtos (Estoque)

- [ ] `GET    /api/produtos` → listar (paginado, filtro por categoria/quantidade)
- [ ] `GET    /api/produtos/{id}` → detalhar
- [ ] `POST   /api/produtos` → cadastrar (nome, descrição, foto, quantidade)
- [ ] `PUT    /api/produtos/{id}` → editar
- [ ] `DELETE /api/produtos/{id}` → remover

> Inclui produtos de limpeza, comida, bebida, materiais , não há recurso separado por categoria, categoria é um atributo do produto.

## Movimentações de Estoque

- [ ] `GET    /api/movimentacoes-estoque` → listar (paginado, filtro por produto/tipo/data)
- [ ] `POST   /api/movimentacoes-estoque` → registrar movimentação (reabastecimento, venda, consumo, perda, doação)

> Tipo de movimentação provavelmente um enum (`REABASTECIMENTO`, `VENDA`, `CONSUMO`, `PERDA`, `DOACAO`). Preço pago/vendido é campo condicional conforme o tipo , detalhar quando o DTO for modelado.

## Doações

- [ ] `GET  /api/doacoes` → listar histórico (paginado)
- [ ] `GET  /api/doacoes/{id}` → detalhar
- [ ] `POST /api/doacoes` → registrar (doador, item/quantia, forma de pagamento se dinheiro)

> Vínculo com "vale refeição" ainda não modelado , depende de como o vale vai ser controlado (saldo? histórico de uso?). Deixar para quando essa regra for definida.

## Pedidos (Registro de vendas no baiten)

- [ ] `GET    /api/pedidos` → listar (paginado, filtro por evento)
- [ ] `GET    /api/pedidos/{id}` → detalhar
- [ ] `POST   /api/pedidos` → registrar pedido (produtos, quantidade, desconto), reduz estoque automaticamente
- [ ] `DELETE /api/pedidos/{id}` → cancelar/estornar (repõe estoque)

## Caixa

- [ ] `GET  /api/caixa` → histórico de pagamentos (paginado)
- [ ] `POST /api/caixa` → registrar entrada (forma de pagamento, valor, horário automático)

> Fiado descartado por ora: entendimento atual é que fiado só existiria durante o evento, sendo quitado até o encerramento, não haveria necessidade de registro persistente. Reavaliar se essa premissa mudar.

## Dashboard / Relatórios

> Endpoints de leitura agregada, só faz sentido detalhar depois que os recursos base (pedidos, movimentações, eventos) estiverem implementados, já que são consultas em cima desses dados.

- [ ] `GET /api/dashboard/produtos-mais-vendidos` → ranking de produtos (mais → menos vendidos)
- [ ] `GET /api/dashboard/horarios-pico` → horários com mais vendas

---

## Fluxo de uso , hipótese em aberto (não modelado)

Cogitamos um fluxo do tipo: **criar evento → iniciar evento → abrir caixa → registrar vendas/pedidos vinculados ao caixa do evento → encerrar evento → fechar caixa**. Essa é apenas uma hipótese de uso levantada em conversa, **não foi decidida nem modelada**. Não está refletida nos endpoints acima (ex: não existe `/eventos/{id}/abrir-caixa` ou conceito de "caixa vinculado a evento" nos recursos listados).

### Rascunho da hipótese (não é checklist , ainda não será implementado)

> É a única hipótese de fluxo até agora, então vale esboçar como ficariam os endpoints, só para servir de base de discussão/crítica pelo time. Sem checkbox, não representa decisão tomada.

Modelo unificado: caixa não é recurso/entidade própria, é um **estado derivado do evento**. Iniciar o evento abre o caixa implicitamente; encerrar o evento fecha o caixa implicitamente. Evita dois estados (evento + caixa) para manter sincronizados, e reflete o que as anotações descrevem , não há indício de caixa com ciclo de vida independente do evento ou de múltiplos caixas por evento.

- `POST  /eventos/{id}/iniciar` → evento passa para `EM_ANDAMENTO` (caixa implicitamente aberto)
- `PATCH /eventos/{id}/encerrar` → evento passa para `ENCERRADO` (caixa implicitamente fechado)
- `POST  /eventos/{id}/pedidos` → só permitido se `evento.status == EM_ANDAMENTO`

Campo de estado sugerido na própria entidade `Evento`: `status` (enum: `PLANEJADO`, `EM_ANDAMENTO`, `ENCERRADO`), talvez com `dataAberturaCaixa`/`dataFechamentoCaixa` se quiserem manter o horário registrado , sem precisar de uma tabela `caixa` separada.

Pontos que essa modelagem ainda não resolve, e que precisam de resposta antes de virar checklist real:
- O que acontece se tentar registrar pedido com evento fora de `EM_ANDAMENTO`? (erro 409/422 , a definir)
- Cabe reabrir (`EM_ANDAMENTO` de novo) um evento já `ENCERRADO` por engano, ou é irreversível?

Confirmado com base na entrevista com os beneficiários (não é mais hipótese aberta):
- Caixa é sempre vinculado a um evento , vendas só acontecem durante eventos. O que acontece fora do evento (compra de produtos para revenda, recebimento de doações, compra de material de limpeza) não passa pelo caixa, é movimentação de estoque/doação separada.
- Um evento tem exatamente um caixa , não há necessidade de múltiplos caixas por evento (ex: múltiplos pontos de venda). O modelo unificado (caixa como estado do evento, sem entidade própria) está confirmado, não só como simplificação, mas como reflexo direto do que foi relatado.

## Fora de escopo por agora

Não incluídos nesta lista por dependerem de decisões de produto ainda em aberto:

- Regras finas de permissões por cargo (endpoint existe, mas o schema de "permissão" não está modelado)
- Relatórios financeiros detalhados por período/mês (mencionados nas anotações, mas sem tela definida ainda)
- Repasse de sobras a preço de custo para outras associações (mencionado como necessidade, sem fluxo de tela definido)