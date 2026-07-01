## Método 1: Instalação Manual (Portátil / Arquivo ZIP)
Este é o método mais à prova de falhas, pois você apenas baixa os arquivos e diz ao Windows onde eles estão.

### Passo 1: Baixar o arquivo ZIP

Acesse a página oficial de lançamentos do GitHub CLI: Releases do cli/cli

Role a página para baixo até a seção Assets.

Baixe o arquivo .zip para Windows (geralmente chamado algo como gh_X.X.X_windows_amd64.zip).

### Passo 2: Extrair os arquivos

Crie uma pasta nos seus documentos ou na raiz do seu usuário (Ex: C:\Users\SeuUsuario\Ferramentas\gh).

Extraia todo o conteúdo do arquivo .zip que você baixou dentro dessa pasta.

Dentro dessa pasta, você verá uma subpasta chamada bin. O caminho completo ficará parecido com C:\Users\SeuUsuario\Ferramentas\gh\bin (anote este caminho).

### Passo 3: Adicionar ao "PATH" do seu usuário
Para não ter que digitar o caminho completo toda vez que for usar o GitHub CLI, precisamos adicioná-lo às variáveis de ambiente (o que pode ser feito sem admin).

Abra o Menu Iniciar do Windows e digite "Editar as variáveis de ambiente para a sua conta" (ou Edit environment variables for your account se o Windows estiver em inglês) e dê Enter.

Na janela que abrir, na parte superior (Variáveis de usuário), procure pela variável chamada Path.

Selecione o Path e clique em Editar...

Clique em Novo e cole o caminho da pasta bin que você anotou no Passo 2 (Ex: C:\Users\SeuUsuario\Ferramentas\gh\bin).

Clique em OK em todas as janelas para salvar.
---
## Método 2: Usando o Scoop (Gerenciador de Pacotes)
O Scoop é um gerenciador de pacotes para Windows desenhado especificamente para instalar programas sem precisar de permissões de administrador.

### Passo 1: Instalar o Scoop
Abra o PowerShell (não precisa ser como administrador) e rode os seguintes comandos, um por vez:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```
(Nota: Se o seu PC for extremamente bloqueado pelas políticas da empresa, este comando pode falhar. Se falhar, use o Método 1).

### Passo 2: Instalar o GitHub CLI
Com o Scoop instalado, basta digitar no PowerShell:

```PowerShell
scoop install gh
```
O Scoop fará o download, a extração e a configuração do PATH automaticamente para você.

Verificando a instalação e fazendo Login
Independente do método que você escolheu, feche o seu terminal (CMD ou PowerShell) e abra-o novamente para que as alterações façam efeito.

Para verificar se funcionou, digite:

```Bash
gh --version
```
Se aparecer a versão do GitHub CLI, a instalação foi um sucesso!

Para autenticar sua conta do GitHub na máquina, basta rodar:

```Bash
gh auth login
```
E seguir as instruções na tela (recomendo escolher a opção Login with a web browser para facilitar).
