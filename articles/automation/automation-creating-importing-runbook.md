<properties
    pageTitle="Crear o importar un runbook de automatización de Azure"
    description="En este artículo se describe cómo crear un nuevo runbook en Azure automatización o importar una desde un archivo."
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
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Crear o importar un runbook de automatización de Azure

Puede agregar un runbook Azure automatización mediante la [creación de una nueva](#creating-a-new-runbook) o importando un runbook existente desde un archivo o de la [Galería de Runbook](automation-runbook-gallery.md). En este artículo se proporciona información sobre cómo crear e importar runbooks desde un archivo.  Puede obtener todos los detalles sobre el acceso a la Comunidad runbooks y módulos en [galerías Runbook y módulo de automatización de Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Crear un nuevo runbook

Puede crear un nuevo runbook de automatización de Azure mediante uno de los portales de Azure o Windows PowerShell. Una vez que se ha creado runbook, puede editarlo utilizando información de [Flujo de trabajo de PowerShell de aprendizaje](automation-powershell-workflow.md) y [edición gráfica de automatización de Azure](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Para crear un nuevo runbook de automatización de Azure con el portal de Azure clásica

Solo puede trabajar con [el flujo de trabajo de PowerShell runbooks](automation-runbook-types.md#powershell-workflow-runbooks) en el portal de Azure.

1. En el portal de Azure clásica, haga clic en **nuevo**, **Servicios de aplicación**, **automatización**, **Runbook**, **Crear rápido**.
2. Escriba la información necesaria y, a continuación, haga clic en **crear**. El nombre del runbook debe comenzar con una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Si desea modificar runbook ahora, haga clic en **Editar Runbook**. En caso contrario, haga clic en **Aceptar**.
4. Su nuevo runbook aparecerá en la pestaña **Runbooks** .


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para crear un nuevo runbook de automatización de Azure con el portal de Azure

1. En el portal de Azure, abra su cuenta de automatización.
2. Haga clic en el mosaico **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un runbook** y, a continuación, **crear una nueva runbook**.
2. Escriba un **nombre** para la runbook y seleccione su [tipo](automation-runbook-types.md). El nombre del runbook debe comenzar con una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Haga clic en **crear** para crear runbook y abrir el editor.


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para crear un nuevo runbook de automatización de Azure con Windows PowerShell

Puede usar el cmdlet [AzureRmAutomationRunbook de nuevo](https://msdn.microsoft.com/library/mt619376.aspx) para crear una vacía [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Puede especificar el parámetro de **nombre** para crear un runbook vacío que puede modificar más adelante, o puede especificar el parámetro de **ruta de acceso** para importar un archivo runbook. También se debe incluir el parámetro de **tipo** para especificar uno de los cuatro tipos de runbook.

Los comandos de ejemplo siguientes muestran cómo crear un nuevo runbook vacío.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importar un runbook desde un archivo a la automatización de Azure

Puede crear un nuevo runbook en Azure automatización importando una secuencia de comandos de PowerShell o flujo de trabajo de PowerShell (extensión. ps1) o un runbook gráfica exportado (.graphrunbook).  Debe especificar el [tipo de runbook](automation-runbook-types.md) que se creará de la importación teniendo en cuenta las siguientes consideraciones.

- Solo se pueden puede importar un archivo .graphrunbook en un nuevo [gráfico runbook](automation-runbook-types.md#graphical-runbooks)y solo se puede crear runbooks gráfica desde un archivo .graphrunbook.
- Solo se puede importar un archivo. ps1 que contiene un flujo de trabajo de PowerShell a un [flujo de trabajo de PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Si el archivo contiene varios flujos de trabajo de PowerShell, se producirá un error en la importación. Debe guardar cada flujo de trabajo a su propio archivo e importar cada una por separado.
- Un archivo. ps1 que no contenga un flujo de trabajo se puede importar a un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) o un [flujo de trabajo de PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Si se importa en un runbook de flujo de trabajo de PowerShell, a continuación, se convertirá en un flujo de trabajo y comentarios se incluirán en runbook especificando los cambios realizados.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Para importar un runbook de un archivo con el portal de Azure clásica
Puede usar el siguiente procedimiento para importar un archivo de script de automatización de Azure.  Tenga en cuenta que solo puede importar un archivo. ps1 a un runbook de flujo de trabajo de PowerShell con este portal.  Debe usar el portal de Azure para otros tipos.

1. En el portal de administración de Azure, seleccione **automatización** y, a continuación, seleccione una cuenta de automatización.
2. Haga clic en **Importar**.
3. Haga clic en **Examinar para el archivo** y busque el archivo de comandos para importar.
4. Si desea modificar runbook ahora, haga clic en **Editar Runbook**. En caso contrario, haga clic en Aceptar.
5. Nuevo runbook aparecerá en la pestaña **Runbooks** para la cuenta de automatización.
6. Se debe [Publicar runbook](#publishing-a-runbook) antes de que pueda ejecutarlo.


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar un runbook de un archivo con el portal de Azure
Puede usar el siguiente procedimiento para importar un archivo de script de automatización de Azure.  

>[AZURE.NOTE] Tenga en cuenta que solo puede importar un archivo. ps1 a un runbook de flujo de trabajo de PowerShell con el portal.

1. En el portal de Azure, abra su cuenta de automatización.
2. Haga clic en el mosaico **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un runbook** y, a continuación, en **Importar**.
4. Haga clic en **archivo Runbook** para seleccionar el archivo para importar
2. Si el campo **nombre** está habilitado, tiene la opción de cambiarlo.  El nombre del runbook debe comenzar con una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Se selecciona automáticamente el [tipo de runbook](automation-runbook-types.md) , pero puede cambiar el tipo después de realizar las restricciones aplicables a la cuenta. 
3. Nuevo runbook aparecerá en la lista de runbooks para la cuenta de automatización.
4. Se debe [Publicar runbook](#publishing-a-runbook) antes de que pueda ejecutarlo.

>[AZURE.NOTE] Después de importar un runbook gráfica o un gráfico runbook de flujo de trabajo de PowerShell, tiene la opción de convertir a otro tipo si deseado. No se puede convertir a texto.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar un runbook de un archivo de comandos con Windows PowerShell

Puede usar el cmdlet [AzureRMAutomationRunbook de importación](https://msdn.microsoft.com/library/mt603735.aspx) para importar un archivo de script como borrador runbook de flujo de trabajo de PowerShell. Si ya existe runbook, la importación se producirá un error a menos que utilice el *-Forzar* parámetro. 

Los comandos de ejemplo siguientes muestran cómo importar un archivo de script a un runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Un runbook de publicación

Al crear o importar un runbook nuevo, debe publicar antes de que pueda ejecutarlo.  Cada runbook de automatización tiene un borrador y una versión publicada. Solo la versión publicada está disponible para ejecutarse y se puede modificar la versión de borrador. La versión publicada se ven afectada por los cambios a la versión de borrador. Cuando la versión de borrador debe estar disponible, a continuación, publicarla que sobrescribe la versión publicada con la versión de borrador.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Para publicar un runbook con el portal clásico de Azure

1. Abra runbook en el portal de Azure clásico.
1. En la parte superior de la pantalla, haga clic en **autor**.
1. En la parte inferior de la pantalla, haga clic en **Publicar** y, a continuación, en **Sí** en el mensaje de comprobación.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Para publicar un runbook con el portal de Azure

1. Abra runbook en el portal de Azure.
1. Haga clic en el botón **Editar** .
1. Haga clic en el botón **Publicar** y luego **Sí** en el mensaje de comprobación.


## <a name="to-publish-a-runbook-using-windows-powershell"></a>Para publicar un runbook con Windows PowerShell

Puede usar el cmdlet [AzureRmAutomationRunbook publicar](https://msdn.microsoft.com/library/mt603705.aspx) para publicar un runbook con Windows PowerShell. Los comandos de ejemplo siguiente muestran cómo publicar un runbook de muestra.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre cómo pueden beneficiarse de la Galería de módulo de PowerShell y Runbook, vea [Runbook y módulo galerías para la automatización de Azure](automation-runbook-gallery.md)
- Para obtener más información sobre la edición de PowerShell y flujo de trabajo de PowerShell runbooks con un editor de texto, vea [runbooks textual de edición en la automatización de Azure](automation-edit-textual-runbook.md)
- Para más información sobre la creación de gráficos runbook, consulte [creación de gráficos en la automatización de Azure](automation-graphical-authoring-intro.md)
