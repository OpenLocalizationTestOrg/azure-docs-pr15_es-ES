<properties
    pageTitle="Comparación de servicios de aplicación, máquinas virtuales, tela de servicio y servicios de nube Azure | Microsoft Azure"
    description="Obtenga información sobre cómo elegir entre el servicio de aplicación de Azure, máquinas virtuales, tela de servicio y servicios en la nube para alojar aplicaciones web."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparación de aplicación de servicio, máquinas virtuales, tela de servicio y servicios de nube de Azure

## <a name="overview"></a>Información general

Azure ofrece varias formas de sitios web de host: [Aplicación de servicio de Azure][], [máquinas virtuales][], [Tela de servicio][]y [Servicios en la nube][]. En este artículo le ayuda a comprender las opciones y elegir la opción correcta para su aplicación web.

Servicio de aplicaciones de Azure es la mejor opción para la mayoría de las aplicaciones web. Implementación y administración se integren en la plataforma de sitios pueden escalar con rapidez para manejar las cargas de tráfico alta y el Administrador de tráfico y equilibrio de carga integrados proporcionan alta disponibilidad. Puede mover los sitios existentes al servicio de la aplicación de Azure fácilmente con una [herramienta de migración en línea](https://www.migratetoazure.net/), use una aplicación de código abierto desde la Galería de la aplicación Web o crear un nuevo sitio con el marco de trabajo y herramientas de su elección. La característica [WebJobs][] facilita la agregar a su aplicación de servicio de aplicación web de procesamiento de trabajo de fondo.

Servicio tela es una buena opción si está creando una nueva aplicación o volver a escribir una aplicación existente para usar una arquitectura de microservice. Aplicaciones, que se ejecutan en un grupo compartido de equipos, pueden iniciar pequeñas y aumentar para ajustar la escala masiva con cientos o miles de equipos según sea necesario. Servicios con estado facilitan la forma correcta y confiable almacenar el estado de la aplicación y servicio tela administra automáticamente partición de servicio, el ajuste de escala y la disponibilidad para usted.  Servicio tela también es compatible con WebAPI con interfaz Web abierta para .NET (OWIN) y núcleo de ASP.NET.  En comparación con la aplicación de servicio, servicio tela también proporciona más control sobre o acceso directo a la infraestructura subyacente. Puede remoto en los servidores o configurar tareas de inicio del servidor. Servicios de nube es similar a servicio tela grado de control en comparación con facilidad de uso, pero ahora es un servicio heredado y tela de servicio se recomienda para el desarrollo de nuevo.

Si tiene una aplicación existente que requieran modificaciones importantes que se ejecute en el servicio de aplicación o servicio tela, puede elegir máquinas virtuales para simplificar la migración a la nube. Sin embargo, correctamente configurar, proteger y mantener las máquinas virtuales requiere más tiempo y la experiencia de TI en comparación con el servicio de aplicación de Azure y tela de servicio. Si está pensando en máquinas virtuales de Azure, asegúrese de que tener en cuenta el esfuerzo de mantenimiento permanente necesario para revisión, actualizar y administrar su entorno de máquina virtual. Máquinas virtuales de Windows Azure es infraestructura como-servicio (IaaS), mientras que el servicio de aplicación y tela de servicio son plataforma como-servicio (Paas). 

## <a name="features"></a>Comparación de características

La siguiente tabla compara las capacidades de servicio de aplicaciones, servicios de nube, máquinas virtuales y tela de servicio para ayudarle a tomar la mejor opción. Para obtener información actual sobre el SLA de cada opción, vea [Azure Service Level Agreements](/support/legal/sla/).

Característica|Aplicación de servicio (aplicaciones web)|Servicios de nube (funciones web)|Máquinas virtuales de Windows|Tela de servicio|Notas
---|---|---|---|---|---
Implementación de casi instantánea|X|||X|Implementar una aplicación o una actualización de aplicación a un servicio de nube o crear una máquina virtual, puede tardar varios minutos al menos; implementar una aplicación a una aplicación web tarda a segundos.
Escalar para equipos más grandes sin bola|X|||X|
Instancias de servidor Web compartan contenido y configuración, lo que significa que no tiene que volver a implementar o volver a medida que escala.|X|||X|
Múltiples entornos de implementación (producción y ensayo)|X|X||X|Servicio tela le permite tener varios entornos para las aplicaciones o para implementar diferentes versiones de la aplicación en paralelo.
Administración automática de la actualización de sistema operativo|X|X|||Actualizaciones automáticas de OS se han planificado para liberar una estructura futura del servicio.
Cambio de plataforma transparente (desplazarse fácilmente entre 32 y 64 bits)|X|X|||
Implementar código con GIT, FTP|X||X||
Implementar código con Web implementar|X||X||Servicios de nube admite el uso de Web implementar para implementar las actualizaciones de las instancias de una función individual. Sin embargo, no puede usar para la implementación inicial de una función, y si usa Web implementar una actualización tiene que implementar por separado cada instancia de una función. Varias instancias son necesarios para obtener el acuerdo de servicio de nube para entornos de producción.
Soporte técnico de WebMatrix|X||X||
Acceso a servicios como Bus de servicio, el almacenamiento de base de datos SQL|X|X|X|X|
Web del host o nivel de servicios web de una arquitectura de varios niveles|X|X|X|X|
Nivel intermedio de host de una arquitectura de varios niveles|X|X|X|X|Aplicaciones de servicio de aplicación web pueden hospedar fácilmente un nivel medio de la API de REST y la característica de [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) puede hospedar procesamiento trabajos en segundo plano. Puede ejecutar WebJobs en un sitio Web para lograr escalabilidad independiente para el nivel. La característica de [aplicaciones de la API](../app-service-api/app-service-api-apps-why-best-platform.md) de vista previa proporciona más funciones para resto servicios de hospedaje.
Soporte de MySQL como servicio integrado|X|X|X||Servicios de nube pueden integrar MySQL como servicio a través de ofertas de ClearDB, pero no como parte del flujo de trabajo de Portal de Azure.
Soporte técnico para ASP.NET, clásico ASP, Node.js, PHP, Python|X|X|X|X|Tela de servicio compatible con la creación de un sitio web front-end mediante [5 ASP.NET](../service-fabric/service-fabric-add-a-web-frontend.md) o bien puede implementar cualquier tipo de aplicación (Node.js, Java, etcetera) como [invitado ejecutable](../service-fabric/service-fabric-deploy-existing-app.md).
Cambiar la escala a varias instancias sin bola|X|X|X|X|Máquinas virtuales puede escalar a varias instancias, pero se deben escribir los servicios que se ejecuten en ellos para controlar este escalado. Deberá configurar un equilibrador de carga para enrutar las solicitudes de los equipos y crear un grupo de afinidad para evitar que se reinicia simultánea de todas las instancias debido a errores de mantenimiento o hardware.
Compatibilidad con SSL|X|X|X|X|Para las aplicaciones de servicio de aplicación web, SSL para nombres de dominio personalizado solo es compatible para el modo estándar y Basic. Para obtener información sobre cómo utilizar SSL con web apps, vea [configurar un certificado SSL para un sitio Web de Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Integración de Visual Studio|X|X|X|X|
Depuración remota|X|X|X||
Implementar código con TFS|X|X|X|X|
Aislamiento de red con la [Red Virtual de Azure](/services/virtual-network/)|X|X|X|X|Consulte también [integración de red Virtual de sitios Web de Azure](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Compatibilidad con [El Administrador de tráfico de Azure](/services/traffic-manager/)|X|X|X|X|
Supervisión de extremo integrado|X|X|X||
Acceso al escritorio remoto para servidores||X|X|X|
Instalar cualquier MSI personalizado||X|X|X|Tela de servicio permite hospedar cualquier archivo ejecutable como [invitado ejecutable](../service-fabric/service-fabric-deploy-existing-app.md) o puede instalar cualquier aplicación en las máquinas virtuales.
Posibilidad de definir y ejecución de las tareas de inicio||X|X|X|
Puede escuchar eventos ETW||X|X|X|

## <a name="scenarios"></a>Escenarios y recomendaciones

Estos son algunos escenarios comunes de aplicación con recomendaciones sobre qué Azure podría ser más adecuado para cada opción de hospedaje de sitios web.

- [Necesito un web front-end con fondo procesamiento y la base de datos back-end para ejecutar aplicaciones empresariales integradas con en activos local.](#onprem)
- [Necesito encontrar una manera confiable para hospedar Mi sitio Web corporativo que escala bien y alcanzan de ofertas globales.](#corp)
- [Tengo una aplicación de IIS6 ejecutar en Windows Server 2003.](#iis6)
- [Soy el propietario de una pequeña empresa y necesito una manera económica para hospedar Mi sitio, pero con un aumento futuro en cuenta.](#smallbusiness)
- [Soy un diseñador gráfico o web y quiero diseñar y crear sitios web para mis clientes.](#designer)
- [Mi aplicación de varios niveles con un sitio web front-end estoy migrar a la nube.](#multitier)
- [Mi aplicación depende de Windows altamente personalizadas o entornos Linux y desea mover a la nube.](#custom)
- [Mi sitio utiliza software de código abierto y deseo hospedar en Azure.](#oss)
- [Tengo una aplicación de línea de negocio que necesita para conectarse a la red corporativa.](#lob)
- [Deseo hospedar una API de REST o un servicio web para clientes móviles.](#mobile)


### <a id="onprem"></a>Necesito un web front-end con fondo procesamiento y la base de datos back-end para ejecutar aplicaciones empresariales integradas con en activos local.

Servicio de aplicaciones de Azure es una solución excelente para las aplicaciones empresariales complejas. Le permite desarrollar aplicaciones que escalan automáticamente en una plataforma de equilibrio de carga, se protegen con Active Directory y conectan a los recursos locales. Facilita la administración de esas aplicaciones fáciles a través de un portal de nivel internacional y API y permite obtener información sobre cómo los clientes utilizan con herramientas de conocimiento de aplicación. La característica [Webjobs][] le permite ejecutar procesos en segundo plano y las tareas como parte de la capa de web, mientras la conectividad híbrido y características VNET que sea más fácil volver a conectarse a los recursos locales. Azure de aplicación de servicio proporciona SLA de tres 9 para aplicaciones web y le permite:

* Ejecutar sus aplicaciones confiable en una plataforma de nube de recuperación automática, revisión automática.
* Ajustar automáticamente a través de una red mundial de centros de datos.
* Realizar copias de seguridad y restauración de recuperación.
* Ser compatible con ISO, SOC2 y PCI.
* Integrar con Active Directory

### <a id="corp"></a>Necesito encontrar una manera confiable para hospedar Mi sitio Web corporativo que escala bien y alcanzan de ofertas globales.

Servicio de aplicaciones de Azure es una solución excelente para el hospedaje de sitios Web corporativos. Le permite escalar rápida y fácilmente para satisfacer la demanda a través de una red global de los centros de las aplicaciones web. Ofrece alcance local, tolerancia a errores y administración de tráfico inteligente. Todo en una plataforma que proporciona herramientas de administración de nivel internacional, lo que permite obtener un conocimiento de estado del sitio y el tráfico de sitio rápida y fácilmente. Azure de aplicación de servicio proporciona SLA de tres 9 para aplicaciones web y le permite:

* Ejecute los sitios Web de forma confiable en una plataforma de nube de recuperación automática, revisión automática.
* Ajustar automáticamente a través de una red mundial de centros de datos.
* Realizar copias de seguridad y restauración de recuperación.
* Administrar los registros y el tráfico con las herramientas integradas.
* Ser compatible con ISO, SOC2 y PCI.
* Integrar con Active Directory

### <a id="iis6"></a>Tengo una aplicación de IIS6 ejecutar en Windows Server 2003.

Servicio de aplicaciones de Azure facilita la evitar los costos de infraestructura asociados con la migración de aplicaciones de IIS6 anteriores. Microsoft ha creado [Herramientas de migración de fácil de usar y Guía de migración detallados](https://www.movemetowebsites.net/) que le permiten comprobar la compatibilidad e identificar los cambios que deben realizarse. Integración con Visual Studio, TFS y herramientas comunes de CMS facilita la implementación de aplicaciones de IIS6 directamente a la nube. Una vez implementado, el Azur Portal ofrece herramientas sólidas de administración que le permiten escalar hacia abajo para administrar los costos y para satisfacer demanda según sea necesario. Con la herramienta de migración, puede:

* Rápida y fácilmente migrar la aplicación web de Windows Server 2003 heredada en la nube.
* Elegir para abandonar el adjunto SQL base de datos local para crear una aplicación híbrida.
* Mover la base de datos SQL junto con la aplicación heredada de forma automática.

### <a id="smallbusiness"></a>Soy el propietario de una pequeña empresa y necesito una manera económica para hospedar Mi sitio, pero con un aumento futuro en cuenta.

Servicio de aplicaciones de Azure es una magnífica solución para este escenario, ya puede empezar a usar de forma gratuita y a continuación, agregue más funciones cuando los necesite. Cada aplicación gratuita web viene con un dominio de Azure (*your_company*. azurewebsites.net), y la plataforma incluye herramientas integradas de implementación y administración, así como una galería de aplicación que facilitan la introducción. Hay muchos otros servicios y opciones de escala que permitir que el sitio evolucionando con petición de usuario. Servicio de aplicación de Azure, puede:

- Comenzar con el nivel gratuito y, a continuación, escalar según sea necesario.
- Utilice la Galería de aplicación para establecer rápidamente aplicaciones web populares, como WordPress.
- Agregar características y servicios de Azure adicionales a la aplicación según sea necesario.
- Proteger la aplicación web con HTTPS.

### <a id="designer"></a>Soy un diseñador gráfico o web y deseo diseñar y crear sitios Web de los clientes

Para los desarrolladores web y diseñadores, servicio de aplicación de Azure se integra fácilmente con una gran variedad de herramientas y marcos, incluye compatibilidad con la implementación de la Git y FTP y ofrece una estrecha integración con herramientas y servicios como Visual Studio y base de datos de SQL. Con el servicio de aplicación, puede:

- Usar herramientas de línea de comandos para [tareas automatizadas][scripting].
- Trabajar con los lenguajes más conocidos como [.net][dotnet], [PHP][], [Node.js][nodejs]y [Python][].
- Seleccione tres niveles de escala diferentes para escalar a capacidades muy altas.
- Integrar con otros servicios de Azure, como [Base de datos SQL][sqldatabase], [Bus de servicio] [ servicebus] y [almacenamiento][]u ofertas de los partners de la [Tienda de Azure][azurestore], como MySQL y MongoDB.
- Integrar con herramientas como Visual Studio, Git, WebMatrix, WebDeploy, TFS y FTP.

### <a id="multitier"></a>Estoy migrar mi aplicación de varios niveles con un servidor web en la nube

Si está ejecutando una aplicación de varios niveles, como un servidor web que se conecta a una base de datos de la aplicación de servicio de Azure es una buena opción que ofrece una estrecha integración con la base de datos de SQL Azure. Y puede usar la característica de WebJobs para ejecutar procesos de back-end.

Si necesita más control sobre el entorno de servidor, como la capacidad remota en el servidor o configurar tareas de inicio del servidor, elija a servicio tela para uno o varios de los niveles.

Si desea usar su propia imagen de la máquina o ejecutar software de servidor o los servicios que no se puede configurar en tela de servicio, elija máquinas virtuales para uno o varios de los niveles.

### <a id="custom"></a>Mi aplicación depende de Windows altamente personalizadas o entornos Linux y desea mover a la nube.

Si la aplicación requiere compleja instalación o configuración de software y el sistema operativo, máquinas virtuales probablemente es la mejor solución. Máquinas virtuales de Windows, puede:

- Usar la Galería de máquina Virtual para empezar con un sistema operativo, como Windows o Linux y, a continuación, personalizarlo para los requisitos de la aplicación.
- Crear y cargar una imagen personalizada de un servidor local existente para que se ejecute en una máquina virtual en Azure.

### <a id="oss"></a>Mi sitio utiliza software de código abierto y deseo hospedar en Azure

Si el marco Abrir origen es compatible con la aplicación de servicio, los idiomas y marcos necesarios para la aplicación se configuran automáticamente. Servicio de la aplicación le permite:

- Usar varios populares Abrir origen idiomas, como [.NET][dotnet], [PHP][], [Node.js][nodejs]y [Python][].
- Configurar WordPress, Drupal, Umbraco, DNN y muchas otras aplicaciones web de terceros.
- Migrar una aplicación existente o cree uno nuevo desde la Galería de aplicación.

Si no se admite el marco de código abierto en la aplicación de servicio, puede ejecutar en uno de lo otro Azure hospedaje opciones. Con máquinas virtuales, instalar y configurar el software en la imagen de la máquina, que puede ser Windows o Linux.

### <a id="lob"></a>Tengo una aplicación de línea de negocio que necesita para conectarse a la red corporativa

Si desea crear una aplicación de línea de negocio, el sitio Web podría requerir acceso directo a los servicios o datos de la red corporativa. Esto es posible en el servicio de aplicación, tela de servicio y máquinas virtuales de Windows mediante el [servicio de red Virtual de Azure](/services/virtual-network/). En la aplicación de servicio puede usar la [característica de integración de VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite a las aplicaciones de Azure ejecuten como si fueran en su red corporativa.

### <a id="mobile"></a>Deseo hospedar una API de REST o un servicio web para clientes móviles

Servicios web basados en HTTP le permiten admitir una gran variedad de clientes, incluidos a los clientes móviles. Marcos como ASP.NET Web API integran con Visual Studio para que sea más fácil crear y consumir servicios del resto.  Estos servicios se exponen desde un extremo de la web, por lo que es posible utilizar cualquier técnica en Azure de hospedaje para admitir este escenario. Sin embargo, el servicio de aplicación es una excelente opción para API de REST de hospedaje. Con el servicio de aplicación, puede:

- Crear rápidamente una [aplicación móvil](../app-service-mobile/app-service-mobile-value-prop.md) o [aplicación API](../app-service-api/app-service-api-apps-why-best-platform.md) para hospedar el servicio web HTTP en uno de los centros de datos de Azure distribución global.
- Migrar los servicios existentes o cree otros nuevos.
- Lograr SLA para disponibilidad con una sola instancia o escalar en varios equipos dedicados.
- Use el sitio publicado para proporcionar las API de REST a los clientes HTTP, incluidos a los clientes móviles.

> [AZURE.NOTE]
> Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, donde puede inmediatamente crear una aplicación de corta duración starter en Azure aplicación de servicio de forma gratuita. No hay ninguna tarjeta de crédito no requerido, compromisos.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener más información acerca de las tres opciones de hospedaje web, vea [Introducción a Azure](../fundamentals-introduction-to-azure.md).

Para empezar con las opciones que elija para su aplicación, vea los siguientes recursos:

* [Servicio de aplicaciones de Azure](/documentation/services/app-service/)
* [Servicios de nube de Azure](/documentation/services/cloud-services/)
* [Máquinas virtuales de Windows Azure](/documentation/services/virtual-machines/)
* [Tela de servicio](/documentation/services/service-fabric)

<!-- URL List -->

[Servicio de aplicaciones de Azure]: /services/app-service/
[Servicios de nube]: http://go.microsoft.com/fwlink/?LinkId=306052
[Máquinas virtuales de Windows]: http://go.microsoft.com/fwlink/?LinkID=306053
[Tela de servicio]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Almacenamiento de información]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
