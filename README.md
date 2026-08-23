# Sistema de Gestão para Associação Cultural Japonesa

## Sobre o projeto

Sistema desenvolvido para uma **associação sem fins lucrativos** dedicada a preservar a cultura japonesa, mantida inteiramente por voluntários. A associação promove eventos como karaokê, gincanas, bingos e torneios (incluindo campeonatos de vôlei), e durante esses eventos opera uma lanchonete para ajudar a cobrir as despesas mensais.

Atualmente todo o controle financeiro, de estoque e de doações é feito manualmente, no papel, o que dificulta o planejamento de compras, a visibilidade sobre sobras de produtos e o entendimento de quanto cada evento efetivamente gerou de lucro. Este projeto tem como objetivo digitalizar e centralizar essa gestão.

## Problema

- Não há controle sobre doações recebidas (dinheiro ou materiais)
- Não se sabe quantos produtos foram vendidos por evento
- Não existe histórico de vendas para auxiliar no planejamento de novas compras
- Não há controle de entrada e saída de produtos/estoque
- O resultado financeiro é calculado manualmente (receita do caixa menos gastos)

## Objetivo do sistema

Fornecer uma ferramenta simples que permita à associação:

- **Controle financeiro**: registrar vendas, despesas e calcular o lucro líquido por evento automaticamente
- **Controle de estoque**: registrar entrada e saída de produtos, com visibilidade das sobras do evento anterior para planejar o próximo
- **Controle de doações**: cadastrar doações em dinheiro, materiais e produtos perecíveis
- **Formas de pagamento**: diferenciar vendas/doações recebidas em dinheiro ou cartão
- **Relatórios e gráficos**: visualizar rapidamente o histórico de vendas, lucros por evento e por período

## Estrutura do repositório

```
/
├── backend/
├── frontend/
├── docs/
│   ├── FLUXO_DE_DESENVOLVIMENTO.md
│   └── PADRONIZACAO_COMMITS.md
├── .gitignore
└── README.md
```

## Fluxo de desenvolvimento

O padrão de branches e commits utilizado no projeto está documentado em:

- [`docs/FLUXO_DE_DESENVOLVIMENTO.md`](./docs/FLUXO_DE_DESENVOLVIMENTO.md)
- [`docs/PADRONIZACAO_COMMITS.md`](./docs/PADRONIZACAO_COMMITS.md)

Resumo do fluxo de branches:

```
feat/nome-da-feature → dev → main
```

## Tecnologias

**Backend**
- Java 21
- Spring Boot
- Spring Data JPA
- PostgreSQL (a definir)
- Docker / Docker Compose

**Frontend**
- React
- Vite

**Ferramentas**
- Maven
- Git / GitHub

## Equipe

| Nome | GitHub |
|---|---|
| Marlon de Souza | [@marlonzl7](https://github.com/marlonzl7) |
| Reginaldo de Souza | [@regisdesouza](https://github.com/regisdesouza) |
| Lucas Eiki | [@lucas-eiki](https://github.com/lucas-eiki) |
| Rayza Gomes | [@RayzaDSbr](https://github.com/RayzaDSbr) |
| Gabriela Pereira | [@GabrielaPereiraSantana](https://github.com/GabrielaPereiraSantana) |
| Daniel Foschini | [@D-Foschini](https://github.com/D-Foschini) |

## Como rodar o projeto

### Pré-requisitos

- Java 21+
- Node.js 18+
- Docker e Docker Compose

### 1. Configurar variáveis de ambiente

Na raiz do projeto, copie o arquivo de exemplo:

```bash
cp .env.example .env
```

### 2. Subir o banco de dados (PostgreSQL)

Na raiz do projeto:

```bash
docker compose up -d
```

Isso sobe um PostgreSQL local na porta `5432`, com as credenciais definidas no `.env`.

### 3. Configurar e rodar o backend

Confirme que `backend/src/main/resources/application-dev.properties` aponta para o mesmo banco configurado no `.env`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/kaizenbaiten
spring.datasource.username=kaizenbaiten
spring.datasource.password=kaizenbaiten
```

Rodar o backend:

```bash
cd backend
./mvnw spring-boot:run
```

A API deve subir em `http://localhost:8080`, com a documentação Swagger disponível em `http://localhost:8080/swagger-ui.html`.

### 4. Configurar e rodar o frontend

```bash
cd frontend
npm install
npm run dev
```

O frontend deve subir em `http://localhost:5173` (porta padrão do Vite).
