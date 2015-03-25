#Powershell

##Realizar script en Powershell

El script debe realizar las siguientes acciones:

- descargar este fichero **http://tomasz.systems/output.txt** . 
- Verificar cada una de las ip aleatorias que contiene si responde a ping o no.
- Exportar a un fichero output.txt el resultado indicando para cada ip True o False en funcion del resultado.
- Subir a github y documentarlo

Extra: 

- Mostrar por pantalla estado actual y porcentaje de exito:
Ej: `7/49 14.28571428571429%` (7 ok de 49 verificadas (14.28571428571429%)

##Solucion:

	#Descargamos el txt de internet  y lo guardamos en data.txt
	$clnt = new-object System.Net.WebClient
	$url = "http://tomasz.systems/output.txt"
	$file = "./data.txt"
	$clnt.DownloadFile($url,$file)

	#Quitamos las lineas en blanco para evitar errores
	(gc ./data.txt) | ? {$_.trim() -ne "" } | set-content ./data.txt
	$servers = get-content ./data.txt
	#Inicializamos variables de contadores
	$ok=0
	$total=0
	$porcentaje=0

	#Indicamos que pruebe cada ip del txt
	foreach ($server in $servers)
	{
	#Incrementamos cada linea en la variable total y calculamos el porcentaje
		$total=$total+1
		$porcentaje=(100*$ok)/$total
	#Si responde a ping incrementamos la variable ok, mostramos en pantalla ok/total % y añadimos ip y true al txt
		if (Test-Connection $server -Count 1 -ea 0 -Quiet)
		{ 
			$ok=$ok+1
			$result = "True"
			Write-Host "$ok/$total $porcentaje%"
			Write-Output "$server   $result" | Out-File ./out.txt -Append
		} 
	#Si no responde a ping mostramos en pantalla ok/total % y añadimos ip y false al txt
		else 
		{ 
			$result = "False"
			Write-Host "$ok/$total $porcentaje%"
			Write-Output "$server    $result" | Out-File ./out.txt -Append 
		}
	}
	
	#Indicamos final del proceso y resultados del mismo
	Write-Host "Proceso finalizado: $ok IP del archivo respondieron a ping de un total de $total. Es un &porcentaje%"
