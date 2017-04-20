<properties 
   pageTitle="Configuración de runbook"
   description="Describe las opciones de configuración para un runbook de automatización de Azure y cómo cambiarlos con el Portal de administración de Azure y Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Configuración de runbook

Cada runbook de automatización de Azure tiene varias opciones que ayudan a identificarse y para cambiar el comportamiento de su registro. Cada uno de estos valores se describe a continuación seguido de procedimientos sobre cómo modificarlos.

## <a name="settings"></a>Configuración

### <a name="name-and-description"></a>Nombre y descripción

No puede cambiar el nombre de un runbook después de que se ha creado. La descripción es opcional y puede tener hasta 512 caracteres.

### <a name="tags"></a>Etiquetas

Las etiquetas le permiten asignar distintas palabras o frases para ayudarle a identificar un runbook. Por ejemplo, cuando envíe un runbook a la [Galería de Runbook](https://msdn.microsoft.com/library/dn781422.aspx), especificar etiquetas concretas para identificar qué categorías runbook debe aparecer en. Puede especificar varias etiquetas para un runbook, sepárelos con comas.

### <a name="logging"></a>Registro de información

De forma predeterminada, detallado y el progreso de los registros no se escriben en historial de trabajos. Puede cambiar la configuración de un determinado runbook para estos registros. Para obtener más información sobre estos registros, vea [Runbook salida y mensajes](https://msdn.microsoft.com/library/dn879148.aspx).

## <a name="changing-runbook-settings"></a>Cambiar la configuración del runbook

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Cambiar la configuración de runbook con el Portal de administración de Azure

Puede cambiar la configuración para un runbook en el Portal de administración de Azure desde la página **Configurar** para runbook.

1. En el Portal de administración de Azure, seleccione **automatización** y, a continuación, haga clic en el nombre de una cuenta de automatización.
1. Seleccione la pestaña **Runbooks** .
1. Haga clic en el nombre de un runbook.
1. Seleccione la ficha **Configurar** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Cambiar la configuración de runbook con Windows PowerShell

Puede usar el cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) para cambiar la configuración para un runbook. Si desea especificar varias etiquetas, puede proporcionar una matriz o una única cadena con valores delimitados por comas para el parámetro de etiquetas. Puede obtener las etiquetas con la [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx)actuales.

Los comandos de ejemplo siguientes muestran cómo establecer las propiedades de un runbook. Este ejemplo agrega tres etiquetas a las etiquetas existentes y especifica que se deben registrar registros detallados.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Artículos relacionados
- [Salida runbook y mensajes](../automation-runbook-output-and-messages) 
- [Crear o importar un Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 