
<properties 
   pageTitle="Notas de la versión de Azure SDK para .NET 2.7 y .NET 2.7.1" 
   description="Notas de la versión de Azure SDK para .NET 2.7 y .NET 2.7.1" 
   services="app-service\web" 
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

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notas de la versión de Azure SDK para .NET 2.7 y .NET 2.7.1

##<a name="overview"></a>Información general

Este documento contiene las notas para el SDK de Azure para .NET 2.7 versión. 

El documento contiene también las notas para el SDK de Azure para .NET 2.7.1 versión.

Azure 2.7 SDK solo es compatible con 2015 de Visual Studio y Visual Studio de 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) es que la última compatibles SDK para Visual Studio 2012.

Para obtener información detallada acerca de esta versión, consulte [publicar el anuncio de Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) y [Publicar anuncio de Azure SDK 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

##<a name="azure-sdk-for-net-27"></a>Azure SDK para .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Inicie sesión en mejoras de Visual Studio de 2015

Azure 2.7 SDK 2015 de Visual Studio es compatible con las nuevas características de administración de identidades en Visual Studio de 2015.  Esto incluye compatibilidad con cuentas de acceso a Azure a través de Control de acceso en función de roles, proveedores de soluciones de la nube, DreamSpark y otros tipos de cuenta y su suscripción.

Las mejoras de inicio de sesión incluidas con Azure SDK 2.7 solo están disponibles en Visual Studio de 2015. Soporte de 2013 de Visual Studio se incluye en el SDK de Azure 2.7.1.


###<a name="mobile-sdk"></a>SDK móvil

Actualizar plantillas de **Aplicaciones móviles** para reflejar el proceso de [paquete NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) y el programa de instalación más reciente.

###<a name="service-bus"></a>Bus de servicio 

Mejoras y correcciones de errores generales. Para obtener información detallada sobre las actualizaciones y las características, consulte las notas de la última [NuGet Bus de servicio](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

###<a name="hdinsight-tools"></a>Herramientas de HDInsight 

En esta versión se han realizado las siguientes actualizaciones. Estas actualizaciones se encuentran en la vista previa. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Gráficos de subárbol para subárbol de trabajos Tez
- Compatibilidad completa de IntelliSense de DML subárbol
- Compatibilidad con plantillas de cerdo
- Plantillas de tormenta para los servicios de Azure

####<a name="breaking-changes"></a>Cambios importantes

- Proyecto de **tormenta** antiguo debe actualizarse cuando se usa esta versión de las herramientas. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Ya no se admite Visual Studio Web Express. Para obtener más información, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Herramientas de servicio de aplicaciones de Azure

En esta versión se han realizado las siguientes actualizaciones en las extensiones de herramientas de Web. Para obtener más información, consulte [este](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

- Soporte técnico para las cuentas de DreamSpark agregado
- Completo de los cambios realizados en las nuevas API de administración de recursos de Azure de soporte técnico y herramientas de Azure
- Soporte técnico para la aplicación de servicio de Azure se agrega al [Explorador de nube](#cloud_explorer)

####<a name="known-issues"></a>Problemas conocidos

Nodos de franja de implementación de aplicación Web no aparecen en el nodo ranuras en Explorador de servidores y los nodos secundarios de Web App implementación franja no se cargan en el Explorador de nube. Este problema se ha resuelto y preparado para la próxima versión SDK. 


###<a name="cloud_explorer"></a>Explorador de nube de Visual Studio de 2015

Azure 2.7 SDK incluye Explorer nube de Visual Studio de 2015 que le permite ver los recursos de Azure, revise sus propiedades y realizar acciones clave programador desde dentro de Visual Studio. 

Explorer nube admite lo siguiente:

- Vistas de grupo de recursos y tipo de recurso de los recursos de Azure 
- Buscar recursos por nombre (disponible en la vista de tipo de recurso)
- Compatibilidad con las suscripciones y recursos que tengan el rol según Access Control (RBAC) aplicado 
- Panel de acción integrado que muestra centrados programador acciones específicas de recursos seleccionados. Por ejemplo: adjuntar depurador remoto para máquinas virtuales creadas con la pila de administrador de recursos, ver los datos de diagnóstico para etcetera máquinas virtuales de Windows.
- Panel de propiedades integrado que muestra las propiedades programador centrados normalmente necesarias durante las pruebas y desarrollo 
- Cambio rápido de la cuenta para usar al enumerar los recursos (utilice el comando configuración de barra de herramientas) 
- Filtrado de suscripciones usar al enumerar los recursos (utilice el comando configuración de barra de herramientas) 
- Vínculos de profundidad en el Portal de Azure para administración de recursos y grupos de recursos 
 
 
###<a name="azure-resource-manager-tools"></a>Herramientas de administrador de recursos de Azure 

Las herramientas de administrador de recursos de Azure se han actualizado para trabajar con los nuevos tipos de suscripción y Control de acceso en función de función (RBAC).  Estos cambios incluye la capacidad de usar nuevas cuentas de almacenamiento, además de almacenamiento clásica, almacenar artefactos durante la implementación.  

Si está usando un proyecto del grupo de recursos de Azure desde una versión anterior de SDK con 2.7 SDK, un nuevo script de implementación es necesaria para implementar con una nueva cuenta de almacenamiento en lugar de almacenamiento clásico.  Se le pedirá antes de realizar cambios en el proyecto para agregar la nueva secuencia de comandos.  Se cambió el nombre de la secuencia de comandos antigua y debe realizar manualmente las modificaciones a la secuencia de comandos nuevo.
 
 
###<a name="storage-explorer-tools"></a>Herramientas del explorador de almacenamiento 

- Posibilidad de ver BLOB anexar. Más información en [esta entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Posibilidad de ver las cuentas de almacenamiento Premium a través del explorador de servidores. Explorador de servidores solo mostrará blobs de página para las cuentas de almacenamiento premium como son el único tipo admitido para las cuentas de almacenamiento premium.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Datos de Azure fábrica Tools para Visual Studio 

Presentación de **Herramientas de generador de datos de Azure** para Visual Studio. A continuación se muestran las características habilitadas. Consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=617530) para obtener más información.

- **Creación de la plantilla basada**: seleccione mayúsculas y minúsculas de uso en función de plantillas, plantillas de movimiento de datos o plantillas de procesamiento de datos para implementar una solución de integración de datos de llevar a cabo y empezar a práctica rápidamente con el generador de datos. 
- **Integración con el Explorador de soluciones para la creación y la implementación de entidades de datos fábrica**: crear e implementar canalizaciones y entidades relacionadas como proyectos de Visual Studio. 
- **Ver la integración con el diagrama de interacción visual durante la edición**: crear visualmente canalizaciones y conjuntos de datos con ayuda de la vista de diagrama. 
- **Integración con el Explorador de servidores para explorar e interacción con entidades ya implementadas**: aprovechar el Explorador de servidores para examinar ha implementado datos fábricas y entidades correspondientes. Importar un generador de datos implementado o cualquier otra entidad (canalización, servicios vinculados, los conjuntos de datos) a su proyecto. 
- **Edición JSON con validación de esquema y enriquecido intellisense**: eficazmente configurar y editar documentos JSON de entidades de generador de datos con la validación de intellisense y esquema enriquecida 
- **Publicación de entorno múltiple**: publicar editada canalizaciones a desarrollo, prueba o entorno de producción mediante la creación de archivos de configuración independiente para cada entorno.
- **Cerdo, subárbol y .net según la compatibilidad de procesamiento de datos**: soporte técnico para cerdo y subárbol secuencias de comandos de proyecto del generador de datos. Soporte técnico para hacer referencia a C# Project para administrar .net actividad.

##<a name="azure-sdk-for-net-271"></a>Azure SDK para .NET 2.7.1

En la sección siguiente contiene las actualizaciones que se han introducido con el SDK de Azure para .NET 2.7.1 versión.
###<a name="hdinsight-tools"></a>Herramientas de HDInsight 

Para obtener información más detallada sobre las actualizaciones de herramientas de HDInsight, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Sección trabajo operador vista (una nueva característica)

    Para ayudarle a entender la consulta subárbol mejor, se agregó la característica de vista del operador de sección. Para ver todos los operadores dentro de un vértice, haga doble clic en los vértices del gráfico de trabajo. Para ver más detalles de un determinado operador, desplace el puntero sobre ese operador.
- Marcador de Error de sección (una nueva característica)

    Para que pueda ver los errores gramaticales al instante, se agregó la característica subárbol marcador de Error. Además, los mensajes de error se han mejorado y ahora puede ver al instante errores gramaticales detallada (hasta esta versión, tenía que enviar una secuencia de comandos de la sección al clúster y espere a que algún tiempo antes de obtener detalles sobre el mensaje de error).  
- Gráfico de topología de tormenta (una nueva característica)

    Visualizar es muy importante cuando desee ver si la topología funciona según lo esperado. En esta versión, hemos agregado visualización para gráficos de tormenta. Puede visualizar las mediciones importantes para su topología (por ejemplo, un color indica meteorología una determinada rayo es "ocupado" o no). También puede hacer doble clic pernos/pitorro para ver más detalles.

- Soporte técnico para clústeres HDInsight que se crearon en el Portal de Azure (corregir un error)

    Ahora puede usar Visual Studio para ver y enviar trabajos a todos los clústeres HDInsight independientemente de dónde se crearon el clúster.

- Más compatibilidad con IntelliSense y carga más rápida subárbol metadatos (una mejora)

    Hemos mejorado la IntelliSense agregando más sugerencias de usuario descriptivo. Por ejemplo, el alias de tabla ahora también pueden sugerir en IntelliSense para que pueda escribir la consulta con más facilidad. Además, hemos mejorado los metadatos de la sección carga de forma que solo tardará unos segundos a la lista de todas las bases de datos, tablas y columnas de la metastore subárbol.

Para obtener información más detallada sobre las actualizaciones de herramientas de HDInsight, consulte [este blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Mejoras en Visual Studio de 2013

- Azure SDK 2.7.1 permite 2013 de Visual Studio tener acceso a cuentas de Azure y suscripciones a través de Control de acceso en función de roles, proveedores de soluciones de la nube y Dreamspark.
- Con Azure SDK 2.7.1, la nueva ventana de herramienta Explorer nube ahora también está disponible en Visual Studio de 2013.

###<a name="known-issues"></a>Problemas conocidos

Instalar el SDK de Azure 2.6 o 2.7.1 de 2013 de comunidad de Visual Studio en una no - inglés OS se muestra una advertencia de que los recursos de inglés y no está en inglés de Visual Studio pueden ser coinciden. Esta advertencia puede eliminarse de forma segura. Sólo ocurrirá si el equipo no contiene una instalación anterior de 2013 de comunidad de Visual Studio y está instalando el SDK en una no - inglés OS. Después de que el paquete de idioma aplica a los recursos RTM a Visual Studio, pero antes de que se aplica la actualización 4, se muestra la advertencia. Descartar la advertencia le permitirá el paquete de idioma continuar y completar la aplicación de la versión 4 de actualización del contenido del paquete de idioma.

Proyectos LightSwitch no están compatible con esta versión. Este problema se resolverá con la próxima versión SDK.

##<a name="also-see"></a>Vea también
[Azure SDK 2.7.1 publicación de anuncio](http://go.microsoft.com/fwlink/?LinkId=623850)

[Publicación de anuncio de Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Información de retirada de Azure SDK para .NET y las API y soporte técnico](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
