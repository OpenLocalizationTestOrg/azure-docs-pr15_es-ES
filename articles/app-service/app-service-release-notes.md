<properties 
   pageTitle="Notas de la versión de Azure SDK para .NET 2.5.1" 
   description="Notas de la versión de Azure SDK para .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Notas de la versión de Azure SDK para .NET 2.5.1

Este documento contiene las notas para el SDK de Azure para .NET 2.5.1 versión. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Notas de la versión de Azure SDK para .NET 2.5.1

Los siguientes son nuevas características y actualizaciones en el SDK de Azure para .NET 2.5.1.

- Nuevo features\scenarios relacionados con **Las extensiones de herramientas de Web**. 

    - Sitios Web Azure se cambió el nombre a la aplicación de servicio de Azure. Para obtener más información, vea [servicio de Azure de aplicaciones y servicios de Azure existentes](app-service-changes-existing-services.md).
    - Se ha agregado Azure soporte de aplicaciones de la API (Preview) para que los clientes pueden publicar proyectos ASP.NET como API de aplicaciones y, a continuación, use el agregar > proyectos gesto de Azure API aplicación cliente en C# para generar código basado en la estructura de implementada API App. 
    - El nodo de sitios Web en el Explorador de servidor está desusado en lugar de nodo de servicio de la aplicación de Azure, que incluye compatibilidad para la agrupación de aplicaciones de la API de Azure, aplicaciones móviles y aplicaciones Web basadas en grupos de recursos.
    - Se ha agregado Azure soporte de aplicaciones de Mobile (Preview) para que los clientes puedan crear nuevos proyectos de aplicaciones móviles, agregar controladores de aplicaciones móviles, publicar los proyectos y depurar aplicaciones de forma remota.
    - Agregar > gesto de la aplicación cliente de Azure API admite archivos locales en Swagger JSON, para los desarrolladores de Web API pueden utilizar NuGets de terceros como Swashbuckle para generar Swagger o crear de forma manual. De este modo, los desarrolladores de cliente pueden usar las características de generación de código para consumir cualquier extremo Swagger en proyectos C#. 
    - Cuadros de diálogo de publicación de App API y Web App se han mejorado para admitir el concepto de Azure Portal de agrupación de recursos y selección y creación de grupos de recursos de Azure y planes de servicio de aplicación están representados en el nuevo diálogo de aprovisionamiento de Web App y API App. 
    - Azure nodos del explorador de servidores de aplicación API proporcionan vínculos al vínculo profundo de aplicaciones de la API en el Portal de Azure, así como otras características como transmisión de registro y depuración remota.

    Problemas conocidos y limitaciones actuales en Azure SDK .NET 2.5.1 [esta](app-service-release-notes.md#known_issues_2_5_1) sección siguiente.


- Nuevo features\scenarios relacionada a **HDInsight de herramientas** en Visual Studio se habilitan en esta versión. 
    - Validación locales de secuencias de comandos de sección. Haga clic en el botón de secuencia de comandos de validar en la barra de herramientas para ver si hay errores en la secuencia de comandos. 
    - Depuración de trabajos de subárbol mejorada. Ahora puede depurar subárbol trabajos accediendo a registros de hilo en Visual Studio. Si su aplicación tiene problemas de rendimiento, investigando hilo registros le proporcionará información útil...
    - (Public Preview) Finalización automática de palabra clave y compatibilidad con IntelliSense para subárbol. Para ayudar a crear las secuencias de comandos de la sección, HDInsight Tools para Visual Studio agrega la finalización automática de palabra clave y compatibilidad con IntelliSense para subárbol.
    - Soporte de tormenta. Ahora puede usar HDInsight Tools para Visual Studio para desarrollar tormenta topologías/Spouts/pernos en C#. A continuación, puede enviar la topología desarrollada a un clúster de tormenta y ver el estado de rayo de topología/pitorro. Puede usar los registros de sistema y de cliente para solucionar los topologías, pernos/Spouts tormenta. También puede usar los activos de JAVA existentes de tormenta en HDInsight.
    
    Para obtener más información, vea [Introducción al uso HDInsight Hadoop Tools para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Azure SDK para .NET 2.5.1 problemas conocidos y limitaciones

- Aplicaciones de Azure API está visible como un destino de implementación de aplicaciones móviles. Aplicaciones de Web debe ser único destino para las aplicaciones móviles hasta una versión posterior. 
- Aprovisionamiento de aplicación de la API de Azure puede producir éxito pero forma intermitente no se puede actualizar el progreso en la ventana de actividad de servicio de aplicación de Azure. Solución alternativa es comprobar el estado de la nueva aplicación API de Azure en el Portal de Azure. 
- Archivo > Nuevo proyecto > API App > experiencia F5 genera un error HTTP porque no hay ningún default/index.html. Solución consiste en Examinar manualmente a la dirección URL de /api/values. 
- De forma intermitente, los iconos del explorador de servidores aparecen planas. Reiniciar VS resuelve este problema. 
- Si se produce una excepción durante el aprovisionamiento de Web App o API App (como excedida errores o nombre de puerta de enlace de aplicación de la API de Azure duplicado), los errores de mostrarán texto sin formato JSON. 
- Problemas de creación de proyectos intermitentes cuando esté activada la información de la aplicación en el momento de creación del proyecto.
- En ocasiones, el código de la aplicación cliente de Azure API generado faltan los espacios de nombres, deben incluirse manualmente (o importados automáticamente mediante indicaciones de Visual Studio) para que poder compilar código. 
- Proyectos de la aplicación móvil se deben publicar en aplicaciones web, pero debe elegir un sitio creado como una aplicación móvil en el Portal de Azure como proyectos de aplicación Mobile requieren una base de datos. 
- La página de inicio para las aplicaciones móviles utiliza el término "servicio móvil" en lugar de "aplicaciones móviles" 
- Creación de proyectos de aplicación Mobile puede tardar hasta un minuto para crear. 
- Durante la API App un error de aprovisionamiento (en algunos casos) vuelve desde la API de Azure que refleja que los permisos no pueden establecerse correctamente, mientras que el App API se ha creado correctamente y esté listo para su uso. Puede establecer manualmente los permisos con el Portal de Azure.
- Perspectivas de aplicación no se admite en plantillas de App API y aplicación móvil.
- Proyectos de API App no se puede utilizar junto con los proyectos de servicio de nube.
- Plantillas de proyectos de API App solo están disponibles en C#.
- Consumo de API App a través del menú contextual ""Agregar Azure API aplicación cliente solo es compatible con C#.

 
