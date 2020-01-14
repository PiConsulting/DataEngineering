# DataEngineering
Repository of code snippets for usual data engineering stuff.

---

**Leer base de datos SQL Server PowerShell**

```
- Uso: script que trae resultadods de una query en sql server desde PowerShell. 

- Palabras clave: sql, sql server, azure, powershell, sqlcmd.

- Lenguaje: PowerShell.

- Autor: Martin Zurita.

```

```
# Datos para BD
$instancia = "InstanciaSqlServer"
$nombreBD = "DBName"
$usuarioBD = "UserDB"
$passwordBD = "Pass"

# Query para traer todas las tablas de la base de datos.
# Si falla diciendo que no encuentra Invoke-Sqlcmd como un comando valido, 
# falta instalar el modulo -> Install-Module -Name SqlServer -AllowClobber
$tablas = Invoke-Sqlcmd -ServerInstance $instancia `
						-Database $nombreBD `
						-Username $usuarioBD `
						-Password $passwordBD `
						-Query "SELECT * FROM SOMETABLE"

```
---
