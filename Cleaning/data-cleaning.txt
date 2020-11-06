# DataEngineering
Repository of code snippets for usual data engineering stuff.


**Eliminar duplicados en una tabla. Script parametrizado SQL Server**

- Uso: Script que genera un stored procedure que elimina duplicados de una tabla, tomando como parámetros su PK y el campo de fecha a utilizar para comparar los registros, con el fin de dejar en la BD sólo el más reciente.

- Palabras clave: duplicados, eliminar, stored procedure, sql.

- Lenguaje: SQL.

- Autor: Iván Ingaramo.


<pre><code>
CREATE PROC usp_eliminarDuplicadosPorID @nombreTabla [nvarchar](300),@campoFecha [datetime] AS
BEGIN

	IF OBJECT_ID('tempdb.dbo.#DuplicadosRaw', 'U') IS NOT NULL
	  DROP TABLE #DuplicadosRaw; 
	IF OBJECT_ID('tempdb.dbo.#DuplicadosPreliminar', 'U') IS NOT NULL
	  DROP TABLE #DuplicadosPreliminar; 
	IF OBJECT_ID('tempdb.dbo.#DuplicadosSK', 'U') IS NOT NULL
	  DROP TABLE #DuplicadosSK; 
  
	declare @query nvarchar(MAX)
	set @query = '	SELECT *
					INTO #DuplicadosRaw
					FROM(SELECT ID 
					FROM ' + @nombreTabla + '
					GROUP BY ID
					HAVING COUNT(*)>1) origen;
	

					SELECT * 
					INTO #DuplicadosPreliminar
					FROM (SELECT nombreTabla.SK,
					nombreTabla.ID,
					ROW_NUMBER() OVER (PARTITION BY nombreTabla.ID ORDER BY ' + @campoFecha + ' DESC) AS ROWN
					FROM ' + @nombreTabla + ' nombreTabla
					JOIN #DuplicadosRaw RAW ON RAW.ID = nombreTabla.ID) origen;

					SELECT SK
					INTO #DuplicadosSK
					FROM (SELECT SK 
					FROM #DuplicadosPreliminar
					WHERE ROWN>1) origen;

					DELETE FROM ' + @nombreTabla + '
					WHERE SK IN (SELECT SK FROM #DuplicadosSK);'

	exec (@query)

	IF OBJECT_ID('tempdb.dbo.#DuplicadosRaw', 'U') IS NOT NULL
		DROP TABLE #DuplicadosRaw; 
	IF OBJECT_ID('tempdb.dbo.#DuplicadosPreliminar', 'U') IS NOT NULL
		DROP TABLE #DuplicadosPreliminar; 
	IF OBJECT_ID('tempdb.dbo.#DuplicadosSK', 'U') IS NOT NULL
		DROP TABLE #DuplicadosSK; 
  
END
</code></pre>
