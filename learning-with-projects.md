# Project Chatbot
## Architecture: Clean Architecture

## Por que começar pelo frontend é uma armadilha?
Quando você cria uma tela antes de ter a API, você está inventando a forma dos dados. Aí quando a API chega, ela retorna algo diferente do que você imaginou — e você refaz a tela. É o retrabalho clássico de "UI-first sem contrato".
```bash
Etapa 1 — Modelo de dados
O que o Omie retorna? Quais campos você precisa? Quais tabelas/cache você vai criar?
Duração: 1 reunião de 30 min com papel e caneta

Etapa 2 — Backend (domain → application → infra → presentation)
domain: entidade OmieProduct, interface IOmieProvider
application: use case GetStockUseCase
infra: OmieApiProvider (chamada HTTP real), OmieModule
Você pode testar com curl antes de tocar no frontend

Etapa 3 — Contrato da API (GET /omie/stock)
Documente: rota, parâmetros, formato de resposta, erros possíveis
Isso é o acordo entre backend e frontend — sem isso você vai refazer a tela
Teste no Postman antes de abrir o VS Code no frontend

Etapa 4 — Frontend (React)
Criar função fetch em src/api/omie.ts
Criar página StockPage ou adicionar card no TicketDetailPage
Agora os dados já existem — você só exibe o que a API já retorna

Etapa 5 — Testes e tratamento de erro
Teste unitário: GetStockUseCase com mock do IOmieProvider
O que acontece se a API do Omie cair? O sistema trava?
``` 

## Por que essa ordem e não a inversa
A lógica é simples: cada etapa produz um artefato que a próxima consome.
O modelo de dados define o que você vai salvar. O backend implementa a lógica e expõe a rota. O contrato da API é o "acordo de entrega" que o frontend vai consumir. Só então você abre o React — porque agora você sabe exatamente que JSON vai chegar.
Quando você inverte e começa pelo frontend, você está inventando o JSON. Quando o backend ficar pronto, basta um campo diferente (productCode vs code, por exemplo) para você refazer a tela inteira.

O roteiro concreto para o módulo Omie
Etapa 1 — Modelo de dados (papel e caneta, 30 min)
Antes de abrir o VS Code, responda:

O que a API do Omie retorna? (leia a documentação deles)
Quais campos o técnico precisa ver? Código da peça, descrição, quantidade em estoque, localização?
Você vai salvar isso no banco ou consultar ao vivo toda vez?

Se você for consultar ao vivo, não precisa de migration. Se for cachear, precisa de tabela. Essa decisão muda tudo que vem depois.

Etapa 2 — Backend
A estrutura que o projeto já usa é esta:
src/modules/omie/
  domain/
    entities/
      omie-product.entity.ts     ← o que é um produto no seu sistema
    interfaces/
      omie-provider.interface.ts ← contrato da integração
  application/
    use-cases/
      get-stock.use-case.ts      ← orquestra: chama provider, filtra, retorna
    dtos/
      stock-item.dto.ts
  infrastructure/
    providers/
      omie-api.provider.ts       ← a chamada HTTP real para api.omie.com.br
  presentation/
    controllers/
      omie.controller.ts         ← expõe GET /omie/stock
  omie.module.ts
A entidade de domínio não sabe que existe uma API do Omie. O use case não sabe se os dados vêm do Omie ou de qualquer outro lugar. Só o provider sabe. Isso é o que permite testar o use case com mock.

Etapa 3 — Contrato da API
Documente (pode ser no api.md que já existe no projeto):
GET /omie/stock?productCode=123

Response 200:
{
  "productCode": "123",
  "description": "Rolamento 6204-2RS",
  "quantity": 12,
  "location": "Almoxarifado A"
}

Errors:
  404 → produto não encontrado no Omie
  503 → API do Omie indisponível (com fallback)
Teste isso no Postman. Só avance para o frontend quando esse endpoint retornar dados reais.

Etapa 4 — Frontend
Com a API funcionando, o frontend é direto:
typescript// src/api/omie.ts
export async function getStock(productCode: string) {
  const { data } = await api.get(`/omie/stock?productCode=${productCode}`);
  return data;
}
E no componente você usa o TanStack Query que o projeto já tem, igual ao que fazem para tickets.
