1. Pipeline de CI (Validação a cada Pull Request)
Crie o arquivo .github/workflows/ci.yml. Este workflow sobe um banco de dados temporário respeitando o encoding obrigatório
, instala as dependências, constrói os projetos, roda os testes e verifica as migrations
.
name: CI Pipeline

on:
  pull_request:
    branches:
      - main

jobs:
  build-and-test:
    name: Build & Test
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:16 # Versão definida na stack [4]
        env:
          POSTGRES_USER: admin
          POSTGRES_PASSWORD: password
          POSTGRES_DB: wsd_test
          # Encoding UTF-8 obrigatório conforme CLAUDE.md e ADR-002 [1, 5]
          POSTGRES_INITDB_ARGS: "--encoding=UTF8 --lc-collate=C --lc-ctype=C"
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Setup Node.js (LTS)
        uses: actions/setup-node@v4
        with:
          node-version: '20' # Versão LTS exigida [4]
          cache: 'npm' # Otimização de cache [6]

      - name: Instalar dependências (Backend)
        working-directory: ./apps/backend
        run: npm ci

      - name: Verificar Migrations Pendentes e Integridade
        working-directory: ./apps/backend
        env:
          DB_HOST: localhost
          DB_PORT: 5432
          DB_USER: admin
          DB_PASSWORD: password
          DB_NAME: wsd_test
        # Verifica se todas as migrations podem ser aplicadas com sucesso [3]
        run: npm run migration:run

      - name: Testes Unitários e Cobertura (Backend)
        working-directory: ./apps/backend
        # Cobertura mínima de 80% exigida para application/use-cases/ [7]
        run: npm run test -- --coverage 

      - name: Build do Backend
        working-directory: ./apps/backend
        run: npm run build

      - name: Instalar dependências (Frontend)
        working-directory: ./apps/frontend
        run: npm ci

      - name: Build do Frontend
        working-directory: ./apps/frontend
        run: npm run build
2. Pipeline de CD (Deploy contínuo via SSH)
Crie o arquivo .github/workflows/cd.yml. Ele será engatilhado no merge da main, fará o build das imagens Docker e as enviará para o GitHub Container Registry (GHCR), acessando a VPS via SSH logo em seguida para o deploy final
.
name: CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build-and-publish:
    name: Build & Push Docker Images
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write # Necessário para publicar no GHCR [11, 12]

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Login no GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }} # Token automático do Actions [11]

      - name: Build e Push (Backend)
        uses: docker/build-push-action@v5
        with:
          context: ./apps/backend
          push: true
          tags: ghcr.io/${{ github.repository }}/wsd-backend:latest

      - name: Build e Push (Frontend)
        uses: docker/build-push-action@v5
        with:
          context: ./apps/frontend
          push: true
          tags: ghcr.io/${{ github.repository }}/wsd-frontend:latest

  deploy:
    name: Deploy to Production VPS
    needs: build-and-publish
    runs-on: ubuntu-latest
    steps:
      - name: Executar Deploy via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.VPS_HOST }} # IP do Servidor [13]
          username: ${{ secrets.VPS_USER }} # Usuário root/ubuntu [14]
          key: ${{ secrets.VPS_SSH_KEY }} # Chave Privada [14]
          port: ${{ secrets.VPS_PORT }} # Porta SSH (ex: 22) [14]
          script: |
            cd /opt/wsd # Diretório de instalação do projeto [10, 15]
            
            # Autenticar VPS no GHCR para pull da imagem privada (se aplicável)
            echo "${{ secrets.GITHUB_TOKEN }}" | docker login ghcr.io -u ${{ github.actor }} --password-stdin
            
            # Puxar código atualizado (docker-compose.yml etc)
            git pull origin main
            
            # Atualizar as imagens e reiniciar os containers [10]
            docker compose pull
            docker compose up -d # O recarregamento será com as novas imagens [16]
Configurações Necessárias no Repositório
Para que o pipeline de CD tenha permissão de acessar a infraestrutura e executar as ações em produção, você precisará configurar as seguintes Secrets (Settings > Secrets and variables > Actions no seu repositório GitHub)
:
VPS_HOST: O endereço IP público da sua máquina virtual de produção
.
VPS_PORT: A porta do serviço SSH (padrão é 22)
.
VPS_USER: O usuário de acesso à máquina (ex: root ou ubuntu)
.
VPS_SSH_KEY: A chave privada gerada (o conteúdo do seu id_rsa ou id_ed25519 correspondente à chave pública cadastrada no servidor)
.
Com essa configuração, você garante que qualquer modificação de código ou esquema passe pelas travas de segurança (Testes a 80%, validação de Typescript e Migrations de TypeORM) e que a implantação em produção seja auditável, removendo completamente o "medo do deploy" e possíveis erros humanos na VPS
