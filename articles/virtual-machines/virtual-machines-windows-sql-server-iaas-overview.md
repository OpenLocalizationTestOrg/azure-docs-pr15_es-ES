<properties
    pageTitle="Información general de SQL Server en máquinas virtuales de Windows Azure | Microsoft Azure"
    description="Obtenga información sobre cómo ejecutar completas ediciones de SQL Server en máquinas virtuales de Azure. Obtener vínculos directos a todas las imágenes de máquina virtual de SQL Server y contenido relacionado."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Información general de SQL Server en máquinas virtuales de Windows Azure

Este tema describe las opciones para ejecutar SQL Server en máquinas virtuales de Azure (VM), junto con [vínculos a imágenes portal](#option-1-create-a-sql-vm-with-per-minute-licensing) y una descripción general de [las tareas comunes](#manage-your-sql-vm).

>[AZURE.NOTE] Si ya está familiarizado con SQL Server y solo quiere ver cómo implementar una VM de SQL Server, consulte [aprovisionar una máquina virtual de SQL Server en el portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Información general
Si es un administrador de la base de datos o un desarrollador, máquinas virtuales de Azure proporciona una manera de mover sus aplicaciones a la nube y cargas de trabajo de SQL Server local. El siguiente vídeo proporciona una introducción técnica de máquinas virtuales de SQL Server Azure.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

El vídeo trata las siguientes áreas:

|Hora|Área|
|---|---|
| 00:21 | ¿Cuáles son las máquinas virtuales de Azure? |
| 01:45 | Seguridad |
| 02:50 | Conectividad |
| 03:30 | Rendimiento y la confiabilidad de almacenamiento |
| 05:20 | Tamaños VM |
| 05:54 | Alta disponibilidad y SLA |
| 07:30 | Soporte de configuración |
| 08:00 | Supervisión |
| 08:32 | Demostración: Crear una máquina virtual de SQL Server 2016 |

>[AZURE.NOTE] El vídeo se centra en SQL Server 2016, pero Azure proporciona imágenes de máquina virtual en muchas versiones de SQL Server, incluido 2008, 2012, 2014 y 2016. 

## <a name="scenarios"></a>Escenarios
Hay muchas razones que pueden elegir para hospedar los datos en Azure. Si la aplicación se mueve a Azure, mejora el rendimiento para mover los datos. Pero hay otras ventajas. Tiene automáticamente acceso a varios centros de datos para una recuperación de presencia y desastres global. Los datos también están muy seguro y resistentes.

SQL Server en máquinas virtuales de Azure es una opción para almacenar los datos relacionales en Azure. Es una buena opción para varios escenarios. Por ejemplo, que desea configurar la máquina virtual de Azure como asimismo como sea posible para un equipo de SQL Server local. O bien, desea ejecutar aplicaciones y servicios adicionales en el mismo servidor de base de datos. Hay dos recursos principales que pueden ayudarle a reflexionar aún más escenarios y consideraciones:

 - [SQL Server en máquinas virtuales de Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) proporciona una descripción general de los escenarios mejores para usar SQL Server en máquinas virtuales de Azure. 
 - [Elegir una opción de SQL Server de nube: base de datos de SQL Azure (PaaS) o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) proporciona una comparación detallada entre la base de datos de SQL y SQL Server se ejecuta en una máquina virtual.

## <a name="create-a-new-sql-vm"></a>Crear una nueva VM SQL
Las siguientes secciones proporcionan vínculos directos al portal de Azure para las imágenes de galería de máquina virtual de SQL Server. Dependiendo de la imagen que seleccione, puede cualquier pago para los costos de forma por minuto de licencias de SQL Server o puede reunir su propia licencia (BYOL).

Encontrará instrucciones detalladas para este proceso en el tutorial, [aprovisionar una máquina virtual de SQL Server en el portal de Azure](virtual-machines-windows-portal-sql-server-provision.md). Además, revise las [prácticas recomendadas de rendimiento para máquinas virtuales de SQL Server](virtual-machines-windows-sql-performance.md), donde se explica cómo seleccionar el tamaño del equipo adecuado y otras características disponibles durante el aprovisionamiento de.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opción 1: Crear una VM SQL con licencia por minuto
La tabla siguiente proporciona una matriz de imágenes de SQL Server disponibles en la Galería de máquina virtual. Haga clic en cualquier vínculo para empezar a crear una nueva VM SQL con la versión especificada, la edición y el sistema operativo.

|Versión|Sistema operativo|Edition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [desarrollo](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SP1 DE SQL DE 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL DE 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), de [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), de [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [estándar](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), de [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Opción 2: Crear una VM SQL con una licencia existente
También puede hacer que su propia licencia (BYOL). En este escenario, solo pagar por la máquina virtual sin los cargos adicionales para las licencias de SQL Server. Para usar su propia licencia, use la matriz de versiones de SQL Server, ediciones y sistemas operativos a continuación. En el portal, estos nombres de imagen prefijo con **{BYOL}**.

|Versión|Sistema operativo|Edition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL estándar](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL estándar](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 Service Pack 2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL estándar](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Para usar imágenes BYOL VM, debe tener y contrato Enterprise con [Licencia movilidad a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/). También se necesita una licencia válida para la edición y versión de SQL Server que desee usar. Debe [proporcionar la información necesaria de BYOL a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) en **10** días de la máquina virtual de aprovisionamiento.

## <a name="manage-your-sql-vm"></a>Administrar la máquina virtual de SQL
Si ya dispone de la VM de SQL Server, hay varias tareas de administración opcional. En muchos aspectos, configurar y administrar SQL Server como lo haría administrar una instancia de SQL Server local. Sin embargo, algunas de las tareas son específicos de Azure. Las secciones siguientes resaltan algunas de estas áreas con vínculos a más información.

### <a name="connect-to-the-vm"></a>Conectarse a la máquina virtual
Uno de los pasos más básicos de administración es para conectarse a su VM de SQL Server a través de herramientas, como SQL Server Management Studio (SSMS). Para obtener instrucciones sobre cómo conectarse a su nueva máquina virtual de servidor de SQL, vea [conectarse a una máquina Virtual de SQL Server en Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar los datos
Si tiene una base de datos existente, desea mover VM SQL recientemente creado. Para obtener una lista de opciones de migración y orientación, vea [migrar una base de datos de SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurar alta disponibilidad
Si necesita alta disponibilidad, considere la posibilidad de configurar grupos de disponibilidad de SQL Server. Esto implica varias máquinas virtuales de Azure en una red virtual. El portal de Azure tiene una plantilla que se establece esta configuración para usted. Para obtener más información, consulte [configurar un grupo de disponibilidad AlwaysOn en máquinas virtuales de administrador de recursos de Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Si desea configurar manualmente su grupo de disponibilidad y escucha asociado, vea [Configurar grupos de disponibilidad AlwaysOn en Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para otras consideraciones de alta disponibilidad, vea [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Hacer copia de seguridad de sus datos
Máquinas virtuales de Azure pueden sacar partido de [Copia de seguridad automática](virtual-machines-windows-sql-automated-backup.md), que crea con regularidad copias de seguridad de la base de datos con el almacenamiento de blobs de Windows. Puede usar esta técnica también manualmente. Para obtener más información, vea [Usar el almacenamiento de Azure para copia de seguridad de SQL Server y restaurar](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obtener información general de todas las opciones de copia de seguridad y restauración, consulte [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizar actualizaciones
Máquinas virtuales de Azure pueden usar [Automatizado revisiones](virtual-machines-windows-sql-automated-patching.md) para programar una ventana de mantenimiento para instalar windows importantes y SQL Server se actualiza automáticamente.

### <a name="customer-experience-improvement-program-ceip"></a>Programa para la mejora de experiencia del usuario (CEIP)
El programa de mejora de experiencia del usuario (CEIP) está habilitado de forma predeterminada. Envía periódicamente informes a Microsoft para ayudar a mejorar SQL Server. No hay ninguna tarea de administración necesaria con CEIP a menos que desee deshabilitar después de aprovisionamiento. Puede personalizar o deshabilitar el CEIP mediante una conexión a la máquina virtual con Escritorio remoto. A continuación, ejecute la utilidad de **informes de uso y errores de SQL Server** . Siga las instrucciones para deshabilitar los informes. 

Para obtener más información, vea la sección CEIP del tema [Aceptar términos de licencia](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Pasos siguientes
[Explorar la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Windows Azure.

¿Pregunta más? En primer lugar, vea el [SQL Server en máquinas virtuales de Azure preguntas más frecuentes](virtual-machines-windows-sql-server-iaas-faq.md). Pero también agregar sus preguntas o comentarios a la parte inferior de cualquier tema de SQL VM para interactuar con Microsoft y la Comunidad.
