<properties
    pageTitle="Base de datos SQL (PaaS) frente a SQL Server en la nube en máquinas virtuales (IaaS) | Microsoft Azure"
    description="Obtenga información sobre qué opción de SQL Server de nube se ajusta a la aplicación: base de datos de SQL Azure (PaaS) o SQL Server en la nube en Azure máquinas virtuales de Windows."
    services="sql-database, virtual-machines"
    keywords="Nube de SQL Server, SQL Server en la nube, la base de datos de PaaS, SQL Server, DBaaS en la nube"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Elija una opción de SQL Server de nube: base de datos de SQL Azure (PaaS) o SQL Server en máquinas virtuales de Azure (IaaS)

Azure tiene dos opciones para las cargas de trabajo de SQL Server en Microsoft Azure de hospedaje:

* [Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/): base de datos de SQL A nativo a la nube, también conocido como una plataforma como una base de datos de servicio (PaaS) o una base de datos como un servicio (DBaaS) que está optimizado para el desarrollo de aplicaciones de software como servicio (SaaS). Ofrece compatibilidad con la mayoría de características de SQL Server. Para obtener más información sobre PaaS, consulte [¿Qué es PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server en Azure máquinas virtuales de Windows](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server instalado y hospedada en la nube en Windows Server máquinas virtuales (VM) ejecuta en Azure, también conocido como una infraestructura como servicio (IaaS).
SQL Server en máquinas virtuales de Windows Azure está optimizado para migrar las aplicaciones de SQL Server existentes. Todas las versiones y ediciones de SQL Server están disponibles. Ofrece 100% compatible con SQL Server, lo que le permite hospedar las bases de datos como sea necesarias y se ejecutan transacciones entre bases de datos. Ofrece control total en SQL Server y Windows.

Obtenga información sobre cómo encaja cada opción en la plataforma de datos de Microsoft y obtener ayuda para que coincidan con la opción adecuada a sus necesidades empresariales. Si prioridades ahorros o administración mínima por delante de todo lo demás, en este artículo puede ayudarle a decidir qué enfoque ofrece frente a los requisitos empresariales que le interesan más.


## <a name="microsofts-data-platform"></a>Plataforma de datos de Microsoft

Una de las primeras cosas para comprender la descripción de Azure frente a bases de datos de SQL Server local es que puede usar todo. La plataforma de datos de Microsoft aprovecha la tecnología de SQL Server y hace que esté disponible en varios equipos locales físicos, entornos de nube privada, entornos de nube privada hospedada de terceros y nube pública. SQL Server en mchines virtual Azure le permite satisfacer las necesidades de negocio únicos y diverso mediante una combinación de local e implementaciones hospedada en la nube, al utilizar el mismo conjunto de productos de servidor, herramientas de desarrollo y experiencia en los siguientes entornos.

   ![Opciones de SQL Server en la nube: base de datos de SQL server en IaaS o SaaS SQL en la nube.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Tal como se muestra en el diagrama, se puede clasificar cada oferta por el nivel de administración que tiene sobre la infraestructura (en el eje X) y el grado de rentabilidad consigue mediante la consolidación de nivel de base de datos y automatización (en el eje Y).

Al diseñar una aplicación, cuatro opciones básicas están disponibles para el elemento de SQL Server de la aplicación de hospedaje:

- SQL Server en máquinas físicas no virtualizado
- SQL Server en máquinas virtualizada de local (de nube privada)
- SQL Server en Azure Virtual Machine (nube pública de Microsoft)
- Base de datos de SQL Azure (nube pública de Microsoft)

En las siguientes secciones, aprenderá a SQL Server en la nube pública de Microsoft: base de datos de SQL Azure y SQL Server en máquinas virtuales de Azure. Además, explore factores de motivación empresarial común para determinar qué opción funciona mejor para la aplicación.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Un vistazo al base de datos de SQL Azure y SQL Server en máquinas virtuales de Azure

**Base de datos de SQL Azure** es un relacional base de datos como-servicio (DBaaS) hospedado en la nube de Azure que se encuentre en las categorías de la industria de *Software como-servicio (SaaS)* y *Plataforma como-servicio (PaaS)*. [Base de datos SQL](sql-database-technical-overview.md) se basa en el estándar hardware y software que es propietario, hospedado y mantiene Microsoft. Con la base de datos de SQL, puede desarrollar directamente en el servicio con funcionalidades y características integradas. Al usar la base de datos de SQL que pago con opciones para ajustar la escala vertical u horizontal para power mayor sin interrupciones.

**SQL Server en Azure máquinas virtuales de Windows (VM)** se encuentra en la categoría del sector *Infraestructura como-servicio (IaaS)* y le permite ejecutar SQL Server en una máquina virtual en la nube. De forma similar a la base de datos de SQL, se basa en hardware estándar que es propietario, hospedado y mantiene Microsoft. Al usar SQL Server en una máquina virtual, puede cualquier pago-como-go para una licencia de SQL Server ya está incluido en una imagen de SQL Server o utilizar fácilmente una licencia existente. También puede pausar o reanudar la máquina virtual según sea necesario y escala-arriba o abajo.

En general, estas dos opciones de SQL están optimizadas para diferentes propósitos:

- **Base de datos SQL** está optimizado para reducir los costos totales al mínimo de aprovisionamiento y administración de muchas bases de datos. Reduce los costos de administración en curso porque no tiene que administrar las máquinas virtuales de Windows, el sistema operativo o el software de base de datos. No tiene que administrar las actualizaciones, alta disponibilidad o [realizar copias de seguridad](sql-database-automated-backups.md). En general, base de datos de SQL Azure pueden aumentar considerablemente el número de bases de datos administradas por un solo TI o recursos de desarrollo.
- **Ejecuta SQL Server en máquinas virtuales de Azure** está optimizado para migrar aplicaciones existentes a Azure o ampliar aplicaciones locales existentes en la nube en implementaciones híbridas. Además, puede usar SQL Server en una máquina virtual para desarrollar y probar aplicaciones tradicionales de SQL Server. Con SQL Server en máquinas virtuales de Azure, tendrá los derechos administrativos completos sobre una instancia de SQL Server dedicada y una máquina virtual basada en nube. Es una opción ideal cuando una organización ya tiene disponible para mantener las máquinas virtuales de recursos de TI. Estas funciones permiten crear un sistema altamente personalizado para tratar los requisitos de disponibilidad y rendimiento específicos de la aplicación.

La siguiente tabla resume las principales características de base de datos de SQL y SQL Server en máquinas virtuales de Azure:

|       | Base de datos SQL | SQL Server en una máquina Virtual de Azure|
| -------------- | ------------ | ---------------------- |
| **La mejor opción para:** | Nuevas diseñado nube aplicaciones que tienen restricciones de tiempo en desarrollo y marketing. |Aplicaciones existentes que requieren migración rápida a la nube con un mínimo de cambios. Rápido desarrollo y pruebas escenarios cuando no desea comprar hardware de SQL Server no es de producción local. |
|| Grupos que necesitan alta disponibilidad incorporada, la recuperación y la actualización de la base de datos. |Equipos que pueden configurar y administrar alta disponibilidad, recuperación y revisiones para SQL Server. Algunos proporcionan funciones automatizadas simplifican considerablemente este. |
||Equipos que no desea administrar el sistema operativo subyacente y los valores de configuración.| Si necesita un entorno personalizado con derechos administrativos completos.|
||Bases de datos de hasta 1 TB o bases de datos mayores que se pueden [dividir horizontalmente o verticalmente](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) mediante un modelo de escalado.|Instancias de SQL Server con hasta 64 TB de almacenamiento. La instancia admite tantas bases de datos según sea necesario. |
||[Aplicaciones de creación Software como-servicio (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Migración y creación de aplicaciones empresariales y híbrido.|
|||||
|**Recursos:**|¿Quiere utilizar recursos de TI para la configuración y administración de la infraestructura subyacente, pero desea centrarse en el nivel de aplicación.|Tiene algunos recursos de TI de configuración y administración. Algunos proporcionan funciones automatizadas simplifican considerablemente este.|
|**Costo total de propiedad:**|Elimina los costos de hardware y reduce los costos administrativos.|Elimina los costos de hardware.|
|**Continuidad empresarial:**|Además de capacidades de infraestructura de tolerancia a errores integrados, base de datos de SQL Azure proporciona características, como [copias de seguridad automatizadas](sql-database-automated-backups.md), [Restaurar en un momento](sql-database-recovery-using-backups.md#point-in-time-restore), [Geo restaurar](sql-database-recovery-using-backups.md#geo-restore)y [La replicación de Geo Active](sql-database-geo-replication-overview.md) para aumentar la continuidad empresarial. Para obtener más información, vea [información general sobre la continuidad de negocio de base de datos de SQL](sql-database-business-continuity.md).|SQL Server en máquinas virtuales de Azure le permite configurar una solución de recuperación de desastres y disponibilidad alta para necesidades específicas de su base de datos. Por lo tanto, puede tener un sistema que está optimizado para la aplicación. Puede probar y ejecutar migraciones tras error manualmente cuando sea necesario. Para obtener más información, vea [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Nube híbrida:**|La aplicación local puede obtener acceso a datos en la base de datos de SQL Azure.|Con SQL Server en máquinas virtuales de Azure, puede tener las aplicaciones que se ejecutan parcialmente en la nube y parcialmente local. Por ejemplo, puede ampliar su red local y el dominio de Active Directory en la nube a través de [Una red Virtual Azure](../virtual-network/virtual-networks-overview.md). Además, puede almacenar archivos de datos local en el almacenamiento de Azure con los [Archivos de datos de SQL Server en Azure](http://msdn.microsoft.com/library/dn385720.aspx). Para obtener más información, vea [Introducción a la nube híbrida de SQL Server de 2014](http://msdn.microsoft.com/library/dn606154.aspx).|
||Admite la [replicación de transacciones de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como suscriptor para replicar los datos.|Totalmente compatible con la [replicación de transacciones de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Grupos de disponibilidad AlwaysOn](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), servicios de integración y envío de registro para replicar datos. Además, las copias de seguridad de SQL Server tradicionales son totalmente compatibles|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Razones comerciales para elegir la base de datos de SQL Azure o SQL Server en máquinas virtuales de Azure

### <a name="cost"></a>Costo

Si es un inicio de sujetó de efectivo o un grupo en una empresa establecida que opera en las restricciones de presupuesto estrecho, fondos limitado suele ser el controlador principal al decidir cómo hospedar las bases de datos. En esta sección, obtener información sobre la facturación y licencias conceptos básicos de Azure con respecto a estas dos opciones de base de datos relacional: base de datos de SQL y SQL Server en máquinas virtuales de Azure. También aprenderá acerca de cómo calcular el costo total de aplicaciones.

#### <a name="billing-and-licensing-basics"></a>Facturación y conceptos básicos de licencias

**Base de datos SQL** se vende a los clientes como un servicio, no con una licencia.  [SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) se vende con una licencia de incluye paga por minuto. Si tiene una licencia existente, también puede usarlo.  

Actualmente, la **Base de datos SQL** está disponible en varios niveles de servicio, que se cargarán por hora a una tasa fija basada en el nivel de servicio y elegir el nivel de rendimiento. Además, se cargarán para el tráfico saliente de Internet en regular [tasas de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/). Los niveles de servicio básico, estándar y Premium están diseñados para ofrecer un rendimiento predecible con varios niveles de rendimiento para que coincida con los requisitos de recursos asignadas de la aplicación. Puede cambiar entre los niveles de servicio y los niveles de rendimiento para satisfacer las necesidades de variados de rendimiento de la aplicación. Si la base de datos tiene gran volumen de transacciones y admitir muchos usuarios simultáneos, se recomienda el nivel de servicio Premium. Para obtener la información más reciente sobre los niveles de servicio compatibles actual, vea [Niveles de servicio de base de datos de SQL Azure](sql-database-service-tiers.md). También puede crear [grupos de base de datos elástica](sql-database-elastic-pool.md) para compartir recursos de rendimiento entre instancias de base de datos.

Con la **Base de datos de SQL**, el software de base de datos automáticamente está configurado, revisado y actualizado por Microsoft, lo que reduce los costos de administración. Además, sus capacidades de [copia de seguridad integrada](sql-database-automated-backups.md) ayudan a lograr ahorros importantes, especialmente si tiene un gran número de bases de datos.

Con **SQL Server en máquinas virtuales de Azure**, puede utilizar cualquiera de las imágenes de SQL Server proporcionado por la plataforma (que incluye una licencia) o recuperar su licencia de SQL Server. Todas las versiones de SQL Server compatibles (2008R2, 2012, 2014, 2016) y hay ediciones (Developer Express, estándar, Web empresarial). Además, versiones Traer-su-propietario-licencia (BYOL) de las imágenes están disponibles. Cuando usa la Azure proporciona imágenes, el costo operativo depende del tamaño de la memoria virtual y la edición de SQL Server que elija. Independientemente del tamaño de la máquina virtual o la edición de SQL Server, pagar por minuto coste de la licencia de SQL Server y Windows Server, junto con el costo de almacenamiento de Azure para los discos VM. La opción de facturación por minuto le permite usar SQL Server para como necesita sin compra adición SQL Server licencias. Si su propia licencia de SQL Server se pone en Azure, le cobrarán para Windows Server y los costos de almacenamiento. Para obtener más información sobre las licencias de traer sus propias, vea [Licencia movilidad a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calcular el costo total de aplicaciones

Al empezar a utilizar una plataforma de nube, el costo de la ejecución de la aplicación incluye los costos de desarrollo y administración, además de los costes de servicio de plataforma de nube pública.

Esto es el cálculo del costo detallada de la aplicación que se ejecuta en la base de datos de SQL y SQL Server en máquinas virtuales de Azure:

**Al usar la base de datos de SQL Azure:**

*Costo total de aplicación = los costos de desarrollo de software, los costos de administración altamente minimizado + costes de servicio de base de datos SQL*

**Al usar SQL Server en máquinas virtuales de Azure:**

*Costo total de aplicación = costo de desarrollo de software altamente minimizado costos de administración de costos de licencia de SQL Server y Windows Server + + costos de almacenamiento de Azure*

Para obtener más información sobre precios, consulte los siguientes recursos:

- [Precios de base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Precios de máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) y [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Hay un pequeño subconjunto de características en SQL Server que no sean aplicables o no está disponible con la base de datos de SQL. Para obtener más información, vea [limitaciones a generales de base de datos de SQL y directrices](sql-database-general-limitations.md) e [información de la base de datos de SQL Transact-SQL](sql-database-transact-sql-information.md) . Si está moviendo una solución existente de SQL Server a la nube, consulte [migrar una base de datos de SQL Server a la base de datos de SQL Azure](sql-database-cloud-migrate.md). Al migrar una aplicación existente de SQL Server local a la base de datos de SQL, considere la posibilidad de actualizar la aplicación para aprovechar las ventajas de las capacidades que ofrecen servicios en la nube. Por ejemplo, puede utilizar el [Servicio de aplicación Web de Azure](https://azure.microsoft.com/services/app-service/web/) o [Los servicios de nube de Azure](https://azure.microsoft.com/services/cloud-services/) para hospedar el nivel de aplicación para aumentar las ventajas de costo.

### <a name="administration"></a>Administración

Para muchas empresas, la decisión de transición a un servicio de nube es como mucho sobre la descarga de complejidad de administración tal y como costo. Con la **Base de datos de SQL**, Microsoft administra el hardware subyacente. Microsoft automáticamente aplica todos los datos para proporcionar alta disponibilidad, configura y actualiza el software de base de datos, administra el equilibrio de carga y no transparente migración tras error si hay un error de servidor. Puede seguir administrar la base de datos, pero que ya no necesita para administrar el motor de base de datos, el sistema operativo del servidor o el hardware.  Algunos ejemplos de elementos que puede seguir para administrar bases de datos y los inicios de sesión, índice y ajuste de consultas y auditoría y seguridad.

Con **SQL Server en máquinas virtuales de Azure**, tiene control total sobre el sistema operativo y la configuración de la instancia de SQL Server. Con una máquina virtual, es hacia arriba a decidir cuándo de actualización del sistema operativo y el software de base de datos y cuándo se debe instalar software adicional, como el antivirus. Se proporcionan algunas características automatizadas para simplificar considerablemente la disponibilidad de revisiones, copia de seguridad y alta. Además, puede controlar el tamaño de la máquina virtual, el número de discos y sus configuraciones de almacenamiento. Azure le permite cambiar el tamaño de una máquina virtual según sea necesario. Para obtener más información, vea [Máquina Virtual y los tamaños de servicio de nube para Azure](../virtual-machines/virtual-machines-linux-sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrato de nivel de servicio (SLA)

Para muchos de los departamentos de TI, obligaciones de tiempo de un contrato de nivel de servicio (SLA) de la reunión es una prioridad. En esta sección, veremos qué SLA se aplica a cada opción de host de la base de datos.

Microsoft proporciona una disponibilidad SLA del 99,99% para los niveles de servicio básico de **Base de datos de SQL** , estándar y Premium. Para obtener la información más reciente, consulte el [Contrato de nivel de servicio](https://azure.microsoft.com/support/legal/sla/sql-database/). Para obtener la información más reciente sobre los niveles de servicio de base de datos de SQL y los planes de continuidad empresarial admitida, vea [Niveles de servicio](sql-database-service-tiers.md).

Para que se **ejecuta SQL Server en máquinas virtuales de Azure**, Microsoft proporciona una disponibilidad SLA de 99,95% que cubre solo la máquina Virtual. Este SLA no cubre los procesos (por ejemplo, SQL Server) que se ejecutan en la máquina virtual y requiere que aloja al menos dos instancias VM en un conjunto de disponibilidad. Para obtener la información más reciente, consulte el [SLA de máquina virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para una base de datos alta disponibilidad (HA) dentro de máquinas virtuales, debe configurar una de las opciones de alta disponibilidad compatibles en SQL Server, como [Los grupos de disponibilidad AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Con una opción de alta disponibilidad compatibles no proporciona un SLA adicional, pero le permite lograr > 99,99% disponibilidad de base de datos.

### <a name="market"></a>Tiempo de mercado

**Base de datos SQL** es la solución correcta para aplicaciones diseñadas nube cuando productividad del desarrollador y rápido al mercado son críticas. Con la funcionalidad de programación como administrador, es perfecto para diseñadores de la nube y programadores como disminuye la necesidad de administrar el sistema operativo y la base de datos subyacente. Por ejemplo, puede usar la [API de REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) y [Cmdlets de PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) para automatizar y administrar las operaciones administrativas miles de bases de datos. Características como [Elástico agrupaciones de base de datos](sql-database-elastic-pool.md) permiten centrarse en el nivel de aplicación y proporcionar la solución al mercado con mayor rapidez.

**Ejecuta SQL Server en máquinas virtuales de Azure** es ideal si las aplicaciones nuevas o existentes requieren grandes bases de datos, bases de datos interrelacionados o acceso a todas las características de SQL Server o Windows. También es una buena opción cuando desee migrar existente local aplicaciones y bases de datos de Azure como-es. Puesto que no necesita cambiar la presentación, aplicación y niveles de datos, ahorra tiempo y presupuesto en cambio de la arquitectura la solución existente. En su lugar, podrá centrarse en migrar todas las soluciones a Azure y hacer algunas optimizaciones de rendimiento que requiera la plataforma Windows Azure. Para obtener más información, consulte [Prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Resumen

En este artículo había explorado base de datos de SQL y SQL Server en Azure máquinas virtuales de Windows (VM) y analizados factores de motivación de negocio comunes que pueden afectar a su decisión. A continuación se muestra un resumen de sugerencias para tener en cuenta:

Elija la **base de datos SQL Azure** si:

- Creación de nuevas aplicaciones en la nube para aprovechar los ahorros de costos y optimización del rendimiento servicios de nube proporcionar. Este enfoque proporciona las ventajas de un servicio de nube totalmente administrado, ayuda a inferior tiempo al mercado inicial y puede proporcionar la optimización de costo a largo plazo.

- Desea tener Microsoft realizar operaciones de administración comunes en las bases de datos y requieren una mayor disponibilidad SLA para bases de datos.



Seleccione **SQL Server en máquinas virtuales de Azure** si:

- Cuenta con aplicaciones locales que desea migrar o extender a la nube, o si desea generar aplicaciones empresariales mayores de 1 TB. Este enfoque proporciona el beneficio de compatibilidad SQL de 100%, la capacidad de base de datos de gran tamaño, control total sobre SQL Server y Windows y proteger túnel a local. Este enfoque minimiza los costos de desarrollo y modificaciones de las aplicaciones existentes.

- Dispone de los recursos de TI existentes y finalmente puede propietario correcciones, las copias de seguridad y alta disponibilidad de la base de datos. Tenga en cuenta que algunas características automatizados simplifican considerablemente estas operaciones. 


## <a name="next-steps"></a>Pasos siguientes
- Consulte [tutorial de base de datos SQL: crear una base de datos SQL en minutos con el portal de Azure](sql-database-get-started.md) comenzar con la base de datos de SQL.
- Vea [precios de base de datos SQL] (https://azure.microsoft.com/pricing/details/sql-database/).
- Vea [aprovisionar una máquina virtual de SQL Server en Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) empezar a trabajar con SQL Server en máquinas virtuales de Azure.
- Consulte [SQL Server en una máquina Virtual Azure: ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
