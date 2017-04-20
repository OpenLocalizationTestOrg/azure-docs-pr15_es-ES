<properties 
    pageTitle="Ofertas de aplicaciones de Azure de aplicación de servicio Web para la empresa" 
    description="Muestra cómo usar aplicaciones de Azure aplicación de servicio Web para crear soluciones de sitio Web para su negocio de la empresa" 
    services="app-service\web" 
    documentationCenter="" 
    authors="apwestgarth" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="anwestg"/>

# <a name="azure-app-service-web-apps-offerings-for-enterprise-whitepaper"></a>Ofertas de aplicaciones de Azure de aplicación de servicio Web para la empresa notas del producto #

La necesidad de reducir los costos y ofrecer soluciones de TI más rápidas en un entorno rápidamente en evolución crea nuevos desafíos para desarrolladores, profesionales de TI y los administradores. Se buscan los usuarios cada vez más para que sus aplicaciones web de línea de negocio (LOB) rápido, responde y está disponible desde cualquier dispositivo. Al mismo tiempo, las empresas están intentando aprovechar la mayor productividad y la eficiencia procedente de integración con la nube y servicios móviles, puede ser algo tan sencillo como inicio de sesión único en todos los dispositivos con Active Directory para colaboración en Office 365 con datos extraídos de una aplicación LOB interna que extrae por separado los datos de la aplicación de empresa de Salesforce. [Aplicaciones de Azure aplicación de servicio Web](http://go.microsoft.com/fwlink/?LinkId=529714) es un servicio de nube de clase empresarial para desarrollar, probar y ejecutar web y aplicaciones móviles, las API de Web y sitios Web genérico. Se puede usar para ejecutar sitios Web corporativos, sitios de la intranet, aplicaciones empresariales y campañas de marketing digitales en una red mundial de centros de datos optimizados para escala y disponibilidad, junto con el soporte técnico para la integración continua y moderna DevOps prácticas.  

Este artículo resalta las capacidades del servicio [Web Apps](/services/app-service/web/) centrada específicamente en aplicaciones Web LOB, sobre la migración de las aplicaciones web existentes e implementación de nuevas aplicaciones web LOB en la plataforma. 

## <a name="audience"></a>Audiencia ##

Los profesionales de TI, arquitectos y administradores que desean para migrar a la nube web las cargas de trabajo que se están ejecutando en local. Cargas de trabajo de Web pueden abarcar en Business para empleados o empresa para aplicaciones web de socios.

## <a name="introduction"></a>Introducción ##

Aplicación de servicio Web Apps es una plataforma ideal hospedar aplicaciones web internos y externos y servicios, ya que proporciona una solución rentable, alta escalabilidad y administrada que le permite concentrarse en ofrecer valor de negocios para los usuarios en lugar de gastos grandes cantidades de tiempo y dinero mantenimiento y auxiliares distintos entornos. Aplicaciones de Web ofrece una plataforma flexible en el que se van a implementar las aplicaciones web de empresa que se ofrece la capacidad para continuar para autenticarse en local Active Directory mediante la integración con Microsoft Azure Active Directory, soporte técnico de implementaciones fáciles y rápidas realizar uso de su continuo integración e implementación prácticas internas, mientras escala automáticamente para aumentar con las necesidades empresariales - en una plataforma administrada que le permite centrarse en la aplicación y no la infraestructura. 

## <a name="problem-definition"></a>Definición del problema ##

Cambia el entorno de TI rápidamente, con un movimiento lejos de hospedaje en servidores tradicionales con sus altos costos de capital en los tiempos de entrega a largo plazo que usa a petición usar servicios que escala automáticamente para controlar la carga. Departamentos de TI que se deben reducir el costo y espacio de infraestructura y el mantenimiento dedica con un enfoque en reducción de gastos de capital y aumentan la agilidad. Al final del ciclo de vida de las plataformas de infraestructura anteriores, como Windows Server 2003, es lo que lleva los departamentos de TI para revisar la migración a la nube como una manera de posible para evitar los costos de capital de nuevo a largo plazo. En el pasado, los directores informáticos haga decisiones de compras para otros departamentos; Sin embargo, cada vez más CMOs y otros jefes de unidad de negocio están realizando una función de cómo se dedica a su presupuesto y cuál es el retorno de la inversión más activa. Cada vez más, las empresas deben sus empleados a ser mucho más móviles que nunca con los empleados trabajan de forma remota, gastos más tiempo con los clientes que necesiten acceso a sistemas complicaciones.

Empresa necesita cambiar mensualmente, semanalmente, diariamente. Empresas que están buscando instantánea escala global con servicios actualizados regulares completos de características nuevas, proporcionadas por terceros o internamente.  En algunos casos empresas buscan las capacidades a aislar sus aplicaciones y acceso a los recursos, mientras que hacer uso de las instalaciones de la nube pública. Los usuarios tienen las expectativas, con muchas que hagan uso de los servicios en sus vidas privadas, como Office 365. Esperan tener acceso a servicios enriquecidos de características similares, actualizado, en su vida de trabajo. Para hacer frente a esta demanda TI debe aspecto para ayudar a la empresa para habilitar esta opción mediante la selección y la integración con terceros servicios, cuidado de selección de plataformas que puede adaptarse a las necesidades empresariales, mientras que también es confiable con un menor costo total de propiedad.

Se buscan los equipos de desarrollo para ofrecer beneficios de negocio inmediata, realiza nuevas características con frecuencia. Busca una plataforma rentable y fiable que se integra con su prueba de prácticas: desarrollo y herramientas existente, release; y trabajar conjuntamente con los departamentos de TI automatiza implementación, administración y alertas, todos con el objetivo de cero el tiempo de inactividad.

<a href="highlevel" />
## <a name="high-level-solution"></a>Solución de nivel alto ##

Marcos y plataformas web cada vez que se sirven para desarrollar, probar y hospedaje de aplicaciones empresariales.  Con una línea de aplicación empresarial, como un sistema de gastos de empleado interno típica a menudo compuestos únicamente de una aplicación web con una base de datos de copia de seguridad para almacenar los datos relacionados con la aplicación.

Aplicaciones del servicio de aplicación Web es una buena opción para alojar estas aplicaciones, que se ofrece la infraestructura scalable y confiable que se administra y revisar con cerca de cero intervención manual y el tiempo de inactividad. La plataforma de Microsoft Azure ofrece muchas opciones de almacenamiento de datos para aplicaciones web hospedadas en aplicaciones Web de base de datos de Microsoft Azure SQL, una administrada scalable relacional base de datos como-servicio, a los populares servicios de nuestros socios como base de datos MySQL ClearDB y MongoDB.

Un enfoque alternativo es hacer uso de la inversión existente en local. En la situación de ejemplo, un sistema de gastos del empleado, puede mantener el almacén de datos dentro de su propia infraestructura interna. Esto puede deberse a la integración con sistemas internos (informes de nómina, facturación, etc.) o satisfacer el requisito de gobernanza de TI.  Web aplicaciones proporciona una serie de métodos de lo que le permite conectarse a su infraestructura local en:

- [Entornos de aplicación de servicio](app-service-app-service-environment-intro.md) : entornos de servicio de aplicación (ASE) son una nueva característica de Premium que recientemente se agregó a la oferta de servicio de aplicaciones de Microsoft Azure.  ASEs proporcionan un entorno completamente aislado y dedicado para ejecutar aplicaciones de servicio de la aplicación de Azure de forma segura a gran escala y también ofrecer aislamiento y acceso seguro a redes   
- [Conexiones de híbrido](../biztalk-services/integration-hybrid-connection-overview.md) : híbrido conexiones son una característica de Microsoft Azure BizTalk Services y habilitar aplicaciones Web para conectarse a local recursos de forma segura, por ejemplo SQL Server, MySQL, las API de Web y servicios web personalizados. 
- [Integración de red virtual](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) : integración de aplicaciones Web con una red Virtual Azure le permite conectar la aplicación web a una red Virtual de Azure que a su vez se pueden conectar a su infraestructura local activado a través de una VPN de sitio a otro. 

Los diagramas siguientes muestran una solución de alto nivel de ejemplo con opciones de conectividad para recursos locales.  El primer ejemplo muestra cómo esto se puede conseguir usando las características estándar de servicio de la aplicación de Azure y el segundo muestra cómo puede lograr mediante la prima ofreciendo entornos de aplicación de servicio.

Usar las características estándar de aplicación de servicio:![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png "Using Standard App Service Features")

Con un entorno de servicio de aplicaciones:![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions-ASE.png "Using an App Service Environment")

## <a name="business-benefits"></a>Ventajas de la empresa ##

Aplicaciones del servicio de aplicación Web proporciona un host de beneficios de negocio que permiten la función ser mucho más rentables y ágiles en la entrega de las necesidades empresariales. 

### <a name="paas-model"></a>Modelo de PaaS ###

Aplicaciones del servicio de aplicación Web se basa en una plataforma como un modelo de los servicios que proporciona una serie de ahorros de costo y la eficiencia.  Ya no necesita pasar horas administrar máquinas virtuales, revisiones de sistemas operativos y marcos. Aplicaciones de Web es un entorno automáticamente con la revisión que le permite centrarse en administrar las aplicaciones web y no VM, salir de equipos libres para proporcionar valor empresarial adicional.

El modelo de PaaS apuntalar Web Apps permite profesionales de la metodología de DevOps satisfacer sus objetivos. Como empresa, esto significa administración completa y la integración durante el ciclo de vida completo de aplicación, incluyendo desarrollo, prueba, lanzamiento, supervisión y administración y soporte técnico. 

Para equipos de desarrollo, se pueden configurar flujos de trabajo continuos de integración e implementación de Visual Studio Team Services, GitHub, TeamCity, Hudson o BitBucket, Habilitar generación automatizada, probar y habilitar lanzamiento rápido ciclos mientras reduce la fricción implicados en puesta de infraestructura de implementación. Web Apps también es compatible con la creación de varias pruebas y entornos para un flujo de trabajo de la versión de prueba, ya no necesita reservar o asignar hardware para ello, puede crear entornos tantos como desee y definir su propios promoción para liberar el flujo de trabajo. Como una empresa que podría decidir a un espacio de prueba de control de código fuente, aplique una serie de pruebas y cuando finalice la promover a un espacio de región y finalmente intercambiar a producción sin tiempo de inactividad, con la ventaja que aplicaciones web hospedadas en aplicaciones Web están precargadas y caliente para proporcionar la mejor posible experiencia del cliente.  Además empresas pueden hacer uso de las pruebas de capacidades de producción de la aplicación de servicio Web Apps para dirigir una sección del tráfico a un espacio distinto, validar los cambios antes de cambiar todo el tráfico a la nueva implementación o restaurar todo el tráfico a la implementación anterior. 

Equipos de operaciones pueden estar seguros de que están en la posición mejor posible ante cualquier problema con cualquiera de sus aplicaciones web hospedadas en aplicaciones Web con integrada en las características de supervisión y alertas. Equipos de operaciones debe ya ha invertido en análisis y soluciones de supervisión de Microsoft Visual Studio aplicación perspectivas, nueva Relic y AppDynamics. Estos también son totalmente compatibles en aplicaciones Web habilitar continuidad y un entorno familiar desde la que se va a supervisar las aplicaciones web.

Por último, aplicaciones Web proporciona funcionalidad automáticamente realizar una copia de su app(s) y bases de datos hospedados directamente a un contenedor de almacenamiento de blobs de Windows Azure. Proporcionar un método de forma y muy rentable fácil con el que desea recuperar de desastre, se reduce la necesidad de complejo en local hardware y software.

### <a name="ease-of-migration"></a>Facilidad de migración ###

Giro y el mantenimiento de hardware es un factor clave para empresas como acelerar ciclos de lanzamiento de hardware y sistemas operativos. ¿Tiene un número de servidores de Windows Server 2003 R2 que llegan al final del soporte técnico de 2015 pero aún alojan aplicaciones web clave para su empresa? Aplicaciones del servicio de aplicación Web es un candidato excelente en la que se va a hospedar las aplicaciones web y para que pueda racionalizar la inmuebles de hardware de empresa. Web Apps le proporciona acceso a un rango de especificaciones de hardware que se administran y se mantiene como parte del servicio, eliminando la necesidad de factor de reemplazo y los costos de administración como parte de su presupuesto de infraestructura.  Migración puede ser tan sencillo como una copia y pegar la operación de su implementación de aplicaciones Web o una migración más compleja donde utilizando el Asistente para la migración de aplicaciones Web agregará valor. Aplicaciones web migrados disfrutarán la completa gama de servicios de Azure, integración de servicios adicionales a las aplicaciones web. Por ejemplo, puede agregar Azure Active Directory para controlar el acceso a la aplicación basada en la asociación de los usuarios a grupos de seguridad. Otro ejemplo se puede agregar servicios de caché para mejorar el rendimiento y reducir la latencia, lo que proporciona total mejor experiencia de usuario. 

### <a name="enterprise-class-hosting"></a>Hospedaje de clase empresarial ###

Aplicaciones del servicio de aplicación Web proporciona una plataforma estable y fiable que se ha comprobado que puedan realizar una amplia variedad de empresa necesita de pequeña interno desarrollo prueba cargas de trabajo y, a sitios Web de tráfico alta escala superior. Realizar con Web Apps, uso de la misma plataforma de hospedaje clase empresarial que Microsoft como una empresa que se utiliza para cargas de trabajo de alto valor web. Aplicaciones Web, junto con todos los servicios en la plataforma Windows Azure, se crean con seguridad y cumplimiento de requisitos normativos, como ISO (ISO/IEC 27001:2005); SOC1 y SOC2 SSAE 16/ISAE 3402 certificados, BAA HIPAA, PCI y Fedramp, en el centro de cada elemento y la característica para obtener más información, vea [http://aka.ms/azurecompliance](/support/trust-center/compliance/). 

La plataforma de Microsoft Azure permite rol autorización controles habilitar empresas distintos niveles de control a los recursos en aplicaciones Web. RBAC ofrece a las empresas implementar sus propias directivas de administración de acceso para todos sus activos en el entorno de Azure, asignar a usuarios a grupos y asignando por separado los permisos necesarios a estos grupos en el activo como una aplicación web. Para obtener más información sobre RBAC en Azure, consulte [http://aka.ms/azurerbac](../active-directory/role-based-access-control-configure.md). Mediante el uso de aplicaciones Web, puede ser que las aplicaciones web se implementan en un entorno seguro y tiene control total en qué territorio se implementan los activos. 

Azure entornos de servicio de aplicación [http://aka.ms/aseintro](http://aka.ms/aseintro) son una nueva opción de plan de servicio premium para clientes empresariales que desee hacer uso de la aplicación de servicio de Azure y proporcionan un entorno completamente aislado y dedicado.  Esto permite a los clientes de la empresa implementar aplicaciones que pueden aprovechar las ventajas de muy alta escala mientras también tiene control total sobre el tráfico de red entrante y saliente y ASEs permiten a aplicaciones tenga conexiones seguras de alta velocidad en redes virtuales a los recursos locales.

Aplicaciones de servicio de la aplicación Web también son capaces de realizar un uso completo de sus inversiones local en ofreciendo la capacidad de volver a conectarse a los recursos internos, como el almacén de datos o el entorno de SharePoint. Como se describe en la [solución de alto nivel](#highlevel) para hacer uso de conexiones híbrido y conectividad de red Virtual para establecer conexiones a infraestructura de local y servicios.

### <a name="global-scale"></a>Escala global ###

Aplicaciones del servicio de aplicación Web es una plataforma global y scalable, habilitar las aplicaciones web crecen y se adaptan a las necesidades de un negocio en crecimiento rápidamente y con la planificación de mínima a largo plazo y costo. En típico en escenarios de infraestructura local, expansión aumento de petición localmente y geográfico ¿requerir una gran cantidad de administración, planificación y gastos aprovisionar y administre la infraestructura adicional. Aplicaciones de Web ofrece la capacidad de las aplicaciones web con la fluctuaciones y el flujo de los requisitos. Por ejemplo con la aplicación de gastos como, por ejemplo, para la mayoría del mes los usuarios son light los usuarios de la aplicación, pero la fecha límite de cada mes para envíos de gastos escribirse y aumenta el uso de la aplicación, aplicaciones Web tiene la capacidad de aprovisionar automáticamente más infraestructura de la aplicación y, a continuación, una vez que ha desaparecido el uso de nuevo puede ampliarse volver a la infraestructura de línea base que defina.

Aplicaciones de Web está disponible globalmente en 24 centros de datos en todo el mundo y de crecimiento. Para obtener la lista actualizada de regiones y ubicación, consulte [http://aka.ms/azlocations](http://aka.ms/azlocations). Con Web Apps, su empresa puede lograr fácilmente escala y alcance global. A medida que crece su empresa en nuevas regiones, la generación de informes paneles de aplicación que utiliza host en aplicaciones Web puede distribuirse fácilmente en centros de datos adicionales y servir usuarios locales mucho más rápidamente a través de la combinación de aplicaciones Web y el Administrador de tráfico de Azure, con la ventaja de la infraestructura scalable debajo poder contraer y expandir como las necesidades de cambio de las oficinas regionales.
 
## <a name="solution-details"></a>Detalles de la solución ##

Veamos un ejemplo de un escenario de migración de la aplicación. También se describen los detalles de cómo características de la aplicación de servicio Web Apps reunirse proporcionar gran solución y valor empresarial.
 
En este ejemplo, la línea de aplicación de empresa que se tratará es una aplicación que permite a los empleados enviar sus gastos de reembolso para informes de gastos. La aplicación se hospeda en un Windows Server 2003 R2 ejecutando IIS6 y la base de datos es una base de datos de SQL Server 2005. El motivo por el elija anterior se encuentra de servidor con las nuevas extremo de servicio de Windows Server 2003 R2 y SQL Server 2005 y tenemos [Herramientas](http://aka.ms/websitesmigration) y [orientación](http://aka.ms/websitesmigrationresources) para migrar automáticamente las cargas de trabajo en Azure. Con esto en mente, el modelo utilizado en este ejemplo se aplica a una amplia verity de escenarios de migración. 

### <a name="migrate-existing-application"></a>Migrar una aplicación existente ###

El primer paso de la solución general para mover una aplicación de línea de negocio a aplicaciones Web es identificar los activos de la aplicación existente y la arquitectura. El ejemplo en este artículo es una aplicación web ASP.NET alojada en un único servidor IIS con la base de datos alojado en un servidor SQL independiente, como se muestra en la siguiente ilustración. Inicio de sesión de empleados en el sistema mediante una combinación de nombre de usuario y contraseña, escriba los detalles de gastos y cargar copias digitalizadas de confirmaciones, en la base de datos para cada elemento de gastos. 
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### <a name="items-to-consider"></a>Elementos para tener en cuenta ####

Cuando la aplicación de migración desde un entorno local, es recomendable tener en cuenta, algunas de las restricciones de aplicaciones Web. Estos son algunos temas claves que debe tener en cuenta al migrar aplicaciones web para las aplicaciones Web ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

-   Enlaces de puerto: Web aplicaciones solo es compatible con el puerto 80 para HTTP y el puerto 443 para el tráfico HTTPS. Si su aplicación usa cualquier otro puerto, a continuación, una vez migrados hará que la aplicación utiliza el puerto 80 para HTTP y puerto 443 para el tráfico HTTPS. A menudo es un problema de responsabilidad como es común en implementaciones locales para hacer uso de los distintos puertos para evitar el uso de nombres de dominio, especialmente en entornos de prueba y desarrollo
-   Autenticación: Web Apps es compatible con la autenticación anónima predeterminada y la autenticación de formularios identificado por una aplicación. Aplicaciones de Web puede ofrecer autenticación de Windows cuando la aplicación se integra con Azure Active Directory y ADFS solo. Esta es una característica que se describe en más detalle [aquí](http://aka.ms/azurebizapp) 
-   GAC según ensamblados: aplicaciones Web no permite la implementación de ensamblados a la caché de ensamblado Global (GAC). Por lo tanto, si la aplicación que se está migrando utiliza esta característica en local, considere la posibilidad de mover los ensamblados a la carpeta bin de la aplicación.
-   IIS 5: El modo de compatibilidad Web aplicaciones no admite el modo de compatibilidad de IIS 5 y así cada instancia de aplicaciones Web y todas las aplicaciones web en la instancia de aplicaciones Web principal ejecutarán en el mismo proceso de trabajo en un único grupo de aplicaciones.
-   Uso de bibliotecas COM – Web aplicaciones no permitir el registro de componentes COM en la plataforma. Por lo tanto, si la aplicación está haciendo uso de los componentes COM, estas necesitan para volver a escribir en código administrado e implementado con la aplicación.
-   Filtros: filtros se pueden admitir en aplicaciones Web. Se necesita para implementar como parte de la aplicación y registrado en el archivo web.config de la aplicación web. Para obtener más información, consulte [http://aka.ms/azurewebsitesxdt](web-sites-transform-extend.md). 

Una vez que se han considerado estos temas, la aplicación web debe estar preparada para la nube. Y no se preocupe si algunos temas no se cumplen totalmente, la herramienta de migración dará mejor esfuerzo a la migración. 

Son los siguientes pasos del proceso de migración para crear una aplicación de servicio de aplicación web y una base de datos de SQL Azure. Existen varios tamaños de instancias de aplicaciones Web con un número diferente de núcleos de CPU y cantidades de RAM disponibles para que pueda seleccionar en función de sus requisitos de aplicaciones web. Para obtener más información y precios, consulte [http://aka.ms/azurewebsitesskus](/pricing/details/websites/). Del mismo modo, base de datos de SQL de Microsoft Azure se encarga de todas las necesidades de un negocio con distintos niveles de servicio y niveles de rendimiento para satisfacer requisitos. Puede encontrar más información en [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/). Una vez creada, la aplicación se ha cargado en la aplicación de servicio Web Apps, ya sea a través de FTP o WebDeploy y, a continuación, pase a la base de datos.

En esta migración la solución utiliza la base de datos de SQL Azure, pero es no la única base de datos que se admite en Azure. También pueden hacer que las empresas el uso de MySQL, MongoDB, Azure DocumentDB y muchos más a través de los complementos que se pueden comprar en la [Tienda de Azure](/marketplace/partner-program/). 

Al crear una base de datos de SQL Azure varias opciones están disponibles para importar una base de datos de un servidor local desde la creación de una secuencia de comandos de una base de datos existente para usar la [importación y exportación de aplicación de nivel de datos](http://aka.ms/dacpac). 

La base de datos de aplicación de gastos se creó mediante la creación de una nueva base de datos de SQL Azure, conectarse a la base de datos con SQL Server Management Studio y, a continuación, ejecute una secuencia de comandos para generar el esquema de la base de datos y rellenar con los datos de la base de datos local.

El paso final de esta primera fase de la migración requiere la actualización de cadenas de conexión a la base de datos de la aplicación. Esto puede conseguirse a través del portal de Azure. Para cada aplicación web puede modificar la configuración específica de la aplicación, incluidas las cadenas de conexión que se usa la aplicación para conectarse a una base de datos que se usa.

### <a name="alternatives-to-using-azure-sql-database"></a>Alternativas al uso de la base de datos de SQL Azure ###

La plataforma de Windows Azure ofrece diversas alternativas al uso de base de datos de SQL Azure como una base de datos principal de aplicaciones web, esto es habilitar diferentes cargas de trabajo, es decir Use de una solución NoSQL o para habilitar la plataforma de necesidades de datos de un negocio. Por ejemplo, una empresa puede contener datos que no deben estar almacenados fuera del sitio o en un entorno de nube pública y, por tanto, sería para mantener el uso de su base de datos local.

#### <a name="connectivity-to-on-premises-resources"></a>Conectividad con recursos locales ####
Aplicaciones del servicio de aplicación Web ofrece varias opciones para conectarse a los recursos locales, como bases de datos, permitiéndole reutilización de infraestructura de valor alto. Las opciones son como se indica a continuación:

- Entornos de servicio de aplicación aislados y creados dentro de una subred de una red virtual, por lo tanto, lo que permite el entorno para comunicarse con los extremos privados ubicados en la misma red virtual - [http://aka.ms/appserviceasenetworking](http://aka.ms/appserviceasenetworking)
- Integración de red Virtual de Web Apps admite la integración entre aplicaciones Web y una red Virtual de Azure, permitir el acceso a los recursos que se ejecuta en su red Virtual que, si conectado a su red local activado con VPN de sitio a otro, permite la conectividad directamente a su en sistemas locales.
- Híbrido conexiones son una característica de Azure BizTalk Services y proporcionan una forma sencilla de conectarse a una persona recursos como SQL Server, MySQL, HTTP Web API y más los servicios Web personalizados local.

#### <a name="scale-and-resiliency"></a>Escala y resistencia ####

Que un negocio crece su personal a través de adquisiciones o crecimiento ecológico natural, así que también debe web escala de aplicaciones para satisfacer estas nuevas necesidades. De hecho hoy es común para ver una difusión aún mayor de empleados remotos y equipos comparten ubicación, por ejemplo las empresas con oficinas en los Estados Unidos, Europa y Asia, con una venta móvil forzar en muchos más territorios. Web Apps tiene la capacidad para controlar los cambios elásticos en escala de forma cómoda y automáticamente.

Aplicaciones del servicio de aplicación Web permite a las aplicaciones web estén configurados para ajustar automáticamente a través del portal de Azure, dependiendo de dos vectores – programadas horas o mediante el uso de la CPU. Autoescala aplicaciones Web, se ofrece una manera rentable y muy flexible satisfacer mayores cambios en uso para todas las aplicaciones de empresa, desde las aplicaciones web como nuestro sistema de informes a los sitios Web, experimentar una ráfaga alto de tráfico durante un período breve de promoción de marketing de gastos. Para obtener más información e instrucciones en escala aplicaciones web mediante aplicaciones Web, vea [cómo a sitios Web de escala](web-sites-scale.md).

Además de la flexibilidad escala de aplicaciones Web, la plataforma general permite continuidad empresarial y la resistencia a través de la distribución posible de aplicaciones web y sus activos en varios centros de datos y regiones geográficas.

## <a name="summary"></a>Resumen ##
Aplicaciones del servicio de aplicación Web ofrece una solución flexible, rentable, responde a las necesidades de la empresa en un entorno rápidamente en evolución dinámicas. Web Apps le ayuda a las empresas aumentar la productividad y la eficiencia haciendo uso de una plataforma administrada con capacidades de DevOps moderna y manos reducidas en administración, proporcionando capacidades de empresa en escala, resistencia, seguridad y la integración con activos local.

## <a name="call-to-action"></a>Llamar a la acción ##
Para obtener más información sobre el servicio, visite [http://aka.ms/enterprisewebsites](/services/websites/enterprise/) donde puede conseguirse más información y el signo de Azure aplicación servicio Web Apps para una versión de prueba hoy en [http://aka.ms/azuretrial](/pricing/free-trial/) para evaluar el servicio y descubra los beneficios para su empresa.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
