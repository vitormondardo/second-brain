** Segurança não tem "zero possibilidades de invasão" como estado alcançável — isso vale para qualquer sistema, de qualquer empresa, mesmo bancos e gigantes de tecnologia com times de centenas de pessoas. O objetivo realista e profissional é reduzir a superfície de ataque a um nível onde a exploração exige esforço desproporcional ao valor do alvo, e ter visibilidade (logs, auditoria) para detectar e responder quando algo passar. Dito isso, vou te dar o plano mais completo possível para esses dois pontos, com o que implementar e o que estudar para cada camada.
#Implementação completa (JWT em cookie httpOnly + defesa CSRF)

1.1 Backend — dependências
```bash
npm install cookie-parser
npm install -D @types/cookie-parser
```

Esperado:
```bash
npm.cmd install cookie-parser

added 3 packages, and audited 47 packages in 31s

5 packages are looking for funding
  run `npm fund` for details
  
npm.cmd install -D @types/cookie-parser

added 10 packages, and audited 57 packages in 1s

5 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

## 1.2 main.ts — registrar middleware e CORS restrito
```typescript
import cookieParser from 'cookie-parser';

app.use(cookieParser());
app.enableCors({
  origin: process.env.CORS_ORIGIN, // origem exata, nunca '*'
  credentials: true,
});
```
