<properties 
   pageTitle="Runbooks secundarios en automatización de Azure | Microsoft Azure"
   description="Describe los diferentes métodos para iniciar un runbook en Azure automatización desde otro runbook y compartir información entre ellas."
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>Runbooks secundarios en automatización de Azure

Es un procedimiento recomendado en Azure automatización escribir runbooks reutilizable, modular con una función independiente que puede ser usada por otras runbooks. Un runbook primario llamará a menudo uno o más runbooks secundarios para realizar la funcionalidad necesaria. Hay dos maneras de llamar a un runbook secundarios, y cada una tiene diferencias inequívocas que debería entender para que pueda determinar cuál será mejor para sus escenarios diferentes.

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar un runbook secundarios mediante la ejecución en línea

Para llamar a un insertado runbook desde otro runbook, use el nombre del runbook y proporcionar los valores para sus parámetros exactamente igual que lo haría una actividad o cmdlet.  Runbooks todos en la misma cuenta de automatización están disponibles para todos los otros para usarse de esta manera. Runbook primario esperará runbook secundarios completar antes de pasar a la siguiente línea y los resultados se devuelven directamente a la principal.

Cuando se invoca un runbook de insertado, se ejecuta en el mismo trabajo como runbook primario. No habrá ninguna indicación en el historial de trabajos del runbook secundarios que funcionaba. Las excepciones y cualquier secuencia de salida de runbook secundarios se asociará con el elemento primario. Resultado menos trabajos y hace que sea más fácil para realizar un seguimiento y solucionar problemas desde cualquier excepción iniciada por runbook secundarios y cualquiera de sus resultados de la secuencia está asociados con el trabajo principal.

Cuando se publica un runbook, ya se debe publicar cualquier runbooks secundarios que llama. Esto es porque automatización de Azure, crea una asociación con cualquier runbooks secundarios cuando se compila un runbook. Si no, runbook primario aparecerá publicar correctamente, pero generará una excepción cuando se inicia. Si esto sucede, puede volver a publicar runbook principal para hacer referencia correctamente a la runbooks secundarios. No es necesario volver a publicar runbook primario si cualquiera de los runbooks secundarios se cambian porque ya se creó la asociación.

Los parámetros de un runbook secundario llamado en línea pueden ser cualquier tipo de datos, incluidos los objetos complejos y no hay ninguna [serialización JSON](automation-starting-a-runbook.md#runbook-parameters) que hay al iniciar runbook con el Portal de administración de Azure o con el cmdlet AzureRmAutomationRunbook de inicio.


### <a name="runbook-types"></a>Tipos de runbook

Qué tipos pueden llamar mutuamente:

- Un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) y [runbooks gráfica](automation-runbook-types.md#graphical-runbooks) pueden llamar a cada en línea (ambos son PowerShell según).
- Un runbooks [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) y flujo de trabajo de PowerShell gráfica puede llamar a cada en línea (ambos son en función de flujo de trabajo de PowerShell)
- Los tipos de PowerShell y los tipos de flujo de trabajo de PowerShell no pueden llamar a ellos en línea y deben utilizar AzureRmAutomationRunbook de inicio.
    
Al publicar la cuestión de pedido:

- El orden de publicación de runbooks solo es importante para el flujo de trabajo de PowerShell y flujo de trabajo de PowerShell gráfica runbooks.


Cuando llame a un runbook secundarios gráfica o flujo de trabajo de PowerShell con la ejecución en línea, simplemente use el nombre del runbook.  Cuando llame a un runbook de PowerShell secundarios, debe precedido su nombre con *.\\ * para especificar que se encuentra la secuencia de comandos en el directorio local. 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se invoca un runbook de secundarios de prueba que acepta tres parámetros, un objeto complejo, un número entero y un valor boolean. El resultado del runbook secundarios se asigna a una variable.  En este caso, runbook secundarios es un runbook de flujo de trabajo de PowerShell

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

A continuación se muestra el mismo ejemplo con un runbook de PowerShell como elemento secundario.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciar un runbook secundarios mediante cmdlet

Puede usar el cmdlet [AzureRmAutomationRunbook de inicio](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar un runbook como se describe en [para iniciar un runbook con Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Existen dos modos de uso para este cmdlet.  En un modo, el cmdlet devuelve el identificador de trabajo tan pronto como se crea el trabajo secundario para runbook secundarios.  En el otro modo, que permiten especificando la **-esperar** parámetros, el cmdlet esperará hasta que el elemento secundario tarea finalice y devolverá el resultado de runbook secundarios.

El trabajo desde un runbook secundarios empezando con un cmdlet se ejecuta en un trabajo independiente de runbook primario. Resultado más trabajos que invocar runbook en línea y hace más difícil realizar un seguimiento. El elemento primario puede iniciar varias runbooks secundarios asincrónica sin tener que esperar para que cada uno completar. Para ese mismo tipo de ejecución en paralelo llamar en línea runbooks secundarios, runbook primario necesarias para usar la [palabra clave paralelo](automation-powershell-workflow.md#parallel-processing).

Parámetros para un runbook secundarios empezando con un cmdlet se proporcionan como una tabla hash como se describe en [Runbook parámetros](automation-starting-a-runbook.md#runbook-parameters). Pueden utilizarse solo los tipos de datos simples. Si runbook tiene un parámetro con un tipo de datos complejos, a continuación, se debe llamar en línea.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se inicia un runbook secundarios con parámetros y, a continuación, espera para que se complete con la AzureRmAutomationRunbook inicio-espere parámetro. Una vez completada, el resultado es recopilado runbook secundarios.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparación de los métodos para llamar a un runbook secundarios

La siguiente tabla resume las diferencias entre los dos métodos para llamar a un runbook desde otro runbook.

| | En línea| Cmdlet|
|:---|:---|:---|
|Trabajo|Runbooks secundarios se ejecutan en el mismo trabajo como principal.|Se creará un trabajo independiente runbook secundarios.|
|Ejecución|Primario runbook espera runbook secundarios completar antes de continuar.|Primario runbook continúa inmediatamente después de que se inicia runbook secundario *o* runbook primario espera el trabajo secundario Finalizar.|
|Salida|Runbook primario directamente puede obtener resultados de runbook secundarios.|Runbook primario debe recuperar resultados de secundarios runbook trabajo *o* runbook primario directamente puede obtener resultados de runbook secundarios.|
|Parámetros|Valores para los parámetros de runbook secundarios se especifican por separado y pueden usar cualquier tipo de datos.|Valores de los parámetros de runbook de secundarios deben combinarse en una sola tabla de hash y sólo pueden incluir simples, matriz y tipos de datos de objeto que serialización JSON de aprovechamiento.|
|Cuenta de automatización|Primario runbook solo puede usar runbook secundario en la misma cuenta de automatización.|Runbook primario puede usar runbook secundarios desde cualquier cuenta de automatización desde la misma suscripción de Azure e incluso una suscripción diferente si tiene una conexión a ella.|
|Publicación|Antes de publica runbook primario, se debe publicar runbook secundarios.|Runbook secundarios se debe publicar en cualquier momento antes de inicia runbook primario.|

## <a name="next-steps"></a>Pasos siguientes

- [Iniciar un runbook de automatización de Azure](automation-starting-a-runbook.md)
- [Salida runbook y mensajes de automatización de Azure](automation-runbook-output-and-messages.md)
