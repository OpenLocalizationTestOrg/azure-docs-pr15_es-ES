<properties 
    pageTitle="Edición runbooks textual en automatización de Azure"
    description="Este artículo proporciona procedimientos diferentes para trabajar con PowerShell y flujo de trabajo de PowerShell runbooks en Azure automatización mediante el editor de texto."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>Edición runbooks textual en automatización de Azure

El editor de texto de automatización de Azure puede usarse para editar [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) y [flujo de trabajo de PowerShell runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Esto tiene las características de otros editores de código como intellisense y codificación en colores con características especiales adicionales para ayudarle a tener acceso a recursos de runbooks típicas.  Este artículo proporciona instrucciones detalladas para realizar funciones diferentes con este editor.

El editor de texto incluye una característica para insertar código de actividades, activos y secundarios runbooks en un runbook. En lugar de escribir el código usted mismo, puede seleccionar de una lista de los recursos disponibles y tiene el código apropiado insertado en runbook.

Cada runbook de automatización de Azure tiene dos versiones, borrador y publicados. Editar la versión de borrador del runbook y, después, publicarlo para que se pueda ejecutar. La versión publicada no pueden editarse. Para obtener más información, consulte [publicar un runbook](automation-creating-importing-runbook.md#publishing-a-runbook) .

Para trabajar con [Gráficos Runbooks](automation-runbook-types.md#graphical-runbooks), consulte [creación de gráficos en la automatización de Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar un runbook con el portal de Azure

Utilice el procedimiento siguiente para abrir un runbook para su edición en el editor de texto.

1. En el portal de Azure, seleccione su cuenta de automatización.
2. Haga clic en el mosaico de **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el nombre del runbook que desea editar y, a continuación, haga clic en el botón **Editar** .
6. Realizar la modificación necesarios.
7. Haga clic en **Guardar** cuando haya completado los cambios que desee.
8. Si desea que la última versión de borrador del runbook publicar, haga clic en **Publicar** .

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Para insertar un cmdlet en un runbook

2. En el lienzo del editor de texto, coloque el cursor donde desea colocar el cmdlet.
3. Expanda el nodo de **Cmdlets** en el control de la biblioteca. 
3. Expanda el módulo que contiene el cmdlet que desea usar.
4. Haga clic derecho en el cmdlet para insertar y seleccione **Agregar al lienzo**.  Si el cmdlet tiene más de un parámetro de configuración, se agregará el conjunto predeterminado.  También puede expandir el cmdlet para seleccionar un conjunto de parámetros diferentes.
4. Se inserta el código para el cmdlet con su lista completa de parámetros.
5. Proporcione un valor apropiado en lugar del tipo de datos entre llaves <> para los parámetros requeridos.  Quitar todos los parámetros que no es necesario.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para insertar el código para un runbook secundarios en un runbook

2. En el lienzo del editor de texto, coloque el cursor donde desea colocar el código para [runbook secundarios](automation-child-runbooks.md).
3. Expanda el nodo **Runbooks** el control de la biblioteca. 
3. Haga clic derecho en runbook para insertar y seleccione **Agregar al lienzo**.
4. Se inserta el código para runbook secundarios con los marcadores de posición para los parámetros de runbook.
5. Reemplace los marcadores de posición con los valores apropiados para cada parámetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para insertar un activo en un runbook

2. En el lienzo del editor de texto, coloque el cursor donde desea colocar el código para runbook secundarios.
3. Expanda el nodo de **activos** en el control de la biblioteca. 
4. Expanda el nodo para el tipo de activo que desee.
3. Haga clic derecho en el activo para insertar y seleccione **Agregar al lienzo**.  Para los [activos de variables](automation-variables.md), seleccione **Agregar "obtener Variable" al lienzo** o **Agregar "establecer Variable" al lienzo** dependiendo de si desea obtener o establecer la variable.
4. El código del activo se inserta en runbook.



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar un runbook con el portal de Azure

Utilice el procedimiento siguiente para abrir un runbook para su edición en el editor de texto.

1. En el portal de Azure, seleccione **automatización** y, a continuación, haga clic en el nombre de una cuenta de automatización.
2. Seleccione la pestaña **Runbooks** .
3. Haga clic en el nombre del runbook que desea editar y, a continuación, seleccione la pestaña de **autor** .
5. Haga clic en el botón **Editar** en la parte inferior de la pantalla.
6. Realizar la modificación necesarios.
7. Haga clic en **Guardar** cuando haya completado los cambios que desee.
8. Si desea que la última versión de borrador del runbook publicar, haga clic en **Publicar** .

### <a name="to-insert-an-activity-into-a-runbook"></a>Para insertar una actividad en un Runbook

1. En el lienzo del editor de texto, coloque el cursor donde desea colocar la actividad.
1. En la parte inferior de la pantalla, haga clic en **Insertar** y, a continuación, **actividad**.
1. En la columna de la **Integración del módulo** , seleccione el módulo que contiene la actividad.
1. En el panel de la **actividad** , seleccione una actividad.
1. En la columna **Descripción** , tenga en cuenta la descripción de la actividad. Si lo desea, haga clic en vista detallada de la ayuda para iniciar la ayuda para la actividad en el explorador.
1. Haga clic en la flecha derecha.  Si la actividad tiene parámetros, se mostrará la información.
1. Haga clic en el botón comprobar.  Ejecución de la actividad de código se insertará en runbook.
1. Si la actividad requiere parámetros, proporcione un valor apropiado en lugar del tipo de datos entre llaves <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para insertar el código para un runbook secundarios en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desea colocar la [runbook secundarios](automation-child-runbooks.md).
2. En la parte inferior de la pantalla, haga clic en **Insertar** y, a continuación, **Runbook**.
3. Seleccione runbook insertar de la columna central y haga clic en la flecha derecha.
4. Si runbook tiene parámetros, se mostrará la información.
5. Haga clic en el botón comprobar.  Código para ejecutar runbook seleccionado se insertará en runbook actual.
7. Si runbook requiere parámetros, proporcione un valor apropiado en lugar del tipo de datos entre llaves <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para insertar un activo en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desea colocar la actividad para recuperar el activo.
1. En la parte inferior de la pantalla, haga clic en **Insertar** y, a continuación, en **configuración**.
1. En la columna **Acción de configuración** , seleccione la acción que desee.
1. Seleccione los activos disponibles en la columna central.
1. Haga clic en el botón comprobar.  Código para obtener o establecer el activo se insertará en runbook.



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar un runbook de automatización de Azure con Windows PowerShell

Para editar un runbook con Windows PowerShell, use el editor de su elección y guardarlo en un archivo. ps1. Puede usar el cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) para recuperar el contenido de la runbook y, a continuación, el cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) reemplazar runbook borrador existente con la modificación.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para recuperar el contenido de un Runbook con Windows PowerShell

Los comandos de ejemplo siguientes muestran cómo recuperar la secuencia de comandos para un runbook y guardarlo en un archivo de script. En este ejemplo, se recupera la versión de borrador. También es posible recuperar la versión publicada del runbook aunque no se puede cambiar esta versión.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para cambiar el contenido de un Runbook con Windows PowerShell

Los comandos de ejemplo siguiente muestran cómo reemplazar el contenido existente de un runbook con el contenido de un archivo de script. Tenga en cuenta que es el mismo procedimiento de ejemplo que [importar un runbook desde un archivo de comandos con Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Artículos relacionados

- [Crear o importar un runbook de automatización de Azure](automation-creating-importing-runbook.md)
- [Flujo de trabajo de PowerShell de aprendizaje](automation-powershell-workflow.md)
- [Edición gráfica de automatización de Azure](automation-graphical-authoring-intro.md)
- [Certificados](automation-certificates.md)
- [Conexiones](automation-connections.md)
- [Credenciales](automation-credentials.md)
- [Programaciones](automation-schedules.md)
- [Variables](automation-variables.md)