# Camadas e ferramentas usadas em um ambiente de produção real
*Não basta funcionar, deve ter Observabilidade, Logs, Erros, Infraestrutura

## Backend - a camada mais importante

1. GitHub Actions → CI/CD
2. Docker + Docker Compose → execução
3. Nginx → proxy reverso
4. Certbot → SSL
5. PostgreSQL gerenciado ou container
6. Prisma migrations
7. Pino/Winston → logs
8. Prometheus + Grafana → observabilidade
9. Sentry → erros
10. Terraform → infraestrutura como código
