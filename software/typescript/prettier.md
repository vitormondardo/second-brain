## Para sumir erros de Prettier

Exemplo:
```bash
Delete ⏎·············eslint[prettier/prettier](https://github.com/prettier/eslint-plugin-prettier#options)
```
Não é um erro de lógica nem de TypeScript. É puramente cosmético: o Prettier encontrou um trecho do código com indentação ou espaçamento que não segue as regras configuradas.

## Como resolver:
```bash
npx prettier --write .
```
Caso peça para instalar:
```bash 
Need to install the following packages:
prettier@3.8.5
Ok to proceed? (y) 
```
Vai na fé, responda: y
---
Exemplo de mudanças que o Prettier corrige:
```typescript
// Antes
function calcular(a,b){return a+b}

// Depois
function calcular(a, b) {
  return a + b;
}
```
