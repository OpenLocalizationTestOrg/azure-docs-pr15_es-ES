<properties
    pageTitle="¿Qué cargas de trabajo puede proteger con la recuperación de sitio de Azure?"
    description="Recuperación de sitio de Azure protege las cargas de trabajo y aplicaciones al coordinar la replicación, la migración tras error y la recuperación de servidores físicos y máquinas virtuales de local a Azure o a un sitio secundario en local"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>¿Qué cargas de trabajo puede proteger con la recuperación de sitio de Azure?


Este artículo describe las cargas de trabajo y las aplicaciones que se pueden replicar con el servicio de recuperación de sitios de Azure.

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia empresarial continuidad y desastres recuperación (BCDR) para mantener las cargas de trabajo y datos de seguros y disponibles durante el tiempo de inactividad planificado y y recuperar tan pronto como sea posible a las condiciones de trabajo normal.

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR. Con la recuperación de sitios, puede implementar replicación compatible con aplicaciones en la nube o en un sitio secundario. Si sus aplicaciones son ventanas o basados en Linux, la ejecución en servidores físicos, VMware o Hyper-V, puede usar recuperación de sitio para coordinar replicación, realizar pruebas de recuperación de desastres y la ejecución de migraciones tras error y la recuperación.


Recuperación de sitio se integra con aplicaciones de Microsoft, incluido SharePoint, Exchange, Dynamics, SQL Server y Active Directory. Microsoft también trabaja con proveedores líderes como Oracle, SAP, IBM y sombrero de color rojo. Puede personalizar las soluciones de replicación cada aplicación por aplicación.

## <a name="why-use-site-recovery-for-application-replication"></a>¿Por qué usar recuperación de sitio para la replicación de la aplicación?

Recuperación de sitio contribuye a la protección de nivel de aplicación y recuperación como sigue:

- Aplicación independiente, que permite la replicación de cualquier cargas de trabajo que se ejecuta en un equipo compatible.
- Replicación cerca sincrónico, con RPOs como mínimos de 30 segundos para satisfacer las necesidades de aplicaciones de empresa más importantes.
- Instantáneas coherentes de aplicación para las aplicaciones de uno o varios niveles.
- Integración con SQL Server AlwaysOn y asociación con otras tecnologías de replicación de nivel de aplicación, la incluido replicación de Active Directory, la SQL AlwaysOn, grupos de disponibilidad de base de datos de Exchange (DAGs) y protección de datos de Oracle.
- Planes de recuperación flexibles, que le permite recuperar una pila de toda la aplicación con un solo clic e incluir para incluir scripts externos y acciones manuales en el plan.
- Administración de red avanzada en Azure y recuperación de sitio para simplificar los requisitos de red de la aplicación, incluida la capacidad para reservar direcciones IP, configurar Equilibrio de carga y la integración con el administrador del tráfico de Azure, para switchovers de red RTO bajos.
-  Una biblioteca de automatización enriquecido que proporciona las secuencias de comandos de producción: preparados, específicos de la aplicación que pueden descargarse e integrados con los planes de recuperación.



## <a name="workload-summary"></a>Resumen de carga de trabajo

Recuperación de sitios puede replicar cualquier aplicación que se ejecuta en un equipo compatible. Además, nos hemos asociado con los equipos de producto para llevar a cabo pruebas de adicionales específicas de la aplicación.

**Carga de trabajo** | **Replicar VM de Hyper-V para un sitio secundario** | **Replicar VM de Hyper-V en Azure** | **Replicar VM de VMware a un sitio secundario** | **Replicar VM de VMware en Azure**
---|---|---|---|---
DNS de Active Directory | Y | Y | Y | Y
Aplicaciones de Web (IIS, SQL) | Y | Y | Y | Y
System Center Operations Manager | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Duplicar sitio SAP en Azure para no clúster | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft)
Exchange (no DAG incluidos) | Y | Próximamente | Y | Y
Escritorio remoto/VDI | Y | Y | Y | N/A.
Linux (sistema operativo y aplicaciones) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Próximamente | Y | Próximamente
Oracle | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft)
Servidor de archivos de Windows | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Replicar Active Directory y DNS

Un Active Directory y la infraestructura de DNS son fundamentales para la mayoría de aplicaciones de empresa. Durante la recuperación, deberá proteger y recuperar estos componentes de infraestructura antes de recuperar su cargas de trabajo y las aplicaciones.

Puede usar la recuperación de sitio para crear un plan de recuperación completo automatizado de Active Directory y DNS. Por ejemplo, si desea un error sobre SharePoint y SAP de un servidor principal en un sitio secundario, puede configurar un plan de recuperación se produce un error en Active Directory en primer lugar y, a continuación, un plan de recuperación adicionales específicas de la aplicación conmutar las otras aplicaciones que se basan en Active Directory.

[Más información](site-recovery-active-directory.md) acerca de cómo protegerse de Active Directory y DNS.

## <a name="protect-sql-server"></a>Proteger SQL Server

SQL Server proporciona una base de datos servicios para los servicios de datos para muchas empresas en un centro de datos local.  Recuperación de sitio puede usarse junto con las tecnologías de SQL Server HA/DR, para proteger aplicaciones de múltiples niveles empresariales que utilizan SQL Server. Recuperación de sitio proporciona:

- Una solución de recuperación de desastres simple y rentable para SQL Server. Replicar varias versiones y ediciones de los servidores de independientes de SQL Server y clústeres, en Azure o en un sitio secundario.  
- Integración con grupos de disponibilidad AlwaysOn SQL, administrar conmutación y con los planes de recuperación de recuperación de sitios de Azure.
- Planes de recuperación de llevar a cabo para todos los niveles de una aplicación, incluidas las bases de datos de SQL Server.
- Ajuste de escala de SQL Server para máximo carga con la recuperación de sitio, "separación" ellos en tamaños de máquina virtual IaaS mayores en Azure.
- Fácil pruebas de recuperación de SQL Server. Puede ejecutar migraciones tras error de prueba para analizar datos y ejecutar comprobaciones de cumplimiento, sin afectar a su entorno de producción.

[Más información](site-recovery-sql.md) acerca de la protección de SQL server.

##<a name="protect-sharepoint"></a>Proteger de SharePoint

Recuperación de sitio de Azure le ayuda a proteger implementaciones de SharePoint, como sigue:

- Elimina la necesidad y la infraestructura asociada costes para un conjunto de espera de recuperación. Utilizar el sitio recuperación para replicar un conjunto de servidores completo (niveles de Web, la aplicación y la base de datos) a Azure o a un sitio secundario.
- Simplifica la administración y la implementación de aplicaciones. Actualizaciones que se implementa en el sitio primario se duplican automáticamente y, por tanto, están disponibles después de la migración y recuperación de un conjunto de servidores en un sitio secundario. También disminuye la complejidad de la administración y costos asociados a mantener una granja espera actualizados.
- Simplifica el desarrollo de aplicaciones de SharePoint y las pruebas mediante la creación de un entorno de réplica a petición de copia de producción para probar y depurar.
- Simplifica la transición a la nube mediante el uso de recuperación de sitios para migrar las implementaciones de SharePoint a Azure.

[Más información](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) acerca de cómo protegerse de SharePoint.


## <a name="protect-dynamics-ax"></a>Proteger Dynamics AX

Recuperación de sitio de Azure le ayuda a proteger la solución Dynamics AX ERP, por:

- Coordinar la replicación de todo su Dynamics AX entorno (niveles de Web y AOS, niveles de base de datos, SharePoint) en Azure o en un sitio secundario.
- Simplificar la migración de implementaciones de Dynamics AX a la nube (Azure).
- Simplificar el desarrollo de aplicaciones de Dynamics AX y pruebas mediante la creación de una copia de producción a petición, para probar y depurar.

[Más información](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) acerca de cómo protegerse de Dynamics AX.

## <a name="protect-rds"></a>Proteger RDS

Servicios de escritorio remoto (RDS) permite la infraestructura de escritorio virtual (VDI), escritorios basados en sesión y aplicaciones, que permite a los usuarios a trabajar en cualquier lugar. Recuperación de sitio de Azure puede:

- Replicar administrados o escritorios virtuales agrupadas en un sitio secundario y aplicaciones remotas y sesiones a un sitio secundario o Azure.
- Esto es lo que puede reproducir:

**RDS** | **Replicar VM de Hyper-V para un sitio secundario** | **Replicar VM de Hyper-V en Azure** | **Replicar VM de VMware a un sitio secundario** | **Replicar VM de VMware en Azure** | **Replicar servidores físicos en un sitio secundario** | **Replicar servidores físicos en Azure**
---|---|---|---|---|---|---
**Compartido de Escritorio Virtual (no administrado)** | Sí | No | Sí | No | Sí | No
**Compartido de Escritorio Virtual (administrada y sin UDP)** | Sí | No | Sí | No | Sí | No
**Sesiones de escritorio (sin UDP) y aplicaciones remotas** | Sí | Sí | Sí | Sí | Sí | Sí


[Más información](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre cómo proteger RDS.


## <a name="protect-exchange"></a>Proteger Exchange

Recuperación de sitio ayuda a proteger Exchange, como sigue:

- Para implementaciones pequeñas de Exchange, como un único o independiente servidores, recuperación de sitios puede replicar y conmutar a Azure o en un sitio secundario.
- Para implementaciones más grandes, recuperación de sitio se integra con DAGS de Exchange.
- DAGs de Exchange son la solución recomendada para recuperación de Exchange en una empresa.  Planes de recuperación de recuperación de sitios pueden incluir DAGs para organizar DAG incluidos migración tras error en sitios.


[Más información](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) acerca de cómo protegerse de Exchange.

## <a name="protect-sap"></a>Proteger SAP

Usar recuperación de sitio para proteger la implementación de SAP, como sigue:

- Habilitar la protección de toda la implementación de SAP, mediante la replicación de capas de implementación diferentes a Azure o en un sitio secundario.
- Simplificar la migración a la nube, mediante recuperación de sitio para migrar su implementación de SAP a Azure.
- Simplificar el desarrollo de SAP y pruebas, copie a petición para probar y depurar aplicaciones mediante la creación de un tipo de producción.

[Más información](http://aka.ms/asr-sap) acerca de cómo protegerse de SAP.

## <a name="next-steps"></a>Pasos siguientes

[Prepararse para la implementación de recuperación de sitios](site-recovery-best-practices.md) 
