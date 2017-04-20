<properties 
   pageTitle="Flujo de trabajo de PowerShell de aprendizaje"
   description="Este artículo está pensado como una lección rápida para autores familiarizados con PowerShell para conocer las diferencias específicas entre PowerShell y flujo de trabajo de PowerShell."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="bwren" />

# <a name="learning-windows-powershell-workflow"></a>Flujo de trabajo de aprendizaje Windows PowerShell

Runbooks en Azure automatización se implementan como flujos de trabajo de Windows PowerShell.  Un flujo de trabajo de Windows PowerShell es similar a un script de Windows PowerShell, pero tiene algunas diferencias importantes que se pueden confundir a un nuevo usuario.  Este artículo está destinado a los usuarios que ya están familiarizados con PowerShell y explica brevemente los conceptos que requieren si va a convertir un script de PowerShell para un flujo de trabajo de PowerShell para su uso en un runbook.  

Un flujo de trabajo es una secuencia de pasos programadas, conectadas que realizan tareas de ejecución larga o requerir coordinar varios pasos en varios dispositivos o nodos administrados. Las ventajas de un flujo de trabajo a través de una secuencia de comandos normal incluyen la capacidad de realizar simultáneamente una acción en varios dispositivos y la capacidad para recuperar automáticamente de errores. Un flujo de trabajo de Windows PowerShell es una secuencia de comandos de Windows PowerShell que aprovecha Windows Workflow Foundation. Mientras el flujo de trabajo está escrito con sintaxis de Windows PowerShell y se inicia Windows PowerShell, se procesa por Windows Workflow Foundation.

Para obtener información detallada sobre los temas de este artículo, vea [Introducción a flujo de trabajo de Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Tipos de runbook

Existen tres tipos de runbook en Azure automatización, *Flujo de trabajo de PowerShell*, *PowerShell* y *gráfica*.  Definir el tipo de runbook cuando cree runbook y no se puede convertir un runbook al otro tipo una vez se ha creado.

Runbooks de flujo de trabajo de PowerShell y PowerShell runbooks son para los usuarios que prefieran para trabajar directamente con el código de PowerShell, ya sea usando el editor de texto de automatización de Azure o un editor sin conexión como PowerShell ISE. Si va a crear un runbook de flujo de trabajo de PowerShell debería entender la información en este artículo. 

Runbooks gráficas permiten crear un runbook utilizando el mismo actividades y cmdlets pero mediante una interfaz gráfica que simplifica el proceso del flujo de trabajo de PowerShell subyacente.  Conceptos en este artículo como punto de comprobación y ejecución en paralelo aún se aplican a runbooks gráfica, pero no tiene que preocuparse por la sintaxis detallada. 

## <a name="basic-structure-of-a-workflow"></a>Estructura básica de un flujo de trabajo

El primer paso para convertir una secuencia de comandos de PowerShell en un flujo de trabajo de PowerShell incluye con la palabra clave de **flujo de trabajo** .  Un flujo de trabajo se inicia con la palabra clave de **flujo de trabajo** seguida del cuerpo de la secuencia de comandos entre llaves. El nombre del flujo de trabajo sigue la palabra clave de **flujo de trabajo** , tal como se muestra en la siguiente sintaxis. 

    Workflow Test-Workflow
    {
       <Commands>
    }

El nombre del flujo de trabajo debe coincidir con el nombre del runbook de automatización. Si se va a importar runbook, el nombre de archivo debe coincidir con el nombre del flujo de trabajo y debe terminar en. ps1.

Para agregar parámetros para el flujo de trabajo, utilice la palabra clave de **parámetro** igual que lo haría en una secuencia de comandos. 

## <a name="code-changes"></a>Cambios de código

Código de flujo de trabajo de PowerShell es prácticamente idéntico al código de secuencia de comandos de PowerShell excepto algunos cambios significativos.  Las secciones siguientes describen los cambios que debe realizar en un script de PowerShell para funcione en un flujo de trabajo.

### <a name="activities"></a>Actividades

Una actividad es una tarea específica en un flujo de trabajo. Como una secuencia de comandos se compone de uno o más comandos, un flujo de trabajo se compone de una o varias de las actividades que se realizan en una secuencia. Flujo de trabajo de Windows PowerShell convierte automáticamente muchos de los cmdlets de Windows PowerShell en actividades cuando se ejecuta un flujo de trabajo. Al especificar uno de estos cmdlets en su runbook, Windows Workflow Foundation realmente ejecuta la actividad correspondiente. Para los cmdlets sin actividad correspondiente, el flujo de trabajo de Windows PowerShell se ejecuta automáticamente el cmdlet dentro de una actividad [InlineScript](#inlinescript) . Hay un conjunto de cmdlets que se excluyen y no se pueden usar en un flujo de trabajo, a menos que incluya explícitamente en un bloque de InlineScript. Para obtener más información sobre estos conceptos, vea [Usar actividades en flujos de trabajo de secuencia de comandos](http://technet.microsoft.com/library/jj574194.aspx).

Actividades de flujo de trabajo comparten un conjunto de parámetros comunes para configurar su funcionamiento. Para obtener más información acerca de los parámetros comunes de flujo de trabajo, consulte [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parámetros de posición

No puede usar los parámetros de posición con actividades y cmdlets en un flujo de trabajo.  Todo esto significa es que debe utilizar los nombres de parámetro.

Por ejemplo, considere el código siguiente que obtiene todos los servicios en ejecución.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Si intenta ejecutar este mismo código en un flujo de trabajo, recibirá un mensaje como "conjunto de parámetros no se puede resolver con parámetros con nombre especificada."  Para corregir esto, simplemente proporcionar el nombre de parámetro como en el ejemplo siguiente.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objetos deserializados

Objetos en flujos de trabajo se deserializan.  Esto significa que sus propiedades siguen estando disponibles, pero no sus métodos.  Por ejemplo, considere el siguiente código de PowerShell que detiene un servicio mediante el método Stop del objeto de servicio.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Si intenta ejecutar esto en un flujo de trabajo, recibirá un error que indica "llamada a un método no es compatible con un flujo de trabajo de Windows PowerShell".  

Una opción es ajustar estas dos líneas de código de un bloque de [InlineScript](#InlineScript) en cuyo caso $Service sería un objeto de servicio dentro del bloque. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

Otra opción es usar otro cmdlet que realiza la misma funcionalidad que el método, si está disponible.  En el caso de nuestro ejemplo, el cmdlet Stop-Service proporciona la misma funcionalidad que el método Stop y puede usar las siguientes acciones para un flujo de trabajo.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

La actividad **InlineScript** es útil cuando se necesita para ejecutar uno o más comandos como tradicional script de PowerShell en lugar de flujo de trabajo de PowerShell.  Mientras los comandos de un flujo de trabajo se envían a Windows Workflow Foundation para el procesamiento, se procesan los comandos de un bloque de InlineScript Windows PowerShell. 

InlineScript utiliza la sintaxis que se muestra a continuación.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Puede devolver resultados de una InlineScript asignando el resultado a una variable. En el ejemplo siguiente se detiene un servicio y, a continuación, se envía el nombre de servicio.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Puede pasar valores en un bloque de InlineScript, pero debe utilizar el modificador de ámbito de **$Using** .  El ejemplo siguiente es idéntico al ejemplo anterior excepto en que el nombre del servicio se proporciona una variable. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Aunque InlineScript actividades pueden ser críticas en determinados flujos de trabajo, que no admiten construcciones de flujo de trabajo y solo se utiliza cuando es necesario por los siguientes motivos:

- No puede usar [los controles](#Checkpoints) dentro de un bloque de InlineScript. Si se produce un error dentro del bloque, se debe reanudar desde el principio del bloque.
- No puede usar la [ejecución en paralelo](#parallel-execution) dentro de un InlineScriptBlock.
- InlineScript afecta a escalabilidad del flujo de trabajo, ya que contiene la sesión de Windows PowerShell para toda la longitud del bloque InlineScript.

Para obtener más información acerca de cómo utilizar InlineScript, vea [Ejecutar comandos de Windows PowerShell en un flujo de trabajo](http://technet.microsoft.com/library/jj574197.aspx) y [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Procesamiento en paralelo

Una ventaja de flujos de trabajo de Windows PowerShell es la capacidad de realizar un conjunto de comandos en paralelo en lugar de secuencialmente como con una secuencia de comandos habituales. 

Puede usar la palabra clave **paralelas** para crear un bloque de script con varios comandos que se ejecutarán al mismo tiempo. Usa la sintaxis que se muestra a continuación. En este caso, se iniciará Activity1 y Activity2 al mismo tiempo. Activity3 se iniciará después de que han completado Activity1 y Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por ejemplo, tenga en cuenta los siguientes comandos de PowerShell que copiar varios archivos a un destino de red.  Estos comandos se ejecutan secuencialmente para que un archivo debe finalizar copia antes de que se inicie la siguiente.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

El siguiente flujo de trabajo ejecuta estos mismos comandos en paralelo para que todos comiencen copiar al mismo tiempo.  Después de que todos están completamente copiado es finalización muestra el mensaje.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Puede usar el **ForEach-paralelo** construcción de comandos de proceso para cada elemento de una colección de forma simultánea. Los elementos de la colección se procesan en paralelo mientras los comandos en el bloque de secuencia de comandos se ejecutan secuencialmente. Usa la sintaxis que se muestra a continuación. En este caso, se iniciará Activity1 al mismo tiempo para todos los elementos de la colección. Para cada elemento, Activity2 se iniciará una vez completada la Activity1. Activity3 se iniciará después de que han completado Activity1 y Activity2 para todos los elementos.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

En el siguiente ejemplo es similar al ejemplo anterior, copiar los archivos en paralelo.  En este caso, se muestra un mensaje para cada archivo después de copia.  Después de que todos están completamente copiado es finales de finalización muestra el mensaje.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  No se recomienda ejecución runbooks secundarios en paralelo, ya que esto ha comprobado que dé resultados poco confiables.  No se mostrará el resultado de runbook secundarios a veces y configuración en un elemento secundario runbook puede afectar a los otros runbooks paralelo secundario 


## <a name="checkpoints"></a>Puntos de comprobación

Un *punto de comprobación* es una instantánea del estado actual del flujo de trabajo que contiene el valor actual de variables y todos los resultados generados a ese punto. Si un flujo de trabajo termina en error o se ha suspendido, a continuación, la próxima vez que se ejecuta comenzará desde el último punto de comprobación en lugar de inicio del flujo de trabajo.  Puede establecer un punto de comprobación en un flujo de trabajo con la actividad de **Flujo de trabajo de control** .

En el siguiente código de ejemplo, se produce una excepción después Activity2 causando finalizar el flujo de trabajo. Cuando se vuelve a ejecutar el flujo de trabajo, se inicia ejecutando Activity2 ya que esto era después de establece el último punto de comprobación.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Debe establecer puntos de comprobación en un flujo de trabajo después de actividades que pueden ser susceptible a excepción y no se deben repetir si se reanude el flujo de trabajo. Por ejemplo, el flujo de trabajo puede crear una máquina virtual. Puede establecer un punto de comprobación antes y después de los comandos para crear la máquina virtual. Si se produce un error en la creación, se podrían repetir los comandos, si el flujo de trabajo se inicie de nuevo. Si el el flujo de trabajo se produce un error cuando se complete correctamente la creación, a continuación, la máquina virtual no se creará nuevo cuando se reanude el flujo de trabajo.

En el ejemplo siguiente se copia varios archivos en una ubicación de red y se establece un punto de comprobación después de cada archivo.  Si se pierde la ubicación de red, finalizará el flujo de trabajo de error.  Cuando se inicie de nuevo, se reanude en el último punto de comprobación, lo que significa que se omitirá únicamente los archivos que ya se han copiado.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Dado que las credenciales de nombre de usuario no se conservan después de llamar a la actividad de [Flujo de trabajo de suspender](https://technet.microsoft.com/library/jj733586.aspx) o después del último punto de control, debe establecer las credenciales null y, a continuación, recuperarlas a otra vez desde el almacén de activos después de llama a **Flujo de trabajo de suspender** o control.  En caso contrario, puede recibir el siguiente mensaje de error: no se puede reanudar *la tarea de flujo de trabajo, ya sea porque datos podrían no se guarden por completo, o guardar datos se ha dañado. Debe reiniciar el flujo de trabajo.*

El mismo código siguiente muestra cómo controlar esto en sus runbooks de flujo de trabajo de PowerShell.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


No es necesario si se autentica con una cuenta de ejecutar como configurada con un servicio principal.  

Para obtener más información acerca de los controles, vea [Agregar controles a un flujo de trabajo de secuencia de comandos](http://technet.microsoft.com/library/jj574114.aspx).


## <a name="next-steps"></a>Pasos siguientes

- Para empezar con PowerShell runbooks de flujo de trabajo, consulte [Mi primera runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md) 
