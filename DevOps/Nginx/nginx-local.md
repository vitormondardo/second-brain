# Iniciando Nginx local

Gerando certificado self-signed HTTPS de teste para o seu ambiente local. Ele não é um certificado válido para produção; serve para você conseguir testar o Nginx com https://localhost.

*Obversação: estes comandos devem ser executados em um terminal Git Bash.*

1. Criar a pasta dos certificados
```powershell
mkdir -p "/c/Users/39000024/nginx/nginx-1.30.4/certs"
```
mkdir = make directory, cria uma pasta.

O -p permite criar a estrutura mesmo que alguma pasta intermediária ainda não exista.

2. Entrar na pasta
```bash
cd "/c/Users/39000024/nginx/nginx-1.30.4/certs"
```
cd = change directory.

A partir desse momento, quando você criar arquivos, eles serão criados dentro de:
```bash
C:\Users\39000024\nginx\nginx-1.30.4\certs
```

3. O comando principal
```bash
MSYS_NO_PATHCONV=1 openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout privkey.pem \
  -out fullchain.pem \
  -days 30 \
  -subj "/CN=localhost"
```

Aqui está acontecendo bastante coisa.

- openssl
É a ferramenta utilizada para trabalhar com criptografia, certificados, chaves etc.

Você pode pensar nela como:
```
OpenSSL
   │
   ├── gera chaves
   ├── gera certificados
   ├── verifica certificados
   ├── trabalha com TLS
   └── trabalha com criptografia
```

# Certificado TLS Self-Signed com OpenSSL

## 1. `req`

Indica que estamos trabalhando com uma **Certificate Signing Request (CSR)** e certificados relacionados.

Nesse caso, estamos usando o comando para gerar o certificado diretamente.

---

## 2. `-x509`

Essa é uma das partes mais importantes.

Estamos dizendo:

> Gere um certificado X.509.

**X.509** é o padrão utilizado para certificados digitais no HTTPS.

Um certificado HTTPS contém informações como:

```text
Certificado
│
├── Quem é o certificado?
├── Para qual domínio?
├── Qual chave pública?
├── Qual algoritmo?
├── Quando começa a validade?
├── Quando expira?
└── Quem assinou?
```

---

## 3. `-newkey rsa:2048`

Isso manda o OpenSSL criar uma **nova chave RSA de 2048 bits**.

Essa chave é, na verdade, um par:

```text
        Par de chaves
             │
       ┌─────┴─────┐
       ↓           ↓
  Chave privada  Chave pública
```

A chave privada será salva em:

```text
privkey.pem
```

A chave pública ficará incorporada ao certificado:

```text
fullchain.pem
```

---

## 4. `-nodes`

Significa que a chave privada será criada **sem criptografia por senha**.

Isso é importante para o Nginx.

Imagine que a chave privada fosse protegida por uma senha:

```text
privkey.pem
     ↓
senha necessária
```

Ao iniciar o Nginx, ele precisaria dessa senha.

Em um servidor automatizado isso seria inconveniente.

Com:

```bash
-nodes
```

fica:

```text
Nginx
  ↓
lê privkey.pem
  ↓
inicia automaticamente
```

### ⚠️ Atenção

Isso torna ainda mais importante proteger o arquivo:

```text
privkey.pem
```

---

## 5. `-keyout privkey.pem`

Define onde será salva a **chave privada**.

Resultado:

```text
certs/
└── privkey.pem
```

Essa é a parte que você **não deve compartilhar**.

Pense assim:

```text
privkey.pem
     ↓
   SEGREDO
```

Se alguém obtiver a chave privada de um certificado de produção, pode causar problemas sérios.

---

## 6. `-out fullchain.pem`

Define o arquivo de saída do certificado:

```text
fullchain.pem
```

Então teremos:

```text
certs/
├── fullchain.pem
└── privkey.pem
```

O Nginx posteriormente poderá usar esses arquivos:

```nginx
ssl_certificate     /etc/nginx/certs/fullchain.pem;
ssl_certificate_key /etc/nginx/certs/privkey.pem;
```

---

## 7. `-days 30`

O certificado terá validade de:

**30 dias.**

Depois disso ele expira.

Para desenvolvimento local isso é totalmente aceitável.

Em produção, você normalmente não ficaria gerando certificados manualmente. Usaria algo como **Let's Encrypt** com renovação automática.

---

## 8. `-subj "/CN=localhost"`

Essa parte diz para quem o certificado foi emitido.

```text
CN = Common Name
```

Nesse caso:

```text
CN=localhost
```

Ou seja:

> Este certificado é destinado ao `localhost`.

Isso faz sentido porque estamos testando:

```text
https://localhost
```

---

# 9. O que é `MSYS_NO_PATHCONV=1`?

Essa parte é específica do **Git Bash no Windows**.

O Git Bash possui uma camada chamada **MSYS** que tenta converter caminhos automaticamente.

Por exemplo, algo começando com:

```text
/c/Users/...
```

pode ser interpretado pelo MSYS como um caminho que precisa ser convertido para o formato Windows.

O:

```bash
MSYS_NO_PATHCONV=1
```

fala:

> Não faça essa conversão automática neste comando.

Isso evita problemas com parâmetros que começam com `/`, como:

```bash
-subj "/CN=localhost"
```

---

# 10. Comando completo

O comando utilizado é:

```bash
MSYS_NO_PATHCONV=1 openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout privkey.pem \
  -out fullchain.pem \
  -days 30 \
  -subj "/CN=localhost"
```

---

# 11. Resultado

Depois de executar o comando, teremos:

```text
C:\Users\39000024\
└── nginx\
    └── nginx-1.30.4\
        └── certs\
            ├── fullchain.pem
            └── privkey.pem
```

Podemos verificar com:

```bash
ls -la
```

Algo semelhante a:

```text
-rw-r--r-- 1 ... fullchain.pem
-rw------- 1 ... privkey.pem
```

O importante nesse momento é que os dois arquivos:

* existam;
* tenham tamanho maior que zero;
* estejam no diretório esperado.

---

# 12. O que acabamos de criar?

Simplificando:

```text
                OpenSSL
                   │
                   │ gera
                   ↓
          ┌─────────────────┐
          │ Par criptográfico│
          └────────┬────────┘
                   │
          ┌────────┴────────┐
          ↓                 ↓
   Chave privada       Chave pública
   privkey.pem              │
          │                 │
          │          incorporada no
          │                 ↓
          │          fullchain.pem
          │
          ↓
       SEGREDO
```

Depois o Nginx vai utilizar os dois:

```text
                   Nginx
                     │
            ┌────────┴────────┐
            ↓                 ↓
     fullchain.pem       privkey.pem
     certificado         chave privada
            │                 │
            └────────┬────────┘
                     ↓
                HTTPS/TLS
                     ↓
            https://localhost
```

---

# 13. Self-Signed Certificate

Existe uma diferença importante entre o certificado que acabamos de criar e um certificado utilizado normalmente em produção.

Esse certificado é **self-signed**, ou seja, ele foi assinado pelo próprio processo que o criou.

Em vez de:

```text
Autoridade Certificadora (CA)
            │
            ↓
       Certificado
```

temos:

```text
       Você / OpenSSL
             │
             ↓
       Certificado
             │
             ↓
        localhost
```

Por isso, quando acessarmos:

```text
https://localhost
```

o navegador provavelmente mostrará algo como:

> Sua conexão não é particular

ou:

```text
NET::ERR_CERT_AUTHORITY_INVALID
```

Isso é esperado.

---

# 14. Isso significa que o TLS está quebrado?

**Não necessariamente.**

O navegador está dizendo que **não confia na autoridade que assinou o certificado**.

Como fomos nós mesmos que criamos e assinamos o certificado, o navegador não possui essa autoridade na sua lista de autoridades confiáveis.

Isso é diferente de dizer que:

> O Nginx não está usando TLS.

O TLS pode estar funcionando corretamente mesmo com o navegador exibindo um alerta de confiança.

---

# 15. Por que usar Self-Signed localmente?

Para desenvolvimento e testes locais, um certificado self-signed é útil porque permite testar:

```text
Nginx
  ↓
TLS
  ↓
HTTPS
  ↓
https://localhost
```

Podemos validar, por exemplo:

* se o Nginx consegue carregar o certificado;
* se a chave privada corresponde ao certificado;
* se o TLS está funcionando;
* se o redirect HTTP → HTTPS funciona;
* se os headers de segurança estão configurados;
* se o reverse proxy funciona;
* se o navegador consegue estabelecer uma conexão TLS.

---

# 16. E em produção?

Em produção, **não devemos utilizar esse certificado self-signed para um site público**.

Na Oracle Cloud, por exemplo, teremos algo como:

```text
Usuário
   │
   │ HTTPS
   ↓
seudominio.com
   │
   ↓
Certificado confiável
   │
   ↓
Nginx
   │
   ↓
Aplicação
```

Normalmente podemos utilizar uma Autoridade Certificadora reconhecida, como o **Let's Encrypt**, com renovação automática.

Assim:

```text
              Let's Encrypt
                    │
                    ↓
             Certificado
                    │
                    ↓
                 Nginx
                    │
                    ↓
             HTTPS público
```

O navegador confia no certificado porque a autoridade certificadora é reconhecida pelo sistema operacional e pelo navegador.

---

# 17. Resumo

| Parâmetro            | Função                                            |
| -------------------- | ------------------------------------------------- |
| `openssl`            | Ferramenta criptográfica                          |
| `req`                | Trabalha com CSR/certificados                     |
| `-x509`              | Gera certificado X.509                            |
| `-newkey`            | Cria uma nova chave                               |
| `rsa:2048`           | Utiliza RSA com 2048 bits                         |
| `-nodes`             | Não protege a chave privada com senha             |
| `-keyout`            | Define o arquivo da chave privada                 |
| `-out`               | Define o arquivo do certificado                   |
| `-days 30`           | Define validade de 30 dias                        |
| `-subj`              | Define informações do certificado                 |
| `CN=localhost`       | Define `localhost` como Common Name               |
| `MSYS_NO_PATHCONV=1` | Impede conversão automática de caminhos pelo MSYS |

## Conceito principal

O que estamos construindo é:

```text
                    OpenSSL
                       │
                       ↓
              ┌─────────────────┐
              │ Certificado TLS │
              └────────┬────────┘
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
   fullchain.pem              privkey.pem
   Certificado                Chave privada
          │                         │
          └────────────┬────────────┘
                       ↓
                     Nginx
                       ↓
                   TLS/HTTPS
                       ↓
              https://localhost
```

**No ambiente local:** self-signed é adequado para testes.

**Em produção:** utilize um certificado emitido por uma CA confiável, como Let's Encrypt.

