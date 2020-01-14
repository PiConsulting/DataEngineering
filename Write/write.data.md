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

#subimos los archivos al blob storage
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


**Usar AzCopy para subir datos a Blob Storage o Lake Gen 2**

```
- Uso: script que automaticamente descarga az-copy, descomprime el ejecutable, toma todos los archivos de una carpeta y los sube al directorio que configuremos en un blob storage. 
Presenta una ventana al usuario para que elija una carpeta. 
Utiliza Shared Access Signature, y SOLO funcionara en windows. 
Para que funcione en linux hay que usar otro link que descargue la version linux de azcopy.
Para generar el SAS token, ir al recurso del lake en el portal de Azure, elegir Shared Access Signature, poner un rango de fechas y hacer click en Generate SAS. Luego copiar el que dice SAS token.

- Palabras clave: blob, data lake, storage account, azure, powershell, shared access signature, subida, azcopy.

- Lenguaje: PowerShell.

- Autor: Martin Zurita.

```

```
Function Get-Folder($initialDirectory) {
    [void] [System.Reflection.Assembly]::LoadWithPartialName('System.Windows.Forms')
    $FolderBrowserDialog = New-Object System.Windows.Forms.FolderBrowserDialog
    $FolderBrowserDialog.RootFolder = 'MyComputer'
    if ($initialDirectory) { $FolderBrowserDialog.SelectedPath = $initialDirectory }
    [void] $FolderBrowserDialog.ShowDialog()
    return $FolderBrowserDialog.SelectedPath
}

$copyURL = get-folder($psscriptroot)
write-output("Bienvenido! Intentaremos subir la carpeta $copyURL")

[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
write-output("Paso 1. Descarga de la herramienta azcopy")
Invoke-WebRequest https://aka.ms/downloadazcopy-v10-windows -OutFile azcopyv10.zip

write-output("Paso 2. Extraccion del ejecutable")
Add-Type -AssemblyName System.IO.Compression.FileSystem
$zip = [IO.Compression.ZipFile]::OpenRead("$PSscriptRoot\azcopyv10.zip")
$entries = $zip.Entries | where {$_.FullName -like '*azcopy.exe'}
$entries | foreach {[IO.Compression.ZipFileExtensions]::ExtractToFile( $_, $PSscriptRoot + '\' + $_.Name) }

# Esto es para un lake gen 2, si es un blob storage comun, reemplazar dfs por blob
$blobdir = "https://<nombre_recurso>.dfs.core.windows.net/<nombre_filesystem>/<alguna_carpeta>/<SAS token>"

write-output("Paso 3. Subida de datos..")
.\azcopy.exe copy $copyURL $blobdir --recursive

$zip.Dispose()
```
---
