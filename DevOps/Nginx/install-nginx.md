# Installation Nginx via terminal

1. Get download in: https://nginx.org/en/download.html?utm_source=chatgpt.com

2. Select the stable version

3. Extraia a pasta
```powershell
New-Item -ItemType Directory -Path "$env:USERPROFILE\nginx" -Force
```
4. Include the package in your user

```powershell
Expand-Archive `
  -Path "$env:USERPROFILE\Downloads\nginx-1.30.4.zip" `
  -DestinationPath "$env:USERPROFILE\nginx" `
  -Force
```

Provavelmente ficará:
```bash
C:\Users\SEU_USUARIO\
└── nginx\
    └── nginx-1.30.4\
        ├── conf\
        ├── html\
        ├── logs\
        └── nginx.exe

```

5. Entre na pasta
```powershell
cd "$env:USERPROFILE\nginx\nginx-1.30.4"
```
Confirme:
```powershell
Get-ChildItem
```

Você deve encontrar:
```bash
nginx.exe
conf
html
logs
```

6. Teste o Nginx
```poweshell
.\nginx.exe -t
```
Se a configuração padrão estiver correta:
```bash
nginx: the configuration file .../conf/nginx.conf syntax is ok
nginx: configuration file .../conf/nginx.conf test is successful
```

