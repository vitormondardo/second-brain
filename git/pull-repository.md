# Como atualizar um repositório local com atualizações de outra máquinas, puxando do Github

Esse é o clássico desencontro de máquinas, um cenário super comum quando desenvolvemos em mais de um ambiente. Para atualizar sua máquina local desatualizada com o código mais recente que está no GitHub, você precisará "puxar" essas alterações.

## 1. Passo
### Abra o terminal na pasta do repositório
Certifique-se de que você está dentro do diretório do projeto na máquina desatualizada.

### Verifique o status atual (Opcional, mas recomendado)
Antes de puxar qualquer coisa, é uma boa prática ver se há alguma alteração não salva nessa máquina.

```powershell
git status
```
Resultado: Your branch is up to date with 'origin/main' -> OK

## 2. Puxe as informações do Github
Execute o comando abaixo, substituindo main pelo nome da sua branch principal, caso você ainda use master ou esteja trabalhando em outra branch específica:
```powershell
git pull origin main
```

## E se der erro? (Problemas comuns)
### Cenário A: Você fez edições na máquina desatualizada e esqueceu de salvar
Se você tiver arquivos alterados que entram em conflito com o que está no GitHub, o Git vai bloquear o pull e avisar que seus arquivos locais seriam sobrescritos.

Solução: Você pode guardar essas alterações temporariamente usando o comando stash:

```Bash
git stash
git pull origin main
git stash pop
```

Isso "esconde" suas edições, atualiza a máquina e depois "devolve" as edições por cima do código novo.
