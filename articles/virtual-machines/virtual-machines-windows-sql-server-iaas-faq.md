<properties
    pageTitle="SQL Server en máquinas virtuales de Azure preguntas más frecuentes | Microsoft Azure"
    description="En este artículo se proporciona respuestas a las preguntas más frecuentes sobre la ejecución de SQL Server en máquinas virtuales de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server en máquinas virtuales de Azure preguntas más frecuentes

Este tema proporciona respuestas a algunas de las preguntas más frecuentes acerca de la ejecución de [SQL Server en Azure máquinas virtuales de Windows](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

1. **¿Cómo puedo crear una máquina virtual Azure con SQL Server?**

    Hay dos formas de hacerlo. La solución más sencilla es crear una máquina Virtual que incluye SQL Server. Para obtener un tutorial sobre cómo suscribirse a Azure y crear una VM SQL desde el portal, vea [aprovisionar una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md). También tiene la opción de instalar a SQL Server en máquinas virtuales y volver a utilizar una licencia local con [Licencia movilidad a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/)manualmente.

1. **¿Cuál es la diferencia entre máquinas virtuales de SQL y el servicio de base de datos SQL?**

    Conceptualmente, ejecuta SQL Server en una máquina virtual Azure difiere no ejecuta SQL Server en un centro de datos remota. En cambio, [Base de datos SQL](../sql-database/sql-database-technical-overview.md) ofrece la base de datos como servicio. Con la base de datos de SQL, no tiene acceso a los equipos que hospeda las bases de datos. Para ver una comparación completa, consulte [Elija una opción de SQL Server de nube: base de datos de SQL Azure (PaaS) o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **¿Cómo puedo migrar la base de datos de SQL Server local a la nube?**

    Primero, cree una máquina virtual Azure con una instancia de SQL Server. A continuación, migrar las bases de datos locales a esa instancia. Para estrategias de migración de datos, vea [migrar una base de datos de SQL Server para SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

2. **¿Puedo cambiar las características instaladas o instala una segunda instancia de SQL Server en el misma VM?**

    Sí. El medio de instalación de SQL Server se encuentra en una carpeta en la unidad **C** . Ejecute **Setup.exe** desde esa ubicación para agregar nuevas instancias de SQL Server o cambiar otras características de SQL Server instaladas en el equipo.

3. **¿Cómo actualizar a una nueva versión y edición de SQL Server en una máquina virtual de Azure?**

    Actualmente, no hay ninguna actualización en contexto para SQL Server que se ejecuta en una máquina virtual de Azure. Crear una nueva máquina virtual Azure con la versión y edición de SQL Server que desee y, a continuación, migrar las bases de datos en el servidor nuevo usando estándar [técnicas de migración de datos](virtual-machines-windows-migrate-sql.md).

4. **¿Cómo puedo instalar mi copia con licencia de SQL Server en una máquina virtual de Azure?**

    Copiar el medio de instalación de SQL Server en la máquina virtual de servidor de Windows y, a continuación, instalar a SQL Server en la máquina virtual. Para las licencias motivos, debe tener [Licencia movilidad a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **¿Tiene que pagar los costos SQL de una máquina virtual si solo se utiliza para en espera y recuperación tras errores?**

    Si va a crear la VM SQL a través de la galería, debe tener una licencia independiente para VM SQL en espera y el precio es el mismo. Si instala a SQL Server manualmente en una máquina virtual con [Movilidad de licencia](https://azure.microsoft.com/pricing/license-mobility/), tiene la opción de tener una instancia SQL pasiva libre para migración tras error. Revise las restricciones y requisitos.

6. **¿Cómo se service packs y actualizaciones aplicados en una VM de SQL Server?**

    Máquinas virtuales ofrecer control sobre el equipo host, incluyendo cuándo y cómo aplicar actualizaciones. Para el sistema operativo, puede aplicar manualmente las actualizaciones de windows o puede habilitar un servicio de programación denominado [Automatizado revisiones](virtual-machines-windows-classic-sql-automated-patching.md). Actualizaciones automatizadas de revisión instalaciones cualquier marcados como importantes, incluidas las actualizaciones de SQL Server en esa categoría. Otras actualizaciones opcionales para SQL Server deben estar instalados manualmente.

7. **¿Es posible configurar configuraciones que no se muestra en la Galería de máquina virtual (por ejemplo Windows 2008 R2 + SQL Server 2012)?**

    No. Imágenes de galería de máquina virtual que incluyen SQL Server, seleccione una de las imágenes proporcionadas.

9. **¿Cómo puedo instalar herramientas de datos de SQL en mi máquina virtual de Azure?**

    Descargue e instale las herramientas de datos de SQL de [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio de 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos

Para obtener información general de SQL Server en Azure máquinas virtuales de Windows, vea el vídeo [Azure VM es la mejor plataforma para SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). También puede obtener una buena introducción en el tema de [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Otros recursos incluyen:

- [Aprovisionar una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migrar una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)
- [Alta disponibilidad y recuperación para SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-performance.md)
- [Patrones de aplicaciones y estrategias de desarrollo de SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
