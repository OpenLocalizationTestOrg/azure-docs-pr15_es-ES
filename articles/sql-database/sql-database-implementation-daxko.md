<properties
   pageTitle="Base de datos de SQL Azure Azure caso práctico - Daxko/CSI | Microsoft Azure"
   description="Obtenga más información sobre cómo Daxko/CSI usa la base de datos SQL para acelerar su ciclo de desarrollo y para mejorar sus servicios de atención al cliente y el rendimiento"
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
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI utiliza Azure para acelerar su ciclo de desarrollo y para mejorar sus servicios de atención al cliente y el rendimiento

![Logotipo de Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software de Daxko/CSI se enfrenta a un reto: su base de clientes de los centros de entrenamiento y ocio creció rápidamente, gracias al éxito de su solución completa de software empresarial, pero mantenerse al día con las necesidades de infraestructura de TI para ese cliente creciente base estaba probando la empresa personal de TI. La compañía cada vez más estaba restringida por aumento sobrecarga de operaciones, especialmente para administrar sus crecientes bases de datos. Peor, dicha sobrecarga de operaciones se cortando a recursos de desarrollo de nuevas iniciativas, como las nuevas características de movilidad para el software de la empresa.

Función David Molina, Director de desarrollo de productos en Daxko/CSI, Azure proporcionado CSI Software con el modelo de plataforma como servicio (PaaS) que necesite para simplificar la administración de la base de datos, aumentar la escalabilidad y liberar los recursos para centrarse en software en lugar de operaciones. "La base de datos de SQL azure es una buena opción para nosotros. No tener que preocuparse de mantener un servidor SQL Server, un clúster y todas las necesidades de la infraestructura de otra es ideal para nosotros".

Desde la migración a Azure, Software de CSI necesita personal de operaciones de sólo dos para administrar más de 600 bases de datos de cliente. La empresa utiliza grupos elásticos de base de datos de SQL Azure mover basadas en el tamaño de bases de datos de cliente y necesita.

Molina continúa, "nuestros clientes rotulador el cambio inmediatamente. Antes de grupos elásticas, en ocasiones tenían tiempos de espera y otros problemas durante los períodos de ráfaga. Con los grupos de elásticos Azure, pueden ráfaga según sea necesario y usar el software sin problemas."

Además de mejorar el rendimiento de los clientes, elástica Azure base de datos liberados los recursos de Software de CSI centrarse en el desarrollo de nuevos servicios y características, en lugar de con operaciones y administración de grupos. Los recursos de TI contribuido CSI Software mejorar su software empresarial ofreciendo SpectrumNG para ayudar a integrarse miembros gimnasio, mejorar la eficacia de personal y proporcionar acceso móvil de personal y los miembros para tareas interactivas y notificaciones en tiempo real.

Azure también ha contribuido CSI Software acelerar y mejorar el desarrollo y el ciclo de control de calidad (AC) habilitando opciones de automatización. Con la implementación de Azure de la empresa, los administradores de generación pueden empaquetar los componentes con el clic de botón. Como se describe en Molina, "como parte de su ciclo de lanzamiento, QA es ahora puede implementar en un entorno de prueba de Azure, que imita estrechamente nuestra pila de producción. Podemos implementar versiones inmediatamente a nuestro entorno de desarrollo a investigar los cambios. Que es un gran logro para nosotros, porque no tenemos paridad para realizar pruebas antes de ".

## <a name="offloading-to-the-cloud"></a>Descarga de la nube

Antes de pasar a la nube, Software CSI correctamente había creado su propia infraestructura multiempresa en un centro de datos local en Houston. Como expandido de la compañía, enfrentarse crecientes crecientes dificultades de compras, aprovisionamiento y mantener todo el hardware y software necesario para admitir a sus clientes. TI personal para controlar las operaciones estuvo botella otro que dio lugar a una disminución en el aprovisionamiento de nuevos recursos y distribuir nuevos servicios a los clientes.

Software de CSI examinó en Opciones de la nube para eliminar esa sobrecarga para que podría centrarse en su código, en lugar de sus operaciones. Descubrió muchos de los proveedores de nube superior solo ofrecen las soluciones de infraestructura como servicio (IaaS) que todavía necesitan un gran personal de TI para administrar la pila de IaaS. Al final, Software de CSI determina que la solución de Azure PaaS fue el mejor se adapte a sus necesidades. Se explica Molina "Azure Obtiene el software de hardware y el sistema de forma, por lo que podemos centrar sobre nuestras ofertas de software, reduciendo la carga de TI".

## <a name="making-the-transition-to-azure"></a>Realizar la transición a Azure

Después de seleccionar Azure para su solución de PaaS, Software de CSI comenzó a migrar su infraestructura de back-end y bases de datos en la nube. Antes de Azure, los clientes de SpectrumNG es necesario para instalar una aplicación de cliente que se comunica con un servicio de Windows Communication Foundation (WCF) en el back-end. Función Molina, "aunque algunos clientes hospedado todo el contenido de sus propios centros de datos, creamos el producto sea multiempresa. Todo el contenido de un centro de datos en Houston, se habían hospeda con SQL Server como almacén de datos.

"Nuestro producto ofreciendo también incluye un miembro orientación portal web (escrito en ASP.net), que se ha diseñado para notas etiquetadas para que coincida con la presencia de web del cliente y API SOAP para admitir las páginas en línea y la integración de terceros."

La migración a la nube no pasó mucho tiempo para la arquitectura. Función Molina, "La mayoría del esfuerzo atenderse modificar el modo en que se leer información del archivo de configuración, una modificación de la cadena de conexión centralizada y automatizar el embalaje, cargar e implementación de nuestras versiones".

Para desarrollar la automatización de generación, ingenieros de Software de CSI usan Azure PowerShell y las API de REST para crear paquetes y cargarlos en un entorno de ensayo para versión cada noche.
La transición a una implementación de Azure en la nube global ha encontrado sin problemas y rápidamente al equipo de TI de Software de CSI. Se explica Molina "en todos, tenemos un entorno beta la nube en tres o cuatro semanas de tomar notas en el proyecto. Fue un logro sorprendente nos".

Después de configurar y probar el entorno, Software de CSI comenzó a migrar los clientes. Los clientes que ya está usando el Software de CSI de hospedaje, la transición fue casi perfecta. Migrar desde una implementación local de los clientes, tomó algún tiempo adicional a la migración a la nube, pero se sigue principalmente sin complicaciones para los clientes y CSI Software.

Para los del nuevos clientes, Software de CSI personal de TI usarlos el siguiente proceso para integrada para Azure:

1.  Azure scripts de PowerShell que se usan para el control de una base de datos del cliente; todos los clientes en primer lugar en un nivel premium para garantizar un rendimiento lo suficientemente inicial para la transición.
2.  Cuando sea posible, Software de CSI usa al Asistente para migración de SQL Azure para mover los datos existentes a una instancia de base de datos de SQL Azure.
3.  Por último, Microsoft SQL Server Integration Services (SSIS) se utilizan para reconciliar cualquier discrepancia en los datos o para realizar la limpieza datos según sea necesario.

En la actualidad, 99 por ciento de los clientes de Software de CSI se hospedan en Azure, en cuatro centros de datos regionales (norte Central, sur Central, este y oeste). Al tener los centros de datos en la región geográfica de cada cliente, latencia se mantiene al mínimo.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Grupos de base de datos Azure elástico liberan los recursos de TI

Varias características de Azure han contribuido CSI Software MAYÚS puedan infraestructura y operaciones centradas en que la característica y el desarrollo centrados. Tal vez ha sido el mayor beneficio de grupos de elásticos de la base de datos.

Software de CSI actualmente proporciona aproximadamente 550 bases de datos de los clientes. Antes de grupos elásticos, era difícil administrar muchas bases de datos dentro de una estructura de nivel. Los administradores de operaciones tenían que asignar niveles de rendimiento según las necesidades de ráfaga de clientes, que requieren sobrecarga significativa TI recursos. Con los grupos de elásticos de la base de datos, los administradores pueden asignar a inquilinos una portada o grupo estándar, según corresponda y, a continuación, mover basados en el tamaño de los clientes y necesita. Los clientes rotulador los efectos de los grupos de base de datos elástico casi inmediatamente; antes de grupos elásticos, los clientes tenían tiempos de espera y otros problemas durante los períodos de uso de ráfaga, pero con agrupaciones elásticos, los clientes pueden experimentar ráfagas de actividad según sea necesario y pueden seguir usando SpectrumNG sin problemas.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Replicación de Geo Active Azure acelera informes

Varios clientes de Software de CSI están también aprovechando de Azure Active Geo replicación. Con Active Geo replicación, hasta cuatro bases de datos secundarios legibles pueden configurarse en las regiones del mismo o distinto del centro de datos. Software de CSI hace uso de activos Geo replicación de dos maneras: en primer lugar, las bases de datos secundarios están disponibles en el caso de una interrupción en el centro de datos o el no para conectarse a la base de datos principal; y en segundo término, las bases de datos secundarios se pueden leer y pueden utilizarse para descargar las cargas de trabajo de solo lectura como trabajos de informes. Algunos clientes de Software de CSI utilizan este beneficio para acelerar los flujos de trabajo de generación de informes.

## <a name="csi-software-application-logic-and-architecture"></a>Arquitectura y lógica de la aplicación de Software de CSI

SpectrumNG utiliza las funciones de web. Dado que la aplicación es varios inquilino, un servicio WCF se usa para controlar la solicitud de conexión inicial de los clientes. Como se indica Molina, "la solicitud identifica a cada cliente, lo que nos crear una cadena de conexión a sus bases de datos de hacer cualquier cosa que necesitamos."

Para el nivel de web de su servicio, Software de CSI aprovecha escala automática Azure, en función de fecha y hora. Recursos disponibles se incrementan automáticamente para acomodar un mayor uso del horario laboral, según la zona horaria de cada centro de datos regional. Recursos también se establecen para reducir el tamaño en los fines de semana, cuando son inferiores necesidades de los clientes.

     
![Arquitectura de Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Una función de trabajador de servicios de nube dibuja datos estructurados de base de datos de SQL Azure y datos semiestructurados de almacenamiento de la tabla. SpectrumNG usuarios interactúan con que el rol web de servicios de datos a través de una nube.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Con un nivel de plan de web y aplicaciones web para aplicaciones móviles

Con liberar los recursos para CSI Software de base de datos SQL Azure para habilitar las nuevas iniciativas, una plataforma móvil completa basado en una API personalizada hospedada en aplicaciones web de Azure. La plataforma permite gimnasio y personal para usar dispositivos móviles para comprobar las programaciones, las clases de libro y recibir mensajes.

La plataforma utiliza arquitectura orientada a servicios (SOA) para un único componente, como un sistema de punto de venta (OC) o un sistema de ventas, muévalo sobre la marcha a otro plan de web y, a continuación, poner en marcha un servicio de soporte técnico componente, dejando todo lo demás en el plan original de web. Esta capacidad proporciona una gran flexibilidad de CSI Software y ayuda a reducir los costos.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure le permite la atención de los desarrolladores de Software CSI en aplicaciones y servicios

Base de datos de SQL Azure no gran ayuda a los clientes SpectrumNG, quién disfrutar el servicio rápido y confiable, también es un gran logro para Software de CSI personal de TI y los programadores. Al descargar operaciones en Azure en la nube, Software de CSI había reducido su sobrecarga de recursos y la infraestructura, acelerar considerablemente su ciclos de desarrollo y ya no necesidades a bases de datos de micromanage para optimizar el rendimiento de inquilinos de sus.

## <a name="more-information"></a>Más información

- Para obtener más información acerca de los grupos de la base de datos Azure elástico, vea [grupos de elásticos de la base de datos](sql-database-elastic-pool.md).

- Para obtener más información sobre herramientas de base de datos y escala elástico, vea [Herramientas de base de datos elásticas y escala elástico](sql-database-elastic-scale-get-started.md).

- Para obtener más información sobre cómo migrar una base de datos de SQL Server, consulte el [Asistente para migración de SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Para obtener más información sobre Active Geo replicación, vea [Replicación de Geo Active](sql-database-geo-replication-overview.md).

- Para obtener más información sobre los roles de Web y roles de trabajo, vea [funciones de trabajo](../fundamentals-introduction-to-azure.md#compute). 

- Para obtener más información acerca de Bus de servicio de Azure, consulte [Bus de servicio de Azure](https://azure.microsoft.com/services/service-bus/).

- Para obtener más información sobre cómo ajustar, vea [Servicios de nube de ajuste de escala](../cloud-services/cloud-services-how-to-scale.md).
