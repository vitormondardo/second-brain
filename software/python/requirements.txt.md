## O que é o requirements.txt
É um arquivo de texto simples que lista todas as bibliotecas Python que seu projeto precisa para funcionar. Quando outra pessoa baixar seu repositório, ela roda um único comando e instala tudo automaticamente — sem precisar adivinhar o que instalar.

##Como ele fica:
```python
pandas==2.2.1
numpy==1.26.4
scikit-learn==1.4.2
matplotlib==3.8.4
seaborn==0.13.2
scipy==1.13.0
jupyterlab==4.1.6
openpyxl==3.1.2
```
## Por que isso importa?
Sem ele, se alguém tentar rodar seu notebook, vai aparecer erro de ModuleNotFoundError. Com ele, basta rodar:
```python
bashpip install -r requirements.txt
```
E tudo funciona. É uma boa prática de Engenharia de Dados e MLOps — reforça exatamente o perfil técnico que seu TCC quer demonstrar.

Como gerar o seu automaticamente
Com seu ambiente virtual ativado e todas as bibliotecas já instaladas, rode:
```python
python -m pip freeze > requirements.txt
```

Se der erro também, é porque o Python pode não estar no PATH. Verifique com:
```python
python --version
```
Se não reconhecer, significa que o Python não foi adicionado ao PATH durante a instalação. A solução mais rápida é reinstalar o Python e marcar a opção "Add Python to PATH" durante o setup.

# Gerenciadores de Pacotes por Linguagem

| Linguagem / Ambiente | Arquivo | Comando para gerar |
|---|---|---|
| **Python (pip)** | `requirements.txt` | `pip freeze > requirements.txt` |
| **Python (Poetry)** | `pyproject.toml` | `poetry init` |
| **Node.js** | `package.json` | `npm init` |
| **Java (Maven)** | `pom.xml` | manual |
| **Java (Gradle)** | `build.gradle` | manual |
| **PHP** | `composer.json` | `composer init` |
| **Ruby** | `Gemfile` | `bundle init` |
| **Rust** | `Cargo.toml` | `cargo init` |
| **.NET / C#** | `*.csproj` | `dotnet new` |

No seu caso, como Python, o requirements.txt resolve tudo. É o padrão mais simples e amplamente reconhecido para projetos acadêmicos e de ciência de dados.
