<properties 
   pageTitle="Las programaciones de automatización Azure | Microsoft Azure"
   description="Las programaciones de automatización se usan para programar runbooks en automatización de Azure se inicie automáticamente. Describe cómo crear y administrar una programación de modo que puede iniciar automáticamente un runbook en un momento determinado o con una programación periódica."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="mgoedtel" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>Programar un runbook de automatización de Azure

Para programar un runbook de automatización de Azure para iniciar en un momento determinado, vincular a una o más programaciones. Se puede configurar una programación de a la programación de ejecución en un recurrente o de una vez cada hora o diariamente para runbooks en el portal de clásico Azure y runbooks en el portal de Azure, puede además programe para semanalmente, mensualmente, días específicos de la semana o días del mes o un día concreto del mes.  Puede vincular un runbook a varias programaciones y una programación puede tener varias runbooks vinculado a ella.

>[AZURE.NOTE]  Planes no compatible con las configuraciones de Azure automatización DSC.

## <a name="windows-powershell-cmdlets"></a>Cmdlets de Windows PowerShell

Los cmdlets en la siguiente tabla se usan para crear y administrar programaciones con Windows PowerShell en automatización de Azure. Se envían como parte del [módulo de PowerShell de Azure](../powershell-install-configure.md).

|Cmdlets de|Descripción|
|:---|:---|
|**Cmdlets de administrador de recursos de Azure**||
|[Get-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603733.aspx)|Recupera una programación.|
|[Nueva AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx)|Crea una nueva programación.|
|[Quitar AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603691.aspx)|Quita una programación.|
|[Establecer AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx)|Establece las propiedades para una programación existente.|
|[Get-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt619406.aspx)|Recupera runbooks programada.|
|[Registrar AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx)|Asocia un runbook una programación.|
|[AzureRmAutomationScheduledRunbook anular el registro](https://msdn.microsoft.com/library/mt603844.aspx)|Anula la asociación de un runbook de una programación.|
|**Cmdlets de administración de servicio de Azure**||
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Recupera una programación.|
|[Nueva AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Crea una nueva programación.|
|[Quitar AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Quita una programación.|
|[Establecer AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Establece las propiedades para una programación existente.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Recupera runbooks programada.|
|[Registrar AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Asocia un runbook una programación.|
|[AzureAutomationScheduledRunbook anular el registro](http://msdn.microsoft.com/library/dn690273.aspx)|Anula la asociación de un runbook de una programación.|

## <a name="creating-a-schedule"></a>Crear una programación

Puede crear una nueva programación para runbooks en el portal de Azure en el portal de clásico o con Windows PowerShell. También tiene la opción de crear una nueva programación cuando se vincula un runbook a una programación con el portal de Azure o clásico Azure.

>[AZURE.NOTE] Al asociar una programación con un runbook, automatización almacena las versiones actuales de los módulos en su cuenta y vincula con ese programa.  Esto significa que si tenía un módulo con la versión 1.0 en su cuenta cuando se creó una programación y, a continuación, actualice el módulo a la versión 2.0, la programación seguirá utilizando 1.0.  Para usar la versión actualizada del módulo, debe crear una nueva programación. 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para crear una nueva programación en el portal de Azure

1. En el portal de Azure, desde su cuenta de automatización, haga clic en el icono de **activos** para abrir el módulo de **activos** .
2. Haga clic en el icono de **programaciones** para abrir el módulo de **programaciones** .
3. Haga clic en **Agregar una programación** en la parte superior de la hoja.
4. En el módulo de **programación de nuevo** , escriba un **nombre** y, opcionalmente, una **Descripción** para el nuevo plan.
5. Seleccione si la programación ejecutará una vez, o en una programación recurrente seleccionando **una vez** o **Periodicidad**.  Si selecciona **una vez** especifique una **hora de inicio** y, a continuación, haga clic en **crear**.  Si selecciona **Periodicidad**, especifique una **hora de inicio** y la frecuencia de la frecuencia con la quiere runbook repetir - por **hora**, **día**, **semana**o por **mes**.  Si selecciona **semana** o **mes** de la lista desplegable, aparecerá la **opción de periodicidad** en el módulo tras la selección, se presentará el módulo de **opción de periodicidad** y puede seleccionar el día de la semana si ha seleccionado la **semana**.  Si ha seleccionado el **mes**, puede elegir por **días de la semana** o días específicos del mes en el calendario y por último, haga lo que desea ejecutar el último día del mes o no y, a continuación, haga clic en **Aceptar**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Para crear una nueva programación en el portal de clásico de Azure

1. En el portal de clásico Azure, seleccione automatización y, a continuación, seleccione el nombre de una cuenta de automatización.
1. Seleccione la ficha **activos** .
1. En la parte inferior de la ventana, haga clic en **Agregar**.
1. Haga clic en **Agregar programa**.
1. Escriba un **nombre** y, opcionalmente, una **Descripción** para la nueva programación de schedule.your ejecutará **Una vez**, **cada hora**, **diaria**, **semanal**o **mensual**.
1. Especifique una **Hora de inicio** y otras opciones según el tipo de programación que ha seleccionado.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para crear una nueva programación con Windows PowerShell

Puede usar el cmdlet [AzureAutomationSchedule de nuevo](http://msdn.microsoft.com/library/azure/dn690271.aspx) para crear una nueva programación de automatización de Azure para runbooks clásico o [AzureRmAutomationSchedule nuevo](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet para runbooks en el portal de Azure. Debe especificar la hora de inicio de la programación y la frecuencia que se debe ejecutar.

Los comandos de ejemplo siguiente muestra cómo crear una programación para el día 15 y 30 de cada mes con un cmdlet de administrador de recursos de Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Los comandos de ejemplo siguientes muestran cómo crear una nueva programación que se ejecute cada día a las 3:30 P.M. comenzando en 20 de enero de 2015 con un cmdlet de administración de servicios de Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Vincular una programación a un runbook

Puede vincular un runbook a varias programaciones y una programación puede tener varias runbooks vinculado a ella. Si un runbook tiene parámetros, puede proporcionar valores para ellos. Debe proporcionar los valores para los parámetros obligatorios y puede proporcionar valores para los parámetros opcionales.  Estos valores se utilizará cada vez que se inicia runbook de esta programación.  Puede adjuntar el mismo runbook a otro plan y especificar distintos valores de parámetro.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Vincular una programación a un runbook con el portal de Azure

1. En el portal de Azure, desde su cuenta de automatización, haga clic en el icono de **Runbooks** para abrir el módulo **Runbooks** .
2. Haga clic en el nombre del runbook programar.
3. Si no está vinculado a una programación runbook, a continuación, le dará la opción de crear una nueva programación o un vínculo a una programación existente.  
4. Si runbook tiene parámetros, puede seleccionar la opción **Modificar ejecutar configuración (predeterminado: Azure)** y se presenta el módulo de **parámetros** donde puede especificar la información según corresponda.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Vincular una programación a un runbook con el portal de clásico de Azure

1. En el portal de clásico Azure, seleccione **automatización** y, a continuación, haga clic en el nombre de una cuenta de automatización.
2. Seleccione la pestaña **Runbooks** .
3. Haga clic en el nombre del runbook programar.
4. Haga clic en la pestaña **programación** .
5. Si no está vinculado a una programación runbook, a continuación, le dará la opción para **Vincular a una nueva programación** o **Vincular a una programación existente**.  Si runbook está vinculado a un calendario, haga clic en el **vínculo** en la parte inferior de la ventana para acceder a estas opciones.
6. Si runbook tiene parámetros, se le pedirá sus valores.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Vincular una programación a un runbook con Windows PowerShell

Puede usar el [Registro AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular a una programación a un runbook clásico o [Registrar AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) cmdlet para runbooks en el portal de Azure.  Puede especificar valores para los parámetros del runbook con el parámetro de parámetros. Para obtener más información sobre cómo especificar valores de parámetro, vea [iniciar un Runbook de automatización de Azure](automation-starting-a-runbook.md) .

Los comandos de ejemplo siguientes muestran cómo vincular una programación a un runbook mediante un cmdlet de administrador de recursos de Azure con parámetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Los comandos de ejemplo siguientes muestran cómo vincular una programación mediante un cmdlet de administración de servicios de Azure con parámetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Deshabilitar una programación

Cuando se deshabilita una programación, cualquier runbooks vinculado a ella ya no se ejecutará en ese programa. Puede deshabilitar una programación o establecer una fecha de caducidad para programaciones con frecuencia cuando se crean manualmente. Cuando se alcanza la fecha de expiración, se deshabilitarán la programación.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para deshabilitar una programación desde el portal de Azure

1. En el portal de Azure, desde su cuenta de automatización, haga clic en el icono de **activos** para abrir el módulo de **activos** .
2. Haga clic en el icono de **programaciones** para abrir el módulo de **programaciones** .
2. Haga clic en el nombre de una programación para abrir el módulo de detalles.
3. Cambiar **habilitado** en **No**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Para deshabilitar una programación desde el portal de clásico de Azure

Puede deshabilitar una programación en el portal de Azure clásica de la página de detalles de la programación para la programación.

1. En el portal de clásico Azure, seleccione automatización y, a continuación, haga clic en el nombre de una cuenta de automatización.
1. Seleccione la ficha activos.
1. Haga clic en el nombre de una programación para abrir su página de detalles.
2. Cambiar **habilitado** en **No**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para deshabilitar una programación con Windows PowerShell

Puede usar el cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para cambiar las propiedades de una programación existente para un runbook clásico o el cmdlet [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) para runbooks en el portal de Azure. Para deshabilitar la programación, especifique **false** para el parámetro **IsEnabled** .

Los comandos de ejemplo siguientes muestran cómo deshabilitar una programación para un runbook mediante un cmdlet de administrador de recursos de Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Los comandos de ejemplo siguientes muestran cómo deshabilitar una programación mediante el cmdlet de administración de servicios de Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Pasos siguientes

- Para empezar con runbooks en automatización de Azure, consulte [iniciar un Runbook de automatización de Azure](automation-starting-a-runbook.md) 