<properties
    pageTitle="Aprovisionar a un equipo Virtual con SQL Server | Microsoft Azure"
    description="Crear y conectarse a una máquina virtual de SQL Server en Azure con el Portal. En este tutorial se utiliza el modo de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionar una máquina virtual de SQL Server en el Portal de Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Este tutorial to-end muestra cómo usar el Portal de Azure proporcionando una máquina virtual que ejecuta SQL Server.

La Galería de Azure máquina virtual () incluye varias imágenes que contienen Microsoft SQL Server. Con unos pocos clics, puede seleccionar una de las imágenes de VM SQL de la galería y aprovisionar en su entorno de Azure.

En este tutorial, podrá:

- [Seleccione una imagen de VM de SQL de la Galería](#select-a-sql-vm-image-from-the-gallery)
- [Configurar y crear la máquina virtual](#configure-the-vm)
- [Abra la máquina virtual con Escritorio remoto](#open-the-vm-with-remote-desktop)
- [Conectarse a SQL Server de forma remota](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Seleccione una imagen de VM de SQL de la Galería

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con su cuenta.

    >[AZURE.NOTE] Si no tiene una cuenta de Azure, visite [Azure prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

1. En el portal de Azure, haga clic en **nuevo**. El portal abre el módulo de **nuevo** . Los recursos de máquina virtual de SQL Server se encuentran en el grupo de **máquinas virtuales** del catálogo de soluciones.

1. En el módulo de **nuevo** , haga clic en **máquinas virtuales de Windows**.

1. Para ver todas las imágenes disponibles, haga clic en **Ver todo** en el módulo de **máquinas virtuales de Windows** .

    ![Módulo de máquinas virtuales de Windows Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. En **servidores de base de datos**, haga clic en **SQL Server**. Es posible que tenga que desplazarse hacia abajo para localizar **los servidores de base de datos**. Revise las plantillas disponibles de SQL Server.

    ![Imágenes de la Galería de máquina virtual SQL](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Cada plantilla identifica una versión de SQL Server y un sistema operativo. Seleccione una de estas imágenes de la lista. A continuación, revise el módulo de detalles que proporciona una descripción de la imagen de la máquina virtual.

    >[AZURE.NOTE] Imágenes de SQL VM incluyen los costes de licencia para SQL Server en el precio por minuto de la máquina virtual que cree. Hay otra opción para traer-su-propietario-licencia (BYOL) y pagar solo para la máquina virtual. Los nombres de imagen prefijo con {BYOL}. Para obtener más información sobre esta opción, vea [Introducción a SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).

1. En **Seleccionar un modelo de implementación**, compruebe que está seleccionado el **Administrador de recursos** . Administrador de recursos es el modelo de implementación recomendado para nuevas máquinas virtuales. Haga clic en **crear**.

    ![Crear VM SQL con el Administrador de recursos](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurar la máquina virtual
Hay cinco módulos para configurar una máquina virtual de SQL Server.

| Paso               | Descripción                          |
|---------------------|-------------------------------|
| **Conceptos básicos**              | [Configuración básica](#1-configure-basic-settings)      |
| **Tamaño**                | [Elija el tamaño de la máquina virtual](#2-choose-virtual-machine-size)   |
| **Configuración**            | [Configurar características opcionales](#3-configure-optional-features)   |
| **Configuración de SQL Server** | [Configuración de SQL server](#4-configure-sql-server-settings) |
| **Resumen**             | [Revise el resumen](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. configurar configuración básica
En el módulo de **conceptos básicos** , proporcione la siguiente información:

* Escriba una **nombre**de máquina virtual única.
* Especifique un **nombre de usuario** para la cuenta de administrador local en la máquina virtual. Esta cuenta también se agrega a la función de servidor fija **sysadmin** de SQL Server.
* Proporcione una **contraseña**de segura.
* Si tiene varias suscripciones, compruebe que la suscripción es correcta para la nueva máquina virtual.
* En el cuadro de **grupo de recursos** , escriba un nombre para un nuevo grupo de recursos. O bien, para usar un recurso existente grupo clic **Seleccione existente**. Un grupo de recursos es una colección de recursos relacionados de Azure (máquinas virtuales, cuentas de almacenamiento, redes virtuales, etcetera).

    >[AZURE.NOTE] Uso de un nuevo grupo de recursos es útil si solo se pruebas o más información sobre implementaciones de SQL Server en Azure. Después de completar la prueba, elimine el grupo de recursos para eliminar automáticamente la máquina virtual y todos los recursos asociados con ese grupo de recursos. Para obtener más información acerca de los grupos de recursos, vea [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

* Seleccione una **ubicación** para esta implementación.
* Haga clic en **Aceptar** para guardar la configuración.

    ![Módulo de conceptos básicos SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. elegir el tamaño de la máquina virtual
En el paso de **tamaño** , elija un tamaño de la máquina virtual en el módulo de **Elegir un tamaño** . El módulo inicialmente muestra los tamaños de máquina recomendada basados en la plantilla seleccionada. También se calcula el coste mensual para ejecutar la máquina virtual.

![Opciones de tamaño de la máquina virtual de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Para cargas de trabajo de producción, recomendamos que seleccione un tamaño de la máquina virtual que admite el [Almacenamiento de Premium](../storage/storage-premium-storage.md). Si no se requieren ese nivel de rendimiento, use el botón **Ver todos** , que muestra todas las opciones de tamaño del equipo. Por ejemplo, puede usar un tamaño de la máquina para un entorno de prueba o desarrollo.

>[AZURE.NOTE] Para obtener más información acerca de la máquina virtual tamaños, consulte [tamaños para máquinas virtuales](virtual-machines-windows-sizes.md). Consideraciones al usar sobre los tamaños de la máquina virtual de SQL Server, consulte [prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-performance.md).

Elija el tamaño del equipo y, a continuación, haga clic en **Seleccionar**.

## <a name="3-configure-optional-features"></a>3. Configurar características opcionales
En el módulo de **configuración** , configurar el almacenamiento de Azure, redes y la supervisión de la máquina virtual.

- En **almacenamiento**, especifique un **tipo de disco** de estándar o Premium (SSD). Almacenamiento de Premium se recomienda para cargas de trabajo de producción.

>[AZURE.NOTE] Si selecciona Premium (SSD) para un tamaño de equipo que no admite el almacenamiento de Premium, se cambia automáticamente el tamaño de su equipo.  

- En la **cuenta de almacenamiento**, puede aceptar el nombre de la cuenta de almacenamiento aprovisiona automáticamente. También puede hacer clic en la **cuenta de almacenamiento** para elegir una cuenta existente y configurar el tipo de cuenta de almacenamiento. De forma predeterminada, Azure crea una nueva cuenta de almacenamiento con almacenamiento redundante local. Para obtener más información acerca de las opciones de almacenamiento, consulte [replicación de almacenamiento de Azure](../storage/storage-redundancy.md).

- En la **red**, puede aceptar los valores rellenados automáticamente. También puede hacer clic en cada característica para configurar manualmente la **red Virtual**, la **subred**, la **dirección IP pública**y el **Grupo de seguridad de red**. Para el propósito de este tutorial, mantenga los valores predeterminados.

- Azure permite la **supervisión** de forma predeterminada con la misma cuenta de almacenamiento designada para la máquina virtual. Puede cambiar esta configuración aquí.

- En **establece disponibilidad**, especifique un conjunto de disponibilidad. Para el propósito de este tutorial, seleccione **Ninguno**. Si va a configurar grupos de disponibilidad AlwaysOn SQL, configure la disponibilidad para evitar volver a crear la máquina virtual.  Para obtener más información, consulte [Administrar la disponibilidad de las máquinas virtuales](virtual-machines-windows-manage-availability.md).

Cuando haya terminado esta configuración, haga clic en **Aceptar**.

## <a name="4-configure-sql-server-settings"></a>4. configuración de SQL server
En el módulo de **configuración de SQL Server** , configurar valores específicos y las optimizaciones para SQL Server. La configuración que puede configurar para SQL Server incluye las siguientes.

| Configuración               |
|---------------------|
| [Conectividad](#connectivity)              |
| [Autenticación](#authentication)                |
| [Configuración de almacenamiento](#storage-configuration)            |
| [Automatizado de revisión](#automated-patching) |
| [Copia de seguridad automática](#automated-backup)             |
| [Integración de Azure depósito clave](#azure-key-vault-integration)             |
| [Servicios de R](#r-services) |

### <a name="connectivity"></a>Conectividad
En **conectividad de SQL**, especifique el tipo de acceso que desea para la instancia de SQL Server en esta máquina virtual. Para el propósito de este tutorial, seleccione **público (internet)** para permitir conexiones a SQL Server en máquinas o servicios de internet. Con esta opción seleccionada, Azure configura automáticamente el firewall y el grupo de seguridad de la red para permitir el tráfico en el puerto 1433.  

![Opciones de conectividad SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Para conectarse a SQL Server a través de internet, también debe habilitar autenticación de SQL Server, que se describe en la siguiente sección.

>[AZURE.NOTE] Es posible agregar más restricciones para las comunicaciones de red a su VM de SQL Server. Puede hacer esto mediante la edición del grupo de seguridad de la red después de crea la máquina virtual. Para obtener más información, vea [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)

Si prefiere no permitir las conexiones con el motor de base de datos a través de internet, elija una de las siguientes opciones:

- **Local (dentro de la máquina virtual solo)** para permitir conexiones a SQL Server sólo desde dentro de la máquina virtual.
- **Privado (dentro de una red Virtual)** para permitir conexiones a SQL Server en máquinas o servicios en la misma red virtual.

>[AZURE.NOTE] La imagen de la máquina virtual de SQL Server Express edition no habilita automáticamente el protocolo TCP/IP. Esto es cierto incluso para las opciones de conectividad públicas y privadas. Para Express edition, debe usar el Administrador de configuración de SQL Server para [habilitar manualmente el protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) después de crear la máquina virtual.

Mejorar la seguridad en general, eligiendo la conectividad más restrictiva que permite a su caso. Pero todas las opciones son protegibles mediante reglas de grupo de seguridad de la red y la autenticación de SQL o Windows.

**Puerto** predeterminado es 1433. Puede especificar un número de puerto diferente.
Para obtener más información, vea [conectarse a una máquina Virtual de SQL Server (Administrador de recursos) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autenticación
Si requiere autenticación de SQL Server, haga clic en **Habilitar la** **autenticación de SQL**.

![Autenticación de SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Si tiene previsto tener acceso a SQL Server a través de internet (es decir, la opción de conectividad pública), debe habilitar la autenticación de SQL aquí. Acceso del público a SQL Server requiere el uso de autenticación de SQL.

Si habilita la autenticación de SQL Server, especifique un **nombre de inicio de sesión** y una **contraseña**. Este nombre de usuario está configurado como un inicio de sesión de autenticación de SQL Server y un miembro de la función de servidor de fija **sysadmin** . Vea [Elegir un modo de autenticación](http://msdn.microsoft.com/library/ms144284.aspx) para obtener más información acerca de los modos de autenticación.

Si no habilita la autenticación de SQL Server, puede usar la cuenta de administrador local en la máquina virtual para conectarse a la instancia de SQL Server.

### <a name="storage-configuration"></a>Configuración de almacenamiento
Haga clic en **configuración de almacenamiento** para especificar los requisitos de almacenamiento.

![Configuración de almacenamiento SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Si selecciona almacenamiento estándar, esta opción no está disponible. Optimización de almacenamiento automático sólo está disponible para el almacenamiento de Premium.

Puede especificar requisitos como operaciones de entrada y salida por segundo (IOPs), rendimiento en MB/s y el tamaño de almacenamiento total. Configurar estos valores mediante las escalas deslizante. El portal calcula automáticamente el número de discos basados en estos requisitos.

De forma predeterminada, Azure optimiza el almacenamiento de 5000 IOPs, 200 MB y 1 GB de espacio de almacenamiento. Puede cambiar esta configuración de almacenamiento en función de la carga de trabajo. En el **almacenamiento optimizado para**, seleccione una de las siguientes opciones:

- **General** es la configuración predeterminada y es compatible con la mayoría de las cargas de trabajo.
- Procesamiento **transaccional** optimiza el almacenamiento de cargas de trabajo OLTP tradicional de la base de datos.
- **Almacenamiento de datos** optimiza el almacenamiento de las cargas de trabajo de análisis y generación de informes.

>[AZURE.NOTE] Los límites superiores en los controles deslizantes varían según el tamaño de la máquina virtual seleccionada.

### <a name="automated-patching"></a>Automatizado de revisión
**Automatizado revisiones** está habilitado de forma predeterminada. Revisiones automatizada permiten Azure a SQL Server y el sistema operativo revisiones automáticamente. Especifique un día de la semana, la hora y la duración de una ventana de mantenimiento. Azure realiza revisiones en esta ventana de mantenimiento. La programación de la ventana de mantenimiento, usa la configuración regional VM por vez. Si no desea Azure a SQL Server y el sistema operativo revisiones automáticamente, haga clic en **Deshabilitar**.  

![SQL automatizado de revisión](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para obtener más información, vea [Automatizado revisiones para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Copia de seguridad automática
Habilitar las copias de seguridad de base de datos automática para todas las bases de datos en **copias de seguridad automatizadas**. Copia de seguridad automática está deshabilitado de forma predeterminada.

Cuando se habilita la copia de seguridad automática de SQL, puede configurar lo siguiente:

- Período de retención (días) para las copias de seguridad
- Cuenta de almacenamiento para las copias de seguridad
- Opción de cifrado y la contraseña para las copias de seguridad

Para cifrar la copia de seguridad, haga clic en **Habilitar**. A continuación, especifique la **contraseña**. Azure crea un certificado para cifrar las copias de seguridad y utiliza la contraseña especificada para proteger el certificado.

![Copias de seguridad automatizadas de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Para obtener más información, consulte [Copia de seguridad automática para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integración de Azure depósito de clave
Para almacenar información confidencial de seguridad en Azure para el cifrado, haga clic en **integración de Azure depósito clave** y haga clic en **Habilitar**.

![Integración de SQL Azure depósito clave](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

La siguiente tabla enumeran los parámetros necesarios para configurar la integración de depósito de clave de Azure.

|PARÁMETRO|DESCRIPCIÓN|EJEMPLO|
|----------|----------|-------|
|**Dirección URL de la cámara clave** |La ubicación de la cámara clave.|https://contosokeyvault.Vault.Azure.NET/ |
|**Nombre principal** |Azure Active Directory nombre principal de servicio. Este nombre también se conoce como el identificador de cliente.  |5-4e11-af04eb07b669ccf2 fde2b411 - 33d|
| **Secreto principal**|Azure Active Directory principal secreto de servicio. Este secreto también se conoce como el secreto de cliente. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Nombre de la credencial**|**Nombre de la credencial**: integración de AKV crea una credencial dentro de SQL Server, permitiendo la máquina virtual tener acceso a la cámara clave. Elija un nombre para esta credencial.| mycred1|

Para obtener más información, vea [Configurar Azure clave depósito de integración de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-ps-sql-keyvault.md).

Cuando haya terminado de configurar la configuración de SQL Server, haga clic en **Aceptar**.

### <a name="r-services"></a>Servicios de R
Para SQL Server 2016 Enterprise edition, tiene la opción de habilitar [Servicios R de SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). Esto le permite usar funciones analíticas avanzadas con SQL Server 2016. En el módulo de **Configuración de SQL Server** , haga clic en **Habilitar** .

![Habilitar servicios de SQL Server R](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Imágenes de SQL Server que no están 2016 Enterprise edition, la opción Habilitar servicios R está deshabilitada.

## <a name="5-review-the-summary"></a>5. Revise el resumen
En el módulo de **Resumen** , revise el resumen y haga clic en **Aceptar** para crear SQL Server, grupo de recursos y recursos especificados para esta máquina virtual.

Puede supervisar la implementación desde el portal de azure. El botón de **notificaciones** en la parte superior de la pantalla muestra el estado básico de la implementación.

>[AZURE.NOTE] Para proporcionarle una idea en los tiempos de implementación, implementa una VM SQL en la región de Estados Unidos oriental con la configuración predeterminada. Esta implementación de prueba realizó un total de 26 minutos en completarse. Pero que surjan rápido o el tiempo de implementación más lento basándose en su región y la configuración seleccionada.

## <a name="open-the-vm-with-remote-desktop"></a>Abra la máquina virtual con Escritorio remoto

Para conectarse a la máquina virtual con Escritorio remoto, siga estos pasos:

1. Después de la máquina virtual de Azure se basa en el icono de la máquina virtual aparece en el panel de Azure. También puede encontrar examinando las máquinas virtuales existentes. Haga clic en la nueva máquina virtual SQL. Un módulo de **Máquina Virtual** muestra los detalles de la máquina virtual.
1. En la parte superior de la hoja de **Máquina Virtual** , haga clic en **Conectar**.
1. El explorador descarga un archivo RDP para la máquina virtual. Abra el archivo RDP.
    ![Escritorio remoto para máquina virtual de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. Conexión a Escritorio remoto le notifica que no se puede identificar el Editor de esta conexión remota. Haga clic en **Conectar** para continuar.
1. En el cuadro de diálogo **Seguridad de Windows** , haga clic en **usar otra cuenta**.
1. En **nombre de usuario** , escriba ** \<nombre de usuario >**, donde <user name> es el nombre de usuario que especificó al configurar la máquina virtual. Deberá agregar una barra diagonal inversa inicial delante del nombre.
1. Escriba la **contraseña** que ya se configuró para este VM y, a continuación, haga clic en **Aceptar** para conectarse.
1. Si otro cuadro de diálogo **Conexión a Escritorio remoto** le pregunta si desea conectarse, haga clic en **Sí**.

Después de conectar con la máquina virtual de SQL Server, puede iniciar SQL Server Management Studio y conectar con la autenticación de Windows con sus credenciales de administrador local. Si habilitó la autenticación de SQL Server, también puede conectar con la autenticación de SQL mediante el inicio de sesión SQL y la contraseña que configura durante el aprovisionamiento.

Acceso al equipo le permite cambiar directamente el equipo y la configuración de SQL Server en función de sus necesidades. Por ejemplo, puede configurar el firewall o cambiar la configuración de SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Conectarse a SQL Server de forma remota

En este tutorial, seleccionamos acceso **público** para la máquina virtual y la **Autenticación de SQL Server**. Esta configuración automáticamente la máquina virtual para permitir las conexiones de SQL Server desde cualquier cliente a través de internet (suponiendo que tienen el inicio de sesión SQL correcta).

>[AZURE.NOTE] Si no seleccionó público durante el aprovisionamiento, son necesarios pasos adicionales para tener acceso a la instancia de SQL Server a través de internet. Para obtener más información, vea [conectarse a una máquina Virtual de SQL Server](virtual-machines-windows-sql-connect.md).

Las secciones siguientes muestran cómo conectarse a su instancia de SQL Server en la máquina virtual desde otro equipo a través de internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el uso de SQL Server en Azure, consulte las [Preguntas más frecuentes](virtual-machines-windows-sql-server-iaas-faq.md)y [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md) .

Para un vídeo de introducción de SQL Server en Azure máquinas virtuales de Windows, vea [Azure VM es la mejor plataforma para SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorar la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Windows Azure.
