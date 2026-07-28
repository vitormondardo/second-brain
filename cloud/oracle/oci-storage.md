# How to connect and configure a Bucket Storage of credentials Oracle Cloud

## Step 1 - Create a bucket

Go in:
```bash
Storage → Object Storage & Archive Storage → Buckets → Create Bucket.
```

Fill out:
- Bucket name: wsd-attachments
- Bucket scope: Regional ← deixar o padrão
- Default storage tier: Standard ← deixar o padrão
- Enable auto-tiering: deixar desabilitado por enquanto
- Enable object versioning: deixar desabilitado — versionamento consome cota do Free Tier desnecessariamente no MVP
- Emit object events: deixar desabilitado
- Uncommitted multipart uploads cleanup: pode habilitar — limpa uploads incompletos automaticamente após 7 dias, é uma boa prática e não tem custo
- Encryption: Encrypt using Oracle managed keys ← deixar o padrão, não precisa de chave própria agora
- Tags: deixar vazio
- Resource logging: deixar desabilitado

## Step 2 - Generate Secret Key

1. Go in:
```bash
Identity & Security → Users → (seu usuário) → Customer Secret Keys → Generate Secret Key
```

2. Generate the secret key

3. Copy the secret key

*Note: the secret key now will be available permanently

## Step 3 - Copy the namespace

Go in **Tenancy Settings**

## Step 4 - Add the credentials OCI
Fill out .env with:
```bash
OCI_S3_ENDPOINT=https://{namespace}.compat.objectstorage.sa-saopaulo-1.oraclecloud.com
OCI_S3_REGION=sa-saopaulo-1
OCI_S3_ACCESS_KEY_ID=
OCI_S3_SECRET_ACCESS_KEY=
OCI_S3_BUCKET_NAME=wsd-attachments
OCI_S3_PRESIGNED_URL_TTL=3600
```
