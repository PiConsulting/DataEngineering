# DataEngineering
Repository of code snippets for usual data engineering stuff.

---

**Subir datos a Blob Storage PowerShell**

```
- Uso: script que sube una carpeta entera a un path especifico de un blob storage con PowerShell. SOLO funciona con el Access Key del lake, no incluye service principal ni user authentication.

- Palabras clave: blob, storage account, azure, powershell, access key, subida.

- Lenguaje: PowerShell.

- Autor: Martin Zurita.

```

```
#Datos de blob storage
$StorageAccountName = "AccName"
$StorageAccountKey = "AccKey"
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
$ContainerName = "container-name"
$target = "C:\Folder\To\Files\"

#subimos los archivos al blob storage y lo eliminamos del directorio local
$archivos = Get-ChildItem -Path $target
$archivos | ForEach-Object($_){
		$BlobNameBackup = "blobFolder\$($_.Name)"
		Set-AzureStorageBlobContent -File "$($target)\$($_.Name)" `
			-Container $ContainerName `
			-Blob $BlobNameBackup `
			-Context $ctx -Force
}

```
---
