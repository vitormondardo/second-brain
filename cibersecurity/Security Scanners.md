# Security Scanners, SAST e DevSecOps

## 1. O que é um Security Scanner?

Um **Security Scanner** é uma ferramenta capaz de analisar código, dependências, configurações, containers ou infraestrutura procurando possíveis vulnerabilidades e configurações inseguras.

Ele não é necessariamente uma IA.

Pode funcionar por meio de:

* regras determinísticas;
* análise estática;
* AST (Abstract Syntax Tree);
* análise de fluxo de dados;
* análise de dependências;
* análise de configurações;
* comparação com bancos de vulnerabilidades;
* heurísticas;
* IA/LLMs como complemento.

Exemplo:

```text
Código
   ↓
Security Scanner
   ↓
Regras + análise
   ↓
Finding
   ↓
Severidade
   ↓
Relatório
```

---

# 2. Scanner não é CI/CD

Essa é uma distinção fundamental.

## CI/CD

CI/CD é o **processo automatizado de integração, teste e entrega/deploy** de software.

Exemplo:

```text
Developer
    ↓
git push
    ↓
CI/CD
    ├── Build
    ├── Tests
    ├── Lint
    ├── Security Scan
    ├── Docker Build
    └── Deploy
```

O Security Scanner é apenas **uma das etapas possíveis do pipeline**.

Portanto:

```text
CI/CD = processo/pipeline

Security Scanner = ferramenta utilizada dentro do pipeline
```

---

# 3. DevSecOps

DevSecOps significa incorporar segurança ao ciclo de desenvolvimento e operação.

Modelo tradicional:

```text
Código
   ↓
Testes
   ↓
Deploy
   ↓
Produção
   ↓
Segurança
```

Problema:

A vulnerabilidade pode ser descoberta somente depois que o sistema já está em produção.

No DevSecOps:

```text
Código
   ↓
Commit
   ↓
CI
   ↓
Security Scan
   ↓
Correção
   ↓
Testes
   ↓
Deploy
```

A segurança passa a ser verificada continuamente.

---

# 4. Principais tipos de Security Scanner

Existem diferentes tipos de scanners porque cada um analisa uma camada diferente da aplicação.

```text
                    Aplicação
                       │
       ┌───────────────┼────────────────┐
       │               │                │
      Código       Dependências    Infraestrutura
       │               │                │
      SAST             SCA              IaC
       │
       ├── Secrets
       │
       └── Security Rules
```

Também existem scanners específicos para containers, APIs, infraestrutura em execução etc.

---

# 5. SAST

## Static Application Security Testing

SAST analisa o **código-fonte sem executar a aplicação**.

Exemplo:

```text
src/
├── auth/
├── controllers/
├── services/
├── repositories/
└── database/
```

O scanner analisa esses arquivos procurando padrões vulneráveis.

Pode detectar:

* SQL Injection;
* XSS;
* hardcoded secrets;
* uso inseguro de criptografia;
* command injection;
* path traversal;
* SSRF;
* problemas de autenticação;
* alguns problemas de autorização;
* uso inseguro de APIs.

---

# 6. Como um SAST funciona?

Um scanner simples poderia procurar:

```text
"md5("
```

Mas isso gera muitos falsos positivos.

Um SAST mais sofisticado pode analisar:

```text
Código
  ↓
Parser
  ↓
AST
  ↓
Symbol Resolution
  ↓
Control Flow
  ↓
Data Flow
  ↓
Rules
  ↓
Findings
```

## AST

AST significa:

> Abstract Syntax Tree

O código:

```javascript
const x = a + b;
```

pode ser representado estruturalmente como uma árvore.

Isso permite ao scanner entender que:

```text
x
└── operação +
    ├── a
    └── b
```

em vez de apenas procurar texto.

---

# 7. Data Flow Analysis

É uma das capacidades mais importantes de scanners modernos.

Considere:

```javascript
const password = req.body.password;

const hash = md5(password);

database.users.create({
    password: hash
});
```

Um scanner simples vê:

```text
md5()
```

Um scanner com análise de fluxo pode entender:

```text
HTTP Request
     ↓
req.body.password
     ↓
password
     ↓
MD5
     ↓
hash
     ↓
Database
```

Isso permite identificar que o MD5 está sendo utilizado no fluxo de armazenamento de senha.

---

# 8. SAST e Cryptographic Failures

As regras que criamos anteriormente poderiam ser utilizadas como regras SAST.

Exemplo:

```text
CRYPTO-002
Weak Password Hash
```

Regra:

```text
MD5/SHA1
    +
password context
    ↓
Finding
```

Resultado:

```text
CRITICAL

Weak Password Hash

src/auth/user.service.ts:47

MD5 is being used to hash a password.

Recommendation:
Use Argon2id, bcrypt or scrypt.
```

---

# 9. Ferramentas SAST

## Semgrep

O Semgrep é uma ferramenta de análise estática baseada em regras.

Conceito:

```text
Semgrep
   │
   ├── Engine
   │
   └── Rules
```

As regras descrevem padrões de código que devem ser encontrados.

É especialmente interessante para:

* regras customizadas;
* segurança;
* análise rápida;
* integração com CI/CD;
* desenvolvimento de regras próprias.

---

# 10. CodeQL

CodeQL é o mecanismo de análise de código utilizado pelo GitHub para Code Scanning.

Conceito simplificado:

```text
Código
   ↓
CodeQL Database
   ↓
Queries
   ↓
Security Findings
```

O diferencial é a capacidade de realizar análises estruturais e consultas sobre o código.

Pode identificar fluxos como:

```text
Source
  ↓
Transformation
  ↓
Sink
```

Por exemplo:

```text
req.body
   ↓
SQL query
```

que pode indicar SQL Injection.

---

# 11. SonarQube

SonarQube é uma plataforma de análise de código que cobre:

* bugs;
* vulnerabilidades;
* security hotspots;
* code smells;
* problemas de qualidade;
* manutenção.

É importante entender que qualidade de código e segurança são relacionadas, mas não são a mesma coisa.

```text
Code Quality
      +
Security
      ↓
Software Quality
```

---

# 12. SCA

## Software Composition Analysis

SCA analisa as **dependências utilizadas pela aplicação**.

Exemplo:

```json
{
  "dependencies": {
    "express": "4.x",
    "lodash": "4.x"
  }
}
```

O SCA verifica:

```text
lodash
   ↓
versão utilizada
   ↓
CVE conhecida?
   ↓
Severity
   ↓
Recommendation
```

Isso é diferente de SAST.

### SAST

Analisa:

```text
SEU CÓDIGO
```

### SCA

Analisa:

```text
SUAS DEPENDÊNCIAS
```

---

# 13. Exemplos de ferramentas SCA

Algumas ferramentas conhecidas:

* Dependabot;
* Snyk;
* OWASP Dependency-Check;
* Renovate.

Exemplo:

```text
package.json
     ↓
SCA
     ↓
express 4.x
     ↓
vulnerabilidade conhecida
     ↓
CVE
     ↓
upgrade recomendado
```

---

# 14. Secret Scanning

Secret Scanning procura credenciais expostas no código ou histórico do repositório.

Exemplos:

```text
AWS Access Key
API Key
JWT Secret
Database Password
Private Key
OAuth Token
```

Exemplo:

```javascript
const API_KEY = "xxxxxxxx";
```

O scanner pode detectar:

```text
CRITICAL

Potential API Key exposed

src/config/api.js:3
```

---

# 15. Por que Secret Scanning é importante?

Imagine:

```text
Developer
    ↓
git add .
    ↓
git commit
    ↓
git push
    ↓
GitHub
```

Se houver:

```text
AWS_SECRET_KEY
```

no commit, o segredo pode ficar registrado no histórico.

Mesmo apagando posteriormente:

```text
git commit 1
   ↓
secret
   ↓
git commit 2
   ↓
secret removed
```

o segredo pode continuar presente no histórico.

Por isso, quando um segredo real é exposto, a prioridade normalmente é:

```text
Detectar
   ↓
Revogar
   ↓
Rotacionar
   ↓
Investigar
```

e não simplesmente apagar a linha do código.

---

# 16. Container Scanning

Quando usamos Docker:

```text
Dockerfile
    ↓
docker build
    ↓
Docker Image
```

podemos analisar a imagem antes do deploy.

O scanner procura:

* pacotes vulneráveis;
* bibliotecas vulneráveis;
* componentes desatualizados;
* configurações inseguras;
* secrets;
* vulnerabilidades conhecidas.

Exemplo:

```text
Docker Image
     ↓
Trivy
     ↓
┌─────────────────────┐
│ openssl             │
│ CVE-XXXX            │
│ HIGH                │
└─────────────────────┘
```

Uma ferramenta conhecida para isso é **Trivy**.

---

# 17. IaC Scanning

## Infrastructure as Code

Analisa arquivos que descrevem infraestrutura.

Exemplos:

```text
Terraform
Kubernetes
Docker Compose
CloudFormation
Ansible
```

Exemplo:

```yaml
ports:
  - "0.0.0.0:5432:5432"
```

Um scanner pode detectar:

```text
CRITICAL

Database exposed to the public network.

PostgreSQL port 5432 is publicly accessible.
```

Isso é diferente de analisar o código da aplicação.

---

# 18. Comparação

| Scanner           | Analisa              | Exemplo               |
| ----------------- | -------------------- | --------------------- |
| SAST              | Código               | SQL Injection         |
| SCA               | Dependências         | CVE em npm            |
| Secret Scanner    | Credenciais          | API Key               |
| Container Scanner | Imagens Docker       | Pacote vulnerável     |
| IaC Scanner       | Infraestrutura       | Security Group aberto |
| DAST              | Aplicação executando | Endpoint vulnerável   |

---

# 19. DAST

## Dynamic Application Security Testing

Enquanto SAST olha:

```text
Código
```

DAST olha:

```text
Aplicação executando
```

Por exemplo:

```text
Internet
   ↓
http://localhost:3000
   ↓
DAST
   ↓
Endpoints
```

Pode testar coisas como:

* headers;
* autenticação;
* configurações HTTP;
* comportamento de endpoints;
* algumas classes de injection;
* problemas de sessão.

Exemplos de ferramentas incluem OWASP ZAP e Burp Suite.

---

# 20. SAST vs DAST

### SAST

```text
Código
   ↓
Scanner
```

Encontra problemas antes da execução.

### DAST

```text
Aplicação
   ↑
Scanner
```

Interage com a aplicação em execução.

Eles são complementares.

---

# 21. Security Gate

Uma parte importante do DevSecOps é definir quando o pipeline deve falhar.

Exemplo:

```text
Security Scan
      ↓
Findings
      ↓
┌─────────────────────────┐
│ CRITICAL = 1            │
│ HIGH = 2                │
│ MEDIUM = 4              │
└────────────┬────────────┘
             ↓
       Security Gate
             ↓
           FAIL
```

O deploy não deveria continuar.

Mas não é necessariamente correto bloquear o deploy por qualquer alerta.

Exemplo:

```text
CRITICAL → bloquear
HIGH     → bloquear
MEDIUM   → revisar
LOW      → registrar
```

A política depende da organização e do risco.

---

# 22. Security Scanning dentro do CI/CD

Um pipeline pode ser:

```text
git push
    ↓
┌──────────────────────┐
│ CI/CD                │
│                      │
│ Build                │
│ Tests                │
│ Lint                 │
│ SAST                 │
│ SCA                  │
│ Secret Scan          │
│ Docker Build         │
│ Container Scan       │
│ IaC Scan             │
└──────────┬───────────┘
           ↓
      Security Gate
           ↓
       ┌───┴───┐
       ↓       ↓
     FAIL     PASS
               ↓
             Deploy
```

---

# 23. Onde entra a IA?

IA não é obrigatória para Security Scanning.

Uma arquitetura tradicional:

```text
Code
 ↓
Parser
 ↓
AST
 ↓
Rules
 ↓
Finding
```

Uma arquitetura com IA:

```text
Code
 ↓
SAST
 ↓
Findings
 ↓
AI
 ↓
Context analysis
 ↓
Prioritization
 ↓
Explanation
```

A IA pode ajudar a:

* explicar findings;
* sugerir correções;
* reduzir falsos positivos;
* analisar contexto;
* priorizar riscos;
* auxiliar na criação de regras.

Mas regras determinísticas continuam extremamente importantes.

Para:

```text
MD5 + password
```

uma regra é mais previsível do que pedir para uma LLM decidir se isso é vulnerável.

---

# 24. O conceito de Engine

"Engine" não é necessariamente uma biblioteca específica.

É o componente que executa a análise.

Conceitualmente:

```text
Rules
   +
Engine
   +
Code
   ↓
Findings
```

Exemplo:

```text
Semgrep
   ├── Engine
   └── Rules
```

Outro modelo:

```text
CodeQL
   ├── Analysis Engine
   ├── Code Database
   └── Queries
```

---

# 25. Criar um scanner próprio

É possível criar um scanner próprio, mas existe uma diferença enorme entre:

### Criar regras

Relativamente viável:

```text
Semgrep/CodeQL
       ↑
Suas regras
```

### Criar um engine

Muito mais complexo:

```text
Lexer
 ↓
Parser
 ↓
AST
 ↓
Symbol Resolution
 ↓
Control Flow
 ↓
Data Flow
 ↓
Taint Analysis
 ↓
Rule Engine
 ↓
Findings
```

Por isso, na prática, muitas organizações utilizam engines existentes e desenvolvem regras próprias.

---

# 26. As specs de Cryptographic Failures

As specs criadas para este estudo podem representar o conhecimento de segurança:

```text
specs/
└── cryptographic-failures/
    ├── CRYPTO-001
    ├── CRYPTO-002
    ├── CRYPTO-003
    └── ...
```

Elas descrevem:

```text
O que procurar
      +
Contexto
      +
Severidade
      +
Risco
      +
Recomendação
```

Posteriormente podem ser transformadas em regras para ferramentas reais.

---

# 27. Arquitetura completa

Uma arquitetura moderna pode ser visualizada assim:

```text
                         Developer
                             │
                             ▼
                          Git Push
                             │
                             ▼
                       ┌───────────┐
                       │   CI/CD   │
                       └─────┬─────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
        ▼                    ▼                    ▼
       SAST                  SCA             Secret Scan
        │                    │                    │
        ▼                    ▼                    ▼
   CodeQL/Semgrep       Dependabot/Snyk      Secret Rules
        │                    │                    │
        └────────────────────┼────────────────────┘
                             │
                             ▼
                       Docker Build
                             │
                             ▼
                    Container Scanning
                             │
                             ▼
                         IaC Scan
                             │
                             ▼
                      Security Gate
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
                  FAIL              PASS
                                      │
                                      ▼
                                    Deploy
```

---

# 28. Conceitos para estudar posteriormente

A sequência recomendada é:

```text
1. OWASP Top 10
        ↓
2. SAST
        ↓
3. AST
        ↓
4. Data Flow Analysis
        ↓
5. Taint Analysis
        ↓
6. Semgrep
        ↓
7. CodeQL
        ↓
8. SCA
        ↓
9. Secret Scanning
        ↓
10. Container Scanning
        ↓
11. IaC Scanning
        ↓
12. DAST
        ↓
13. DevSecOps
        ↓
14. Security Gates
        ↓
15. Security CI/CD
        ↓
16. AppSec
```

---

# 29. Principal ideia para memorizar

Não confunda:

```text
CI/CD
```

com:

```text
Security Scanner
```

nem:

```text
Security Scanner
```

com:

```text
SAST
```

A relação é:

```text
CI/CD
 │
 └── Security
      │
      ├── SAST
      ├── SCA
      ├── Secret Scanning
      ├── Container Scanning
      ├── IaC Scanning
      └── DAST
```

E:

```text
SAST
 │
 ├── Engine
 └── Rules
```

O **engine analisa**.

As **rules definem o que procurar**.

O **CI/CD executa o processo automaticamente**.

E o **DevSecOps coloca segurança dentro do ciclo de desenvolvimento**.
