<properties 
    pageTitle="Configurar el panel Monitor, escala, y conexiones híbrido en servicios de BizTalk | Microsoft Azure" 
    description="Obtenga más información sobre los controles y supervisar el rendimiento en las fichas de portal clásicas de servicios de BizTalk: paneles, Monitor, escala, configurar y conexiones híbrido. MABS, WABS" 
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
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Revise las fichas de paneles, Monitor, escala, configurar y conexión híbrido

Después de crear su BizTalk Service e implementar la aplicación, puede cambiar algunas de las configuraciones de BizTalk Service y supervisar el rendimiento de la aplicación. 

Al abrir el portal clásico Azure, se coloca automáticamente en la pestaña **Todos los elementos** . Para ver su BizTalk Service, seleccione su BizTalk Service en la pestaña **Todos los elementos** o seleccione la pestaña **Servicios BIZTALK** ; y, a continuación, seleccione su nombre de BizTalk Service.

Se abrirá una ventana nueva con las siguientes pestañas. Este tema describe estas pestañas.

## <a name="quick-start-quick-startquickstart"></a>Inicio rápido (![Inicio rápido][QuickStart])
Según la edición de servicios de BizTalk, todas las opciones enumeradas no esté disponibles. 
<table border="1">
    <tr>
        <td><strong>Obtener las herramientas</strong></td>
        <td>Descargar el SDK de servicios de BizTalk para instalar las plantillas de proyecto de Visual Studio en el equipo de desarrollo local. Estas plantillas de crean los <strong>Servicios de BizTalk</strong> (puente) y los proyectos de Visual Studio de <strong>Artefactos de servicio de BizTalk</strong> (transformación) que se implementan en su BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">¿Cómo puedo comenzar a utilizar el SDK de servicios de Azure BizTalk</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">instalar el SDK de servicios de Azure BizTalk</a> incluye los pasos para empezar.
        </td>
    </tr>
    <tr>
        <td><strong>Crear contratos de partner</strong></td>
        <td>Se abre el Portal de servicios de BizTalk Azure hospedado en Azure donde agregar socios y crear X12, AS2 y contratos de EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes EDI mensajería en el Portal de servicios de BizTalk</a> incluye los pasos para empezar.
        </td>
    </tr>

<tr>
        <td><strong>Más información acerca de los servicios de BizTalk</strong></td>
        <td>Vaya al <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centro de aprendizaje</a> para obtener más información acerca de los servicios de BizTalk de Azure.</td>
</tr>
</table>


En la barra de tareas en la parte inferior, puede:

<table border="1">

<tr>
<td><strong>Administrar</strong> la implementación de aplicaciones</td>
<td>Se abre el portal de servicios de BizTalk de Azure. El Portal de servicios de BizTalk es la entrada a la configuración de EDI, incluida la adición de socios y crear X12, AS2 y contratos de EDIFACT.
<br/><br/>
Esto es el mismo como <strong>contratos de partner de crear</strong> en la ficha <strong>Inicio rápido</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes EDI mensajería en el Portal de servicios de BizTalk</a> proporciona más información sobre el Portal de servicios de BizTalk.</td>
</tr>

<tr>
<td><strong>Información de conexión</strong> de la Namespace de Control de acceso</td>
<td>Cuando se selecciona la información de conexión, se muestran el Namespace de Control de acceso, el emisor predeterminada y la clave predeterminada. Puede copiar estos valores.
<br/><br/>
También puede abrir el Portal de Control de acceso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Crear un control de acceso Namespace</a> proporciona más información sobre el Portal de Control de acceso.</td>
</tr>

<tr>
<td><strong>Sincronizar las teclas</strong> en la cuenta de almacenamiento</td>
<td>Cuando se crea una cuenta de almacenamiento, se crean automáticamente una clave principal y clave secundaria. Estas teclas de cifrado controlan el acceso a su cuenta de almacenamiento. Su BizTalk Service usa automáticamente la clave principal. <strong>Teclas de sincronización</strong> permiten a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir BizTalk Service.
<br/><br/>
Por ejemplo, desea el BizTalk Service para usar una nueva clave principal para la cuenta de almacenamiento. Para hacer esto:
<br/><br/>
<ol>
<li>Seleccione su BizTalk Service y <strong>Las teclas de sincronización</strong>. Seleccione la clave secundaria. Cuando hace esto, BizTalk Service inicia con la clave secundaria.</li>
<li>En el portal de Azure clásico, seleccione su cuenta de almacenamiento y regenerar la clave principal. Recuerde que la BizTalk Service usa la clave secundaria.</li>
<li>Seleccione su BizTalk Service y <strong>Las teclas de sincronización</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que regenera.</li>
<li>En el portal de clásico Azure, seleccione su cuenta de almacenamiento y regenerar la clave secundaria.</li>
</ol>
<br/>
Este proceso se denomina "claves de sustitución". El propósito es permitir a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> la aplicación</td>
<td>Cuando seleccione eliminar su BizTalk Service y se quitan todos los elementos que se implementa en él.</td>
</tr>
</table>


## <a name="dashboard"></a>Panel
Según la edición de servicios de BizTalk, todas las opciones enumeradas no esté disponibles. 

Cuando se selecciona el nombre de BizTalk Service, se muestra la pestaña panel. En el panel, puede:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Información general de uso: Muestra el número de conexiones usadas de híbrido
También se muestra el uso de datos en GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Gráfico de métrica: Muestra una lista fija de indicadores de rendimiento
Estas métricas facilite los valores en tiempo real sobre el estado de BizTalk Service. También puede elegir los valores **relativa** o **absoluta** y el intervalo de tiempo de **intervalo** de las mediciones que se muestran en el gráfico. 

Para obtener una descripción de estos indicadores de rendimiento, vaya a [Estadísticas disponibles](#Metrics) en este tema.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Referencia rápida: Listas sus propiedades BizTalk Service

<table border="1">

<tr>
<td><strong>Actualizar credenciales de base de datos de seguimiento</strong></td>
<td>Cambia el nombre de usuario y contraseña que utiliza para iniciar sesión en la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Actualizar el certificado SSL</strong></td>
<td>Puede actualizar el BizTalk Service para usar un certificado SSL diferente. Un certificado SSL autofirmado se crea automáticamente al <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">crear el servicio de BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Descargar certificado</strong></td>
<td>Puede descargar el certificado SSL utilizado por su BizTalk Service a un equipo local.</td>
</tr>
<tr>
<td><strong>Estado</strong></td>
<td>Muestra el estado actual de su BizTalk Service. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">servicios BizTalk: gráfico de estado del servicio</a>. </td>
</tr>
<tr>
<td><strong>Dirección URL del servicio</strong></td>
<td>La dirección URL de su servicio de BizTalk. Esta es la misma que la <strong>Dirección URL del dominio</strong> especificado cuando se crea la BizTalk Service.</td>
</tr>
<tr>
<td><strong>Dirección IP pública Virtual (VIP)</strong></td>
<td>La dirección IP asignada a su BizTalk Service. Se usa para todos los extremos de entrada y es la dirección de origen para el tráfico saliente. Esta dirección IP pertenece a su BizTalk Service siempre y cuando se crea. Si elimina BizTalk Service, la dirección IP se asigna a otro BizTalk Service.</td>
</tr>
<tr>
<td><strong>Namespace de ACS</strong></td>
<td>Se autentica con el servicio de BizTalk.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Listas de la edición especificarse cuando se crea BizTalk Service.</td>
</tr>
<tr>
<td><strong>Ubicación</strong></td>
<td>Muestra la región geográfica que hospeda su BizTalk Service.</td>
</tr>
<tr>
<td><strong>Creado</strong></td>
<td>Muestra la fecha y hora de que creación BizTalk Service.</td>
</tr>
<tr>
<td><strong>Base de datos de seguimiento</strong></td>
<td>El nombre de la base de datos de SQL Azure que almacena las tablas de seguimiento usadas por su BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Explicación de requisitos</a> proporciona información detallada sobre la base de datos de seguimiento.</td>
</tr>
<tr>
<td><strong>Almacenamiento de supervisión de archivado</strong></td>
<td>El nombre de la cuenta de almacenamiento de Azure que almacena los resultados de la supervisión de su BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Explicación de requisitos</a> se proporciona información detallada sobre la cuenta de almacenamiento.</td>
</tr>
<tr>
<td><strong>Nombre de suscripción</strong></td>
<td>Enumera la suscripción que hospeda su BizTalk Service. La suscripción rige acceder al portal clásico Azure.</td>
</tr>
<tr>
<td><strong>Id. de suscripción</strong></td>
<td>Cuando se crea una suscripción, se genera automáticamente un identificador de suscripción. Al utilizar las API de REST, tendrá que escriba el identificador de suscripción.</td>
</tr>
</table>

[Servicios BizTalk: aprovisionamiento portal clásico de Azure usando](http://go.microsoft.com/fwlink/p/?LinkID=302280) se enumeran los pasos para crear un BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Administrar información de conexión, las claves de sincronización y eliminar en la barra de tareas:

<table border="1">

<tr>
<td><strong>Administrar</strong> la implementación de aplicaciones</td>
<td>Se abre el Portal de servicios de BizTalk Azure. El Portal de servicios de BizTalk es la entrada a la configuración de EDI, incluida la adición de socios y crear X12, AS2 y contratos de EDIFACT.
<br/><br/>
Esto es el mismo como <strong>contratos de partner de crear</strong> en la ficha <strong>Inicio rápido</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes EDI mensajería en el Portal de servicios de BizTalk</a> proporciona más información sobre el Portal de servicios de BizTalk.</td>
</tr>
<tr>
<td><strong>Información de conexión</strong> de la Namespace de Control de acceso</td>
<td>Muestra el Namespace de Control de acceso, emisor predeterminado y valores de la clave predeterminada; que se pueden copiar.
<br/><br/>
También puede abrir el Portal de Control de acceso. Este Portal de Control de acceso es igual a utilizar la opción de Active Directory en el panel de navegación izquierdo.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administrar su Namespace de ACS</a> proporciona más información sobre el Portal de Control de acceso.</td>
</tr>
<tr>
<td><strong>Sincronizar las teclas</strong> en la cuenta de almacenamiento</td>
<td>Cuando se crea una cuenta de almacenamiento, se crean automáticamente una clave principal y clave secundaria. Estas teclas de cifrado controlan el acceso a su cuenta de almacenamiento. Su BizTalk Service usa automáticamente la clave principal. <strong>Teclas de sincronización</strong> permiten a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir BizTalk Service.
<br/><br/>
Por ejemplo, desea el BizTalk Service para usar una nueva clave principal para la cuenta de almacenamiento. Para hacer esto:
<br/><br/>
<ol>
<li>Seleccione su BizTalk Service y <strong>Las teclas de sincronización</strong>. Seleccione la clave secundaria. Cuando hace esto, BizTalk Service inicia con la clave secundaria.</li>
<li>En el portal de Azure clásico, seleccione su cuenta de almacenamiento y regenerar la clave principal. Recuerde que la BizTalk Service usa la clave secundaria.</li>
<li>Seleccione su BizTalk Service y <strong>Las teclas de sincronización</strong>. Ahora, seleccione la clave principal. Esta es la nueva clave principal que regenera.</li>
<li>En el portal de clásico Azure, seleccione su cuenta de almacenamiento y regenerar la clave secundaria.</li>
</ol>
<br/>
Este proceso se denomina "claves de sustitución". El propósito es permitir a los usuarios cambiar entre la clave principal y la clave secundaria sin interrumpir BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> la aplicación</td>
<td>Su BizTalk Service y todos los elementos que se implementa en la se quitan.</td>
</tr>
</table>


## <a name="monitor"></a>Monitor
No se aplica a la edición gratuita.

Cuando se selecciona el nombre de BizTalk Service, la pestaña Monitor está disponible y muestra la siguiente:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Gráfico de métrica: Muestra las métricas de rendimiento seleccionado
Estas métricas facilite los valores en tiempo real sobre el estado de BizTalk Service. Elija se muestran los indicadores de rendimiento. Se pueden mostrar simultáneamente un máximo de seis indicadores de rendimiento. 

También puede elegir los valores **relativa** o **absoluta** y el intervalo de tiempo de **intervalo** de las mediciones que se muestran. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Para quitar o mostrar medidas en el gráfico:
1. Seleccione la pestaña **Monitor** .
2. En la barra de tareas, seleccione **Agregar métricas** :  
![Seleccione Agregar métricas][AddMetrics]
3. Comprobar las métricas de rendimiento que desee mostrar.
4. Seleccione la marca de verificación para volver a la pestaña **Monitor** .
5. Seleccione el círculo situado junto a la métrica para mostrar el valor de esa métrica en el gráfico.  

    Por ejemplo, está atenuada la métrica de **Uso de CPU** ; el resultado no se muestra en el gráfico:  
![Métrica de uso de CPU está atenuado][GrayedMetric]  

    Seleccione el sombreado círculo para habilitar la métrica de **Uso de CPU** mostrar el resultado en el gráfico:  
![Métrica de uso de CPU está habilitada][EnabledMetric]

6. Para quitar una métrica de gráfico de visualización y la lista, seleccione **Eliminar métrica** en la barra de tareas. Para agregar el reverso métrico a la lista, seleccione **Agregar métricas** en la barra de tareas, compruebe la métrica y seleccione la marca de verificación para volver a la pestaña **Monitor** . Seleccione el sombreado círculo para habilitar la métrica.

## <a name="Metrics"></a>Estadísticas disponibles
Las siguientes contadores de rendimiento y métricas están disponibles:

<table border="1">

<tr>
<td><strong>Latencia de RountdTrip</strong></td>
<td>Muestra el tiempo medio necesario en milisegundos (ms) para procesar un mensaje desde el momento en que se recibió el mensaje hasta que el mensaje completo es procesado por BizTalk Service en todos los puentes. Se cuentan sólo los mensajes que se ha procesado correctamente.<br/><br/>
Cuando ocurre lo siguiente, se crea una marca de tiempo:
<ul>
<li>Mensaje entra en la puerta de enlace</li>
<li>Mensaje se envió al destino</li>
<li>Se recibió respuesta de destino</li>
<li>Respuesta de confirmación de destino enviado a la puerta de enlace</li>
</ul>
<br/>
Esta métrica muestra el resultado del cálculo siguiente:
<br/><br/>
[Destino acuse de recibo respuestas enviadas a la puerta de enlace] - [mensaje entra en la puerta de enlace]</td>
</tr>
<tr>
<td><strong>Errores de origen</strong></td>
<td>Muestra el número total de mensajes de error por la BizTalk Service cuando extraen mensajes de los extremos de origen.</td>
</tr>
<tr>
<td><strong>Uso de la CPU</strong></td>
<td>Muestra el promedio % tiempo de procesador de todas las instancias de la función.</td>
</tr>
<tr>
<td><strong>Latencia de procesamiento</strong></td>
<td>Muestra el tiempo promedio que se tarda en milisegundos (ms) para procesar un mensaje BizTalk Service en todos los puentes, excluyendo el tiempo invertido en destinos. Se cuentan sólo los mensajes que se ha procesado correctamente.<br/><br/>
Cuando cada uno de los siguientes eventos se producen, se crea una marca de tiempo:

<ul>
<li>Mensaje entra en la puerta de enlace</li>
<li>Mensaje se envió al destino</li>
<li>Se recibió respuesta de destino</li>
<li>Respuesta de confirmación de destino enviado a la puerta de enlace</li>
</ul>
<br/>Esta métrica muestra el resultado del cálculo siguiente:<br/><br/>
[Destino acuse de recibo respuestas enviadas a la puerta de enlace] - [mensaje entra en la puerta de enlace] - [se recibió respuesta de destino] + [mensaje se envió al destino]</td>
</tr>
<tr>
<td><strong>Errores de proceso</strong></td>
<td>Muestra el número total de mensajes de error durante el proceso de BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes enviados</strong></td>
<td>Muestra el número total de mensajes enviados por BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando un mensaje enviado desde una canalización alcanza la ruta de destino. Esta métrica no indica que un mensaje se ha procesado correctamente.<br/><br/>
En un escenario de respuesta de la solicitud, la métrica se incrementa cuando la ruta de destino envía un acuse de recibo la canalización.</td>
</tr>
<tr>
<td><strong>Mensajes recibidos</strong></td>
<td>Muestra el número total de mensajes recibidos por BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando se recibe un nuevo mensaje de la canalización.</td>
</tr>
<tr>
<td><strong>Mensajes en proceso</strong></td>
<td>Muestra el número total de mensajes está procesando actualmente BizTalk Service dentro de un intervalo de tiempo.</td>
</tr>
<tr>
<td><strong>Mensajes procesados</strong></td>
<td>Muestra el número total de mensajes correctamente procesados por BizTalk Service a través de todos los puentes dentro de un intervalo de tiempo. Esta métrica se incrementa cuando un mensaje se recibió correctamente la canalización y enruta correctamente al destino.</td>
</tr>
</table>


## <a name="scale"></a>Escala
En la ficha escala, puede sumar o restar el número de unidades utilizado por su BizTalk Service. De forma predeterminada, hay una unidad configurado. Pueden agregarse unidades adicionales escalar su BizTalk Service. Cuando aumenta la escala, aumenta el rendimiento. La cantidad de recursos también aumenta, como puentes de implementada, agreements, conexiones de LOB y capacidad de procesamiento. Por ejemplo, aumentar la escala de unidad de 1 a 2 unidades. En esta situación, puede implementar doble del número de puentes, doble los contratos, doble las conexiones de LOB y doble la potencia de procesamiento.

Algunas ediciones de BizTalk no ofrecen una opción de escala. En este caso, se permite una unidad. Para determinar el número de unidades puede ampliar su edición, consulte [servicios BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md).

Aumentar el número de unidades puede afectar precios. Si aumenta las unidades, seleccione **Guardar** muestra un mensaje que indica si se ve afectada la facturación. A continuación, elija continuar. Cuando aumente el número de unidades, los cambios de estado de BizTalk Service desde Active para actualizar. En el estado de actualización, su BizTalk Service sigue ejecutar.

[Servicios BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md) define una unidad de"".


## <a name="configure"></a>Configurar
No se aplica a las conexiones de híbrido.

El estado de copia de seguridad se establece en ninguno o automático. Cuando se establece en ninguno, se crean automáticamente sin copias de seguridad. Cuando se establece en automático, configure la ubicación de copia de seguridad, la frecuencia de la copia de seguridad y cuánto tiempo para mantener los archivos de copia de seguridad. 

[Servicios BizTalk: copia de seguridad y restaurar](biztalk-backup-restore.md) proporciona los detalles. 


## <a name="HybridConnections"></a>Conexiones de híbrido
Híbrido conexiones conectan una aplicación de Azure, como aplicaciones Web o aplicaciones móviles en Azure aplicación de servicio, a un recurso local que usa un puerto TCP estático, como SQL Server, MySQL, HTTP Web API y mayoría de los servicios Web personalizados. En servicios de BizTalk administra conexiones híbrido en el portal de clásico de Azure.

Para crear conexiones híbrido en el servicio de aplicación de Azure, consulte [acceso recursos mediante conexiones híbrido en el servicio de aplicación de Azure local](../app-service-web/web-sites-hybrid-connection-get-started.md).

Para crear o administrar conexiones híbrido en Azure BizTalk Services, vea [Conexiones híbrido](integration-hybrid-connection-overview.md).



## <a name="next"></a>Siguiente
Ahora que ya está familiarizado con las diferentes fichas, puede obtener más información sobre las características de Azure BizTalk Services:

- [Servicios de BizTalk: límite](biztalk-throttling-thresholds.md)  
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](biztalk-issuer-name-issuer-key.md)  
- [Servicios de BizTalk: Copia de seguridad y restauración](biztalk-backup-restore.md)

## <a name="see-also"></a>Vea también
- [Conexiones de híbrido](integration-hybrid-connection-overview.md)  
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](biztalk-editions-feature-chart.md)  
- [Servicios de BizTalk: Portal clásico de Azure usando el aprovisionamiento](biztalk-provision-services.md)  
- [Servicios de BizTalk: Gráfico de estado del servicio de BizTalk](biztalk-service-state-chart.md)  
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
