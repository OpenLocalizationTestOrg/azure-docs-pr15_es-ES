<properties 
    pageTitle="Probar un runbook de automatización de Azure | Microsoft Azure"
    description="Antes de publicar un runbook de automatización de Azure, puede probar para asegurarse de que funciona como se esperaba.  En este artículo se describe cómo probar un runbook y ver sus resultados."
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

# <a name="testing-a-runbook-in-azure-automation"></a>Probar un runbook de automatización de Azure
Cuando se prueba un runbook, se ejecuta la [versión de borrador](automation-creating-importing-runbook.md#publishing-a-runbook) y se hayan completado todas las acciones que lleva a cabo. No se creará ningún historial de trabajo, pero las secuencias de [salida](automation-runbook-output-and-messages.md#output-stream) y de [Error y advertencia](automation-runbook-output-and-messages.md#message-streams) se muestran en la prueba de panel de resultados. Mensajes en la [secuencia detallado](automation-runbook-output-and-messages.md#message-streams) se muestran en el panel de resultados solo si se establece la [variable $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) para continuar.

Aunque se ejecuta la versión de borrador, runbook aún ejecuta el flujo de trabajo normalmente y realiza las acciones de los recursos en el entorno. Por este motivo, debe probar solo runbooks a recursos de producción no.

El procedimiento para cada [tipo de runbook](automation-runbook-types.md) de prueba es el mismo y no hay ninguna diferencia en las pruebas entre el editor de texto y el editor gráfico en el portal de Azure.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Para probar una runbook en el portal de Azure

Puede trabajar con cualquier [tipo de runbook](automation-runbook-types.md) en el portal de Azure.

1. Abra la versión de borrador del runbook en el [editor de texto](automation-editing-a-runbook.md#Portal) o un [editor gráfico](automation-graphical-authoring-intro.md).
2. Haga clic en el botón de **prueba** para abrir el módulo de prueba.
3. Si runbook tiene parámetros, se mostrará en el panel izquierdo, donde puede proporcionar los valores que se usará para la prueba.
4. Si desea ejecutar la prueba en un [Híbrido Runbook trabajo](automation-hybrid-runbook-worker.md), cambiar la **Configuración de ejecutar** **Híbrido** trabajador y seleccione el nombre del grupo de destino.  En caso contrario, mantenga el valor predeterminado de **Azure** para ejecutar la prueba en la nube.
5. Haga clic en el botón **Iniciar** para iniciar la prueba.
6. Si runbook es [El flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o [gráficos](automation-runbook-types.md#graphical-runbooks), puede detener o suspender él mientras se está probando con los botones que se encuentra debajo del panel de resultados. Cuando se suspende runbook, complete la actividad actual antes de que se ha suspendido. Una vez que se ha suspendido runbook, puede detenerla o reinícielo.
7. Inspeccionar la salida de runbook en el panel de resultados.


## <a name="next-steps"></a>Pasos siguientes

- Para aprender a crear o importar un runbook, vea [crear o importar un runbook de automatización de Azure](automation-creating-importing-runbook.md)
- Para obtener más información sobre la creación de gráficos, consulte [creación de gráficos en la automatización de Azure](automation-graphical-authoring-intro.md)
- Para empezar con PowerShell runbooks de flujo de trabajo, consulte [Mi primera runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
- Para más información acerca de cómo configurar runboks para devolver los mensajes de estado y errores, incluidos los procedimientos recomendados, vea [Runbook resultados y mensajes de automatización de Azure](automation-runbook-output-and-messages.md)