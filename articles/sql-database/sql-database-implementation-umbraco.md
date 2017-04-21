<properties
   pageTitle="Base de datos de SQL Azure Azure caso práctico - Umbraco | Microsoft Azure"
   description="Obtenga información sobre cómo Umbraco usa la base de datos SQL aprovisionar rápidamente y servicios de escala miles de inquilinos en la nube"
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
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco usa la base de datos de SQL Azure rápidamente los servicios de aprovisionamiento y escala miles de inquilinos en la nube

![Logotipo de Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco es un sistema de administración de contenido de código abierto popular (CMS) que puede ejecutar nada de sitios de campaña o folletos pequeños a aplicaciones complejas para empresas Fortune 500 y sitios Web de medios global. 

> "Tenemos bastante una gran comunidad de desarrolladores que usan el sistema, con más de 100000 desarrolladores en nuestros foros y sitios más de 350.000 directo, ejecute Umbraco".

> : Umbraco Morten Christensen, director técnico,

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Para simplificar las implementaciones de cliente, Umbraco agregado Umbraco como-servicio (UaaS): una oferta de software como-servicio (SaaS) que elimina la necesidad de implementaciones locales, proporciona escala integrados y quita administración sobrecarga al permitir a los desarrolladores centrarse en la administración de innovaciones en lugar de solución de producto. Umbraco es capaz de proporcionar todos estos beneficios al confiar en el modelo flexible plataforma como servicio (PaaS) ofrecido por Microsoft Azure.

UaaS permite a los clientes de SaaS usar las capacidades de CMS Umbraco estaban fuera de su alcance. Estos clientes se aprovisiona con un entorno de CMS de trabajo que incluye una base de datos de producción. Los clientes pueden agregar hasta dos bases de datos adicionales para entornos de desarrollo y ensayo, según sus necesidades. Cuando se solicita un nuevo entorno, un proceso automatizado asigna a dicho cliente una base de datos automáticamente. La nueva base de datos está listo en segundos, porque la base de datos ya se han configurado previamente por Umbraco desde un grupo de Azure elástico de bases de datos disponibles (consulte la figura 1).


![Ciclo de vida de aprovisionamiento de Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Ciclo de vida de aprovisionamiento de Umbraco como un servicio (UaaS)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Grupos de elásticos Azure y automatización simplifican las implementaciones

Con la base de datos de SQL Azure y otros servicios de Azure, clientes Umbraco automática pueden proporcionar sus entornos y Umbraco supervisar y administrar fácilmente las bases de datos como parte de un flujo de trabajo intuitiva:

1.  Aprovisionar

    Umbraco mantiene una capacidad de 200 previamente aprovisionados bases de datos disponibles desde elásticos grupos. Cuando un cliente se conecta para UaaS, Umbraco proporciona al cliente con un entorno de CMS casi en tiempo real asignándoles una base de datos de la agrupación de disponibilidad.

    Cuando un grupo de disponibilidad llega a su umbral, se crea un nuevo grupo elástico y nuevas bases de datos se aprovisiona previamente que se asignará a los clientes según sea necesario.

    Implementación totalmente automatizada mediante las bibliotecas de administración de C# y colas de Bus de servicio de Azure.

2.  Utilizar

    Los clientes utilizan uno a tres entornos (para producción, ensayo y desarrollo), cada con su propia base de datos. Bases de datos de cliente están en grupos de elásticos de la base de datos, lo que permite Umbraco proporcionar eficaz escala sin tener que exceso aprovisionar.

    ![Información general del proyecto Umbraco](./media/sql-database-implementation-umbraco/figure2.png)

    ![Umbraco detalle del proyecto](./media/sql-database-implementation-umbraco/figure3.png)

    Figura 2. Sitio Web de cliente de Umbraco como-servicio (UaaS), que muestra detalles y descripción general del proyecto

    Base de datos de SQL Azure utiliza a unidades de transacción de base de datos (DTUs) para representar la eficacia relativa necesaria para las transacciones de la base de datos del mundo real. UaaS de los clientes, bases de datos suelen funcionan a 10 DTUs, pero cada tiene la elasticidad escalar a petición. Esto significa que UaaS puede asegurarse de que los clientes siempre tienen recursos necesarios, incluso durante las horas. Por ejemplo, durante un evento de deportes domingo por la noche reciente, una UaaS cliente bases de datos experimentados picos DTUs hasta 100 para la duración del juego. Grupos de elásticos Azure facilitó Umbraco a esa gran demanda sin degradación del rendimiento de soporte técnico.

3.  Monitor

    Monitores Umbraco actividad usar paneles en el portal de Azure, junto con las alertas de correo electrónico personalizado de la base de datos.

4.  Recuperación

    Azure ofrece dos opciones de recuperación (DR): la replicación de Geo Active y Geo restaurar. La opción de DR que debe seleccionar una empresa depende de sus [objetivos de continuidad empresarial](sql-database-business-continuity.md).

    Replicación de Geo Active proporciona el nivel más rápido de respuesta en caso de que el tiempo de inactividad. Con la replicación de Geo Active, puede crear un máximo de cuatro secundarios legibles en servidores de diferentes regiones y, a continuación, puede iniciar migración tras error a cualquiera de los secundarios si se produce un error.

    Umbraco no requiere replicación Geo, pero que aprovechar al máximo de Azure Geo-restaurar para ayudar a asegurar el tiempo de inactividad mínimo en caso de una interrupción. Restaurar geo se basa en copias de seguridad de base de datos en el almacenamiento de Azure geo redundantes. Permite a los usuarios restaurar a partir de una copia de seguridad cuando hay una interrupción en el área principal.

5.  La disposición

    Cuando se elimina un entorno del proyecto, se quitan las bases de datos asociados (desarrollo, provisional o directo) durante la limpieza de cola de Bus de servicio de Azure. Este proceso automatizado restaura las bases de datos sin usar al grupo de elásticos de disponibilidad de base de datos de Umbraco, deja disponibles para los futuros aprovisionamiento conservando utilización máxima.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Grupos de elásticos permiten UaaS escalar con facilidad

Aprovechando agrupaciones de base de datos Azure elástico, Umbraco puede optimizar el rendimiento para sus clientes sin tener que exceso o aprovisionar incompleta. Umbraco actualmente tiene casi 3.000 bases de datos a través de 19 grupos elásticos de la base de datos, con la capacidad de escalar fácilmente según sea necesario para adaptarse a cualquiera de sus 325.000 clientes existentes o nuevos clientes que están preparados para implementar un CMS en la nube.

De hecho, según Morten Christensen, responsable técnico de Umbraco, "UaaS es ahora experimentando crecimiento de 30 nuevos clientes al día. Nuestros clientes están con la comodidad de poder aprovisionar nuevos proyectos en segundos, al instante publicar actualizaciones en sus sitios directo desde un entorno de desarrollo utilizando 'implementación de un solo clic' y realice los cambios como rápidamente si encuentra errores."

Si un cliente no requiere un entorno de segundo y tercer ya, simplemente puede quitar los entornos. Que libera recursos que se pueden usar para los clientes como parte de la agrupación de disponibilidad de base de datos elástico Umbraco.

![Arquitectura de implementación de Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Arquitectura de implementación de UaaS en Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>La ruta de acceso desde el centro de datos en la nube

Cuando los desarrolladores Umbraco inicialmente la decisión de migrar a un modelo de SaaS, sabía que necesitan una manera rentable y scalable para crear el servicio.

> "Agrupaciones de base de datos elásticos son ideal para nuestra oferta de SaaS porque nos podemos marcar capacidad de arriba y abajo según sea necesario. Aprovisionamiento es fácil y con nuestra configuración, podemos evitar utilización máximo."

> : Umbraco Morten Christensen, director técnico,

"Queremos nuestro tiempo en resolver problemas de nuestros clientes, no administración de infraestructura. Queremos que sea más fácil para nuestros clientes aprovechar al máximo,"dice Niels Hartvig, fundador de Umbraco. "Se considera inicialmente los servidores nosotros de hospedaje, pero el planeamiento de capacidad hubiera una pesadilla". Casualmente, Umbraco no emplea los administradores de la base de datos, lo que destaca una propuesta de valor de clave para usar UaaS.

Un objetivo importante para los desarrolladores Umbraco era proporciona una manera de proporcionar entornos de manera rápida y sin las limitaciones de capacidad de los clientes de UaaS. Pero, lo que proporciona un servicio hospedado dedicado en centros de datos Umbraco habría requerido una gran cantidad de capacidad adicional para controlar ráfagas de procesamiento. Que habría significado adición de infraestructura de cálculo considerable habría sido subutilizada con regularidad.

Además, el equipo de desarrollo Umbraco quería una solución que permita que volver a gran parte de su código existente como sea posible. Estados como Umbraco programador Mikkel Madsen, "hemos satisfechos con las herramientas de desarrollo de Microsoft ya estuvieran familiarizados, como Microsoft SQL Server, base de datos SQL de Microsoft Azure, ASP.net y Internet Information Services (IIS). Antes de invertir en un IaaS o un PaaS nube solución, queremos Asegúrese de que se admite nuestras plataformas y herramientas de Microsoft para realizar cambios masivos en nuestro código base no tendríamos. "

Para satisfacer todos sus criterios, Umbraco busca un socio de la nube con las calificaciones siguientes:

-   Suficiente capacidad y confiabilidad
-   Compatibilidad con herramientas de desarrollo de Microsoft, para que no tendrá Umbraco ingenieros convertirse completamente su entorno de desarrollo
-   Presencia en todos los mercados geográficos en el que UaaS compite (empresas debe asegurarse de que puede tener acceso a sus datos rápidamente y que sus datos se almacenan en una ubicación que cumpla los requisitos normativos regionales)

##<a name="why-umbraco-chose-azure-for-uaas"></a>¿Por qué Umbraco eligió Azure para UaaS

Según Morten Christensen "después de considerar todas las opciones de nuestras, seleccionamos Azure porque cumplen todos nuestros criterios de facilidad de uso y escalabilidad a estar familiarizado y rentabilidad. Nos configurar los entornos en máquinas virtuales de Azure y cada entorno tiene su propia instancia de base de datos de SQL Azure con todas las instancias de grupos de elásticos de la base de datos. Al separar bases de datos entre entornos de desarrollo, ensayo y directo, podemos ofrecer nuestros clientes aislamiento sólido rendimiento coincide con escala: un gran logro. "

Morten continúa, "antes, hemos tenido que aprovisionar servidores para bases de datos web manualmente. Ahora, no hay que tener en cuenta. Todo está automatizado, desde el aprovisionamiento de limpieza. "

Morten también está satisfecho con las capacidades escaladas de Azure. "Agrupaciones de base de datos elásticos son ideal para nuestra oferta de SaaS porque nos podemos marcar capacidad de arriba y abajo según sea necesario. Aprovisionamiento es fácil y con nuestra configuración, podemos evitar utilización máximo." Estados Morten, "la simplificar de grupos elásticos, junto con la seguridad de DTUs basada en niveles de servicio, nos proporciona la potencia proporcionando nuevos grupos de recursos a petición. Recientemente, uno de nuestros clientes más grandes puntiagudo a 100 DTUs en su entorno activo. Con Azure, nuestros grupos elásticos proporcionan bases de datos del cliente con los recursos que tenían en tiempo real sin necesidad de predecir los requisitos de DTU. En pocas palabras, nuestros clientes obtengan el tiempo que esperan y podemos satisfacer nuestros contratos de nivel de servicio de rendimiento."

Mikkel Madsen la suma: "Nos hemos adoptado el algoritmo de Azure eficaces que se conecta un escenario de SaaS común (integrado nuevos clientes en tiempo real en escala) a nuestro modelo de aplicación (previamente el aprovisionamiento de bases de datos, desarrollo y live) encima de la tecnología subyacente (mediante colas de Azure Bus de servicio junto con la base de datos SQL Azure)."

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Con Azure, UaaS supera las expectativas del cliente

Desde elegir Azure como su partner de la nube, Umbraco ha sido capaz de proporcionar a los clientes de UaaS performance optimizado de administración de contenido, sin necesidad de la inversión de recursos de TI obligatorio de una solución independiente. Como Morten dice "le gusta la comodidad de desarrollador y escalabilidad que nos de Azure y nuestros clientes son impresionados con las características y la confiabilidad. En general, ha sido un gran logro para nosotros!"
 
## <a name="more-information"></a>Más información

- Para obtener más información acerca de los grupos de la base de datos Azure elástico, vea [grupos de elásticos de la base de datos](sql-database-elastic-pool.md).

- Para obtener más información acerca de Bus de servicio de Azure, consulte [Bus de servicio de Azure](https://azure.microsoft.com/services/service-bus/).

- Para obtener más información sobre los roles de Web y roles de trabajo, vea [funciones de trabajo](../fundamentals-introduction-to-azure.md#compute). 

- Para obtener más información acerca de la red virtual, consulte [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Para obtener más información acerca de la copia de seguridad y recuperación, consulte [continuidad empresarial](sql-database-business-continuity.md).  

- Para obtener más información acerca de cómo supervisar ppols, vea [supervisión de grupos](sql-database-elastic-pool-manage-portal.md). 

- Para obtener más información sobre Umbraco como un servicio, consulte [Umbraco](https://umbraco.com/cloud).

