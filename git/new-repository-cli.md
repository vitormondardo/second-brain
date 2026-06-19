# Como inicializar o git em um novo repositório.
*Repositório já existente no Github

## 1. Inicialize o git
```powershell
git init
```

## 2. Adicione as alterações
```powershell
git add . 
```

## 3. Commit inicial
```powershell
git commit -m "feat: commit inicial"
```

## 4. Mude o nome da branch: master -> main
```powershell
git branch -M main
```

## 5. Vincule ao repositório existente no Github.
```powershell
git remote add origin https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
```

## 6. Push para a branch
```powershell
git push -u origin main
```
