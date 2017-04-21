<properties
    pageTitle="Supervisar, diagnosticar y solucionar problemas de almacenamiento | Microsoft Azure"
    description="Usar características como el análisis de almacenamiento, registro de cliente y otras herramientas de terceros para identificar, diagnostican y solucionar problemas relacionados con el almacenamiento de Azure."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Supervisar, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Información general

Diagnosticar y solucionar problemas en una aplicación distribuida alojado en un entorno de nube pueden ser más complejas que en entornos tradicionales. Pueden implementar aplicaciones en una infraestructura PaaS o IaaS, de forma local, en un dispositivo móvil o en una combinación de las siguientes opciones. Normalmente, el tráfico de red de la aplicación puede recorrer redes públicas y privadas y puede utilizar la aplicación varias tecnologías de almacenamiento, como Microsoft Azure almacenamiento de tablas, BLOB, colas o archivos además de otros datos almacena tal como relacionales y bases de datos de documentos.

Para administrar estas aplicaciones correctamente debe supervisar ellos proactiva y comprender cómo diagnosticar y solucionar problemas de todos los aspectos de ellos y sus tecnologías dependientes. Como un usuario de servicios de almacenamiento de Azure, debe continuamente supervisar los servicios de almacenamiento, que utilice la aplicación para los cambios inesperados en comportamiento (por ejemplo, más lento que los tiempos de respuesta habitual) y usar el registro para recopilar datos más detallados y analizar un problema en profundidad. La información de diagnóstico que se obtienen de supervisión y el registro le ayudará a determinar la causa del problema producido en la aplicación. A continuación, puede solucionar el problema y determinar los pasos adecuados que puede realizar para corregirlo. Almacenamiento de Azure es un servicio de Azure de principal y formularios de una parte importante de la mayoría de las soluciones que los clientes implementar la infraestructura de Azure. Almacenamiento de Azure incluye funciones para simplificar la supervisión, diagnosticar y solucionar los problemas de almacenamiento en las aplicaciones basadas en la nube.

> [AZURE.NOTE] Cuentas de almacenamiento con un tipo de replicación de almacenamiento de zona redundantes (ZRS) no tiene la métrica o la capacidad de registro habilitado en este momento. Además, el servicio de archivos de Azure no admite el registro en este momento.

Para obtener una guía práctica para llevar a cabo para solucionar problemas de aplicaciones de almacenamiento de Azure, vea [To-End usando métrica de almacenamiento de Azure y registro, AzCopy y el analizador de mensaje de solución de problemas](storage-e2e-troubleshooting.md).

+ [Introducción]
    + [¿Cómo se organiza esta guía]
+ [Supervisar el servicio de almacenamiento]
    + [Supervisar el estado del servicio]
    + [La capacidad de supervisión]
    + [Supervisar la disponibilidad]
    + [Supervisar el rendimiento]
+ [Diagnosticar problemas de almacenamiento]
    + [Problemas de servicio de estado]
    + [Problemas de rendimiento]
    + [Diagnóstico de errores]
    + [Problemas de emulador de almacenamiento]
    + [Herramientas de registro de almacenamiento]
    + [Usar herramientas de registro de red]
+ [Seguimiento de llevar a cabo]
    + [Correlación de datos de registro]
    + [Id. de solicitud de cliente]
    + [Id. de solicitud de servidor]
    + [Marcas de tiempo]
+ [Guía de solución de problemas]
    + [Métricas mostrar AverageE2ELatency alta y baja AverageServerLatency]
    + [Métricas mostrar AverageE2ELatency baja y baja AverageServerLatency pero el cliente está experimentando latencia alta]
    + [Métricas mostrar AverageServerLatency alta]
    + [Experimenta retrasos inesperados en la entrega de mensajes de una cola]
    + [Métricas mostrar un aumento en PercentThrottlingError]
    + [Métricas mostrar un aumento en PercentTimeoutError]
    + [Métricas mostrar un aumento en PercentNetworkError]
    + [El cliente está recibiendo mensajes de HTTP 403 (prohibido)]
    + [El cliente está recibiendo mensajes de HTTP 404 (no encontrado)]
    + [El cliente está recibiendo mensajes de HTTP 409 (conflicto)]
    + [Métricas mostrar PercentSuccess baja o las entradas del registro de análisis tienen operaciones con el estado de la transacción de ClientOtherErrors]
    + [Métricas de capacidad mostrar un aumento inesperado en uso de la capacidad de almacenamiento]
    + [Experimenta inesperado reinicio de máquinas virtuales que tiene una gran cantidad de VHD adjunto]
    + [El problema se produce desde mediante el emulador de almacenamiento de desarrollo o de prueba]
    + [Se produzcan problemas al instalar el SDK de Azure para .NET]
    + [Tiene un problema distinto con un servicio de almacenamiento]
    + [Solución de problemas de archivos de Azure con Windows y Linux](storage-troubleshoot-file-connection-problems.md)
+ [Apéndices]
    + [Apéndice 1: Utilizar Fiddler para capturar el tráfico HTTP y HTTPS]
    + [Apéndice 2: Usar Wireshark para capturar el tráfico de red]
    + [Apéndice 3: Usando el analizador de mensaje de Microsoft para capturar el tráfico de red]
    + [Apéndice 4: Usar Excel para ver métricas y registro de datos]
    + [Apéndice 5: Supervisión con recomendaciones de aplicación para Visual Studio Team Services]

## <a name="introduction"></a>Introducción

Esta guía le muestra cómo usar funciones como el análisis de almacenamiento de Azure cliente registro en la biblioteca de cliente de almacenamiento de Azure y otras herramientas de terceros para identificar, diagnosticar y solucionar problemas de almacenamiento de Azure problemas relacionados con el.

![][1]

*Figura 1 supervisión, diagnósticos y solución de problemas*

Esta guía está pensada para que puedan leer principalmente por los desarrolladores de servicios en línea que usan servicios de almacenamiento de Azure y los profesionales de TI responsable de administrar dichos servicios en línea. Los objetivos de esta guía son:

- Para ayudarle a mantener la salud y el rendimiento de sus cuentas de almacenamiento de Azure.
- Para proporcionarle los procesos es necesarios y herramientas para ayudarle a decidir si un problema o en una aplicación se relaciona con el almacenamiento de Azure.
- Para proporcionar instrucciones útiles para solucionar problemas relacionados con el almacenamiento de Azure.

### <a name="how-this-guide-is-organized"></a>¿Cómo se organiza esta guía

La sección "[supervisión de su servicio de almacenamiento]", describe cómo supervisar el mantenimiento y rendimiento de los servicios de almacenamiento de Azure con métrica de análisis de almacenamiento de Azure (métrica de almacenamiento).

La sección "[diagnosticar problemas de almacenamiento]" describe cómo diagnosticar problemas con Azure almacenamiento de análisis de registro (el registro de almacenamiento). También se describe cómo habilitar el registro de cliente con las instalaciones de una de las bibliotecas de cliente, como la biblioteca de cliente de almacenamiento para .NET o el SDK de Azure para Java.

En la sección "[seguimiento de llevar a cabo]" se describe cómo puede relacionar la información contenida en varios archivos de registro y datos de métricas.

La sección "[Guía de solución de problemas]" proporciona guía de solución de algunos de los problemas comunes relacionados con el almacenamiento de información que se pueden encontrar.

La "[apéndices]" incluyen información sobre cómo utilizar otras herramientas como Wireshark y Netmon para analizar datos de paquetes de red, Fiddler para analizar mensajes HTTP/HTTPS y analizador de mensaje de Microsoft de correlación de los datos de registro.


## <a name="monitoring-your-storage-service"></a>Supervisar el servicio de almacenamiento

Si está familiarizado con la supervisión de rendimiento de Windows, puede considerar que de métrica de almacenamiento como un equivalente de Azure almacenamiento de contadores del Monitor de rendimiento de Windows. En métrica de almacenamiento, encontrará una serie completa de mediciones (contadores en terminología de Monitor de rendimiento de Windows) como la disponibilidad de servicio, el número total de solicitudes de servicio, o el porcentaje de solicitudes de servicio. Para obtener una lista completa de las mediciones disponibles, vea [Esquema de la tabla de métrica de almacenamiento análisis](http://msdn.microsoft.com/library/azure/hh343264.aspx). Puede especificar si desea que el servicio de almacenamiento para recopilar y agregan métricas cada hora o cada minuto. Para obtener más información acerca de cómo habilitar métricas y supervisar sus cuentas de almacenamiento, consulte [Habilitar métrica de almacenamiento y ver datos de métricas](http://go.microsoft.com/fwlink/?LinkId=510865).

Puede elegir qué métricas por hora que desea mostrar en el [Portal de Azure](https://portal.azure.com) y configurar reglas que notificar a los administradores por correo electrónico siempre una métrica por hora supera un determinado umbral. Para obtener más información, vea [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). 

El servicio de almacenamiento recopila métricas con un mejor esfuerzo, pero no puede grabar todas las operaciones de almacenamiento.

En el Portal de Azure, puede ver métricas como disponibilidad, solicitudes de total y números de latencia promedio de una cuenta de almacenamiento. Una regla de notificación también se configuró para alertar a un administrador si disponibilidad cae por debajo de un determinado nivel. Visualización de estos datos, un área posible para investigación es el porcentaje de éxito de servicio de tabla están por debajo de 100% (para obtener más información, consulte la sección "[métricas mostrar PercentSuccess baja o las entradas del registro de análisis tienen operaciones con el estado de la transacción de ClientOtherErrors]").

Debe supervisar continuamente las aplicaciones de Azure para asegurarse de que están realizando según lo esperado por y en buen estado:

- Establecimiento de algunas métricas de línea base para la aplicación que le permitirá comparar datos actuales e identificar cambios significativos en el comportamiento de almacenamiento de Azure y la aplicación. Los valores de las métricas de línea base, en muchos casos, se mostrará específicos de la aplicación y debe establecer cuando la aplicación de pruebas de rendimiento.
- Grabación métricas minutos y usarlos para supervisar la forma activa para errores inesperados y anomalías como picos error cuentan o solicitar tasas.
- Grabación métricas por hora y usarlos para supervisar valores promedio como promedio recuentos de errores y solicitar tasas.
- Investigar posibles problemas con herramientas de diagnóstico, como se describe más adelante en la sección "[diagnosticar problemas de almacenamiento]."

Los gráficos en la figura 3 ilustran cómo promedio que se produce para cada horas métricas puede ocultar las picos de actividad. Aparecen las mediciones por hora para mostrar una velocidad constante de solicitudes, mientras el minuto métricas revelan la variaciones que realmente están llevando a cabo.

![][3]

El resto de esta sección describe qué medidas debe supervisar y por qué.

### <a name="monitoring-service-health"></a>Supervisar el estado del servicio

Puede usar el [Portal de Azure](https://portal.azure.com) para ver el estado del servicio de almacenamiento (y otros servicios de Azure) en todas las regiones Azure todo el mundo. Esto le permite ver inmediatamente si un problema fuera de su control afecta el servicio de almacenamiento en la región que usa para la aplicación.

El [Portal de Azure](https://portal.azure.com) también puede proporcionar las notificaciones de las incidencias que afectan a los diversos servicios de Azure.
Nota: Esta información estaba disponible anteriormente, junto con los datos históricos, en el [Panel de servicio de Azure](http://status.azure.com).

Mientras el [Portal de Azure](https://portal.azure.com) recopila información de estado desde dentro de los centros de datos de Azure (supervisión de salida interior), también puede adoptar un enfoque de exterior para generar transacciones de síntesis periódicamente obtener acceso a la aplicación web hospedado Azure desde varias ubicaciones. Los servicios ofrecidos por [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) y perspectivas de aplicación para Visual Studio Team Services son ejemplos de este enfoque en exterior. Para obtener más información acerca de la información de la aplicación para Visual Studio Team Services, consulte el apéndice "[Apéndice 5: supervisión con recomendaciones de aplicación para Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>La capacidad de supervisión

Métrica de almacenamiento sólo almacena métricas de capacidad para el servicio de blobs porque BLOB cuenta normalmente la mayor parte de los datos almacenados (en el momento de escritura, no es posible usar métrica de almacenamiento para supervisar la capacidad de las tablas y colas). Puede encontrar estos datos en la tabla **$MetricsCapacityBlob** si se ha habilitado la supervisión para el servicio de blobs de Windows. Métrica de almacenamiento registra estos datos una vez al día, y puede usar el valor de la **RowKey** para determinar si la fila contiene una entidad relacionada con datos de usuario ( **datos**de valor) o análisis ( **análisis**de valor). Cada entidad almacenada con información sobre la cantidad de almacenamiento utilizado (**capacidad** medido en bytes) y el número actual de contenedores (**ContainerCount**) y BLOB (**ObjectCount**) en uso en la cuenta de almacenamiento. Para obtener más información acerca de las métricas de capacidad almacenadas en la tabla **$MetricsCapacityBlob** , vea el [Esquema de la tabla de métrica de almacenamiento análisis](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [AZURE.NOTE] Debe supervisar estos valores para una advertencia que se están acercando a los límites de capacidad de su cuenta de almacenamiento. En el Portal de Azure, puede agregar reglas de alerta para que le notifique si el uso de almacenamiento agregado supera o está por debajo de los umbrales que especifique.

Para obtener ayuda estimar el tamaño de varios objetos de almacenamiento como BLOB, consulte el blog [Descripción Azure almacenamiento de facturación: ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Supervisar la disponibilidad

Debe supervisar la disponibilidad de los servicios de almacenamiento en su cuenta de almacenamiento controlando el valor de la columna de **disponibilidad** en las tablas de cada hora o minuto métricas: **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. La columna **disponibilidad** contiene un valor porcentual que indica la disponibilidad del servicio o la operación de API representada por la fila (el **RowKey** se muestra si la fila contiene métricas para el servicio completo o para una operación de API específica).

Cualquier valor menor que 100% indica que se producen errores en algunas solicitudes de almacenamiento. Puede ver por qué se producen errores examinando las demás columnas en los datos de métricas que muestran los números de peticiones con tipos de error diferente como **ServerTimeoutError**. Debería ver la **disponibilidad** descender temporalmente 100% por razones como tiempo de espera del servidor transitorias mientras el servicio mueve las particiones a petición de equilibrio de carga mejor; la lógica de reintento en la aplicación cliente debe controlar las condiciones intermitentes. El artículo [operaciones de registro de análisis de almacenamiento y mensajes de estado](http://msdn.microsoft.com/library/azure/hh343260.aspx) enumeran los tipos de transacción métrica de almacenamiento incluye en el cálculo de **disponibilidad** .

En el [Portal de Azure](https://portal.azure.com), puede agregar reglas de alerta para que le notifique si la **disponibilidad** de un servicio está por debajo de un umbral que especifique.

La sección "[Guía de solución de problemas]" de esta guía describen algunos problemas comunes de servicio de almacenamiento de información relacionada con la disponibilidad.

### <a name="monitoring-performance"></a>Supervisar el rendimiento

Para supervisar el rendimiento de los servicios de almacenamiento, puede usar las siguientes métricas de las tablas de métricas por horas y minutos.

- Los valores de las columnas **AverageE2ELatency** y **AverageServerLatency** muestran el promedio de tiempo el servicio de almacenamiento o tipo de operación API dura para procesar las solicitudes. **AverageE2ELatency** es una medida de latencia to-end que incluye el tiempo necesario para leer la solicitud y enviar la respuesta además del tiempo necesario para procesar la solicitud (por lo tanto, incluye latencia de red una vez que la solicitud llegue el servicio de almacenamiento); **AverageServerLatency** es una medida de la hora de procesamiento y, por tanto, excluya cualquier latencia de red relacionados con la comunicación con el cliente. Consulte la sección "[métricas mostrar AverageE2ELatency alta y baja AverageServerLatency]" más adelante en esta guía para obtener información sobre por qué podría haber una diferencia significativa entre estos dos valores.
- Los valores de las columnas **TotalIngress** y **TotalEgress** muestran la cantidad total de datos, en bytes, ir y venir fuera de su servicio de almacenamiento o a través de un tipo específico de operación API.
- Los valores de la columna de **TotalRequests** mostrar el número total de solicitudes que recibe el servicio de almacenamiento de la operación de API. **TotalRequests** es el número total de solicitudes que recibe el servicio de almacenamiento.

Normalmente, se supervisar cambios inesperados en cualquiera de estos valores como un indicador de que tiene un problema que requiere investigación.

En el [Portal de Azure](https://portal.azure.com), puede agregar reglas de alerta para que le notifique si cualquiera de las mediciones de rendimiento para este otoño servicio debajo o supere un umbral que especifique.

La sección "[Guía de solución de problemas]" de esta guía describen algunos problemas comunes de servicio de almacenamiento de información relacionada con el rendimiento.


## <a name="diagnosing-storage-issues"></a>Diagnosticar problemas de almacenamiento

Hay varias maneras que puede convertirse en cuenta un problema o un problema en la aplicación, que incluyen:

- Error de principal que hace que la aplicación que se bloquea o deje de funcionar.
- Cambios significativos de los valores de línea base de las mediciones se supervisión como se describe en la sección anterior "[su servicio de almacenamiento de supervisión]".
- Informes de usuarios de la aplicación que no hayan completado alguna operación particular como se esperaba o que algunas características no funciona.
- Errores generados dentro de la aplicación que aparecen en los archivos de registro o a través de algún otro método de notificación.

Normalmente, los problemas relacionados con los servicios de almacenamiento de Azure se dividen en cuatro categorías:

- La aplicación tiene un problema de rendimiento, o bien informado de los usuarios o revela por los cambios en las métricas de rendimiento.
- Hay un problema con la infraestructura de almacenamiento de Azure en una o varias áreas.
- La aplicación produzca un error, o bien informado de los usuarios o revela por un aumento en una de las mediciones de recuento de error que supervisar.
- Durante el desarrollo y prueba, puede que esté utilizando el emulador almacenamiento local; puede encontrar algunos problemas relacionados específicamente con el uso del emulador de almacenamiento.

Las siguientes secciones describen los pasos que debe seguir para diagnosticar y solucionar problemas de cada uno de estos cuatro categorías. La sección "[Guía de solución de problemas]" más adelante en esta guía proporciona más detalles sobre algunos problemas comunes que puede encontrarse.

### <a name="service-health-issues"></a>Problemas de servicio de estado

Problemas de estado del servicio suelen fuera de su control. El [Portal de Azure](https://portal.azure.com) proporciona información sobre los problemas en curso con Azure servicios incluidos los servicios de almacenamiento. Si ha optado por almacenamiento Geo redundantes de acceso de lectura cuando creó su cuenta de almacenamiento, a continuación, en caso de que los datos no está disponible en la ubicación principal, la aplicación puede cambiar temporalmente la copia de sólo lectura en la ubicación secundaria. Para ello, la aplicación debe poder cambiar entre el uso de las ubicaciones de almacenamiento primario y secundario y puedan trabajar en modo de funcionalidad reducida con datos de sólo lectura. Las bibliotecas de cliente de almacenamiento de Azure permiten definir una directiva de reintento que puede leer desde el almacenamiento secundario en caso de que se produce un error de lectura desde el almacenamiento principal. La aplicación también debe tener en cuenta que los datos en la ubicación secundaria están finalmente coherentes. Para obtener más información, consulte el blog [Opciones de redundancia de almacenamiento de Azure y almacenamiento de acceso de lectura Geo redundantes](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemas de rendimiento

El rendimiento de una aplicación puede ser subjetivo, especialmente desde la perspectiva del usuario. Por lo tanto, es importante tener métricas de línea base para ayudarle a identificar dónde puede haber un problema de rendimiento. Muchos factores pueden afectar al rendimiento de un servicio de almacenamiento de Azure desde la perspectiva de la aplicación de cliente. Estos factores pueden funcionar en el servicio de almacenamiento, en el cliente o en la infraestructura de red. por lo tanto, es importante tener una estrategia para identificar el origen del problema de rendimiento.

Después de que ha identificado a la ubicación de la causa del problema de rendimiento de las mediciones es muy probable que, a continuación, puede usar los archivos de registro para buscar información detallada para diagnosticar y solucionar el problema.

La sección "[Guía de solución de problemas]" más adelante en esta guía se proporciona más información sobre algunos rendimiento comunes relacionados con los problemas que puede surgir.

### <a name="diagnosing-errors"></a>Diagnóstico de errores

Usuarios de la aplicación pueden informarle de errores notificados por la aplicación cliente. Métrica de almacenamiento también registros recuentos de tipos de error diferente de los servicios de almacenamiento, como **NetworkError**, **ClientTimeoutError**o **AuthorizationError**. Mientras métrica de almacenamiento solo registros recuentos de tipos de error diferente, puede obtener más detalles acerca de las solicitudes individuales examinando del servidor, cliente y registros de red. Normalmente, el código de estado HTTP devuelto por el servicio de almacenamiento dará una indicación de por qué ha fallado la solicitud.

> [AZURE.NOTE] Recuerde que debe esperar ver algunos errores intermitentes: por ejemplo, los errores debido a problemas temporales de red o los errores de aplicación.

Los recursos siguientes son útiles para la descripción de los códigos de estado y de error relacionados con el almacenamiento:

- [Códigos de Error de la API de REST comunes](http://msdn.microsoft.com/library/azure/dd179357.aspx)
- [Códigos de Error del servicio de blobs](http://msdn.microsoft.com/library/azure/dd179439.aspx)
- [Códigos de Error del servicio de cola](http://msdn.microsoft.com/library/azure/dd179446.aspx)
- [Códigos de Error del servicio de tabla](http://msdn.microsoft.com/library/azure/dd179438.aspx)
- [Códigos de Error del servicio de archivo](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemas de emulador de almacenamiento

El SDK de Azure incluye un emulador de almacenamiento que se puede ejecutar en una estación de trabajo de desarrollo. Este emulador simula la mayoría del comportamiento de los servicios de almacenamiento de Azure y resulta útil durante el desarrollo y prueba, lo que le permite ejecutar aplicaciones que usan servicios de almacenamiento de Azure sin necesidad de una suscripción de Azure y una cuenta de almacenamiento de Azure.

La sección "[Guía de solución de problemas]" de esta guía describe algunos problemas comunes de uso del almacenamiento.

### <a name="storage-logging-tools"></a>Herramientas de registro de almacenamiento

Registro de almacenamiento proporciona un registro de servidor de solicitudes de almacenamiento en su cuenta de almacenamiento de Azure. Para obtener más información acerca de cómo habilitar el registro del servidor y tener acceso a los datos del registro, vea [activar el registro de almacenamiento y acceso a datos de registro](http://go.microsoft.com/fwlink/?LinkId=510867).

La biblioteca de cliente de almacenamiento para .NET le permite recopilar datos de registro de cliente se relación con las operaciones de almacenamiento realizadas por la aplicación. Para obtener más información, consulte [el registro de cliente con la biblioteca de cliente de almacenamiento de .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [AZURE.NOTE] En algunos casos (por ejemplo, errores de autorización de SA), un usuario puede informar de un error que no puede encontrar datos de solicitud en los registros de almacenamiento del servidor. Puede usar las capacidades de registro de la biblioteca de cliente de almacenamiento para investigar si es la causa del problema en el cliente o usar herramientas de supervisión de red para investigar la red.

### <a name="using-network-logging-tools"></a>Usar herramientas de registro de red

Puede capturar el tráfico entre el cliente y el servidor para proporcionar información detallada sobre los datos que se va a intercambiar el cliente y el servidor y las condiciones de red subyacente. Herramientas de registro de red útil incluyen:

- [Fiddler](http://www.telerik.com/fiddler) es un proxy que permite examinar los encabezados y los datos de la carga de los mensajes de solicitud y respuesta HTTP y HTTPS de depuración de web gratuita. Para obtener más información, vea [Apéndice 1: utilizar Fiddler para capturar el tráfico HTTP y HTTPS](#appendix-1).
- [Microsoft Network Monitor (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) y [Wireshark](http://www.wireshark.org/) son analizadores gratuita que le permiten ver información detallada de paquete para una amplia gama de protocolos de red. Para obtener más información acerca de Wireshark, vea "[Apéndice 2: usar Wireshark para capturar el tráfico de red](#appendix-2)".
- Analizador de mensaje de Microsoft es una herramienta de Microsoft que reemplaza Netmon y que además captura los datos del paquete de red, le ayuda a ver y analizar los datos de registro que se genera desde otras herramientas. Para obtener más información, vea "[Apéndice 3: usar el analizador del mensaje de Microsoft para capturar el tráfico de red](#appendix-3)".
- Si desea realizar una prueba de conectividad básica para comprobar que el equipo cliente puede conectarse con el servicio de almacenamiento de Azure en la red, no puede hacerlo con la herramienta estándar **ping** en el cliente. Sin embargo, puede usar la [herramienta **tcping** ](http://www.elifulkerson.com/projects/tcping.php) para comprobar la conectividad.

En muchos casos, los datos del registro de registro de almacenamiento y la biblioteca de cliente de almacenamiento será suficientes para diagnosticar un problema, pero en algunos casos, tendrá la información más detallada que pueden proporcionar estas herramientas de registro de red. Por ejemplo, utilizar Fiddler para ver mensajes HTTP y HTTPS permite ver encabezado y carga de los datos enviados a y desde los servicios de almacenamiento, que le permitirá examinar cómo las operaciones de almacenamiento de reintentos de una aplicación de cliente. Analizadores de protocolo como Wireshark trabajan en el nivel de paquete que permite ver datos TCP, que le permitirá solucionar problemas de conectividad y de pérdida de paquetes. Analizador de mensajes puede funcionar en capas de HTTP y TCP.

## <a name="end-to-end-tracing"></a>Seguimiento de llevar a cabo

Seguimiento de llevar a cabo con una amplia variedad de archivos de registro es una técnica útil para investigar posibles problemas. Puede usar la información de fecha y hora de los datos de métricas como indicación de dónde comenzar a pensar en los archivos de registro para la información detallada que le ayudará a solucionar el problema.

### <a name="correlating-log-data"></a>Correlación de datos de registro

Al ver registros de aplicaciones cliente, seguimiento de red y registro de almacenamiento del servidor es fundamental para poder relacionar solicitudes entre los archivos de registro diferentes. Los archivos de registro incluyen un número de distintos campos que son útiles como identificadores de correlación. El identificador de solicitud de cliente es el campo más útil utilizar para relacionar las entradas en los distintos registros. Sin embargo a veces, puede ser útil utilizar el Id. de solicitud de servidor o las marcas de tiempo. Las siguientes secciones proporcionan más detalles acerca de estas opciones.

### <a name="client-request-id"></a>Id. de solicitud de cliente

La biblioteca de cliente de almacenamiento genera automáticamente un identificador de solicitud de cliente único para cada solicitud.

- En el registro de cliente que crea la biblioteca de cliente de almacenamiento, el id de cliente aparece en el campo **Id. de solicitud de cliente** de cada entrada del Registro relativos a la solicitud.
- En una traza de red, como una captura Fiddler, el identificador de solicitud de cliente está visible en los mensajes de solicitud como el valor del encabezado **x-ms-cliente--Id. de solicitud** HTTP.
- En el registro de registro de almacenamiento del servidor, el id de cliente aparece en la columna ID de solicitud de cliente.

> [AZURE.NOTE]Es posible que varias solicitudes compartir el mismo id de cliente porque el cliente puede asignar este valor (aunque la biblioteca de cliente de almacenamiento se asigna automáticamente un nuevo valor). En el caso de reintentos desde el cliente, todos los intentos de compartan el mismo id de cliente. En el caso de un lote enviado desde el cliente, el lote tiene un identificador de solicitud de solo cliente.


### <a name="server-request-id"></a>Id. de solicitud de servidor

El servicio de almacenamiento genera automáticamente identificadores de solicitud del servidor.

- En el registro de registro de almacenamiento del servidor, el identificador de solicitud del servidor aparece la columna **ID de solicitud de encabezado** .
- En una traza de red, como una captura Fiddler, el identificador de solicitud del servidor aparece en los mensajes de respuesta como el valor del encabezado **x-ms-id de solicitud** HTTP.
- En el registro de cliente que crea la biblioteca de cliente de almacenamiento, el identificador de solicitud del servidor aparece en la columna de **Texto de la operación** de la entrada del registro con detalles de la respuesta del servidor.

> [AZURE.NOTE] El servicio de almacenamiento siempre asigna a un único servidor Id. de solicitud a cada solicitud recibe, por lo que cada reintento desde el cliente y cada operación incluidos en un lote tienen un identificador de solicitud de servidor únicos.

Si la biblioteca de cliente de almacenamiento produce una **StorageException** en el cliente, la propiedad **RequestInformation** contiene un objeto de **RequestResult** que incluye una propiedad **ServiceRequestID** . También puede acceder a un objeto **RequestResult** desde una instancia de **OperationContext** .

El ejemplo de código siguiente muestra cómo establecer un valor personalizado de **ClientRequestId** adjuntando un objeto **OperationContext** la solicitud del servicio de almacenamiento. También se muestra cómo recuperar el valor de **ServerRequestId** desde el mensaje de respuesta.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Marcas de tiempo

También puede usar las marcas de tiempo para localizar las entradas del registro relacionadas, pero tenga cuidado de cualquier desfase del reloj entre el cliente y el servidor que exista. Debe buscar más o menos 15 minutos para la coincidencia de entradas de servidor en función de la marca de tiempo en el cliente. Recuerde que los metadatos de blobs de los BLOB que contienen métricas indican el intervalo de tiempo para las mediciones almacenados en el blob; Esto es útil si tiene muchos BLOB métricas para la misma hora o el minuto.

## <a name="troubleshooting-guidance"></a>Guía de solución de problemas

Esta sección le ayudará a con el diagnóstico y solución de problemas de algunos de los problemas comunes de la aplicación puede surgir al usar los servicios de almacenamiento de Azure. Use la siguiente lista para buscar la información pertinente para el problema concreto.

**Solución de problemas de árbol de decisión**

----------

¿El problema se relacionan con el rendimiento de uno de los servicios de almacenamiento?

- [Métricas mostrar AverageE2ELatency alta y baja AverageServerLatency]
- [Métricas mostrar AverageE2ELatency baja y baja AverageServerLatency pero el cliente está experimentando latencia alta]
- [Métricas mostrar AverageServerLatency alta]
- [Experimenta retrasos inesperados en la entrega de mensajes de una cola]

----------

¿El problema se relacionan con la disponibilidad de uno de los servicios de almacenamiento?

- [Métricas mostrar un aumento en PercentThrottlingError]
- [Métricas mostrar un aumento en PercentTimeoutError]
- [Métricas mostrar un aumento en PercentNetworkError]

----------

¿Es la aplicación cliente recibe una respuesta HTTP 4XX (como 404) de un servicio de almacenamiento?

- [El cliente está recibiendo mensajes de HTTP 403 (prohibido)]
- [El cliente está recibiendo mensajes de HTTP 404 (no encontrado)]
- [El cliente está recibiendo mensajes de HTTP 409 (conflicto)]

----------

[Métricas mostrar PercentSuccess baja o las entradas del registro de análisis tienen operaciones con el estado de la transacción de ClientOtherErrors]

----------

[Métricas de capacidad mostrar un aumento inesperado en uso de la capacidad de almacenamiento]

----------

[Experimenta inesperado reinicio de máquinas virtuales que tiene una gran cantidad de VHD adjunto]

----------

[El problema se produce desde mediante el emulador de almacenamiento de desarrollo o de prueba]

----------

[Se produzcan problemas al instalar el SDK de Azure para .NET]

----------

[Tiene un problema distinto con un servicio de almacenamiento]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostrar AverageE2ELatency alta y baja AverageServerLatency

La siguiente ilustración desde el [Portal de Azure](https://portal.azure.com) herramienta de supervisión muestra un ejemplo donde el **AverageE2ELatency** es mucho mayor que la **AverageServerLatency**.

![][4]

Observe que el servicio de almacenamiento sólo calcula la métrica **AverageE2ELatency** para las solicitudes y, a diferencia de **AverageServerLatency**, incluye el tiempo tarda el cliente para enviar los datos y recibir confirmación del servicio de almacenamiento. Por lo tanto, podría ser una diferencia entre **AverageE2ELatency** y **AverageServerLatency** debido a la aplicación cliente es lento responder o debido a las condiciones de la red.

> [AZURE.NOTE] También puede ver **E2ELatency** y **ServerLatency** para las operaciones de almacenamiento individuales de los datos del registro de registro de almacenamiento.

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de rendimiento de cliente

Posibles motivos para el cliente responde lentamente incluyen tener un número limitado de subprocesos o conexiones disponibles o se está quedando sin recursos como el ancho de banda de CPU, la memoria o la red. Es posible que pueda resolver el problema modificando el código de cliente para que sea más eficaz (por ejemplo, mediante llamadas asincrónicas al servicio de almacenamiento), o mediante una máquina Virtual más grande (con más núcleos y más memoria).

Para los servicios cola de la tabla, el algoritmo de Nagle puede causar alta **AverageE2ELatency** en comparación con **AverageServerLatency**: para obtener más información, consulte la entrada de [algoritmo de Nagle es no descriptivo hacia las solicitudes de pequeñas](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Puede deshabilitar el algoritmo de Nagle en código mediante el uso de la clase **ServicePointManager** en el espacio de nombres **System.Net** . Debe hacer esto antes de realizar llamadas a la tabla o los servicios de cola de la aplicación, ya que esto no afecta a las conexiones que ya están abiertos. En el ejemplo siguiente se incluye desde el método **Application_Start** en una función de trabajo.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Compruebe el registro de cliente para ver cuántas solicitudes que está enviando la aplicación cliente y comprobar si .NET generales relacionados con botella de rendimiento en el cliente, como la CPU, .NET recolección, uso de la red o la memoria. Como punto de partida para solucionar problemas de aplicaciones cliente. NET, vea [Depurar, seguimiento y perfiles](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latencia de red

Alto nivel de latencia de llevar a cabo causado por la red suele ser debido a condiciones transitorias. Puede investigar los problemas de red transitorias y persistente como paquetes perdidos mediante herramientas como Wireshark o el analizador de mensaje de Microsoft.

Para obtener más información sobre cómo usar Wireshark para solucionar problemas de red, consulte "[Apéndice 2: usar Wireshark para capturar el tráfico de red]."

Para obtener más información sobre cómo usar el analizador de mensaje de Microsoft para solucionar problemas de red, consulte "[Apéndice 3: usando el analizador de Microsoft Message para capturar el tráfico de red]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostrar AverageE2ELatency baja y baja AverageServerLatency pero el cliente está experimentando latencia alta

En este escenario, lo más probable es un retraso en las solicitudes de almacenamiento que se comunique con el servicio de almacenamiento. Debe investigar por qué solicitudes del cliente no logran a través del servicio de blobs de Windows.

Un motivo para el cliente de retrasar el envío de solicitudes es que hay un número limitado de conexiones disponibles o subprocesos.

También debe comprobar si el cliente está realizando varios intentos e investigar la razón si este es el caso. Para determinar si el cliente está realizando varios intentos, puede:

- Examine los registros de análisis de almacenamiento. Si se producen varios reintentos, verá varias operaciones con el mismo ID de cliente, pero con identificadores de solicitud de servidor diferente.
- Examine los registros del cliente. El registro detallado indicará que se ha producido un reintento.
- Depurar el código y compruebe las propiedades del objeto **OperationContext** asociado a la solicitud. Si se vuelve a intentar la operación, la propiedad **RequestResults** incluye varios solicitud del servidor únicos identificadores. También puede comprobar las horas de inicio y finalización de cada solicitud. Para obtener más información, vea el código de ejemplo en la sección [Id. de solicitud del servidor].

Si no existen problemas en el cliente, debe investigar posibles problemas de red, como la pérdida de paquetes. Puede usar herramientas como Wireshark o analizador de mensaje de Microsoft para investigar problemas de red.

Para obtener más información sobre cómo usar Wireshark para solucionar problemas de red, consulte "[Apéndice 2: usar Wireshark para capturar el tráfico de red]."

Para obtener más información sobre cómo usar el analizador de mensaje de Microsoft para solucionar problemas de red, consulte "[Apéndice 3: usando el analizador de Microsoft Message para capturar el tráfico de red]."

### <a name="metrics-show-high-AverageServerLatency"></a>Métricas mostrar AverageServerLatency alta

En el caso de alta **AverageServerLatency** para las solicitudes de descarga de blobs, debe usar los registros de almacenamiento de registro para ver si hay solicitudes repetidas para el mismo blob (o conjunto de BLOB). Las solicitudes de carga para blobs, debe investigar qué bloque que está usando el cliente de tamaño (por ejemplo, bloquea inferior a 64 KB de tamaño puede dar lugar a gastos a menos que las lecturas están también en inferior a 64 KB fragmentos), y si va a varios clientes cargar bloques en el mismo blob en paralelo. También debe comprobar las mediciones por minuto picos en el número de solicitudes de resultado sobrepasan la por segundo destinos escalabilidad: consulte también "[métricas muestran un aumento de PercentTimeoutError]".

Si está viendo alta **AverageServerLatency** para BLOBs descargar solicitudes cuando hay solicitudes repetidas la misma blob o conjunto de BLOB, debe considerar almacenamiento en caché estos BLOB con Azure caché o la red de entrega de contenido (CDN) de Azure. Para las solicitudes de carga, puede mejorar el rendimiento utilizando un tamaño de bloque más grande. Para las consultas de tablas, también es posible implementar la caché del cliente en clientes que realizan las mismas operaciones de consulta y donde los datos no cambian con frecuencia.

Valores altos de **AverageServerLatency** también pueden ser un síntoma de mal diseñadas tablas o consultas que resultado en operaciones de análisis o sigan el modelo de contra anexar/anteponga. Para obtener más información, vea "[métricas muestran un aumento de PercentThrottlingError]".

> [AZURE.NOTE] Puede encontrar aquí una lista de comprobación del rendimiento de lista de comprobación completa: [Microsoft Azure almacenamiento de rendimiento y escalabilidad lista de comprobación](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Experimenta retrasos inesperados en la entrega de mensajes de una cola

Si está experimentando un retraso entre la hora de que una aplicación agrega un mensaje de una cola y el tiempo que esté disponible para la lectura de la cola, debe tener los siguientes pasos para diagnosticar el problema:

- Compruebe que la aplicación está agregando correctamente los mensajes a la cola. Compruebe que la aplicación está no volver a intentar el método **AddMessage** varias veces antes de poder continuar. Los registros de la biblioteca de cliente de almacenamiento mostrarán los repetidos reintentos de operaciones de almacenamiento.
- Compruebe no hay ningún reloj skew entre la función de trabajo que agrega el mensaje a la cola y la función de trabajo que se lee el mensaje de la cola que hace que aparezcan como si hay un retraso en el procesamiento.
- Compruebe si es un error en la función de trabajo que lee los mensajes de la cola. Si un cliente de cola llama al método **GetMessage** pero deja de responder con una confirmación, el mensaje permanecerá visible en la cola hasta que expire la **invisibilityTimeout** . En este momento, el mensaje esté disponible para el procesamiento de nuevo.
- Compruebe si la longitud de cola crece con el tiempo. Esto puede ocurrir si no tiene suficientes trabajadores disponibles para procesar todos los mensajes que otros trabajadores va a colocar en la cola. Compruebe también las métricas para ver si las peticiones de eliminación se repiten falla y el recuento de quitar en los mensajes que podrían indicar intentos para eliminar el mensaje.
- Examine los registros del registro de almacenamiento de las operaciones de cola que tienen superiores a los valores de **E2ELatency** y **ServerLatency** esperados sobre un período de tiempo más largo habitual.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Métricas mostrar un aumento en PercentThrottlingError

Se producen errores de limitación cuando supere los objetivos de escalabilidad de un servicio de almacenamiento. El servicio de almacenamiento hace esto para asegurarse de que ningún inquilino o solo cliente puede usar el servicio a costa de otros usuarios. Para obtener más información, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md) para más información sobre los objetivos de escalabilidad para cuentas de almacenamiento y objetivos de rendimiento de particiones dentro de las cuentas de almacenamiento.

Si la métrica de **PercentThrottlingError** mostrar un aumento en el porcentaje de solicitudes que se producen errores con un error de límite, debe investigar uno de dos escenarios:

- [Aumentar transitoria en PercentThrottlingError]
- [Aumentar permanente PercentThrottlingError error]

Un aumento de **PercentThrottlingError** con qué frecuencia se produce al mismo tiempo como un aumento en el número de solicitudes de almacenamiento, o cuando haya inicialmente cargar probar la aplicación. También se puede manifestar en el cliente como "503 servidor ocupado" o mensajes de estado HTTP "tiempo de espera de operación 500" de las operaciones de almacenamiento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumentar transitoria en PercentThrottlingError

Si está viendo picos en el valor de **PercentThrottlingError** que coincidan con los períodos de alta actividad de la aplicación, debe implementar un back exponencial (no lineal) desactivar estrategia de reintentos en el cliente: esto reducir la carga inmediata en la partición y ayudar a la aplicación para mejorar el tráfico intenso. Para obtener más información sobre cómo implementar directivas de reintento mediante la biblioteca de cliente de almacenamiento, consulte [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [AZURE.NOTE] Es posible que vea picos en el valor de **PercentThrottlingError** que no coinciden con los períodos de alta actividad de la aplicación: lo más probable aquí es el servicio de almacenamiento mover particiones para mejorar el equilibrio de carga.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumentar permanente PercentThrottlingError error

Si está viendo un valor continuamente alto **PercentThrottlingError** siguiente las pruebas de un aumento permanente en su volúmenes transacción o cuando se realiza la carga inicial de la aplicación, será necesario evaluar cómo utiliza la aplicación particiones de almacenamiento y se está acercando a los objetivos de escalabilidad de una cuenta de almacenamiento. Por ejemplo, si está viendo el límite de errores de una cola (que se cuenta como una sola partición), a continuación, debe considerar usar colas adicionales para extender las transacciones a varias particiones. Si está viendo la limitación de los errores en una tabla, debe considerar usar un esquema de particiones diferentes para extender las transacciones a varias particiones utilizando una amplia gama de valores de clave de partición. Una de las causas comunes de este problema es el patrón contra prepend/anexar donde se selecciona la fecha como la clave de partición y, a continuación, se escribe todos los datos en un momento determinado para una partición: en carga, esto puede producir un botella de escritura. Debe tener en cuenta un diseño de partición diferente o evaluar si usa el almacenamiento de blobs podría ser mejor solución. También debe comprobar si el límite se produce como resultado el tráfico intenso e investigar formas de la trama de solicitudes de suavizado.

Si distribuye las transacciones entre varias particiones, aún debe tener en cuenta los límites de escalabilidad definido para la cuenta de almacenamiento. Por ejemplo, si utiliza diez colas cada procesamiento el número máximo de 2.000 mensajes de 1KB por segundo, estará en el límite de 20 000 mensajes por segundo para la cuenta de almacenamiento general. Si necesita procesar más de 20.000 entidades por segundo, considere la posibilidad de usar varias cuentas de almacenamiento. También debe tener en cuenta que el tamaño de sus solicitudes y entidades tiene un gran impacto en cuando el servicio de almacenamiento limita sus clientes: si tiene más grande solicitudes y entidades, puede acelerado antes.

Diseño de la consulta ineficaz puede causar que alcanzar los límites de escalabilidad de particiones de tabla. Por ejemplo, una consulta con un filtro que solo selecciona uno por ciento de las entidades de una partición pero que explora todas las entidades de una partición tendrán que tener acceso a cada entidad. Cada entidad de lectura se contabilizan en el número total de transacciones en esa partición; por lo tanto, se pueden llegar fácilmente a los objetivos de escalabilidad.

> [AZURE.NOTE] Las pruebas de rendimiento deben revelar los diseños de consulta ineficaz de la aplicación.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Métricas mostrar un aumento en PercentTimeoutError

Las métricas mostrarán un aumento **PercentTimeoutError** para uno de los servicios de almacenamiento. Al mismo tiempo, el cliente recibe un gran volumen de mensajes de estado HTTP "tiempo de espera de operación 500" de las operaciones de almacenamiento.

> [AZURE.NOTE] Es posible que vea errores de tiempo de espera temporalmente como el servicio de almacenamiento de la carga de solicitudes de saldos moviendo una partición en un servidor nuevo.

La métrica **PercentTimeoutError** es la suma de las siguientes métricas: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**y **SASServerTimeoutError**.

El tiempo de espera del servidor es causado por un error en el servidor. Los tiempos de espera de cliente ocurrir porque una operación en el servidor ha superado el tiempo de espera especificado por el cliente; Por ejemplo, un cliente que utiliza la biblioteca de cliente de almacenamiento puede establecer un tiempo de espera para una operación mediante la propiedad **ServerTimeout** de la clase **QueueRequestOptions** .

Tiempo de espera del servidor indica un problema con el servicio de almacenamiento que precisa una investigación más. Puede usar métricas para ver si se están alcanzando los límites de escalabilidad al servicio y a identificar los picos de tráfico que podría estar causando este problema. Si el problema es intermitente, puede deberse a Equilibrio de carga actividad en el servicio. Si el problema es persistente y no está causado por la aplicación alcance los límites de escalabilidad del servicio, debe provocar un problema de soporte técnico. Para los tiempos de espera de cliente, debe decidir si el tiempo de espera se establece en un valor apropiado en el cliente y establece el valor de tiempo de espera en el cliente de cualquiera de los cambios o investigar cómo puede mejorar el rendimiento de las operaciones en el servicio de almacenamiento, por ejemplo por la optimización de las consultas de tabla o reducir el tamaño de los mensajes.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Métricas mostrar un aumento en PercentNetworkError

Las métricas mostrarán un aumento **PercentNetworkError** para uno de los servicios de almacenamiento. La métrica **PercentNetworkError** es la suma de las siguientes métricas: **NetworkError**, **AnonymousNetworkError**y **SASNetworkError**. Se producen cuando el servicio de almacenamiento detecta un error de red cuando el cliente realiza una solicitud de almacenamiento.

La causa más común de este error es un cliente desconectar antes de que caduque un tiempo de espera en el servicio de almacenamiento. Debe investigar el código en el cliente de entender por qué y cuándo el cliente se desconecta del servicio de almacenamiento. También puede usar Wireshark, el analizador de mensaje de Microsoft o Tcping para investigar problemas de conectividad de red del cliente. Estas herramientas se describen en los [apéndices].

### <a name="the-client-is-receiving-403-messages"></a>El cliente está recibiendo mensajes de HTTP 403 (prohibido)

Si la aplicación cliente es producir errores de HTTP 403 (prohibido), una causa probable es que el cliente está utilizando una firma de acceso de compartido expirados (SA) cuando envía una solicitud de almacenamiento (aunque otras posibles causas incluyen reloj claves no válidos sesgo y encabezados vacíos). Si una clave de SA expirada es la causa, no verá las entradas en los datos de registro de registro de almacenamiento del servidor. En la tabla siguiente se muestra un ejemplo del registro de cliente generado por la biblioteca de cliente de almacenamiento que muestra que se produzca este problema:

Origen|Nivel de detalle|Nivel de detalle|Id. de solicitud de cliente|Texto de la operación
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Información|3|85d077ab-...|Iniciar la operación con ubicación principal modo ubicación PrimaryOnly por.
Microsoft.WindowsAzure.Storage|Información|3|85d077ab-...|Iniciar solicitud sincrónico a https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;hueco = MiDirectiva&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ 3D&amp;versión api = 14 de 02 de 2014.
Microsoft.WindowsAzure.Storage|Información|3|85d077ab-...|Esperando respuesta.
Microsoft.WindowsAzure.Storage|Advertencia|2|85d077ab-...|Excepción de tiempo de espera de respuesta: el servidor remoto devolvió un error: (403) prohibido...
Microsoft.WindowsAzure.Storage|Información|3|85d077ab-...|Se recibió respuesta. Código de estado = 403, solicitar ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, contenido MD5 = ETag =.
Microsoft.WindowsAzure.Storage|Advertencia|2|85d077ab-...|Excepción durante la operación: el servidor remoto devolvió un error: (403) prohibido...
Microsoft.WindowsAzure.Storage|Información|3 |85d077ab-...|Comprobar si debe volver a intentar la operación. Número de reintentos = 0, código de estado HTTP = 403, excepción = el servidor remoto devolvió un error: (403) prohibido...
Microsoft.WindowsAzure.Storage|Información|3|85d077ab-...|Se ha establecido la siguiente ubicación principal, según el modo de ubicación.
Microsoft.WindowsAzure.Storage|Error|1|85d077ab-...|Directiva de reintento no permitía un reintento. Error con el servidor remoto devolvió un error: (403) prohibido.

En este escenario, debe investigar por qué el token de SA va a expirar antes de que el cliente envía el token en el servidor:

- Normalmente, no debe establecer una hora de inicio cuando se crea una SA para un cliente a utilizar inmediatamente. Si hay diferencias pequeño reloj entre el host de generar las asociaciones de seguridad con la hora actual y el servicio de almacenamiento, es posible que el servicio de almacenamiento para recibir un SA que no es válido.
- No debe establecer una fecha de caducidad muy breve en una SA. Nuevamente, diferencias pequeño reloj entre el host de generar las asociaciones de seguridad y el servicio de almacenamiento pueden producir un SA aparentemente va a expirar antes de lo esperado.
- ¿Es el parámetro de versión de la clave de SA (por ejemplo **VP = 2015-04-05**) coincide con la versión de la biblioteca de cliente de almacenamiento está usando? Se recomienda utilizar siempre la versión más reciente de la [Biblioteca de cliente de almacenamiento](https://www.nuget.org/packages/WindowsAzure.Storage/).
- Si regenerar las claves de acceso de almacenamiento, esto puede invalidar cualquier tokens SA existentes. Esto puede ser un problema si generar tokens SA con una fecha de caducidad largos a aplicaciones cliente de caché.

Si está utilizando la biblioteca de cliente de almacenamiento para generar tokens SA, es fácil crear un token válido. Sin embargo, si está utilizando la API de REST de almacenamiento y construir los tokens SA a mano lea detenidamente el tema [Delegar acceso con una firma de acceso compartido](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>El cliente está recibiendo mensajes de HTTP 404 (no encontrado)
Si la aplicación cliente recibe un mensaje de HTTP 404 (no encontrado) desde el servidor, esto significa que el objeto que el cliente estaba intentando utilizar (por ejemplo, una entidad, una tabla, blob, contenedor o cola) no existe en el servicio de almacenamiento. Hay varias razones posibles para ello, como por ejemplo:

- [El cliente u otro proceso eliminó el objeto]
- [Un problema de autorización de firma de acceso compartido (SA)]
- [Código de JavaScript de cliente no tiene permiso para tener acceso al objeto]
- [Error de red]

#### <a name="client-previously-deleted-the-object"></a>El cliente u otro proceso eliminó el objeto
En escenarios donde el cliente está intentando leer, actualizar o eliminar datos en un servicio de almacenamiento es generalmente fácil de identificar en los registros del servidor de una operación anterior que elimina el objeto en cuestión del servicio de almacenamiento. A menudo, los datos del registro muestran que otro usuario o proceso eliminó el objeto. En el registro de registro de almacenamiento del servidor, el tipo de operación y columnas de la clave del objeto solicitado mostrar cuando un cliente elimina un objeto.

En el escenario donde un cliente está intentando insertar un objeto, puede no ser obvio por qué esto da como resultado una respuesta HTTP 404 (no encontrado) dado que el cliente es crear un nuevo objeto. Sin embargo, si el cliente es creando un blob deben poder encontrar el contenedor de blob, si el cliente es crear un mensaje, debe poder buscar una cola, y el cliente está agregando una fila debe ser puede encontrar la tabla.

Puede usar el registro de cliente desde la biblioteca de cliente de almacenamiento para obtener un conocimiento más detallada del cuando el cliente envía solicitudes específicas para el servicio de almacenamiento.

El siguiente registro de cliente generado por la biblioteca de cliente de almacenamiento ilustra el problema cuando el cliente no encuentra el contenedor para el blob que está creando. Este registro incluye detalles acerca de las siguientes operaciones de almacenamiento:

Id. de solicitud|Operación
---|---
07b26a5d-...|Método **DeleteIfExists** para eliminar el contenedor de blob. Tenga en cuenta que esta operación incluye una solicitud de **cabeza** para comprobar la existencia del contenedor.
e2d06d78...|Método de **CreateIfNotExists** para crear el contenedor de blob. Tenga en cuenta que esta operación incluye una solicitud de **cabeza** comprueba la existencia del contenedor. La **cabeza** devuelve un mensaje de 404 pero sigue.
de8b1c3c-...|Método de **UploadFromStream** para crear el blob. Se produce un error en la solicitud de **poner** con un mensaje de 404

Entradas de registro:

Id. de solicitud |  Texto de la operación
---|---
07b26a5d-...|Iniciar solicitud sincrónico a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, de 2014 03 de Jun 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|Esperando respuesta.
07b26a5d-... | Se recibió respuesta. Código de estado = 200, solicitar ID = eeead849... Contenido MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | Encabezados de respuesta procesados correctamente, seguir con el resto de la operación.
07b26a5d-... | Descargar el cuerpo de la respuesta.
07b26a5d-... | Operación finalizada correctamente.
07b26a5d-... | Iniciar solicitud sincrónico a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | Esperando respuesta.
07b26a5d-... | Se recibió respuesta. Código de estado = 202, solicitar ID = 6ab2a4cf-..., contenido MD5 = ETag =.
07b26a5d-... | Encabezados de respuesta procesados correctamente, seguir con el resto de la operación.
07b26a5d-... | Descargar el cuerpo de la respuesta.
07b26a5d-... | Operación finalizada correctamente.
e2d06d78-... | Iniciar una solicitud asincrónica a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| Esperando respuesta.
de8b1c3c-... | Iniciar solicitud sincrónico a https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... |  StringToSign = SUPERPONER... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-ms-BLOB-Type:BlockBlob.x-ms-Client-Request-ID:de8b1c3c-...x-ms-Date:Tue, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Prepararse escribir los datos de la solicitud.
e2d06d78-... | Excepción de tiempo de espera de respuesta: el servidor remoto devolvió un error: (404) no encontrado...
e2d06d78-... | Se recibió respuesta. Código de estado = 404, solicitar ID = 353ae3bc-..., contenido MD5 = ETag =.
e2d06d78-... | Encabezados de respuesta procesados correctamente, seguir con el resto de la operación.
e2d06d78-... | Descargar el cuerpo de la respuesta.
e2d06d78-... | Operación finalizada correctamente.
e2d06d78-... | Iniciar una solicitud asincrónica a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = SUPERPONER... 0...x-ms-Client-Request-ID:e2d06d78-...x-ms-Date:Tue, de 2014 03 de Jun 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | Esperando respuesta.
de8b1c3c-... | Datos de la solicitud de escritura.
de8b1c3c-... | Esperando respuesta.
e2d06d78-... | Excepción de tiempo de espera de respuesta: el servidor remoto devolvió un error: conflicto (409)...
e2d06d78-... | Se recibió respuesta. Código de estado = 409, solicitar ID = c27da20e-..., contenido MD5 = ETag =.
e2d06d78-... | Descargar el cuerpo de la respuesta de error.
de8b1c3c-... | Excepción de tiempo de espera de respuesta: el servidor remoto devolvió un error: (404) no encontrado...
de8b1c3c-... | Se recibió respuesta. Código de estado = 404, solicitar ID = 0eaeab3e-..., contenido MD5 = ETag =.
de8b1c3c-...| Excepción durante la operación: el servidor remoto devolvió un error: (404) no encontrado...
de8b1c3c-... | Directiva de reintento no permitía un reintento. Error con el servidor remoto devolvió un error: (404) no encontrado...
e2d06d78-... | Directiva de reintento no permitía un reintento. Error con el servidor remoto devolvió un error: conflicto (409)...

En este ejemplo, el registro muestra que el cliente es intercalación solicitudes desde el método de **CreateIfNotExists** (solicitud e2d06d78 del identificador...) con las solicitudes desde el método de **UploadFromStream** (de8b1c3c...); Esto ocurre porque la aplicación cliente es invocar estos métodos de forma asincrónica. Debe modificar el código asincrónico en el cliente para asegurarse de que crea el contenedor antes de intentar cargar los datos en un objeto binario en ese contenedor. Lo ideal es que, debe crear todos los contenedores con anticipación.

#### <a name="SAS-authorization-issue"></a>Un problema de autorización de firma de acceso compartido (SA)

Si la aplicación cliente intenta utilizar una clave de SA que no incluye los permisos necesarios para la operación, el servicio de almacenamiento devuelve un mensaje de HTTP 404 (no encontrado) al cliente. Al mismo tiempo, también verá un valor distinto de cero para **SASAuthorizationError** en las mediciones.

La siguiente tabla muestra un ejemplo de un mensaje de registro del servidor del archivo de registro de registro de almacenamiento:

<table>
  <tr>
    <td>Hora de inicio de la solicitud</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Tipo de operación</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Estado de la solicitud</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Código de estado HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Tipo de autenticación</td>
    <td>Asociaciones de seguridad</td>
  </tr>
  <tr>
    <td>Tipo de servicio</td>
    <td>BLOB</td>
  </tr>
  <tr>
    <td>URL de la solicitud</td>
    <td>
    https://domemaildist.BLOB.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp; sr = c&amp;amp; hueco = MiDirectiva&amp;amp; sig = XXXXX&amp;amp; versión api = 2014-02-14&amp;Ayúdanos a mejorar</td>
  </tr>
  <tr>
    <td>Encabezado de identificador de la solicitud</td>
    <td>a1f348d5-8032-4912-93EF-b393e5252a3b</td>
  </tr>
  <tr>
    <td>Id. de solicitud de cliente</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Debe investigar por qué la aplicación cliente está intentando realizar una operación para que no se han concedido permisos.

#### <a name="JavaScript-code-does-not-have-permission"></a>Código de JavaScript de cliente no tiene permiso para tener acceso al objeto

Si está utilizando a un cliente de JavaScript y el servicio de almacenamiento devuelve mensajes HTTP 404, busque los siguientes errores de JavaScript en el explorador:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] Puede usar las herramientas de desarrollo F12 en Internet Explorer para el seguimiento de los mensajes intercambiados entre el explorador y el servicio de almacenamiento para solucionar problemas de JavaScript de cliente.

Estos errores se producen porque el explorador web implementa las restricciones de seguridad de la [Directiva de mismo origen](http://www.w3.org/Security/wiki/Same_Origin_Policy) que impide que llamar a una API en un dominio distinto del dominio de de que la página proviene una página web.

Para solucionar el problema de JavaScript, puede configurar cruzada Origin recursos de uso compartido (CORS) el cliente tiene acceso al servicio de almacenamiento. Para obtener más información, vea [soporte técnico de uso compartido de recursos entre origen (CORS) para los servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

El ejemplo siguiente muestra cómo configurar el servicio de blobs para permitir que JavaScript que se ejecuta en el dominio de Contoso tener acceso a un blob en el servicio de almacenamiento de blobs:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Error de red

En algunos casos, pueden provocar paquetes de red pierden el servicio de almacenamiento devolver mensajes HTTP 404 al cliente. Por ejemplo, cuando la aplicación cliente es la eliminación de una entidad del servicio de tabla verá el cliente producir un informe de excepción de almacenamiento un "HTTP 404 (no encontrado)" mensaje de estado del servicio de la tabla. Al investigar la tabla en el servicio de almacenamiento de la tabla, verá que el servicio ha eliminado la entidad que solicita.

Los detalles de excepción en el cliente de incluyen el identificador de solicitud (7e84f12d...) asignado por el servicio de la tabla de la solicitud: puede usar esta información para localizar los detalles de la solicitud en los registros del servidor almacenamiento mediante la búsqueda en la columna **id de solicitud de encabezado** en el archivo de registro. También puede utilizar las métricas para identificar cuando errores como esta se producen y, a continuación, busque los archivos de registro basados en el tiempo de que las mediciones registran este error. Esta entrada del registro muestra que no se pudo eliminar con un mensaje de estado "Cliente otros errores HTTP (404)". La misma entrada del registro también incluye el identificador de solicitud generado por el cliente en la columna **Id. de solicitud de cliente** (813ea74f...).

El registro de servidor también incluye otra entrada con el mismo valor de **Id. de solicitud de cliente** (813ea74f...) para una operación de eliminación correcta para la misma entidad y desde el mismo cliente. Esta operación de eliminación correcta tuvo lugar muy poco antes de elimina el error solicitud.

Este escenario más probable es que el cliente de enviar una solicitud de eliminación de la entidad al servicio de tabla, que se realizó correctamente, pero no se ha recibido confirmación desde el servidor (quizás debido a un problema temporal de la red). Después, el cliente vuelve a intentar la operación (con el mismo **Id. de solicitud de cliente**), y este reintento error porque ya se eliminó la entidad.

Si este problema se produce con frecuencia, debe investigar por qué el cliente no recibe confirmaciones del servicio de la tabla. Si el problema es intermitente, debe capturar el error "HTTP (404) no encontrado" y registrar en el cliente, pero permitir que el cliente continuar.

### <a name="the-client-is-receiving-409-messages"></a>El cliente está recibiendo mensajes de HTTP 409 (conflicto)

La siguiente tabla muestra un extracto del registro del servidor de dos operaciones de cliente: **DeleteIfExists** seguido inmediatamente por **CreateIfNotExists** con el mismo nombre de contenedor de blob. Tenga en cuenta que cada resultados de la operación de cliente en dos solicitudes envían al servidor, primero una solicitud de **GetContainerProperties** para comprobar si existe el contenedor, seguido de la solicitud de **DeleteContainer** o **CreateContainer** .

Marca de tiempo|Operación|Resultado|Nombre del contenedor|Id. de solicitud de cliente
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

El código de la aplicación cliente elimina e inmediatamente vuelve a crear un contenedor de blobs con el mismo nombre: el método **CreateIfNotExists** (cliente solicitar ID bc881924-...) no se completa con el error HTTP 409 (conflicto). Cuando elimina un cliente contenedores de blobs, tablas o colas hay un breve período antes del nombre vuelva a estar disponible.

La aplicación cliente debe utilizar nombres de contenedor único al crear nuevos contenedores si el modelo de eliminar y volver a crear es comunes.

### <a name="metrics-show-low-percent-success"></a>Métricas mostrar PercentSuccess baja o las entradas del registro de análisis tienen operaciones con el estado de la transacción de ClientOtherErrors

La métrica **PercentSuccess** captura el porcentaje de las operaciones que tuvieron éxito en función de su código de estado de HTTP. Operaciones con los códigos de estado de 2XX contabilizan correctamente, mientras que las operaciones con los códigos de estado en rangos 3XX, 4XX y 5XX se cuentan como incorrecta y disminuir el valor de métrica de **PercentSucess** . En los archivos de registro de almacenamiento del servidor, estas operaciones se registran con un estado de transacción de **ClientOtherErrors**.

Es importante que tenga en cuenta que estas operaciones se han completado correctamente y, por tanto, no afectará a otras métricas como disponibilidad. Algunos ejemplos de operaciones que se ejecute correctamente pero que puede dar lugar a códigos de estado HTTP incorrectos:
- **ResourceNotFound** (No encontrado 404), por ejemplo de una solicitud GET a un blob que no existe.
- **ResouceAlreadyExists** (Conflicto 409), por ejemplo de una operación de **CreateIfNotExist** donde el recurso ya existe.
- **ConditionNotMet** (No modificado 304), por ejemplo de una operación condicional, como cuando un cliente envía un valor de **ETag** y un encabezado HTTP **Si lo ninguna coincidencia** para solicitar una imagen solo si se ha actualizado desde la última operación.

Puede encontrar una lista de códigos de error de API de REST comunes que devuelven los servicios de almacenamiento en la página de [Códigos de Error de API de REST comunes](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Métricas de capacidad mostrar un aumento inesperado en uso de la capacidad de almacenamiento


Si ve rápida, cambios inesperados en el uso de la capacidad de su cuenta de almacenamiento, puede investigar las razones por examina las métricas de disponibilidad; Por ejemplo, un aumento en el número de eliminación no solicitudes podrían producir un aumento de almacenamiento de blobs de que está utilizando como operaciones de limpieza específicas de aplicación que esperar para ser liberar espacio no funciona como se esperaba (por ejemplo, porque los tokens SA utilizados para liberar espacio han expirado).

### <a name="you-are-experiencing-unexpected-reboots"></a>Experimenta reinicio inesperado de Azure máquinas virtuales de Windows que tiene una gran cantidad de VHD adjunto

Si una máquina Virtual (VM) de Azure tiene una gran cantidad de VHD adjunto que están en la misma cuenta de almacenamiento, podría superar los objetivos de escalabilidad para una cuenta de almacenamiento individuales causando la máquina virtual no se realice correctamente. Debe comprobar las mediciones minutos para la cuenta de almacenamiento (**TotalRequests**/**TotalIngress**/**TotalEgress**) picos que supere los objetivos de escalabilidad de una cuenta de almacenamiento. Consulte la sección "[que métricas muestran un aumento de PercentThrottlingError]" para obtener ayuda en la determinación de si el límite se ha producido en su cuenta de almacenamiento.

En general, cada entrada individual o una operación de salida en un disco duro virtual de una máquina Virtual se traduce a la **Página obtener** o **Poner** operaciones en el blob de página subyacente. Por lo tanto, puede usar las IOPS estimadas en su entorno de ajustar cuántos VHD puede tiene en una cuenta de almacenamiento único basado en el comportamiento específico de la aplicación. No es recomendable tener más de 40 discos en una cuenta de almacenamiento única. Vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md) para obtener información detallada de los objetivos de escalabilidad actual para las cuentas de almacenamiento, en particular la tasa de solicitud de total y el ancho de banda total para el tipo de cuenta de almacenamiento que está utilizando.
Si se sobrepasan los objetivos de escalabilidad para su cuenta de almacenamiento, debe colocar el VHD en varias cuentas de almacenamiento diferente para reducir la actividad de cada cuenta individual.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>El problema se produce desde mediante el emulador de almacenamiento de desarrollo o de prueba

Normalmente, almacenamiento usarlo durante el desarrollo y prueba para evitar el requisito de una cuenta de almacenamiento de Azure. Los problemas comunes que pueden ocurrir cuando se usa el emulador de almacenamiento son:

- [No funciona la característica "X" en el emulador de almacenamiento]
- [Error "el valor de uno de los encabezados HTTP no está en el formato correcto" cuando el uso del almacenamiento]
- [Ejecuta el emulador almacenamiento requiere privilegios de administrador]

#### <a name="feature-X-is-not-working"></a>No funciona la característica "X" en el emulador de almacenamiento

El emulador de almacenamiento no admite todas las características de los servicios de almacenamiento de Azure, como el servicio de archivo. Para obtener más información, consulte [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md).

Para las características que no admite el emulador de almacenamiento, use el servicio de almacenamiento de Azure en la nube.

#### <a name="error-HTTP-header-not-correct-format"></a>Error "el valor de uno de los encabezados HTTP no está en el formato correcto" cuando el uso del almacenamiento

Está probando la aplicación que utilizan la biblioteca de cliente de almacenamiento en el emulador de almacenamiento local y las llamadas a método como **CreateIfNotExists** un error con el mensaje de error "el valor de uno de los encabezados HTTP no está en el formato correcto". Esto indica que la versión del emulador almacenamiento que está usando no admite la versión de la biblioteca de cliente de almacenamiento que está usando. La biblioteca de cliente de almacenamiento agrega la del encabezado **x-ms-versión** a todas las solicitudes hace. Si el emulador de almacenamiento no reconoce el valor en el encabezado de **versión de ms x** , rechaza la solicitud.

Puede usar los registros del cliente de la biblioteca de almacenamiento para ver el valor del **encabezado x-ms-versión** de envío. También puede ver el valor del **encabezado x-ms-versión** si usa Fiddler para trazar las solicitudes desde la aplicación cliente.

Este escenario suele ocurre si instala y usar la versión más reciente de la biblioteca de cliente de almacenamiento sin actualizar el emulador de almacenamiento. Debe instalar la última versión del emulador almacenamiento o usar el almacenamiento de nube en lugar del emulador para desarrollo y pruebas.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Ejecuta el emulador almacenamiento requiere privilegios de administrador

Cuando se ejecuta el emulador de almacenamiento, se le pedirá las credenciales de administrador. Esto sólo se produce cuando se están iniciando el emulador de almacenamiento por primera vez. Después de haber inicializado emulador de almacenamiento, no necesita privilegios de administrador para ejecutar de nuevo.

Para obtener más información, consulte [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md). Tenga en cuenta que también se puede inicializar el emulador almacenamiento en Visual Studio, que también requieren privilegios de administrador.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Se produzcan problemas al instalar el SDK de Azure para .NET

Cuando intenta instalar el SDK, se produce un error intentando instalar el emulador de almacenamiento en su equipo local. El registro de instalación contiene uno de los siguientes mensajes:

- CAQuietExec: Error: no se puede obtener acceso a la instancia de SQL
- CAQuietExec: Error: no se puede crear la base de datos

La causa es un problema con la instalación de LocalDB existente. De forma predeterminada, el emulador de almacenamiento utiliza LocalDB para conservar los datos cuando simula los servicios de almacenamiento de Azure. Puede restablecer su instancia LocalDB, ejecute los comandos siguientes en una ventana del símbolo del sistema antes de intentar instalar el SDK.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

El comando **Eliminar** quita los archivos de base de datos antiguos de las instalaciones anteriores de emulador de almacenamiento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Tiene un problema distinto con un servicio de almacenamiento

Si no incluye las secciones anteriores para solucionar el problema que está teniendo con un servicio de almacenamiento, debe adoptar el método siguiente para diagnosticar y solucionar el problema.

- Comprobar su métricas para ver si hay algún cambio de su comportamiento esperado de línea base. Desde la métrica, es posible que pueda determinar si el problema es temporal o permanente y las operaciones de almacenamiento afecta el problema.
- Puede usar la información de métricas para ayudarle a buscar los datos de registro del servidor para obtener más información sobre los errores que se producen. Esta información puede ayudarle a resolver el problema.
- Si la información en los registros del servidor no es suficiente para solucionar el problema correctamente, puede usar los registros de cliente de la biblioteca de cliente de almacenamiento para investigar el comportamiento de la aplicación cliente y herramientas como Fiddler, Wireshark y analizador de mensaje de Microsoft para investigar la red.

Para obtener más información sobre el uso de Fiddler, vea "[Apéndice 1: utilizar Fiddler para capturar el tráfico HTTP y HTTPS]."

Para obtener más información sobre el uso de Wireshark, vea "[Apéndice 2: usar Wireshark para capturar el tráfico de red]."

Para obtener más información sobre cómo usar el analizador de mensaje de Microsoft, consulte "[Apéndice 3: usando el analizador de Microsoft Message para capturar el tráfico de red]."

## <a name="appendices"></a>Apéndices

Los apéndices describen varias herramientas que pueden resultar útiles cuando se diagnosticar y solucionar problemas con el almacenamiento de Azure (y otros servicios). Estas herramientas no forman parte de almacenamiento de Azure y algunos son productos de terceros. Por lo tanto, no se tratan las herramientas descritas en estos anexos cualquier acuerdo de soporte, es posible que tenga con Microsoft Azure o almacenamiento de Azure y, por tanto, como parte del proceso de evaluación debe examinar las licencias y opciones de soporte disponibles por parte de los proveedores de estas herramientas.

### <a name="appendix-1"></a>Apéndice 1: Utilizar Fiddler para capturar el tráfico HTTP y HTTPS

[Fiddler](http://www.telerik.com/fiddler) es una herramienta muy útil para analizar el tráfico HTTP y HTTPS entre la aplicación cliente y el servicio de almacenamiento de Azure que está utilizando.

> [AZURE.NOTE] Fiddler puede descodificar tráfico HTTPS; Lea la documentación de Fiddler detenidamente para comprender cómo funciona y comprender las implicaciones de seguridad.

Este apéndice proporciona un breve tutorial sobre cómo configurar Fiddler para capturar el tráfico entre el equipo local donde ha instalado Fiddler y los servicios de almacenamiento de Azure.

Una vez haya iniciado Fiddler, comenzará capturar el tráfico HTTP y HTTPS en el equipo local. Estos son algunos comandos útiles para controlar Fiddler:

- Detener e iniciar la captura de tráfico. En el menú principal, vaya a **archivo** y, a continuación, haga clic en **Capturar tráfico** para activar y desactivar la captura.
- Guardar datos de tráfico capturado. En el menú principal, vaya a **archivo**, haga clic en **Guardar**y, a continuación, haga clic en **Todas las sesiones**: Esto le permite guardar el tráfico en un archivo de sesión. Puede volver a cargar un archivo de sesión para el análisis o enviarlo si se solicita soporte técnico de Microsoft.

Para limitar la cantidad de tráfico que Fiddler captura, puede usar filtros que se configura en la ficha **filtros** . La siguiente captura de pantalla muestra un filtro que captura sólo el tráfico enviado al extremo de almacenamiento **contosoemaildist.table.core.windows.net** :

![][5]

### <a name="appendix-2"></a>Apéndice 2: Usar Wireshark para capturar el tráfico de red

[Wireshark](http://www.wireshark.org/) es un analizador de protocolos de red que le permite ver información detallada de paquete para una amplia gama de protocolos de red.

El procedimiento siguiente muestra cómo capturar información detallada de paquete para el tráfico desde el equipo local que se instaló Wireshark con el servicio de la tabla en la cuenta de almacenamiento de Azure.

1.  Inicie Wireshark en su equipo local.
2.  En la sección **Iniciar** , seleccione la interfaz de red local o interfaces que están conectados a internet.
3.  Haga clic en **Opciones de captura**.
4.  Agregar un filtro en el cuadro de texto del **Filtro de captura** . Por ejemplo, **host contosoemaildist.table.core.windows.net** configurará Wireshark para capturar exclusivamente los paquetes enviados a o desde el extremo de servicio de tabla en la cuenta de almacenamiento de **contosoemaildist** . Consulte la [lista completa de filtros capturar](http://wiki.wireshark.org/CaptureFilters).

    ![][6]

5.  Haga clic en **Inicio**. Wireshark ahora capturará todos los paquetes de envío a o desde el extremo de servicio de tabla como usar la aplicación cliente en el equipo local.
6.  Cuando haya terminado, en el menú principal, haga clic en **capturar** y, a continuación, en **Detener**.
7.  Para guardar los datos capturados en un archivo de captura en Wireshark, en el menú principal, haga clic en **archivo** y, a continuación, en **Guardar**.

WireShark resaltará los errores que existen en la ventana de **packetlist** . También puede usar la ventana de **Información de experto** (haga clic en **analizar**y, a continuación, **Información de experto**) para ver un resumen de errores y advertencias.

![][7]

También puede elegir ver los datos TCP tal como ve en el nivel de aplicación haciendo doble clic en los datos TCP y seleccionando **siga flujo TCP**. Esto es especialmente útil si genera la descarga sin un filtro de captura. Para obtener más información, consulte [siguiente secuencias TCP] (http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [AZURE.NOTE] Para obtener más información sobre el uso de Wireshark, consulte la [Guía del usuario de Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).

### <a name="appendix-3"></a>Apéndice 3: Usando el analizador de mensaje de Microsoft para capturar el tráfico de red

Puede utilizar el analizador de mensaje de Microsoft para capturar el tráfico HTTP y HTTPS de manera similar a Fiddler y capturar el tráfico de red de manera similar a Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurar una sesión de seguimiento de web con el analizador de mensaje de Microsoft

Para configurar una sesión de seguimiento de web para el tráfico HTTP y HTTPS con el analizador de mensaje de Microsoft, ejecute la aplicación analizador de mensaje de Microsoft y, a continuación, en el menú **archivo** , haga clic en **Captura/seguimiento**. En la lista de escenarios de traza disponibles, seleccione **Web Proxy**. A continuación, en el panel de **Configuración del escenario de seguimiento** , en el cuadro de texto **HostnameFilter** , agregue los nombres de los extremos de almacenamiento (puede buscar estos nombres en el [Portal de Azure](https://portal.azure.com)). Por ejemplo, si el nombre de su cuenta de almacenamiento de Azure es **contosodata**, debe agregar el siguiente en el cuadro de texto **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Un carácter de espacio separa los nombres de host.

Cuando esté listo para iniciar la recopilación de datos de seguimiento, haga clic en el botón **Iniciar con** .

Para obtener más información sobre el seguimiento del analizador de mensaje de Microsoft **Proxy Web** , vea [El proveedor de servicios de Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

El seguimiento de **Proxy Web** integrado en el analizador de mensaje de Microsoft se basa en Fiddler; se puede capturar el tráfico de cliente HTTPS y aparecerá sin cifrar mensajes HTTPS. El seguimiento de **Proxy Web** funciona mediante la configuración de proxy local para todo el tráfico HTTP y HTTPS que le proporciona acceso a los mensajes sin cifrar.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticar problemas de red con el analizador de mensaje de Microsoft

Además de usar el seguimiento del analizador de mensaje de Microsoft **Web Proxy** para capturar los detalles del tráfico HTTP/HTTPs entre la aplicación cliente y el servicio de almacenamiento, también puede usar el seguimiento de **Nivel de vínculo Local** integrado para capturar información de paquetes de red. Esto permite capturar datos de forma similar a la que se puede capturar con Wireshark y diagnosticar problemas de red, como los paquetes perdidos.

La siguiente captura de pantalla muestra un ejemplo traza de **Nivel de vínculo Local** con algunos mensajes **informativos** en la columna **DiagnosisTypes** . Al hacer clic en un icono en la columna **DiagnosisTypes** muestra los detalles del mensaje. En este ejemplo, el servidor retransmitidas mensaje #305 porque no ha recibido confirmación desde el cliente:

![][9]

Cuando se crea la sesión de seguimiento en el analizador de mensaje de Microsoft, puede especificar filtros para reducir la cantidad de ruido en el seguimiento. **Capturar y hacer un seguimiento** en la página donde puede definir el seguimiento, en el vínculo **Configurar** junto a **Microsoft-Windows-NDIS-PacketCapture**. La siguiente captura de pantalla muestra una configuración que filtra el tráfico TCP para las direcciones IP de tres servicios de almacenamiento:

![][10]

Para obtener más información sobre el seguimiento de nivel de vínculo Local de analizador de mensajes de Microsoft, consulte [Microsoft-PEF-NDIS-PacketCapture proveedor](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Apéndice 4: Usar Excel para ver métricas y registro de datos

Muchas de las herramientas que pueda descargar los datos de métrica de almacenamiento de almacenamiento de tablas de Azure en un formato delimitado que facilita la carga los datos en Excel para la visualización y análisis. Datos de registro de almacenamiento de almacenamiento de blobs de Windows Azure ya están en un formato delimitado que puede cargar en Excel. Sin embargo, debe agregar encabezados de columna apropiados según la información de [Formato de registro de análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh343259.aspx) y el [Esquema de la tabla de métrica de almacenamiento análisis](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Para importar los datos de registro de almacenamiento en Excel después de descargar el almacenamiento de blobs:

- En el menú **datos** , haga clic en **Desde texto**.
- Busque el archivo de registro que desea ver y haga clic en **Importar**.
- En el paso 1 del **Asistente para importar texto**, seleccione **delimitado**.

En el paso 1 del **Asistente para importar texto**, seleccione el **punto y coma** como delimitador único y elija comillas dobles como **calificador de texto**. A continuación, haga clic en **Finalizar** y elija dónde desea colocar los datos en el libro.

### <a name="appendix-5"></a>Apéndice 5: Supervisión con recomendaciones de aplicación para Visual Studio Team Services

También puede usar la característica de aplicación perspectivas para Visual Studio Team Services como parte de la supervisión del rendimiento y disponibilidad. Esta herramienta puede:

- Asegúrese de que el servicio web está disponible y capacidad de respuesta. Si su aplicación es un sitio web o una aplicación de dispositivo que usa un servicio web, puede probar la dirección URL de cada pocos minutos de ubicaciones en todo el mundo y le avise si hay un problema.
- Rápidamente diagnosticar problemas de rendimiento o excepciones en el servicio web. Averigüe si se expandirse CPU u otros recursos, obtenga seguimientos de la pila de excepciones y buscar fácilmente entre seguimientos de registro. Si el rendimiento de la aplicación por debajo de los límites aceptables, podemos enviarle un mensaje de correo. Puede supervisar los servicios web .NET y Java.

Puede encontrar más información en [¿Qué es la aplicación perspectivas?](../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introducción]: #introduction
[¿Cómo se organiza esta guía]: #how-this-guide-is-organized

[Supervisar el servicio de almacenamiento]: #monitoring-your-storage-service
[Supervisar el estado del servicio]: #monitoring-service-health
[La capacidad de supervisión]: #monitoring-capacity
[Supervisar la disponibilidad]: #monitoring-availability
[Supervisar el rendimiento]: #monitoring-performance

[Diagnosticar problemas de almacenamiento]: #diagnosing-storage-issues
[Problemas de servicio de estado]: #service-health-issues
[Problemas de rendimiento]: #performance-issues
[Diagnóstico de errores]: #diagnosing-errors
[Problemas de emulador de almacenamiento]: #storage-emulator-issues
[Herramientas de registro de almacenamiento]: #storage-logging-tools
[Usar herramientas de registro de red]: #using-network-logging-tools

[Seguimiento de llevar a cabo]: #end-to-end-tracing
[Correlación de datos de registro]: #correlating-log-data
[Id. de solicitud de cliente]: #client-request-id
[Id. de solicitud de servidor]: #server-request-id
[Marcas de tiempo]: #timestamps

[Guía de solución de problemas]: #troubleshooting-guidance
[Métricas mostrar AverageE2ELatency alta y baja AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Métricas mostrar AverageE2ELatency baja y baja AverageServerLatency pero el cliente está experimentando latencia alta]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Métricas mostrar AverageServerLatency alta]: #metrics-show-high-AverageServerLatency
[Experimenta retrasos inesperados en la entrega de mensajes de una cola]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Métricas mostrar un aumento en PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumentar transitoria en PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumentar permanente PercentThrottlingError error]: #permanent-increase-in-PercentThrottlingError
[Métricas mostrar un aumento en PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Métricas mostrar un aumento en PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[El cliente está recibiendo mensajes de HTTP 403 (prohibido)]: #the-client-is-receiving-403-messages
[El cliente está recibiendo mensajes de HTTP 404 (no encontrado)]: #the-client-is-receiving-404-messages
[El cliente u otro proceso eliminó el objeto]: #client-previously-deleted-the-object
[Un problema de autorización de firma de acceso compartido (SA)]: #SAS-authorization-issue
[Código de JavaScript de cliente no tiene permiso para tener acceso al objeto]: #JavaScript-code-does-not-have-permission
[Error de red]: #network-failure
[El cliente está recibiendo mensajes de HTTP 409 (conflicto)]: #the-client-is-receiving-409-messages

[Métricas mostrar PercentSuccess baja o las entradas del registro de análisis tienen operaciones con el estado de la transacción de ClientOtherErrors]: #metrics-show-low-percent-success
[Métricas de capacidad mostrar un aumento inesperado en uso de la capacidad de almacenamiento]: #capacity-metrics-show-an-unexpected-increase
[Experimenta inesperado reinicio de máquinas virtuales que tiene una gran cantidad de VHD adjunto]: #you-are-experiencing-unexpected-reboots
[El problema se produce desde mediante el emulador de almacenamiento de desarrollo o de prueba]: #your-issue-arises-from-using-the-storage-emulator
[No funciona la característica "X" en el emulador de almacenamiento]: #feature-X-is-not-working
[Error "el valor de uno de los encabezados HTTP no está en el formato correcto" cuando el uso del almacenamiento]: #error-HTTP-header-not-correct-format
[Ejecuta el emulador almacenamiento requiere privilegios de administrador]: #storage-emulator-requires-administrative-privileges
[Se produzcan problemas al instalar el SDK de Azure para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Tiene un problema distinto con un servicio de almacenamiento]: #you-have-a-different-issue-with-a-storage-service

[Apéndices]: #appendices
[Apéndice 1: Utilizar Fiddler para capturar el tráfico HTTP y HTTPS]: #appendix-1
[Apéndice 2: Usar Wireshark para capturar el tráfico de red]: #appendix-2
[Apéndice 3: Usando el analizador de mensaje de Microsoft para capturar el tráfico de red]: #appendix-3
[Apéndice 4: Usar Excel para ver métricas y registro de datos]: #appendix-4
[Apéndice 5: Supervisión con recomendaciones de aplicación para Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
