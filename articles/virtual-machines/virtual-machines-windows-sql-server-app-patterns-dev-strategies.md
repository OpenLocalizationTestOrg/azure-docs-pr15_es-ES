<properties
    pageTitle="Patrones de aplicaciones de SQL Server en máquinas virtuales | Microsoft Azure"
    description="En este artículo trata sobre patrones de aplicaciones para SQL Server en máquinas virtuales de Azure. Proporciona solución arquitectos y desarrolladores una base para el diseño y la arquitectura de la aplicación correcta."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Patrones de aplicaciones y estrategias de desarrollo de SQL Server en máquinas virtuales de Windows Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Resumen:
Determinar qué modelo de aplicación o patrones para usar para las aplicaciones basadas en servidor de SQL en Azure entorno es una decisión importante de diseño y requiere un sólido entendimiento del funcionan conjunto de SQL Server y cada componente de infraestructura de Azure. Con SQL Server en servicios de infraestructura de Azure, fácilmente puede migrar, mantener y supervisar las aplicaciones de SQL Server existentes integradas de Windows Server en máquinas virtuales de Azure.

El objetivo de este artículo es proporcionar una base de solución arquitectos y desarrolladores de buena arquitectura de aplicaciones y diseño, que pueden seguir al migrar aplicaciones existentes a Azure así como desarrollar nuevas aplicaciones en Azure.

Para cada modelo de aplicación, encontrará un escenario local, su solución de nube habilitada respectivo y las recomendaciones técnicas relacionadas. Además, el artículo explica estrategias de desarrollo de Azure específicos para que las aplicaciones puede diseñar correctamente. Debido a los patrones de muchas posibles de la aplicación, se recomienda que los diseñadores y programadores deben elegir el modelo más adecuado para sus aplicaciones y los usuarios.

**Colaboradores técnicos:** Luis Carlos Vargas arenque, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introducción

Puede desarrollar varios tipos de aplicaciones de n niveles al separar los componentes de las capas de aplicación diferente en equipos distintos, así como en los distintos componentes. Por ejemplo, puede colocar la aplicación cliente y reglas de negocios de componentes en un equipo, el nivel de web front-end y componentes de nivel de acceso a datos en otro equipo y un nivel de base de datos back-end en otro equipo. Este tipo de estructurar ayuda a aislar cada nivel entre sí. Si cambia de dónde proceden los datos, no es necesario cambiar la aplicación web o el cliente, pero los componentes del nivel de acceso a datos.

Una aplicación típica *capas* incluye el nivel de presentación, el nivel empresarial y el nivel de datos:


| Nivel              | Descripción                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Presentación** | El *nivel de presentación* (nivel de web y nivel de front-end) es la capa en la que los usuarios interactuar con una aplicación.                                                                      |
| **Empresa**     | El *nivel de empresa* (nivel intermedio) es la capa que el nivel de presentación y el nivel de datos utilizar para comunicarse con ellos e incluye la funcionalidad básica del sistema. |
| **Datos**         | El *nivel de datos* es básicamente el servidor que almacena los datos de la aplicación (por ejemplo, un servidor que ejecuta SQL Server).                                                             |

Capas de aplicación, describen las agrupaciones lógicas de la funcionalidad y los componentes de una aplicación; mientras que niveles describen la distribución física de los componentes y la funcionalidad en servidores físicos independientes, equipos, redes o ubicaciones remotas. Las capas de una aplicación pueden residen en el mismo equipo físico (el mismo nivel) o pueden estar distribuidas por equipos independientes (n niveles), y los componentes de cada capa comunican con componentes de otras capas a través de interfaces bien definidas. Puede considerar que el término nivel como que hacen referencia a los patrones de distribución físicos como dos niveles, tres niveles y capas. Un **modelo de aplicación de nivel 2** contiene dos niveles de la aplicación: servidor de aplicaciones y servidor de base de datos. Pasa la comunicación directa entre el servidor de aplicaciones y el servidor de base de datos. El servidor de aplicaciones contiene los componentes de nivel de web y de nivel empresarial. En **el modelo de aplicación de nivel 3**, hay tres niveles de la aplicación: web server, servidor de aplicaciones, que contiene el nivel de la lógica empresarial o los componentes de acceso de datos de nivel de empresa y el servidor de base de datos. La comunicación entre el servidor web y el servidor de base de datos se pasa por el servidor de aplicaciones. Para obtener información detallada sobre los niveles y capas de la aplicación, vea la [Guía de arquitectura de aplicaciones de Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de empezar a leer este artículo, debe tener conocimientos sobre los conceptos fundamentales de SQL Server y Azure. Para obtener más información, vea [Libros en pantalla de SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md) y [Azure.com](https://azure.microsoft.com/).

En este artículo se describe varios patrones de aplicación que pueden ser adecuados para las aplicaciones simples, así como las aplicaciones empresariales muy complejos. Antes de que detalla cada trama, le recomendamos que debe familiarizarse con los servicios de almacenamiento de datos disponibles en Azure, como [El almacenamiento de Azure](../storage/storage-introduction.md), [Base de datos de SQL Azure](../sql-database/sql-database-technical-overview.md)y [SQL Server en una máquina Virtual de Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tomar las mejores decisiones de diseño para sus aplicaciones, comprender cuándo usar qué servicio de almacenamiento de datos con claridad.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Seleccione SQL Server en una máquina Virtual Azure, cuando:

- Necesita control en SQL Server y Windows. Por ejemplo, podría incluir la versión de SQL Server, revisiones especiales, configuración de rendimiento, etcetera.

- Se necesita una compatibilidad completa con SQL Server local y desea mover las aplicaciones existentes a Azure como-es.

- Desea aprovechar las capacidades del entorno de Azure pero la base de datos de SQL Azure no admite todas las características que requiere la aplicación. Esto podría incluir las siguientes áreas:

    - **Tamaño de la base de datos**: en el momento en que se ha actualizado en este artículo, base de datos SQL es compatible con una base de datos de hasta 1 TB de datos. Si la aplicación requiere más de 1 TB de datos y no desea implementar soluciones sharding personalizado, se recomienda usar SQL Server en una máquina Virtual de Azure. Para obtener la información más reciente, consulte [Escala a Azure bases de datos SQL](https://msdn.microsoft.com/library/azure/dn495641.aspx) y [niveles de servicio de base de datos de SQL Azure y niveles de rendimiento](../sql-database/sql-database-service-tiers.md).
    - **Cumplimiento de HIPAA**: salud clientes y proveedores de Software independientes (ISV) pueden elegir [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md) en lugar de la [Base de datos de SQL Azure](../sql-database/sql-database-technical-overview.md) porque SQL Server en una máquina Virtual de Azure está cubierto por el contrato de asociar de negocio de HIPAA (BAA). Para obtener información sobre el cumplimiento, vea [Microsoft Azure Trust Center: cumplimiento](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Características de nivel de instancia**: en este momento, base de datos SQL no admite características que se encuentran fuera de la base de datos (como servidores vinculados, agente de trabajo, FileStream, agente de servicio, etcetera.). Para obtener más información, vea [directrices de base de datos de SQL Azure y limitaciones](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>nivel 1 (simple): única máquina virtual

En este modelo de aplicación, se implementan la aplicación de SQL Server y la base de datos en una máquina virtual de independiente en Azure. La misma máquina virtual contiene la aplicación web del cliente, componentes empresariales, capa de acceso a datos y el servidor de base de datos. La presentación, la empresa y el código de acceso de datos separados lógicamente pero se encuentran en un equipo de un solo servidor. Iniciar la mayoría de los clientes con este modelo de aplicación y, a continuación, escalado horizontal agregando más funciones web o máquinas virtuales a su sistema.

En este modelo de aplicación es útil cuando:

- Desea realizar una migración simple a la plataforma Windows Azure para evaluar si responde a la plataforma de requisitos de la aplicación o no.

- Desea conservar todos los niveles de aplicación alojados en la misma máquina virtual en el mismo centro de datos de Azure para reducir la latencia entre niveles.

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Desea realizar pruebas de esfuerzo para diversos niveles de carga de trabajo, pero al mismo tiempo no desea propietario y mantener muchas máquinas virtuales de todo el tiempo.

El siguiente diagrama se muestra una sencilla local situación y cómo puede implementar su solución de nube habilitada en una sola máquina virtual en Azure.

![modelo de aplicación de nivel 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implementar la capa de negocio (lógica empresarial y componentes de acceso a datos) en el mismo nivel físico como capa de presentación puede maximizar el rendimiento de la aplicación, a menos que se debe usar un nivel independiente debido a problemas de escalabilidad o seguridad.

Puesto que se trata de un modelo muy común inicialmente, útil el siguiente artículo de migración para mover los datos a su VM de SQL Server: [migrar una base de datos de SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>nivel 3 (simple): varias máquinas virtuales

En este modelo de aplicación, se implemente una aplicación de nivel 3 en Azure colocando cada nivel de la aplicación en una máquina virtual diferente. Esto proporciona un entorno flexible para un fácil escenarios de escalado y escalado. Cuando una máquina virtual contiene la aplicación web del cliente, las otras uno aloja los componentes empresariales y los demás uno aloja el servidor de base de datos.

En este modelo de aplicación es útil cuando:

- Desea realizar una migración de aplicaciones de base de datos complejos a Azure máquinas virtuales de Windows.

- Desea diferentes niveles de aplicaciones para estar hospedadas en diferentes regiones. Por ejemplo, podría han compartido bases de datos que se implementan en varias áreas para generar informes.

- Desea mover las aplicaciones empresariales de plataformas virtualizado en local a Azure máquinas virtuales de Windows. Para obtener información detallada en aplicaciones empresariales, consulte [¿Qué es una aplicación de empresa](https://msdn.microsoft.com/library/aa267045.aspx).

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Desea realizar pruebas de esfuerzo para diversos niveles de carga de trabajo, pero al mismo tiempo no desea propietario y mantener muchas máquinas virtuales de todo el tiempo.

El diagrama siguiente muestra cómo puede colocar una aplicación de nivel 3 simple en Azure colocando cada nivel de la aplicación en una máquina virtual diferente.

![modelo de aplicación de nivel 3](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

En este modelo de aplicación, hay solo una máquina virtual () en cada nivel. Si tiene varios VM en Azure, se recomienda configurar una red virtual. [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md) crea un límite de seguridad de confianza y también permite máquinas virtuales para comunicarse entre sí a través de la dirección IP privada. Además, siempre asegúrese de que todas las conexiones a Internet sólo ir al nivel de presentación. Cuando se siguen este modelo de aplicación, administrar las reglas de grupo de seguridad de red para controlar el acceso. Para obtener más información, vea [Permitir el acceso externo a su máquina virtual con el portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md).

En el diagrama, protocolos de Internet puede ser TCP, UDP, HTTP o HTTPS.

>[AZURE.NOTE] Configurar una red virtual en Azure es de forma gratuita. Sin embargo, se cargan de la puerta de enlace VPN que se conecta a local. Este cargo se basa en la cantidad de tiempo que la conexión se aprovisiona y está disponible.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>escalado de nivel 2 y 3 niveles con la presentación

En este modelo de aplicación, implementar aplicación de base de datos de 2 o 3 niveles a Azure máquinas virtuales de Windows colocando cada nivel de la aplicación en una máquina virtual diferente. Además, escala el nivel de presentación debido a mayor volumen de solicitudes de cliente entrantes.

En este modelo de aplicación es útil cuando:

- Desea mover las aplicaciones empresariales de plataformas virtualizado en local a Azure máquinas virtuales de Windows.

- Desea ajustar el nivel de presentación debido a mayor volumen de solicitudes de cliente entrantes.

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Desea realizar pruebas de esfuerzo para diversos niveles de carga de trabajo, pero al mismo tiempo no desea propietario y mantener muchas máquinas virtuales de todo el tiempo.

- Desea el propietario de un entorno de infraestructura que puede escalar hacia arriba y hacia abajo a petición.

El diagrama siguiente muestra cómo puede colocar los niveles de aplicación en varios equipos virtuales en Azure escalado el nivel de presentación debido a mayor volumen de solicitudes de cliente entrantes. Tal como se muestra en el diagrama, equilibrador de carga de Azure es responsable de distribuir tráfico en varios equipos virtuales y también determinar qué servidor web al que conectarse. Tener varias instancias de los servidores web detrás de un equilibrador de carga garantiza la alta disponibilidad del nivel de la presentación.

![Modelo de aplicación - escala de nivel de presentación horizontal](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Prácticas recomendadas para tramas de nivel 2, nivel 3 o n niveles que tienen varias VM en un nivel

Se recomienda que coloque las máquinas virtuales que pertenecen al mismo nivel en el mismo servicio de nube y en la misma la disponibilidad establecer. Por ejemplo, coloque un conjunto de servidores web en **CloudService1** y **AvailabilitySet1** y un conjunto de servidores de base de datos en **CloudService2** y **AvailabilitySet2**. Una disponibilidad establecer en Azure le permite colocar los nodos de alta disponibilidad en dominios de error independiente y actualizar los dominios.

Para aprovechar varias instancias de máquina virtual de un nivel, debe configurar equilibrador de carga de Azure entre niveles de la aplicación. Para configurar el equilibrio de carga en cada nivel, crear un extremo de equilibrio de carga en máquinas virtuales de cada nivel por separado. Para un nivel específico, crear máquinas virtuales en el mismo servicio de nube. Esto garantiza que tienen la misma dirección IP Virtual pública. A continuación, cree un punto final en una de las máquinas virtuales en ese nivel. A continuación, asignar el mismo extremo a los otros equipos virtuales en ese nivel de equilibrio de carga. Mediante la creación de un conjunto de equilibrio de carga, distribuir el tráfico entre varias máquinas virtuales y también permiten la equilibrador de carga determinar qué nodo conectarse cuando se produce un error en un nodo VM back-end. Por ejemplo, tener varias instancias de los servidores web detrás de un equilibrador de carga garantiza la alta disponibilidad del nivel de la presentación.

Como práctica recomendada, siempre asegúrese de que todas las conexiones a internet vaya a nivel de presentación. El nivel de presentación tiene acceso a nivel de empresa y, a continuación, el nivel de empresa tiene acceso el nivel de datos. Para obtener más información acerca de cómo permitir el acceso a la capa de presentación, vea [Permitir el acceso externo a su máquina virtual con el portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Observe que el equilibrador de carga en Azure funciona similar a equilibradores en un entorno local de carga. Para obtener más información, vea [Equilibrio de carga de servicios de infraestructura de Azure](virtual-machines-windows-load-balance.md).

Además, se recomienda que configurar una red privada para las máquinas virtuales mediante red Virtual de Azure. Esto les permite comunicarse entre sí a través de la dirección IP privada. Para obtener más información, consulte [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>escalado de nivel 2 y 3 niveles con business

En este modelo de aplicación, implementar aplicación de base de datos de 2 o 3 niveles a Azure máquinas virtuales de Windows colocando cada nivel de la aplicación en una máquina virtual diferente. Además, que desea distribuir los componentes de servidor de aplicaciones para varios equipos virtuales debido a la complejidad de la aplicación.

En este modelo de aplicación es útil cuando:

- Desea mover las aplicaciones empresariales de plataformas virtualizado en local a Azure máquinas virtuales de Windows.

- Desea distribuir los componentes de servidor de aplicaciones para varios equipos virtuales debido a la complejidad de la aplicación.

- Desea mover visible de lógica de negocios en local a Azure máquinas virtuales de Windows las aplicaciones LOB (línea de negocio). Aplicaciones LOB son un conjunto de aplicaciones de equipo críticas que son fundamentales para ejecutar una empresa, por ejemplo, Contabilidad, recursos humanos (HR), nómina, administración de la cadena de suministro y aplicaciones de planeación de recursos.

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Desea realizar pruebas de esfuerzo para diversos niveles de carga de trabajo, pero al mismo tiempo no desea propietario y mantener muchas máquinas virtuales de todo el tiempo.

- Desea el propietario de un entorno de infraestructura que puede escalar hacia arriba y hacia abajo a petición.

En el diagrama siguiente se muestra un escenario de local y su solución de nube habilitada. En este escenario, que colocan los niveles de la aplicación en varios equipos virtuales en Azure escalado el nivel de empresa, que contiene el nivel de la lógica empresarial y los componentes de acceso a datos. Tal como se muestra en el diagrama, equilibrador de carga de Azure es responsable de distribuir tráfico en varios equipos virtuales y también determinar qué servidor web al que conectarse. Tener varias instancias de los servidores de aplicaciones detrás de un equilibrador de carga garantiza la alta disponibilidad del nivel de la empresa. Para obtener más información, consulte [prácticas recomendadas de nivel 2, 3 niveles o modelos de aplicación de n niveles que tienen varias máquinas virtuales de un nivel](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Modelo con escala de nivel empresarial fuera de la aplicación](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 y 3 niveles con presentación y empresas niveles escalado y HADR

En este modelo de aplicación, implementar aplicación de base de datos de 2 o 3 niveles a Azure máquinas virtuales de Windows mediante la distribución de los componentes de (servidor de aplicaciones) de nivel de empresa a varias máquinas virtuales y el nivel de presentación (servidor web). Además, implementar soluciones de recuperación de desastres y alta disponibilidad para las bases de datos en máquinas virtuales de Windows Azure.

En este modelo de aplicación es útil cuando:

- Que desea mover las aplicaciones empresariales de plataformas virtualizado local a Azure implementando SQL Server alta disponibilidad y capacidad de recuperación.

- Desea ajustar el nivel de presentación y el nivel de empresa debido a mayor volumen de solicitudes de cliente entrantes y la complejidad de la aplicación.

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Desea realizar pruebas de esfuerzo para diversos niveles de carga de trabajo, pero al mismo tiempo no desea propietario y mantener muchas máquinas virtuales de todo el tiempo.

- Desea el propietario de un entorno de infraestructura que puede escalar hacia arriba y hacia abajo a petición.

En el diagrama siguiente se muestra un escenario de local y su solución de nube habilitada. En este escenario, escala el nivel de presentación y los componentes de nivel de empresa en varios equipos virtuales en Azure. Además, implementar alta disponibilidad técnicas y desastres recuperación (HADR) para bases de datos de SQL Server en Azure.

Ejecutar varias copias de una aplicación en diferentes máquinas virtuales Asegúrese de que son de equilibrio de carga solicitudes entre ellos. Cuando haya varias máquinas virtuales, debe para asegurarse de que todos sus máquinas virtuales sean accesibles y ejecución en un momento en el tiempo. Si configura el equilibrio de carga, equilibrio de carga de Azure realiza un seguimiento del estado de máquinas virtuales y dirige las llamadas entrantes a los nodos VM funcionamiento correcto correctamente. Para obtener información sobre cómo configurar el equilibrio de carga de los equipos virtuales, vea [Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-windows-load-balance.md). Tener varias instancias de aplicaciones y servidores web detrás de un equilibrador de carga garantiza la alta disponibilidad de los niveles de presentación y empresa.

![Escalado y alta disponibilidad](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Prácticas recomendadas para modelos de aplicación que requieren SQL HADR

Al configurar alta disponibilidad de SQL Server y las soluciones de recuperación de desastres en Azure máquinas virtuales de Windows, la configuración de una red virtual para las máquinas virtuales con [Una red Virtual Azure](../virtual-network/virtual-networks-overview.md) es obligatoria.  Máquinas virtuales dentro de una red Virtual tendrá una dirección IP privada estable incluso después de un tiempo de inactividad de servicio, lo que puede evitar la hora de actualización necesaria para la resolución de nombres DNS. Además, la red virtual le permite ampliar su red local a Azure y crea un límite de seguridad de confianza. Por ejemplo, si su aplicación tiene las restricciones de dominio corporativo (por ejemplo, la autenticación de Windows, Active Directory), la configuración de [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md) es necesaria.

La mayoría de los clientes, que se están ejecutando el código de producción en Azure, mantiene réplicas primaria y secundarias en Azure.

Para obtener información completa y tutoriales en alta disponibilidad y técnicas de recuperación de desastres, consulte [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>con máquinas virtuales de Azure y servicios de nube de nivel 3 y 2

En este modelo de aplicación, implementar aplicación de 2 o 3 niveles en Azure con ambos [Servicios de nube de Azure](../cloud-services/cloud-services-choose-me.md#tellmecs) (funciones web y trabajador - plataforma como servicio (PaaS)) y [máquinas virtuales de Azure](virtual-machines-windows-about.md) (infraestructura como servicio (IaaS)). Usar [Servicios de nube de Azure](https://azure.microsoft.com/documentation/services/cloud-services/) el nivel de negocio y de nivel de presentación y SQL Server en [máquinas virtuales de Azure](virtual-machines-windows-about.md) para el nivel de datos es útil para la mayoría de las aplicaciones ejecuta en Azure. El motivo es que tener una instancia de cálculo que se ejecuta en servicios de nube proporciona una fácil administración, implementación, supervisión y escalado.

Con servicios de nube, Azure mantiene la infraestructura para usted, realiza mantenimiento, revisiones de los sistemas operativos e intenta recuperarse de errores de hardware y servicio. Cuando la aplicación necesita escalado, opciones de escalado de automáticas y manuales están disponibles para el proyecto de servicio de nube por aumentar o reducir el número de instancias o máquinas virtuales que utilizan la aplicación. Además, puede usar local Visual Studio para implementar la aplicación en un proyecto de servicio de nube en Azure.

En resumen, si no desea que el propietario extensiva nivel de las tareas administrativas para el negocio y presentación y la aplicación no requiere ninguna configuración compleja de software o el sistema operativo, usar los servicios de nube de Azure. Si la base de datos de SQL Azure no admite todas las características que está buscando, use SQL Server en una máquina Virtual de Azure para el nivel de datos. Ejecutar una aplicación en servicios de nube de Azure y almacenar los datos en Azure máquinas virtuales de Windows combina las ventajas de ambos servicios. Para obtener una comparación detallada, consulte la sección de este tema en [estrategias de desarrollo de comparación en Azure](#comparing-web-development-strategies-in-azure).

En este modelo de aplicación, el nivel de presentación incluye una función de web, que es un componente de servicios de nube que se ejecuta en el entorno de ejecución de Azure y se personaliza para la programación de aplicaciones web que admite IIS y ASP.NET. El nivel de empresa o back-end incluye una función de trabajo, que es un componente de servicios de nube que se ejecuta en el entorno de ejecución de Azure y es útil para el desarrollo generalizado y puede realizar el procesamiento en segundo plano para un rol web. El nivel de la base de datos reside en una máquina virtual de SQL Server en Azure. La comunicación entre el nivel de presentación y la base de datos pasa directamente o a través de la capa empresarial: componentes de la función de trabajo.

En este modelo de aplicación es útil cuando:

- Que desea mover las aplicaciones empresariales de plataformas virtualizado local a Azure implementando SQL Server alta disponibilidad y capacidad de recuperación.

- Desea el propietario de un entorno de infraestructura que puede escalar hacia arriba y hacia abajo a petición.

- Base de datos de SQL Azure no admite todas las características que necesita la aplicación o la base de datos.

- Desea realizar pruebas de esfuerzo para diversos niveles de carga de trabajo, pero al mismo tiempo no desea propietario y mantener muchas máquinas virtuales de todo el tiempo.

En el diagrama siguiente se muestra un escenario de local y su solución de nube habilitada. En este escenario, coloque el nivel de presentación en roles de web, el nivel de empresa en roles de trabajo, pero el nivel de datos en máquinas virtuales de Azure. La ejecución de varias copias del nivel de la presentación en roles diferentes web garantiza para cargar equilibrar las solicitudes entre ellos. Cuando se combinan servicios de nube de Azure con Azure máquinas virtuales de Windows, se recomienda que configure [Una red Virtual Azure](../virtual-network/virtual-networks-overview.md) también. Con [Una red Virtual Azure](../virtual-network/virtual-networks-overview.md), puede tener estables y persistentes direcciones IP dentro del mismo servicio de nube en la nube. Una vez que define una red virtual para las máquinas virtuales y servicios de nube, pueden iniciar comunicación entre sí a través de la dirección IP privada. Además, tener máquinas virtuales y roles de web y trabajador de Azure en la misma [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md) proporciona baja latencia y conectividad sea más segura. Para obtener más información, consulte [¿Qué es un servicio de nube](../cloud-services/cloud-services-choose-me.md).

Tal como se muestra en el diagrama, equilibrador de carga de Azure distribuye el tráfico entre varias máquinas virtuales y también determina qué servidor web o un servidor de aplicaciones para conectarse a. Tener varias instancias de los aplicaciones y servidores web detrás de un equilibrador de carga garantiza la alta disponibilidad de nivel de presentación y el nivel de empresa. Para obtener más información, consulte [prácticas recomendadas para modelos de aplicación que requieren SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Patrones de aplicaciones con servicios de nube](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Otra implementar este modelo de aplicación consiste en usar una función de web consolidada que contiene el nivel de presentación y los componentes de nivel empresarial tal como se muestra en el diagrama siguiente. En este modelo de aplicación es útil para aplicaciones que requieren diseño con estado. Dado que Azure proporciona nodos de cálculo independientes en roles de web y trabajador, recomendamos que implementar una lógica para almacenar el estado de sesión con una de las siguientes tecnologías: [Almacenamiento en caché de Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Almacenamiento de tablas de Azure](../storage/storage-dotnet-how-to-use-tables.md) o [Base de datos de SQL Azure](../sql-database/sql-database-technical-overview.md).

![Patrones de aplicaciones con servicios de nube](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Modelo con máquinas virtuales de Azure, base de datos SQL Azure y Azure de aplicación de servicio (aplicaciones Web)

Es el objetivo principal de este modelo de aplicación mostrar cómo combinar Azure infraestructura como un componentes de servicio (IaaS) con Azure componentes de plataforma como servicio (PaaS) en la solución. Este patrón se centra en base de datos de SQL Azure almacenamiento de datos relacional. No incluye SQL Server en una máquina virtual Azure, que forma parte de la infraestructura de Azure como una oferta de servicio.

En este modelo de aplicación, implementar una aplicación de base de datos en Azure colocando los niveles de presentación y la empresa en la misma máquina virtual y obtener acceso a una base de datos en servidores de base de datos de Azure SQL (base de datos de SQL). Puede implementar el nivel de presentación mediante soluciones web basados en IIS tradicional. O bien, puede implementar una presentación combinada y nivel empresarial con [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

En este modelo de aplicación es útil cuando:

- Ya tiene un servidor de base de datos SQL existente configurado en Azure y desea probar la aplicación rápidamente.

- Desea probar las capacidades del entorno de Azure.

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Los componentes de acceso a datos y lógica de empresa pueden ser independientes en una aplicación web.

En el diagrama siguiente se muestra un escenario de local y su solución de nube habilitada. En este escenario, coloque los niveles de aplicación en una sola máquina virtual en Azure y acceso a datos en la base de datos de SQL Azure.

![Modelo de aplicación mixto](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Si elige implementar web combinada y el nivel de aplicación mediante aplicaciones Web de Azure, le recomendamos que mantenga el nivel de aplicación o de nivel intermedio como bibliotecas de vínculos dinámicos (DLL) en el contexto de una aplicación web.

Además, revise las recomendaciones indicadas en la sección de [estrategias de desarrollo de web de comparación de Azure](#comparing-web-development-strategies-in-azure) al final de este artículo para obtener más información sobre técnicas de programación.

## <a name="n-tier-hybrid-application-pattern"></a>Modelo de aplicación de N niveles híbrido

En el modelo de aplicación de n niveles híbrido, implementar la aplicación en varios niveles que se distribuye entre Azure y local. Por lo tanto, crear un sistema híbrido flexible y reutilizable, que puede modificar o agregar un nivel específico sin cambiar otros niveles. Para ampliar su red corporativa a la nube, use el servicio de [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md) .

En este modelo de aplicación híbrido es útil cuando:

- Desea generar aplicaciones que se ejecutan parcialmente en la nube y parcialmente local.

- Desea migrar algunos o todos los elementos de una aplicación local existente en la nube.

- Desea mover las aplicaciones empresariales de plataformas virtualizado en local a Azure.

- Desea el propietario de un entorno de infraestructura que puede escalar hacia arriba y hacia abajo a petición.

- Desea rápidamente aprovisionar desarrollo y entornos de prueba durante breves períodos de tiempo.

- Desea una manera rentable para realizar copias de seguridad para las aplicaciones de base de datos empresariales.

El siguiente diagrama muestra un patrón de aplicación de n niveles híbrido abarca Azure y local. Como se muestra en el diagrama, infraestructura local incluye el controlador de dominio de [Los servicios de dominio de Active Directory](https://technet.microsoft.com/library/hh831484.aspx) para admitir autenticación de usuario y la autorización. Tenga en cuenta que el diagrama se muestra un escenario, donde algunas partes de la capa de datos live en un centro de datos local que algunas partes de la capa de datos live en Azure. Según las necesidades de la aplicación, puede implementar otros escenarios híbrido. Por ejemplo, puede mantener el nivel de presentación y el nivel de empresa en un entorno local, pero el nivel de datos de Azure.

![Modelo de aplicación de N niveles](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

En Azure, puede usar Active Directory como un directorio de nube independiente para su organización, o también puede integrar de Active Directory local existente con [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Tal como se muestra en el diagrama, los componentes de nivel de empresa pueden acceder a varios orígenes de datos, como [SQL](virtual-machines-windows-sql-server-iaas-overview.md) Server en Azure a través de una dirección IP interna privada, local de SQL Server a través de [Una red Virtual Azure](../virtual-network/virtual-networks-overview.md)o a la [Base de datos de SQL](../sql-database/sql-database-technical-overview.md) con las tecnologías de proveedor de datos de .NET Framework. En este diagrama, la base de datos de SQL Azure es un servicio de almacenamiento de datos opcional.

En el modelo de aplicación de n niveles híbrido, puede implementar el flujo de trabajo siguiente en el orden especificado:

1. Identificar las aplicaciones de base de datos empresariales que necesitan para moverse a la nube mediante la [evaluación de Microsoft y Kit de herramientas de Planning (MAP)](http://microsoft.com/map). El Kit de herramientas de mapa reúne los datos de inventario y el rendimiento de equipos que piensa para virtualización y proporciona recomendaciones sobre la capacidad y planificación de evaluación.

1. Planear los recursos y la configuración necesaria en la plataforma Windows Azure, como las cuentas de almacenamiento y máquinas virtuales de Windows.

1. Configurar la conectividad de red entre la red corporativa local y la [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md). Para configurar la conexión entre la red corporativa local y una máquina virtual en Azure, use uno de los dos métodos siguientes:

    1. Establecer una conexión entre local y Azure a través de públicos puntos finales en una máquina virtual en Azure. Este método facilita la instalación y le permite usar autenticación de SQL Server en la máquina virtual. Además, configurar las reglas de grupo de seguridad de red para controlar el tráfico público a la máquina virtual. Para obtener más información, vea [Permitir el acceso externo a su máquina virtual con el portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Establecer una conexión entre local y Azure a través Azure Virtual privada túnel de red (VPN). Este método le permite ampliar las directivas de dominio a una máquina virtual en Azure. Además, puede configurar reglas de firewall y usar autenticación de Windows en su máquina virtual. Actualmente, Azure admite VPN de sitio a sitio seguro y conexiones VPN punto a sitio:

        - Con conexión a sitios segura, puede establecer la conectividad de red entre su red local y la red virtual en Azure. Se recomienda para conectar su entorno de centro de datos locales con Azure.

        - Con conexión punto a sitio segura, puede establecer la conectividad de red entre su red virtual en Azure y los equipos individuales que se ejecutan en cualquier lugar. Se recomienda principalmente para fines de pruebas y desarrollo.

    Para obtener información sobre cómo conectarse a SQL Server en Azure, vea [conectarse a una máquina Virtual de SQL Server en Azure](virtual-machines-windows-classic-sql-connect.md).

1. Configurar tareas programadas y alertas de copia de seguridad local de datos en un disco de máquina virtual en Azure. Para obtener más información, vea [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Windows Azure](https://msdn.microsoft.com/library/jj919148.aspx) y [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-backup-recovery.md).

1. Según las necesidades de la aplicación, puede implementar uno de los siguientes tres escenarios comunes:

    1. Puede mantener el servidor web, el servidor de aplicaciones y los datos sin tener en cuenta en un servidor de base de datos de Azure mientras que mantener datos confidenciales local.

    1. Puede mantener el servidor web y servidor de aplicaciones local mientras que el servidor de base de datos en una máquina virtual en Azure.

    1. Puede mantener el servidor de base de datos, el servidor web y servidor de aplicaciones local mientras que mantener las réplicas de la base de datos en máquinas virtuales de Azure. Esta configuración permite a los servidores de web de local o aplicaciones de generación de informes para tener acceso a las réplicas de la base de datos de Azure. Por lo tanto, se puede conseguir para reducir la carga de trabajo en una base de datos local. Le recomendamos que implementar este escenario para visible cargas de trabajo y efectos para el desarrollo de lectura. Para obtener información sobre cómo crear réplicas de la base de datos de Azure, consulte grupos de disponibilidad AlwaysOn en [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparación de estrategias de desarrollo de web en Azure

Para implementar una aplicación basada en SQL Server en Azure de varios niveles, puede usar uno de los dos métodos de programación siguientes:

- Configurar un servidor web tradicional (IIS - Internet Information Services) en Azure y acceso a bases de datos de SQL Server en Azure máquinas virtuales de Windows.

- Implementar e implementar un servicio de nube en Azure. A continuación, asegúrese de que este servicio de nube puede tener acceso a bases de datos de SQL Server en máquinas virtuales de Azure. Un servicio de nube puede incluir varias funciones de web y trabajador.

La tabla siguiente proporciona una comparación de desarrollo de web tradicional con los servicios de nube de Azure y Azure Web Apps con respecto a SQL Server en Azure máquinas virtuales de Windows. La tabla incluye aplicaciones Web de Azure como es posible usar SQL Server en máquina virtual de Azure como origen de datos para aplicaciones Web de Azure a través de su dirección IP virtual pública o el nombre DNS.

||Desarrollo de web tradicional en Azure máquinas virtuales de Windows|Servicios de nube de Azure|Hospedaje con aplicaciones Web de Azure|
|---|---|---|---|
|**Migración de aplicaciones locales**|Las aplicaciones existentes como-es.|Las aplicaciones necesitan los roles de web y trabajador.|Las aplicaciones existentes como-pero sirve para autocontenido aplicaciones y servicios web que requieren escalabilidad rápida.|
|**Desarrollo e implementación**|Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, el Administrador de IIS, PowerShell.|Visual Studio, SDK Azure, TFS, PowerShell. Cada servicio de nube tiene dos entornos a los que puede implementar la configuración y el paquete de servicio: ensayo y producción. Puede implementar un servicio de nube en el entorno de ensayo para probarlo antes de ascender a producción.|Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, mercuriano, TFS, Web implementar PowerShell.|
|**Administración y configuración**|Usted es responsable de las tareas administrativas en la aplicación, datos, las reglas de firewall, una red virtual y sistema operativo.|Usted es responsable de las tareas administrativas en la aplicación, datos, las reglas de firewall y una red virtual.|Usted es responsable de las tareas administrativas en la aplicación y sólo los datos.|
|**Alta disponibilidad y recuperación (HADR)**|Se recomienda colocar máquinas virtuales en el mismo conjunto de disponibilidad y en el mismo servicio de nube. Mantener sus máquinas virtuales en el mismo conjunto de disponibilidad permite Azure para colocar los nodos de alta disponibilidad en dominios de error independiente y actualizar los dominios. Asimismo, mantener sus máquinas virtuales en el mismo servicio de nube permite equilibrio de carga y máquinas virtuales pueden comunicarse directamente con otra a través de la red local dentro de un centro de datos de Azure.<br/><br/>Usted es responsable de implementar una solución de recuperación de desastres para SQL Server y de alta disponibilidad en Azure máquinas virtuales de Windows para evitar el tiempo de inactividad. Para las tecnologías HADR admitidas, vea [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Usted es responsable de la copia de seguridad de sus propios datos y la aplicación.<br/><br/>Azure puede mover las máquinas virtuales si se produce un error en el equipo host en el centro de datos debido a problemas de hardware. Además, puede haber un tiempo de inactividad planeado de la máquina virtual cuando se actualiza el equipo host para el software de seguridad o actualizaciones. Por lo tanto, le recomendamos que mantenga al menos dos máquinas virtuales en cada nivel de aplicación para garantizar la disponibilidad continua. Azure no proporciona SLA para una sola máquina virtual. Para obtener más información, vea [Guía técnica de la resistencia de Azure](../resiliency/resiliency-technical-guidance.md).|Azure administra los errores resultantes del software de hardware o sistema operativo subyacente. Se recomienda que implemente varias instancias de una función web o de trabajo para garantizar la alta disponibilidad de la aplicación. Para obtener más información, vea [Servicios de nube, máquinas virtuales y contrato de nivel de servicio de red Virtual](http://www.microsoft.com/download/details.aspx?id=38427) y [recuperación y alta disponibilidad para las aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Usted es responsable de la copia de seguridad de sus propios datos y la aplicación.<br/><br/>Bases de datos que se encuentran en una base de datos de SQL Server en una máquina virtual de Azure, usted es responsable de implementar una solución de recuperación de desastres y disponibilidad alta para evitar el tiempo de inactividad. Para las tecnologías HDAR admitidas, vea alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows.<br/><br/>**Reflejo de base de datos de SQL Server**: usar con servicios de nube de Azure (funciones web o trabajador). Máquinas virtuales de SQL Server y un proyecto de servicio de nube pueden estar en la misma red Virtual de Azure. Si no está en la misma red Virtual VM de SQL Server, debe crear un Alias de SQL Server para enrutar comunicaciones a la instancia de SQL Server. Además, el nombre de alias debe coincidir con el nombre de SQL Server.|Alta disponibilidad se hereda de base de datos de SQL Azure, almacenamiento de blobs de Windows Azure y funciones de trabajo de Azure. Por ejemplo, el almacenamiento de Azure mantiene tres réplicas de todos los blob, tabla y los datos de cola. En cualquier momento, la base de datos de SQL Azure mantiene tres réplicas de ejecución de datos: una réplica principal y dos réplicas secundarias. Para obtener más información, vea [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) y [Base de datos de SQL Azure](../sql-database/sql-database-technical-overview.md).<br/><br/>Al usar SQL Server en máquina virtual de Azure como origen de datos para aplicaciones Web de Azure, tenga en cuenta que las aplicaciones Web de Azure no admite red Virtual de Azure. En otras palabras, todas las conexiones de aplicaciones Web de Azure a las máquinas virtuales de SQL Server en Azure deben ir públicos puntos finales de máquinas virtuales. Esto puede provocar que algunas limitaciones de alta disponibilidad y recuperación de desastres escenarios. Por ejemplo, la aplicación de cliente en aplicaciones Web de Azure conexión a máquina virtual de SQL Server con la base de datos que coinciden con ' d no podrá conectar con el nuevo servidor principal como reflejo de base de datos necesita configurar una red Virtual Azure entre máquinas virtuales de host de SQL Server en Azure. Por lo tanto, usar **Reflejo de base de datos de SQL Server** con aplicaciones de Azure Web no es compatible actualmente.<br/><br/>**Grupos de disponibilidad de SQL Server AlwaysOn**: puede configurar grupos de disponibilidad AlwaysOn con aplicaciones Web de Azure las máquinas virtuales de SQL Server en Azure. Pero necesita configurar AlwaysOn disponibilidad grupo escucha para enrutar la comunicación a la réplica principal a través de los extremos de equilibrio de carga de públicos.|
|**Conectividad entre local**|Puede usar una red Virtual Azure para conectarse a local.|Puede usar una red Virtual Azure para conectarse a local.|Una red Virtual Azure es compatible. Para obtener más información, vea [Integración de red Virtual de Web Apps](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Escalabilidad**|Escalado está disponible aumentar el tamaño de la máquina virtual o agregando más discos. Para obtener más información sobre los tamaños de máquina virtual, vea [Tamaños de máquina Virtual para Azure](virtual-machines-windows-sizes.md).<br/><br/>**Para el servidor de base de datos**: escalado está disponible a través de técnicas de partición de base de datos y grupos de disponibilidad de SQL Server AlwaysOn.<br/><br/>Para cargas de lectura, puede usar [Grupos de disponibilidad AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) en varios nodos secundarios, así como replicación de SQL Server.<br/><br/>Para las cargas de trabajo de escritura pesado, puede implementar datos partición horizontales a través de varios servidores físicos para proporcionar escalado de aplicación.<br/><br/>Además, puede implementar un escalado con [SQL Server con enrutamiento según los datos](https://technet.microsoft.com/library/cc966448.aspx). Con datos dependientes enrutamiento (DDR), debe implementar el mecanismo de partición en la aplicación cliente, normalmente en la capa de nivel empresarial, para enrutar las solicitudes de base de datos a varios nodos de SQL Server. El nivel de empresa contiene asignaciones a cómo se dividen los datos y el nodo que contiene los datos.<br/><br/>Puede cambiar la escala de aplicaciones que se ejecutan máquinas virtuales de Windows. Para obtener más información, vea [cómo ajustar el tamaño de una aplicación](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Nota importante**: la característica **Autoescala** en Azure le permite automáticamente aumentar o reducir las máquinas virtuales que se utilizan en la aplicación. Esta característica garantiza que la experiencia del usuario no se ve afectada negativa durante los períodos y máquinas virtuales se cierran cuando la demanda sea baja. Se recomienda no establezca la opción Ajustar automáticamente el servicio de nube si incluye máquinas virtuales de SQL Server. El motivo es que la característica Autoescala permite Azure para activar una máquina virtual cuando el uso de CPU en esa máquina virtual es mayor que el umbral de algunos y desactivar una máquina virtual cuando el uso de CPU va inferior a ella. La característica Autoescala es útil para aplicaciones sin estado, como servidores web, donde cualquier VM puede administrar la carga de trabajo sin ninguna referencia a cualquier estado anterior. Sin embargo, la característica Autoescala no es útil para las aplicaciones con estado, como SQL Server, donde solo una instancia permite escribir en la base de datos.|Escalado está disponible mediante varias funciones de web y trabajador. Para obtener más información sobre los tamaños de máquina virtual para funciones de web y las funciones de trabajo, consulte [Configurar tamaños de servicios en la nube](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Cuando se usa **Servicios de nube**, puede definir varias funciones para distribuir el procesamiento y también lograr flexible escalado de la aplicación. Cada servicio de nube incluye funciones web o funciones de trabajo, cada una con sus propios archivos de la aplicación y la configuración. Puede escalado un servicio de nube aumentando el número de instancias de la función (máquinas virtuales) implementado para un rol y escala hacia abajo un servicio de nube reduciendo el número de instancias de la función. Para obtener información detallada, vea [Modelos de ejecución de Azure](../cloud-services/cloud-services-choose-me.md).<br/><br/>Escalado está disponible a través de la compatibilidad de Azure integradas de alta disponibilidad mediante [servicios en la nube, máquinas virtuales y contrato de nivel de servicio de red Virtual](http://www.microsoft.com/download/details.aspx?id=38427) y equilibrador de carga.<br/><br/>Para una aplicación de varios niveles, se recomienda que se conecte aplicación funciones web/trabajo máquinas virtuales a través de una red Virtual Azure del servidor de base de datos. Además, Azure proporciona equilibrio de carga para máquinas virtuales en el mismo servicio de nube, reparte las solicitudes de usuario en ellos. Máquinas virtuales conectados de esta manera puede comunicarse directamente con otra en la red local dentro de un centro de datos de Azure.<br/><br/>Puede configurar **Autoescala** en el portal de clásico Azure, así como las horas programadas. Para obtener más información, vea [cómo ajustar el tamaño de una aplicación](../cloud-services/cloud-services-how-to-scale.md).|**Escalar hacia arriba y hacia abajo**: se puede aumentar o disminuir el tamaño de la instancia (VM) reservado para su sitio web.<br/><br/>Escalar: puede agregar más reservados instancias (VM) de su sitio web.<br/><br/>Puede configurar **Autoescala** en el portal, así como las horas programadas. Para obtener más información, vea [Cómo escala Web Apps](../app-service-web/web-sites-scale.md).|

Para obtener más información sobre cómo elegir entre estos métodos de programación, vea [Azure Web Apps, servicios en la nube y VM: cuál](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo ejecutar SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Introducción a máquinas virtuales de Windows Azure](virtual-machines-windows-sql-server-iaas-overview.md).
