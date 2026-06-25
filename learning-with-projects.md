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
