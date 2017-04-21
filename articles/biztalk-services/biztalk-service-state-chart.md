<properties 
    pageTitle="Las tareas que se permiten en distintos Estados o Estados en servicios de BizTalk | Microsoft Azure" 
    description="Las acciones u operaciones permitidas en un estado MABS diferente: detener, iniciar, reiniciar, suspender, reanudar, eliminar, escalar, actualizar la configuración y respaldo hacia arriba" 
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



# <a name="biztalk-services-service-state-chart"></a>Servicios de BizTalk: Gráfico de estado del servicio
Dependiendo del estado actual del servicio de BizTalk, hay operaciones que se pueden o no se puede realizar en el servicio de BizTalk.

Por ejemplo, proporcionando un nuevo servicio de BizTalk en el portal de clásico de Azure. Cuando se complete correctamente, el servicio de BizTalk está en estado activo. En el estado activo, puede detener el servicio de BizTalk. Si detener es correcta, el servicio de BizTalk pase a un estado bloqueado. Si se produce un error de detención, el servicio de BizTalk pase a un estado de StopFailed. En el estado de StopFailed, puede reiniciar el servicio de BizTalk. Si se trata de una operación que no está permitida, como Resume el servicio BizTalk, el siguiente error:

**Operación no permitida**

Para aprovisionar un BizTalk Service, vaya a [servicios BizTalk: portal clásico de aprovisionamiento Azure usando](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Las tablas siguientes enumeran las operaciones o las acciones que pueden realizarse cuando BizTalk Service está en un estado específico. Un ✔ significa que la operación está permitida en ese estado. Una entrada en blanco significa que no se puede realizar la operación en ese estado.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Iniciar, detener, reiniciar, suspender, reanudar y eliminar operaciones
<table border="1">
<tr>
        <th colspan="15">Operación o acción</th>
</tr>

<tr>
        <th rowspan="18">Estado del servicio de BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Inicio</th>
        <th>Detener</th>
        <th>Reiniciar</th>
        <th>Suspender</th>
        <th>Currículum vítae</th>
        <th>Eliminar</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Deshabilitado</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspendido</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Detener</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Error de actualización de servicio</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Escala, la configuración de actualización y las operaciones de copia de seguridad
<table border="1">
<tr>
        <th colspan="15">Operación o acción</th>
</tr>

<tr>
        <th rowspan="18">Estado del servicio de BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Escala</th>
        <th>Configuración de actualización</th>
        <th>Copia de seguridad</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Deshabilitado</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspendido</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Detener</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Error de actualización de servicio</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Vea también
- [Servicios de BizTalk: Portal clásico de Azure usando el aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: límite](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
