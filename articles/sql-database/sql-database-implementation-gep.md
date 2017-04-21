<properties
   pageTitle="Base de datos de SQL Azure Azure caso práctico - GEP | Microsoft Azure"
   description="Obtenga más información sobre cómo GEP usa la base de datos SQL para llegar a los clientes más globales y lograr una mayor eficiencia"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure proporciona alcance global de GEP y mayor eficiencia

![Logotipo GEP](./media/sql-database-implementation-gep/geplogo.png)

GEP ofrece software y servicios que permiten líderes de adquisiciones todo el mundo maximizar su impacto en las operaciones de su negocio, estrategias y rendimientos financieros. Además de los servicios de consultorías y administrados, la empresa ofrece inteligente por GEP®, una plataforma de software de adquisiciones completa, en la nube. Sin embargo, GEP detectaron limitaciones intentar admitir soluciones como inteligente por GEP con sus propios centros de datos locales: las inversiones necesarias estaban pronunciadas y requisitos normativos en otros países habría realizado las inversiones necesarias más complicado todavía. Si se mueve a la nube, GEP ha liberar recursos de TI, lo que le permite preocuparse menos acerca de las operaciones de TI y centrarse más en el desarrollo de nuevos orígenes de valor para sus clientes en todo el mundo.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expandir servicios y crecimiento mediante el uso de Azure

INTELIGENTES por amor de clientes GEP características de la plataforma y facilidad de uso; los clientes pueden administrar los procesos desde cualquier lugar, en cualquier momento y en cualquier dispositivo: portátil, tableta o teléfono. Moviendo a Microsoft Azure, GEP ha sido capaz de adaptarse a su rápido crecimiento y su potencial para expandir a nuevos mercados. Acuerdo VP de tecnología de GEP, Dhananjay Nagalkar, "Microsoft Azure ha reproducir una función clave en éxito del GEP por lo que nos permite escalar rápidamente servicios con rapidez y proporcionando los centros de datos regionales que nos ayudan a satisfacer las necesidades de nuestros clientes internacionales normativas."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>Las limitaciones de un centro de datos por

En 2013, coordinadores GEP reconocen que necesitan una manera de asegurarse de rendimiento y escalabilidad como que crecieron base a sus clientes. Nagalkar explicada, "para satisfacer esa demanda con nuestros centros de datos existente, se habría tenido que expandir considerablemente de nuestra infraestructura y recursos de TI. La inversión y el período de tiempo para hubiera sido enormes." Físicos y máquinas virtuales requieren configuración extensiva, administración, ajuste de escala, copia de seguridad y revisiones a una tasa que podría haberse costo prohibitivo para GEP en local. Soluciones de nube, por otro lado, ofrecen simplificar y la comodidad que habilitado GEP centrarse más en el desarrollo en lugar de administrar grandes y de crecimiento: las operaciones de TI. Nagalkar sabía que GEP podría reducir su sobrecarga de compras, configuración y administración de la infraestructura mediante la migración a la nube.

GEP también necesita una manera de superar obstáculos reglamentarios que mantiene en algunos mercados internacionales. Para muchos de los clientes europeos posibles de GEP, el cumplimiento de reglamentaciones requeriría tener datos almacenados en sus regiones geográficas locales. Pero no habría sido práctico para GEP creación varios centros de datos. "Las inversiones en infraestructura generalizado y trabajo de TI costos aportar un impacto significativo para los márgenes," según Nagalkar. "Como resultado, realmente había ausente activar clientes potenciales que requiere datos almacenados localmente."

Nagalkar sabía que una solución de nube puede ser la respuesta a este dilema. Si se puede mover GEP a un proveedor de servicios de nube con una presencia global, podría satisfacer mejor sus clientes normativas y rendimiento necesita almacenar los datos más cerca de ubicaciones físicas de los clientes.

## <a name="finding-smooth-skies-in-the-cloud"></a>Cielo suave de buscar en la nube

Nagalkar y su equipo exploran varias opciones de la nube, pero la mayoría estaban infraestructura como-servicio (IaaS) – soluciones que habría requerido GEP invirtiendo mucho en los recursos de TI para configurar y administrar el servicio. La solución de Azure plataforma como servicio (PaaS) resultada para ser mucho mejor ajuste.

"Con Azure, GEP no necesita tratar con la administración de la base de datos, configuración de la máquina virtual, revisiones u otras tareas de administración de la infraestructura" indica Nagalkar. "En su lugar, nos podemos centrar nuestros recursos en lo que se mejor: aprovechar nuestra experiencia en los contratos de escribir software que realmente ofrece resultados para nuestros clientes." 

De hecho, el cambio a Azure habilitó GEP a reducir su personal de operaciones de TI mientras simultáneamente permite mayor funcionalidad de los clientes.

Aprovechando centros de datos de Azure en todo el mundo, GEP puede ampliar fácilmente su alcance en Europa y Asia. Los centros de datos globales habilitar GEP escalar con rapidez y para satisfacer las necesidades de cliente para almacenar datos localmente que reduce la latencia y cumplan los requisitos normativos.

## <a name="smart-by-gep-architecture"></a>INTELIGENTE arquitectura GEP

GEP había elaborado inteligente por GEP conceptos básicos de Azure. Una crítica motivación para GEP era mayor escalabilidad, menos tiempo de inactividad, y los costos de mantenimiento reducidos GEP podría experimentar con la base de datos de SQL de Azure en comparación con qué GEP podrían lograr local. Sin embargo, una vez que se mueve a la nube, GEP descubre nuevas posibilidades de desarrollo en la nube, como prototipos rápida y eficiente de ingeniería para responder mejor a las necesidades de los clientes. Desarrollar en Azure le permiten GEP ausente hacer con el software de licencias pesado que sus programadores pueden surgir local. El núcleo de inteligente por GEP es la base de datos de SQL Azure, aunque GEP usa muchos otros servicios de Azure para continuar mejorar inteligente por GEP rápida y fácilmente.

![INTELIGENTE GEP arquitectura](./media/sql-database-implementation-gep/figure1.png) figura 1. INTELIGENTE arquitectura GEP

## <a name="structured-data"></a>Datos estructurados

En el centro de la inteligente por aplicación GEP son las instancias de base de datos de SQL Azure esa solución power la administración de adquisiciones enterprise. Cuando GEP ingeniería inteligente por GEP, vio la base de datos de SQL Azure como ideal para su arquitectura, uno que permita la empresa para lograr el mayor grado de protección de datos y para satisfacer sus requisitos normativos. GEP hace que ofrece la base de datos SQL Azure, incluido el uso de las varias capas de protección de datos:

- Cifrar datos almacenados mediante el cifrado de datos transparente.
- Seguridad de la autenticación mediante la integración de base de datos de SQL Azure con Azure Active Directory.
- Limitar el acceso a un subconjunto de datos con seguridad a nivel de fila adecuado.
- Masking datos en tiempo real mediante directivas.
- Seguimiento de eventos de base de datos a través de auditoría de base de datos de SQL Azure.

> "Podemos usamos todas estas opciones sin realizar cambios en el código principal y con un mínimo impacto en el rendimiento," dice Nagalkar.

Al usar la base de datos de SQL Azure, GEP automáticamente tiene mayor capacidad de recuperación que se han diseñado económico local debido a las características de tolerancia a errores integradas en base de datos de SQL Azure. GEP utiliza la capacidad de replicación de Geo activa en la base de datos de SQL Azure, junto con múltiples activas, puede leer y online secundarias réplicas (siempre en disponibilidad de grupos) en diferentes regiones geográficas, para formar pares de alta disponibilidad. La replicación de inteligente por GEP datos en todas las regiones significa que, en el caso de un desastre de toda la región GEP fácilmente puede recuperar los datos del cliente con un objetivo de punto de recuperación mínimo (RPO) y el objetivo de tiempo de recuperación (RTO).

Cada inteligente por cliente GEP tiene dos instancias de base de datos de SQL Azure: uno para el procesamiento de transacciones en línea (OLTP) y otro para el análisis (por ejemplo, cliente dedica y análisis de informes). Grupos de elásticos de la base de datos de base de datos de SQL Azure habilitar GEP administrar fácilmente miles de bases de datos de todo el mundo para gestionar demandas imprevistos de recursos de base de datos. Grupos de elásticos sirven para GEP para asegurarse de que las bases de datos del cliente pueden escalar según sea necesarios, sin aprovisionamiento en exceso o en-aprovisionamiento, mientras que también permite GEP para controlar los costos. Además, como se trata de un servicio PaaS, GEP Obtiene todas las nuevas funciones de base de datos de SQL Azure con las actualizaciones automáticas.

## <a name="unstructured-and-semi-structured-data"></a>Datos estructurados y semiestructurados

Sin embargo, algunos inteligente por los datos del cliente GEP necesita almacenamiento menos estrictamente estructurado. Para este tipo de datos, GEP emplea el almacenamiento de blobs de Windows Azure, almacenamiento de tablas de Azure y Azure Redis caché. Almacenamiento de blobs de Windows Azure aloja los datos adjuntos que inteligente de carga de usuarios GEP en la aplicación. También es donde inteligente por GEP almacena el contenido estático, como archivos de JavaScript y hojas de estilos en cascada (CSS).

GEP almacena datos de no acceden a cliente, como inteligente con GEP registro de datos, en el almacenamiento de tablas de Azure, lo cual proporciona GEP eficaz ilimitado almacenamiento rentable y tiempos de recuperación rápidamente sin tener que preocuparse sobre la configuración de un esquema de los datos. GEP usa Azure Redis caché para una caché principal.

## <a name="authentication-and-routing"></a>Autenticación y enrutamiento

Servicio de Control de acceso (ACS) Azure proporciona inteligente por usuarios GEP con una amplia variedad de opciones para iniciar sesión en el software. Azure ACS puede federarse con cualquier proveedor de identidades que cambia los datos de autenticación con seguridad aserción lenguaje de marcado (SAML), como los servicios de dominio de Active Directory, la identidad de Ping, OneLogin o SiteMinder. Esto le permite GEP implementar el inicio de sesión único (SSO) para los clientes sin preocuparse de almacenar credenciales de usuario y mantener las directivas de contraseña del usuario.

Una vez que haya iniciado sesión, los clientes tienen acceso a los recursos de empresa correcta en inteligente por GEP. GEP usa Azure tráfico administrador redirigir y solicitudes de equilibrio de carga procedentes de dispositivos móviles de los clientes y las sesiones del explorador.

## <a name="other-azure-services"></a>Otros servicios de Azure

GEP emplea un número de otros servicios de Azure para inteligente por GEP responde al cliente necesita. GEP usa servicios de nube de Azure (funciones web y trabajador) para presentación de aplicación de host y los servicios de lógica de negocios seguros. Servicios de nube realizar para desarrolladores administre la infraestructura como código (IAC) e implementar inteligentes nuevas aplicaciones GEP en una fracción del tiempo que requiere con centros de datos locales: todo sin ninguna participación de TI. Los desarrolladores GEP pueden usar los servicios de nube entorno para probar nuevas versiones sin afectar a la implementación de producción actual de ensayo. Una vez probado, GEP usa las características de intercambio de VIP de servicios de nube de Azure para mover el código de ensayo a la franja de producción dentro de un minuto, lo que reduce el tiempo de inactividad de implementación.

Para reducir la latencia de la aplicación, GEP usa la red de entrega de contenido (CDN) de Azure para colocar el contenido estático almacenado en el almacenamiento de blobs de Windows Azure (como archivos CSS y JavaScript) en los servidores perimetrales cerca de los usuarios. Usos GEP Bus de servicio de Azure para admitir los patrones de arquitectura de la aplicación, que abarca desde publicación suscripción a parcialmente comando consulta responde separación (CQRS) y capas arquitectura con acoplamiento y comunicación asincrónica. GEP usa Azure Media Services para mejorar su servicio de asistencia al cliente. GEP encontró que fácilmente podría publicar vídeos de soporte para el usuario en Azure Media Services. Estos vídeos responden a preguntas comunes de los usuarios, lo que ayuda a mejora la inteligente mediante la satisfacción del usuario GEP teniendo parte de la carga de soporte técnico quitarlas personal de soporte técnico de GEP.

Para enviar las miles de transacciones correos electrónicos inteligentes por GEP generadas diariamente, la empresa utiliza la API de .NET SendGrid integrar con Azure. Para los desarrolladores GEP, es fácil: el complemento SendGrid para Azure está disponible directamente en Azure Marketplace. Los desarrolladores GEP podrían configurar inteligente por GEP mediante el uso de la derecha de paquete SendGrid NuGet en Microsoft Visual Studio; TI GEP puede supervisar el tráfico de correo electrónico de SendGrid del software directamente de Azure.

Por último, inteligente por GEP usa máquinas virtuales de Azure, el servicio de Azure IaaS: alojar aplicaciones y servicios que no tenía sentido, en el momento de ingeniería para reemplazar con software como-servicio (SaaS) o soluciones de PaaS. Por ejemplo, GEP aloja servicios de integración de API en máquinas virtuales de integración de negocio a negocio (B2B) con locales planeamiento de recursos empresariales (ERP) sistemas los clientes, como SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP y Lawson y soluciones de cliente SaaS eficazmente intercambiar documentos de compras, como facturas.

> "Edificio inteligente por GEP en la nube de Microsoft Azure se quita por completo la necesidad de local TI, no solo para GEP sino también para operaciones de compras de nuestros clientes." 

> : Nagalkar Dhananjay, VP de soluciones de tecnología

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Expanda la satisfacción del cliente sin expandir TI

Desde la migración de los centros de datos locales a Azure y generar inteligente por GEP desde cero en la plataforma Windows Azure, GEP ha mayor flexibilidad y escalabilidad sin tener que expandir su infraestructura o personal de TI. De hecho, la empresa no ha agregado los recursos de TI en más de cuatro años. El modelo de PaaS cómodo de Azure habilitó GEP reducir sus gastos en administración de soporte técnico y las operaciones de proveedor. Como resultado, GEP ha sido capaz de concentrar los recursos en el desarrollo de software. y desarrollar en la nube permite a los desarrolladores GEP probar rápidamente nuevas ideas sin tener que perder tiempo coordinar con TI o preocuparse local requisitos de licencia. Base de datos de SQL Azure garantiza GEP mejor que sus clientes siempre tienen rendimiento y el servicio excepcional.
 
## <a name="more-information"></a>Más información

- Página de inicio GEP: [GEP](http://www.gep.com)
- Inteligente por GEP: [inteligente por GEP](http://www.smartbygep.com)

##<a name="gep-contributors"></a>Colaboradores GEP

- Huzaifa Matawala, Director de asociar: Architect, GEP
- Sathyan Narasingh, Administrador de ingeniería, GEP
- Deepa Velukutty, arquitecto de base de datos, GEP
