<properties
    pageTitle="Migrar una base de datos de SQL Server a SQL Server en una máquina virtual | Microsoft Azure"
    description="Obtenga información sobre cómo migrar una base de datos de usuario local de SQL Server en una máquina virtual en Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar una base de datos de SQL Server a SQL Server en una máquina virtual de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Modelo de administrador de recursos.


Existen varios métodos para migrar una base de datos de usuario local de SQL Server a SQL Server en una máquina virtual de Azure. Este artículo se explique brevemente diversos métodos, recomienda el mejor método para diversos escenarios e incluir un [tutorial](#azure-vm-deployment-wizard-tutorial) para guiarle a través del uso del Asistente para **implementar una base de datos de SQL Server a una VM de Microsoft Azure** . 

Es el método mediante el Asistente para **implementar una base de datos de SQL Server a una VM de Microsoft Azure** que se describe en el [tutorial](#azure-vm-deployment-wizard-tutorial) para el modelo de la implementación clásica. 

## <a name="what-are-the-primary-migration-methods"></a>¿Cuáles son los métodos de migración principal?

Los métodos de migración principal son:

- Utilice la implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM
- Realizar copia de seguridad local con la compresión y copie manualmente el archivo de copia de seguridad en la máquina virtual de Azure
- Realizar una copia de seguridad a la dirección URL y restauración en la máquina virtual Azure desde la dirección URL
- Separar y, a continuación, copie los archivos de datos y de registro para almacenamiento Azure blob y, a continuación, adjuntar a SQL Server en la máquina virtual de Azure desde dirección URL
- Convertir máquina física local en VHD de Hyper-V, cargar en almacenamiento Azure Blob y luego implementar como nueva máquina virtual usando cargado VHD
- Unidad de disco duro de envío mediante servicio de importación y exportación de Windows
- Si tiene una implementación AlwaysOn local, utilice el [Asistente para agregar réplicas de Azure](virtual-machines-windows-classic-sql-onprem-availability.md) para crear una réplica de Azure y, a continuación, conmutación por error, que señala a los usuarios a la instancia de base de datos de Azure
- Utilizar [la duplicación transaccional](https://msdn.microsoft.com/library/ms151176.aspx) de SQL Server para configurar la instancia de Azure SQL Server como un suscriptor y, a continuación, deshabilite la replicación, que señala a los usuarios a la instancia de base de datos de Azure



## <a name="choosing-your-migration-method"></a>Elegir el método de migración

Para el rendimiento de transferencia de datos óptima, la migración de los archivos de base de datos en la máquina virtual de Azure utilizando un archivo de copia de seguridad comprimido suele ser el mejor método. Este es el método que utiliza la [implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) . Este asistente es el método recomendado para migrar de una ejecución de base de datos de usuario local en SQL Server 2005 o posterior para SQL Server 2014 o mayor cuando el archivo de copia de seguridad de base de datos comprimida está menos de 1 TB.

Para minimizar el tiempo de inactividad durante el proceso de migración de base de datos, utilice la opción AlwaysOn o la opción de duplicación transaccional.

Si no es posible utilizar los métodos anteriores, migrar manualmente la base de datos. Con este método, suele iniciarse con una copia de seguridad de la base de datos seguida de una copia de la en Azure de copia de seguridad de la base de datos y, a continuación, realice una restauración de base de datos. Puede copiar también los archivos de base de datos en Azure y luego adjuntarlos. Existen varios métodos que puede llevar a cabo este proceso manual de migrar una base de datos en una máquina virtual de Azure.

> [AZURE.NOTE] Al actualizar a SQL Server 2014 o 2016 de SQL Server de versiones anteriores de SQL Server, debe considerar si son necesarios cambios. Recomendamos que enfrentan todas las dependencias de características no admitidas por la nueva versión de SQL Server como parte de su proyecto de migración. Para obtener más información sobre las ediciones compatibles y escenarios, vea [actualizar a SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

En la siguiente tabla enumera cada uno de los métodos de migración principal y se explica cuándo es más apropiado el uso de cada método.

| Método  | Versión de la base de datos de origen  |  Versión de la base de datos de destino | Restricción de tamaño de copia de seguridad de base de datos de origen  | Notas  |
|---|---|---|---|---|
| [Utilice la implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 o superior | SQL Server 2014 o mayor | < 1 TB  | Método más rápido y sencillo, uso siempre que sea posible migrar a una instancia de SQL Server nueva o existente en una máquina virtual en Azure | 
| [Utilice el agregar Asistente para crear réplicas de Azure](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 o superior | SQL Server 2012 o superior | [Límite de almacenamiento de Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Minimiza el downtime, Utilícelo cuando disponga de una implementación de local AlwaysOn |
| [Usar la replicación transaccional de SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizar cuando necesite minimizar el downtime y no tienen una implementación local de AlwaysOn |
| [Realizar copia de seguridad local con la compresión y copie manualmente el archivo de copia de seguridad en la máquina virtual de Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizar sólo cuando no se puede utilizar al asistente, por ejemplo, cuando la versión de la base de datos de destino es menor que CU2 de SP1 de SQL Server 2012 o el tamaño de copia de seguridad de base de datos es mayor que 1 TB (12,8 TB con 2016 de SQL Server) |
| [Realizar una copia de seguridad a la dirección URL y restauración en la máquina virtual Azure desde la dirección URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 o superior | SQL Server 2012 SP1 CU2 o superior | < 12,8 TB de 2016, SQL Server, en caso contrario < 1 TB | Generalmente mediante la [copia de seguridad a la dirección URL](https://msdn.microsoft.com/library/dn435916.aspx) es equivalente mediante el Asistente para el rendimiento y no tan fácil |
| [Separar y, a continuación, copie los archivos de datos y de registro para almacenamiento Azure blob y adjuntar a SQL Server en la máquina virtual de Azure desde dirección URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 o superior | SQL Server 2014 o mayor | [Límite de almacenamiento de Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilice este método cuando planea [almacenar estos archivos mediante el servicio de almacenamiento Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) y adjuntarlos a la de SQL Server que se ejecuta en una VM de Azure, especialmente con grandes bases de datos |
| [Convertir la máquina local a discos duros virtuales de Hyper-V, cargar en almacenamiento Azure Blob y, a continuación, implementar una nueva máquina virtual usando VHD cargado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Se utiliza cuando [Traer su propia licencia de SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md), al migrar una base de datos que se ejecutará en una versión anterior de SQL Server o al migrar el usuario y del sistema de bases de datos juntos como parte de la migración de la base de datos depende de otras bases de datos de usuario o las bases de datos del sistema. |
| [Unidad de disco duro de envío mediante servicio de importación y exportación de Windows](#ship-hard-drive) | SQL Server 2005 o superior | SQL Server 2005 o superior | [Límite de almacenamiento de Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizar el [Servicio de importación y exportación de Windows](../storage/storage-import-export-service.md) cuando el método de copia manual es demasiado lento, como con bases de datos muy grandes |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Tutorial de Azure VM deployment wizard

Utilice el Asistente para **implementar una base de datos de SQL Server a una VM de Microsoft Azure** en Microsoft SQL Server Management Studio para migrar un servidor SQL Server 2005, el usuario local de SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 o 2016 de SQL Server de base de datos (hasta 1 TB) a SQL Server 2014 o 2016 de SQL Server en una máquina virtual en Azure. Utilice a este asistente para migrar una base de datos de usuario a una máquina virtual Azure existente o a una máquina virtual de Azure con SQL Server creada por el asistente durante el proceso de migración. Al migrar una base de datos a una versión más reciente de SQL Server, la base de datos se actualiza automáticamente durante el proceso.

El método es sólo el modelo clásico de implementación. 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Obtener última versión de la implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM

Utilice la versión más reciente de Microsoft SQL Server Management Studio para SQL Server para asegurarse de que tiene la versión más reciente del Asistente para **implementar una base de datos de SQL Server a una VM de Microsoft Azure** . La versión más reciente de este asistente incorpora las actualizaciones más recientes en el portal del clásico Azure y admite las imágenes de Azure VM más reciente en la Galería (las versiones anteriores del asistente no funcionen). Para obtener la versión más reciente de Microsoft SQL Server Management Studio para SQL Server, [descargarlo](http://go.microsoft.com/fwlink/?LinkId=616025) e instalarlo en un equipo cliente con conexión a la base de datos que va a migrar y a internet.

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>Configurar el equipo virtual Azure existente y la instancia de SQL Server (si procede)

Si va a migrar una máquina virtual existente de Azure, se requieren los siguientes pasos de configuración:

- Configurar la máquina virtual de Azure y la instancia de SQL Server para habilitar la conectividad desde otro equipo, siga los pasos para [conectarse a la instancia de SQL Server VM de SSMS en otro equipo](virtual-machines-windows-sql-connect.md). Sólo el 2014 de SQL Server y SQL Server 2016 imágenes en la Galería son compatibles si va a migrar con el asistente.
- Configurar un extremo abierto para el servicio de nube adaptador de SQL Server en la puerta de enlace de Microsoft Azure con puerto privado de 11435. Este puerto se crea como parte del aprovisionamiento de 2014 de SQL Server o SQL Server 2016 en una VM de Microsoft Azure. El adaptador de nube también crea una regla de Firewall de Windows para permitir las conexiones TCP entrantes en el puerto predeterminado 11435. Este extremo permite al Asistente para utilizar el servicio de adaptador de nube para copiar los archivos de copia de seguridad de la instancia local a la máquina virtual de Azure. Para obtener más información, vea [Adaptador de nube para SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

    ![Crear extremo del adaptador de nube](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Ejecutar el uso de la implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM

1. Abra Microsoft SQL Server Management Studio de 2016, Microsoft SQL Server y conéctese a la instancia de SQL Server que contiene la base de datos de usuario que va a migrar a una VM de Azure.
2. Haga clic en la base de datos que va a migrar, seleccione tareas y, a continuación, haga clic en implementar una VM de Microsoft Azure.

    ![Asistente de inicio](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. En la página Introducción, haga clic en siguiente.
4. En la página de configuración de código fuente, conectarse a la instancia de SQL Server que contiene la base de datos que va a migrar a una VM de Azure.
5. Especifique una ubicación temporal para los archivos de copia de seguridad. Si se conecta a un servidor remoto, debe especificar una unidad de red.

    ![Configuración de código fuente](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Haga clic en siguiente.
7. En la página de inicio de sesión de Microsoft Azure, haga clic en iniciar sesión e inicio de sesión a su cuenta de Azure.
8. Seleccione la suscripción que desea utilizar y haga clic en siguiente.

    ![Identifícate Azure](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. En la página de configuración de implementación, puede especificar una nueva o un servicio de nube existente nombre y nombre de la máquina Virtual:

 - Especifique un nuevo nombre en el nombre de servicio de nube y la máquina Virtual para crear un nuevo servicio de nube con una nueva máquina virtual Azure mediante una imagen de 2014 de SQL Server o SQL Server 2016 Galería.

     - Si especifica un nuevo nombre de servicio de nube, especifique la cuenta de almacenamiento que va a utilizar.

     - Si especifica un nombre de servicio de nube existente, la cuenta de almacenamiento se recuperará y se escriba de forma automática.

 - Especifique un nombre de servicio de nube existente y el nuevo nombre de máquina Virtual para crear una nueva máquina virtual Azure en un servicio de nube existente. Especifique sólo una imagen de la Galería 2014 de SQL Server o SQL Server 2016.
 - Especifique un nombre de servicio de nube existente y el nombre de máquina Virtual para usar una máquina virtual de Azure existente. Debe ser una imagen construida mediante una imagen de galería 2014 de SQL Server o SQL Server 2016.

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Haga clic en configuración
  - Si especifica un nombre de servicio de nube existente y el nombre de la máquina Virtual, se le pedirá que proporcione el nombre de usuario y la contraseña.

        ![Configuración de la máquina de Azure](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - Si especifica un nuevo nombre de la máquina Virtual, se le pedirá que seleccione una imagen de la lista de la Galería de imágenes y proporcione la siguiente información:
      - Imagen: seleccione sólo 2014 de SQL Server o SQL Server 2016
        - Nombre de usuario
        - Nueva contraseña
        - Confirmar contraseña
        - Ubicación
        - Tamaño.
    - Además, haga clic para aceptar el certificado generado automáticamente para esta nueva Azure Máquina Virtual de Microsoft y, a continuación, haga clic en Aceptar.

    ![Configuración de la máquina nueva Azure](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Especifique el nombre de la base de datos de destino si es diferente del nombre de la base de datos de origen. Si la base de datos de destino ya existe, el sistema será automáticamente incrementar el nombre de la base de datos en lugar de sobrescribir la base de datos existente.
12. Haga clic en siguiente y, a continuación, haga clic en Finalizar.

    ![Resultados](./media/virtual-machines-windows-migrate-sql/results.png)

13. Cuando el asistente finaliza, conéctese a la máquina virtual y compruebe que se ha migrado la base de datos.
14. Si ha creado una nueva máquina virtual, configurar la máquina virtual de Azure y la instancia de SQL Server, siga los pasos para [conectarse a la instancia de SQL Server VM de SSMS en otro equipo](virtual-machines-windows-sql-connect.md).

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>Copia de seguridad para el archivo y copiar a VM y restauración

Utilice este método cuando no se puede utilizar la implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM porque va a migrar a una versión de SQL Server anteriores a SQL Server 2014 o el archivo de copia de seguridad es mayor que 1 TB. Si el archivo de copia de seguridad es mayor que 1 TB, debe bandas porque el tamaño máximo de un disco VM es de 1 TB. Utilice los siguientes pasos generales para migrar una base de datos de usuario con este método manual:

1.  Realizar una copia de seguridad completa de la base de datos a una ubicación local.
2.  Crear o cargar una máquina virtual con la versión de SQL Server que desea.
3.  Instalación basada en sus requerimientos de conectividad. Vea [Conectar con un equipo de SQL Server Virtual en Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4.  Copie los archivos de copia de seguridad en la VM mediante Escritorio remoto, el Explorador de Windows o el comando Copiar desde un símbolo del sistema.

## <a name="backup-to-url-and-restore"></a>Copia de seguridad de URL y restauración

Utilice el método de [copia de seguridad a la dirección URL](https://msdn.microsoft.com/library/dn435916.aspx) cuando no puede utilizar la implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM porque el archivo de copia de seguridad es mayor de 1 TB y están migrando desde y hacia SQL Server 2016. Para bases de datos menores que 1 TB o ejecutando una versión de SQL Server antes de 2016 de SQL Server, se recomienda el uso del asistente. Con 2016 de SQL Server, conjuntos de copia de seguridad con bandas son compatibles, se recomienda para rendimiento y necesarios para superar los límites de tamaño por el blob. Para bases de datos muy grandes, se recomienda el uso del [Servicio de importación y exportación de Windows](../storage/storage-import-export-service.md) .

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>Separar y copie la dirección URL y adjuntar desde dirección URL

Utilice este método cuando se planea [almacenar estos archivos mediante el servicio de almacenamiento Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) y adjuntarlos a la de SQL Server que se ejecuta en una VM de Azure, particularmente con bases de datos muy grandes. Utilice los siguientes pasos generales para migrar una base de datos de usuario con este método manual:

1.  Separar los archivos de base de datos de la instancia de base de datos local.
2.  Copie los archivos de base de datos separada en almacenamiento Azure blob mediante la [utilidad de línea de comandos AZCopy](../storage/storage-use-azcopy.md).
3.  Adjuntar los archivos de base de datos de la dirección URL de Azure a la instancia de SQL Server en la máquina virtual de Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Convertir a VM y cargar a URL e implementar como nueva máquina virtual

Utilice este método para migrar todas las bases de datos de sistema y de usuario en una instancia de SQL Server local a la máquina virtual de Azure. Utilice los siguientes pasos generales para migrar una instancia completa de SQL Server con este método manual:

1.  Convertir máquinas físicas o virtuales a discos duros virtuales de Hyper-V mediante [Convertidor de máquina Virtual de Microsoft](http://technet.microsoft.com/library/dn873998.aspx).
2.  Cargar archivos VHD en el almacenamiento de Azure mediante el [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.  Implementar una nueva máquina virtual con el VHD cargado.

> [AZURE.NOTE] Para migrar una aplicación completa, considere el uso de [Recuperación de sitios de Azure](../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Unidad de disco duro de envío

Utilice el [método de servicio de importación y exportación de Windows](../storage/storage-import-export-service.md) para transferir grandes cantidades de datos de archivos a almacenamiento Azure Blob en situaciones donde la carga a través de la red es prohibitivo o no factible. Con este servicio, enviar una o más unidades de disco duro que contiene los datos que va a un centro de datos de Azure, donde los datos se cargarán a tu cuenta de almacenamiento.

## <a name="next-steps"></a>Próximos pasos

Para obtener más información acerca de cómo ejecutar SQL Server Azure máquinas virtuales, consulte [SQL Server en máquinas virtuales de Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Para obtener instrucciones sobre cómo crear una máquina Virtual de Azure SQL Server desde una imagen capturada, consulte [sugerencias y trucos sobre la 'clonación' máquinas virtuales de SQL Azure desde las imágenes captadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) en el blog de ingenieros de SQL Server de CSS.
