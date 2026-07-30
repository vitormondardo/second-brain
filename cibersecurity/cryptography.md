PS C:\> $bytes = New-Object byte[] 32
PS C:\> [System.Security.Cryptography.RandomNumberGenerator]::Create().GetBytes($bytes)
PS C:\> -join ($bytes | ForEach-Object { $_.ToString('x2') })
