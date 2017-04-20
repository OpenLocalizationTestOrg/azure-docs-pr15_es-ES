<properties 
   pageTitle="Notas de la versión de Azure SDK para .NET 2.6" 
   description="Notas de la versión de Azure SDK para .NET 2.6" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Notas de la versión de Azure SDK para .NET 2.6

Este documento contiene las notas para el SDK de Azure para .NET 2.6 versión. 

Con Azure SDK 2.6 puede desarrollar aplicaciones de servicio de nube (PaaS) identificación .NET 4.5.2 o 4.6 .NET siempre que instalar manualmente .NET Framework de destino en el rol de servicio de nube. Vea [instalar .NET en una función de servicio de nube](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Actualizaciones de Bus de servicio

- Evento Hubs: 

    - Ahora permite controlar el acceso destino al enviar eventos al exponer extremo de publisher adicionales para Hubs de evento.
    - Estabilidad adicional y mejora agregado a la característica de Hubs de evento.
    - Agregar compatibilidad del protocolo Amqp sobre WebSocket mensajería y Hubs de evento.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>Herramientas de HDInsight para actualizaciones de Visual Studio

- **Mejora de IntelliSense**: sugerencia de metadatos remoto

    HDInsight Tools para Visual Studio admite ahora al obtener metadatos remoto cuando edita la secuencia de comandos de la sección. Por ejemplo, puede escribir * *Seleccione* FROM** y se muestran todos los nombres de tabla. Además, se muestran los nombres de columna después de especificar una tabla.

- **Soporte técnico de emulador HDInsight**

    Ahora HDInsight Tools para Visual Studio admite la conexión a HDInsight emulador, por lo que puede desarrollar las secuencias de comandos de la sección localmente sin introducir los costes, a continuación, ejecute las secuencias de comandos en los clústeres HDInsight. 

    Para obtener más información, consulte [este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **HDInsight Tools para Visual Studio de soporte técnico para clústeres de Hadoop genéricos** (Vista previa)

    Ahora HDInsight Tools para Visual Studio admiten clústeres Hadoop genéricos, por lo que puede usar HDInsight Tools para Visual Studio para hacer lo siguiente:

    - conectar con el clúster 
    - Escribir consulta subárbol con la compatibilidad de IntelliSense y Autocompletar mejorada 
    - ver todas las tareas en el clúster con una interfaz de usuario intuitiva. 

    Para obtener más información, consulte [este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##<a name="in-role-cache-updates"></a>Actualizaciones de la caché de funciones

- **En la función de caché** se actualizó para usar **Microsoft Azure almacenamiento SDK** versión 4.3. Hasta ahora, la **Caché de funciones** estaba utilizando el SDK de almacenamiento de Azure versión 1.7.

    Los clientes usando Azure SDK 2.5 o a continuación deben actualizar a Azure SDK 2.6 y mover a la nueva versión del SDK de almacenamiento de Azure. 

    En este momento está programado Azure almacenamiento versión 2011-08-18 quitarse el 1 de agosto de 2016. Las migraciones de caché de en función de Azure SDK 2.5 o inferior a 2.6 debe completar en este momento. Para obtener más información sobre la jubilación de almacenamiento de Azure versión 2011-08-18, consulte [actualización de la eliminación de la versión de servicio de Microsoft Azure almacenamiento: extensión a 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Estamos estamos presentando la jubilación 30 de noviembre de 2016, para el servicio de caché administrada de Azure y Azure en la función de caché. Se recomienda migrar a Azure Redis caché en preparación para esta retirada. Para obtener más información sobre las fechas y la Guía de migración, consulte [oferta de caché de Azure que es adecuada para mí?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Herramientas de servicio de aplicaciones de Azure

Los siguientes elementos se han actualizado en la versión de Azure SDK 2.6.

- Publicación de Azure mejorada para incluir las aplicaciones de la API de Azure como un destino de distribución.
- Aplicaciones de API de aprovisionamiento funcionalidad para permitir que los usuarios con la funcionalidad de creación y el suministro de API App.
- Explorador de servidores cambia para reflejar nuevo nodo de servicio de aplicaciones, con las aplicaciones Web, móvil y API agrupadas por grupo de recursos.
- Agregar gesto de cliente de aplicación de Azure API agregado a la mayoría proyectos C# que permiten la generación automática de aplicaciones de API habilitado Swagger ejecutando de suscripción de Azure de un usuario.
- Herramientas de aplicaciones API y nodos de servicio de aplicación en el Explorador de servidor sólo están disponibles en Visual Studio de 2013. 

##<a name="azure-resource-manager-tools-updates"></a>Actualizaciones de herramientas de administrador de recursos de Azure

Las herramientas del Administrador de recursos de Azure se han actualizado para incluir plantillas de máquinas virtuales, redes y almacenamiento. La experiencia de edición de JSON se ha actualizado para incluir una nueva vista de esquema para las plantillas y la capacidad de editar las plantillas de fragmentos JSON. Plantillas implementadas desde Visual Studio usan un script de PowerShell proporcionado con el proyecto, por lo que se usarán los cambios realizados en la secuencia de comandos de Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Mejoras de diagnóstico para los servicios de nube

Azure SDK 2.6 vuelve a soporte técnico para recopilar registros de diagnóstico en el emulador cálculo Azure y transferirlos a almacenamiento de desarrollo. Registros de diagnósticos de cualquier (incluido el seguimiento de la aplicación registros, el seguimiento de registros de Windows (ETW), contadores de rendimiento, infraestructura de registros de eventos de windows y de los registros de eventos) generados cuando se ejecuta la aplicación en el emulador se puede transferir a almacenamiento de desarrollo para comprobar que el registro de diagnóstico está trabajando en el equipo local. 

Ahora puede especificarse la cuenta de almacenamiento de información de diagnóstico en el archivo de configuración (.cscfg) de servicio facilitar la utilización de cuentas de almacenamiento de diagnósticos diferentes para diferentes entornos. Existen algunas diferencias importantes entre cómo trabajado 2,4 SDK de Azure y Azure SDK 2.6 la cadena de conexión. Para obtener más información sobre cómo usar la conexión de almacenamiento de información de diagnóstico cadena y cómo afecta a los proyectos vea [Configuración de diagnósticos de servicios de nube de Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Cambios importantes

###<a name="azure-resource-manager-tools"></a>Herramientas de administrador de recursos de Azure 

- Se ha cambiado el tipo de proyecto de **Proyectos de implementación de nube** disponible en el 2,5 de Azure SDK al **Grupo de recursos de Azure**.
- Tipo de **Proyectos de implementación de nube** de proyectos creados en la 2.5 SDK Azure puede usarse en 2.6 pero se producirá un error en la implementación de la plantilla de Visual Studio. Sin embargo, implementar con la secuencia de comandos de PowerShell ¿seguirán funcionando como antes.  Para obtener información sobre cómo usar **Los proyectos de implementación de nube** en 2.6 lea esta [entrada](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Problemas conocidos

- Recopilar registros de diagnóstico en el emulador requiere un sistema operativo de 64 bits. No se recopilará registros de diagnósticos cuando se ejecuta en sistemas operativos de 32 bits. Esto no afecta a cualquier otra funcionalidad emulador. 

- Azure SDK 2.6 publicado en 29/4/2015 tenía dos cuestiones: 

    - No se pudo cargar la aplicación universal de 2015 de Visual Studio al Azure SDK 2.6 instalado en el equipo.
    - Depurar un proyecto de servicio de nube no en 2013 de Visual Studio y de 2015 Visual Studio donde Visual Studio deja de responder y se bloquea mientras se muestra un cuadro de diálogo con el mensaje "Configuración de diagnósticos emulador".
    
    Se publicó una actualización de Azure SDK 2.6 en 18/5/2015. La versión actualizada está 2.6.30508.1601; contiene correcciones para dos problemas descritos anteriormente. Puede identificar la compilación del SDK desde el Panel de Control -> programas y características -> herramientas de Microsoft Azure de Microsoft Visual Studio 2013-v 2.6. La columna versión mostrará el número de compilación.

    Si aún se encuentra en los problemas anteriores, instalar la última versión de Azure 2.6 SDK de [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Vea también

[Información de retirada de Azure SDK para .NET y las API y soporte técnico](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
