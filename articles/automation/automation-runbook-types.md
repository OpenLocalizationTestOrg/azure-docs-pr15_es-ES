<properties 
   pageTitle="Tipos de Runbook de automatización de Azure"
   description="Describe los diferentes tipos de runbooks que puede usar en la automatización de Azure y consideraciones que debe tener en cuenta al determinar qué tipo para usar. "
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

# <a name="azure-automation-runbook-types"></a>Tipos de runbook de automatización de Azure

Automatización de Azure admite cuatro tipos de runbooks que se describen brevemente en la tabla siguiente.  Las secciones siguientes proporcionan información adicional sobre cada tipo incluidos consideraciones sobre cuándo usar cada uno.


| Tipo |  Descripción |
|:---|:---|
| [Gráfica](#graphical-runbooks) | Basado en Windows PowerShell y creado y editado completamente en el editor gráfico en el portal de Azure. | 
| [Flujo de trabajo de PowerShell gráfica](#graphical-runbooks) | En función de flujo de trabajo de Windows PowerShell y creados y editados completamente en el editor gráfico en el portal de Azure. 
| [PowerShell](#powershell-runbooks) | Runbook de texto en función de la secuencia de comandos de Windows PowerShell.
| [Flujo de trabajo de PowerShell](#powershell-workflow-runbooks) | Texto runbook basada en el flujo de trabajo de Windows PowerShell. |


## <a name="graphical-runbooks"></a>Runbooks gráfica

Crear y editar con el editor gráfico en el portal de Azure runbooks de flujo de trabajo de PowerShell gráficos y [gráficos](automation-runbook-types.md#graphical-runbooks) .  Puede exportarlos a un archivo y, a continuación, importarlos a otra cuenta de automatización, pero no puede crear o editar con otra herramienta.  Runbooks gráficas generar código de PowerShell, pero no puede ver o modificar el código directamente. No se puede convertir runbooks gráfica a uno de los [formatos de texto](automation-runbook-types.md)ni un runbook de texto se puede convertir al formato de gráfico. Runbooks gráfica puede convertirse en el flujo de trabajo de gráficos PowerShell runbooks durante la importación y viceversa.

### <a name="advantages"></a>Ventajas

- Crear runbooks con conocimientos mínimos de [PowerShell](automation-powershell-workflow.md).
- Representar visualmente los procesos de administración.
- Incluir otros runbooks runbooks secundarios para crear flujos de trabajo de nivel alto.


### <a name="limitations"></a>Limitaciones

- No se puede editar runbook fuera de Azure portal.
- Puede requerir una actividad de código que contiene el código de PowerShell para realizar lógica compleja.
- No puede ver ni editar directamente el código de PowerShell que se crea el flujo de trabajo de gráficos. Tenga en cuenta que puede ver el código que cree en las actividades de código.


## <a name="powershell-runbooks"></a>Runbooks PowerShell

PowerShell runbooks se basan en Windows PowerShell.  Editar directamente el código del runbook mediante el editor de texto en el portal de Azure.  También puede usar cualquier editor de texto sin conexión e [Importar runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) en automatización de Azure.

### <a name="advantages"></a>Ventajas

- Implementar toda la lógica compleja con código de PowerShell sin las complejidades adicionales de flujo de trabajo de PowerShell. 
- Runbook se inicia más rápido que runbooks gráfica o flujo de trabajo de PowerShell, ya que no es necesario que se compila antes de ejecutar.

### <a name="limitations"></a>Limitaciones

- Debe estar familiarizado con secuencias de comandos de PowerShell.
- No puede usar el [procesamiento en paralelo](automation-powershell-workflow.md#parallel-processing) para realizar varias acciones en paralelo.
- No puede usar [los controles](automation-powershell-workflow.md#checkpoints) para reanudar runbook en caso de error.
- Flujo de trabajo de PowerShell runbooks y runbooks gráfica solo pueden incluir como secundarios runbooks mediante el cmdlet Start AzureAutomationRunbook que crea un nuevo trabajo.

### <a name="known-issues"></a>Problemas conocidos
A continuación se muestran actuales problemas conocidos con PowerShell runbooks.

- PowerShell runbooks no puede no se puede recuperar un sin cifrar [variable activos](automation-variables.md) con un valor nulo.
- PowerShell runbooks no puede recuperar una [variable activos](automation-variables.md) con *~* en el nombre.
- Proceso de obtener un bucle en un PowerShell runbook puede bloquearse después de iteraciones aproximadamente 80. 
- Un runbook de PowerShell puede fallar si intenta escribir una gran cantidad de datos a la secuencia de salida a la vez.   Para evitar este problema normalmente puede generar únicamente la información que necesaria al trabajar con objetos de gran tamaño.  Por ejemplo, en lugar de salida algo como *Get-Process*, puede mostrar solo los campos obligatorios con *Get-Process | Seleccione el nombre de proceso de CPU*.

## <a name="powershell-workflow-runbooks"></a>Flujo de trabajo de PowerShell runbooks

Flujo de trabajo de PowerShell runbooks son runbooks de texto en función de [Flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md).  Editar directamente el código del runbook mediante el editor de texto en el portal de Azure.  También puede usar cualquier editor de texto sin conexión e [Importar runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) en automatización de Azure.

### <a name="advantages"></a>Ventajas

- Implementar toda la lógica compleja con código de flujo de trabajo de PowerShell.
- Use [los controles](automation-powershell-workflow.md#checkpoints) para reanudar runbook en caso de error.
- Utilizar [procesamiento en paralelo](automation-powershell-workflow.md#parallel-processing) para realizar varias acciones en paralelo.
- Puede incluir otros runbooks gráfica y el flujo de trabajo de PowerShell runbooks runbooks secundarios para crear flujos de trabajo de nivel alto.


### <a name="limitations"></a>Limitaciones

- Autor debe estar familiarizado con el flujo de trabajo de PowerShell.
- Runbook debe tratar la complejidad adicional de flujo de trabajo de PowerShell como [deserializar objetos](automation-powershell-workflow.md#code-changes).
- Runbook tarda más tiempo iniciar de PowerShell runbooks debido a que debe compilar antes de ejecutar.
- PowerShell runbooks solo se puede incluir como secundarios runbooks mediante el cmdlet Start AzureAutomationRunbook que crea un nuevo trabajo.


## <a name="considerations"></a>Consideraciones

Se deben tener en cuenta las siguientes consideraciones adicionales al determinar qué tipo para usar para una determinada runbook.

- No se puede convertir runbooks de gráfica a tipo de texto o viceversa.
- Hay limitaciones utilizando runbooks de los diferentes tipos como un runbook secundarios.  Para obtener más información, vea [runbooks secundarios en automatización de Azure](automation-child-runbooks.md) .

  
## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la creación de gráficos runbook, consulte [creación de gráficos en la automatización de Azure](automation-graphical-authoring-intro.md)
- Para conocer las diferencias entre PowerShell y PowerShell flujos de trabajo para runbooks, vea [Flujo de trabajo de aprendizaje Windows PowerShell](automation-powershell-workflow.md)
- Para obtener más información acerca de cómo crear o importar un Runbook, vea [crear o importar un Runbook](automation-creating-importing-runbook.md)



