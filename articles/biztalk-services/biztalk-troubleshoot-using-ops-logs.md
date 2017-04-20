<properties 
    pageTitle="Solucionar problemas de los servicios de BizTalk mediante los registros de operación | Microsoft Azure" 
    description="Solucionar problemas de los servicios de BizTalk mediante los registros de operación. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Servicios de BizTalk: Solucionar problemas con los registros de operación

## <a name="what-are-the-operation-logs"></a>¿Qué son los registros de operación
Registros de las operaciones es una característica de los servicios de administración disponible en el portal de clásico Azure que le permite ver los registros de históricos de operaciones realizadas en los servicios de Azure, incluidos los servicios de BizTalk. Esto le permite ver datos históricos relacionados con las operaciones de administración de su suscripción de BizTalk Service anteriores hasta 180 días.

> [AZURE.NOTE]Esta característica solo captura los registros para operaciones de administración de servicios de BizTalk, como cuando se inició el servicio, copia hacia arriba, y así sucesivamente. Estas operaciones se controlan con independencia de si se realiza desde el portal de clásico Azure o mediante las [API de REST de servicio de BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obtener una lista completa de las operaciones que se controlan mediante servicios de administración, consulte [Operaciones marcas usando Azure Management Services](#bizops).<br/><br/>
No se captura los registros de actividades relacionadas con el tiempo de ejecución de BizTalk Service (como mensaje procesada puentes y así sucesivamente.). Para ver estos registros, use la vista de seguimiento desde el portal de servicios de BizTalk. Para obtener más información, vea [Seguimiento de mensajes](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Ver registros de las operaciones de servicios de BizTalk
1. En el portal de clásico Azure, seleccione **Administración de servicios**y, a continuación, seleccione la ficha **Registros de la operación** .
2. Puede filtrar los registros en función de distintos parámetros como suscripción, intervalo de fechas, el tipo de servicio (por ejemplo, los servicios de BizTalk), nombre de servicio o estado de la operación (éxito, error).
3. Seleccione la marca de verificación para ver la lista filtrada. La imagen siguiente muestra las actividades relacionadas con testbiztalkservice:  ![ver registros de operación][ViewLogs] 
4. Para ver más información sobre una operación específica, seleccione la fila y haga clic en **Detalles** en la barra de tareas en la parte inferior.


## <a name="bizops"></a>Operaciones controlan mediante servicios de administración de Azure
La siguiente tabla enumeran las operaciones que se controlan con los servicios de administración de Azure:

Nombre de la operación | Tarea
--- | ---
CreateBizTalkService | Operación para crear un nuevo BizTalk Service
DeleteBizTalkService | Operación para eliminar un BizTalk Service
RestartBizTalkService | Operación de reiniciar una BizTalk Service
StartBizTalkService | Operación para iniciar un BizTalk Service
StopBizTalkService | Operación de detener una BizTalk Service
DisableBizTalkService | Operación de deshabilitar un BizTalk Service
EnableBizTalkService | Operación para habilitar un BizTalk Service
BackupBizTalkService | Operación que debe realizar copias de seguridad un BizTalk Service
RestoreBizTalkService | Operación para restablecer un BizTalk Service de copia de seguridad especificada
SuspendBizTalkService | Operación para suspender una BizTalk Service
ResumeBizTalkService | Operación para reanudar una BizTalk Service
ScaleBizTalkService | Operación escalar un BizTalk Service hacia arriba o hacia abajo
ConfigUpdateBizTalkService | Operación para actualizar la configuración de un BizTalk Service
ServiceUpdateBizTalkService | Operación de actualización o degradar un BizTalk Service a una versión diferente
PurgeBackupBizTalkService | Operación de purga copias de seguridad de la BizTalk Service fuera del período de retención


## <a name="see-also"></a>Vea también
- [Servicio de copia de seguridad de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar el servicio de BizTalk de copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Servicios de BizTalk: Portal clásico de Azure usando el aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Servicios de BizTalk: Gráfico de estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Servicios de BizTalk: límite](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
