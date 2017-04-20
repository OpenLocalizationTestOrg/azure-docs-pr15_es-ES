<properties 
    pageTitle="Obtenga más información sobre la limitación en servicios de BizTalk | Microsoft Azure" 
    description="Obtenga información sobre la limitación de los umbrales y resultante comportamientos de tiempo de ejecución de servicios de BizTalk. Limitación se basa en el uso de memoria y el número de mensajes. MABS, WABS" 
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





# <a name="biztalk-services-throttling"></a>Servicios de BizTalk: límite

Servicios de BizTalk Azure implementa el límite de servicio basado en dos condiciones: uso de memoria y el número de mensajes simultáneamente procesamiento. En este tema se enumera los umbrales de limitación y describe el comportamiento de tiempo de ejecución cuando se produce una condición de limitación.

## <a name="throttling-thresholds"></a>Umbrales de limitación

La tabla siguiente muestra el límite origen y los umbrales de:

||Descripción|Umbral inferior|Umbral de alto|
|---|---|---|---|
|Memoria|% del total del sistema memoria disponible/PageFileBytes. <p><p>PageFileBytes disponible total es de aproximadamente 2 veces la memoria RAM del sistema.|60%|70%|
|Procesamiento de mensajes|Número de mensajes de procesamiento de forma simultánea|40 * número de núcleos|100 * número de núcleos|

Al alcanza un umbral alto, servicios de Azure BizTalk inicia limitar. Limitación se detiene cuando se alcance el umbral inferior. Por ejemplo, el servicio usa 65% memoria del sistema. En este caso, no limitar el servicio. El servicio inicia con memoria del sistema de 70%. En esta situación, el servicio se limita y continúa limitar hasta que el servicio usa la memoria del sistema de 60% (umbral inferior).

Servicios de BizTalk Azure realiza un seguimiento de la limitación estado normal y estado limitado y la duración de limitación.


## <a name="runtime-behavior"></a>Comportamiento de tiempo de ejecución

Cuando los servicios de BizTalk Azure entra en un estado de limitación, ocurre lo siguiente:

- El límite es por cada instancia de rol. Por ejemplo:<br/>
Límite de RoleInstanceA. No se atasca RoleInstanceB. En esta situación, se procesan los mensajes en RoleInstanceB según lo esperado. Mensajes en RoleInstanceA se descartan y el error siguiente:<br/><br/>
**Servidor está ocupado. Vuelva a intentarlo.**<br/><br/>
- Los orígenes de extracción no sondear ni descargar un mensaje. Por ejemplo:<br/>
Una canalización extrae los mensajes de un origen externo de FTP. Obtiene la instancia de la función realiza la extracción en un estado de limitación. En este caso, la canalización de detiene la descarga de mensajes adicionales hasta que la instancia de rol deja de limitación.
- Para que el cliente puede volver a enviar el mensaje, se envía una respuesta al cliente.
- Debe esperar a que el límite se ha resuelto. Más concretamente, debe esperar hasta que se alcance el umbral inferior.

## <a name="important-notes"></a>Notas importantes
- No se puede deshabilitar la limitación.
- No se pueden modificar los umbrales de limitación.
- Limitación se implementa todo el sistema.
- El servidor de base de datos de SQL Azure también tiene limitación integrados.

## <a name="additional-azure-biztalk-services-topics"></a>Temas de Azure BizTalk servicios adicionales

-  [Instalación de los servicios de BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriales: Servicios de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servicios de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Vea también
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servicios de BizTalk: Portal clásico de Azure usando el aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: Gráfico de estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
