<properties
    pageTitle="SQL Server Business Intelligence | Microsoft Azure"
    description="Este tema usa recursos creados con el modelo de implementación clásica y describe las características de inteligencia empresarial (BI) de SQL Server se ejecuta en Azure máquinas virtuales de Windows (VM)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Inteligencia empresarial de SQL Server en máquinas virtuales de Windows Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

La Galería de máquina Virtual de Microsoft Azure incluye imágenes que contienen las instalaciones de SQL Server. Las ediciones de SQL Server compatibles con la Galería de imágenes son los mismos archivos de instalación, que puede instalar en equipos locales y máquinas virtuales. En este tema se resumen las características de Business Intelligence (BI) de SQL Server instaladas en las imágenes y los pasos de configuración necesarios después de que se aprovisiona una máquina virtual. Este tema describe también topologías de implementación compatibles para características de BI y los procedimientos recomendados.

## <a name="license-considerations"></a>Consideraciones de licencia

Hay dos formas de licencia de SQL Server en Microsoft Azure máquinas virtuales de Windows:

1. Ventajas de movilidad de licencia que forman parte de Software Assurance. Para obtener más información, vea [Licencia movilidad a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Pagar por la tasa de horas de Azure máquinas virtuales de Windows con SQL Server instalado. Consulte la sección "SQL Server" en [Máquinas virtuales de precios](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para obtener más información sobre las tasas de licencia y actuales, vea [Máquinas virtuales licencias preguntas más frecuentes](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server imágenes disponibles en la Galería de máquina Virtual Azure

La Galería de máquina Virtual de Microsoft Azure incluye varias imágenes que contienen Microsoft SQL Server. El software instalado en las imágenes de máquina virtual varía según la versión del sistema operativo y la versión de SQL Server. La lista de imágenes disponibles en la Galería de Azure máquina virtual cambia con frecuencia.

![Imagen SQL en la Galería de azure VM](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) El siguiente script de PowerShell devuelve la lista de imágenes de Azure que contienen "SQL Server" en la ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para obtener más información sobre las ediciones y las características compatibles con SQL Server, consulte lo siguiente:

- [Ediciones de SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Características compatibles con las ediciones de SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Características de BI instaladas en las imágenes de la Galería de máquina Virtual de SQL Server

La siguiente tabla resume las características de inteligencia empresarial instaladas en las imágenes de la Galería de máquina Virtual de Microsoft Azure comunes para SQL Server"

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- SQL Server 2014 SP1 Standard

- SQL Server 2012 SP2 Enterprise

- Estándar SQL Server 2012 Service Pack 2

|Características de BI de SQL Server|Instalado en la imagen de la Galería|Notas|
|---|---|---|
|**Modo nativo de Reporting Services**|Sí|Instalado pero requiere configuración, incluida la dirección URL de administrador de informes. Consulte la sección [Configurar Reporting Services](#configure-reporting-services).|
|**Modo de SharePoint de Reporting Services**|No|La imagen de la Galería de máquina Virtual de Microsoft Azure no incluye SharePoint o SharePoint archivos de instalación. <sup>1</sup>|
|**Minería de datos y Multidimensional de Analysis Services (OLAP)**|Sí|Instalado y configurado como la instancia de Analysis Services predeterminado|
|**Tabular de Analysis Services**|No|Compatible con SQL Server 2012, 2014 y 2016 imágenes pero no está instalado de forma predeterminada. Instalar otra instancia de Analysis Services. Consulte la sección instalar otras características y servicios de SQL Server en este tema.|
|**Analysis Services de PowerPivot para SharePoint**|No|La imagen de la Galería de máquina Virtual de Microsoft Azure no incluye SharePoint o SharePoint archivos de instalación. <sup>1</sup>|

<sup>1</sup> para obtener información adicional sobre SharePoint y máquinas virtuales de Windows Azure, consulte [Microsoft Azure arquitecturas para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [Implementación de SharePoint en Microsoft Azure máquinas virtuales de Windows](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Ejecute el siguiente comando PowerShell para obtener una lista de servicios instalados que contienen "SQL" en el nombre del servicio.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>General y recomendaciones

- El tamaño mínimo recomendado para una máquina virtual es **A3** al usar SQL Server Enterprise Edition. Se recomienda que el tamaño de la máquina virtual de **A4** para implementaciones de BI de SQL Server Analysis Services y Reporting Services.

    Para obtener información sobre los tamaños VM actuales, vea [Tamaños de máquina Virtual para Azure](virtual-machines-linux-sizes.md).

- Mejores prácticas para la administración de discos es almacenar datos, registro y archivos de copia de seguridad en unidades que no sean **C**: y **D**:. Por ejemplo, crear discos de datos **E**: y **F**:.

    - La unidad de almacenamiento en caché de la directiva de la unidad predeterminada **C**: no es óptima para trabajar con datos.

    - La **d.**: unidad es una unidad temporal que se utiliza principalmente para el archivo de la página. La **d.**: unidad no se conserva y no se guarda en el almacenamiento de blobs de Windows. Administración de tareas como un cambio en el tamaño de la máquina virtual restablece la **d.**: unidad. Se recomienda **no** usar la **d.**: unidad para los archivos de base de datos, incluyendo tempdb.

    Para obtener más información sobre cómo crear y conectar discos, consulte [cómo adjuntar un disco de datos a una máquina Virtual](virtual-machines-windows-classic-attach-disk.md).

- Detener o desinstalar servicios que no va a usar. Por ejemplo si la máquina virtual solo se usa para Reporting Services, detener o desinstalar Analysis Services y SQL Server Integration Services. La imagen siguiente es un ejemplo de los servicios que se inician de forma predeterminada.

    ![Servicios de SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] El motor de base de datos de SQL Server es necesario en los escenarios de BI admitidos. En un solo servidor topología VM, el motor de base de datos debe estar ejecutándose en la misma máquina virtual de.

    Para obtener más información, vea lo siguiente: [Desinstalar Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) y [desinstalar una instancia de Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Consulte **Windows Update** nueva 'actualizaciones importantes'. Con frecuencia se actualizan las imágenes de máquina Virtual de Microsoft Azure; Sin embargo actualizaciones importantes podrían estarán disponibles desde **Windows Update** después de la imagen de VM se actualizó por última vez.

## <a name="example-deployment-topologies"></a>Topologías de implementación de ejemplo

Los siguientes son implementaciones de ejemplo que usan máquinas virtuales de Microsoft Azure. Las topologías en estos diagramas son solo algunas de las posibles topologías que puede usar con características de BI de SQL Server y máquinas virtuales de Microsoft Azure.

### <a name="single-virtual-machine"></a>Máquina Virtual única

Analysis Services, Reporting Services, el motor de base de datos de SQL Server y orígenes de datos en una sola máquina virtual.

![escenario de NIC BI con 1 máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Dos máquinas virtuales

- Analysis Services, Reporting Services y el motor de base de datos de SQL Server en una única máquina virtual. Esta implementación incluye las bases de datos del servidor de informes.

- Orígenes de datos en una máquina virtual segunda. La segunda VM incluye el motor de base de datos de SQL Server como origen de datos.

![escenario de BI iaas con 2 máquinas virtuales](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure mixto: datos en la base de datos de SQL Azure

- Analysis Services, Reporting Services y el motor de base de datos de SQL Server en una única máquina virtual. Esta implementación incluye las bases de datos del servidor de informes.

- Origen de datos es la base de datos de SQL Azure.

![BI iaas escenarios vm y AzureSQL como origen de datos](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrido: datos locales

- En esta implementación de ejemplo de Analysis Services, Reporting Services y el motor de base de datos de SQL Server se ejecutan en una sola máquina virtual. La máquina virtual aloja las bases de datos del servidor de informes. La máquina virtual está unida a un dominio local a través de la red Virtual de Azure o algunas otra soluciones de túnel de VPN.

- Origen de datos es local.

![BI iaas escenarios vm y en los orígenes de datos local](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuración de modo nativo de Reporting Services

La imagen de la Galería de máquina virtual para SQL Server incluye el modo nativo de Reporting Services instalado, pero no está configurado el servidor de informes. Los pasos descritos en esta sección Configurar el servidor de informes de Reporting Services. Para obtener más información sobre la configuración de modo nativo de Reporting Services, vea [Instalar Reporting Services nativa modo informe Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Contenido similar que utiliza secuencias de comandos de Windows PowerShell para configurar el servidor de informes, consulte [Usar PowerShell para crear una Azure VM con unos nativa modo de servidor de informes](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Conectarse a la máquina Virtual e iniciar el Administrador de configuración de servicios de generación de informes

Hay dos flujos de trabajo comunes para conectarse a una máquina Virtual de Azure:

- Para conectar en, haga clic en el nombre de la máquina virtual y, a continuación, haga clic en **Conectar**. Se abre una conexión a Escritorio remoto y se rellena automáticamente el nombre del equipo.

    ![conectarse a azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Conectarse a la máquina virtual con conexión a Escritorio remoto. En la interfaz de usuario del escritorio remoto:

    1. Escriba el **nombre de servicio de nube** como el nombre del equipo.

    1. Escriba el número de puerto público que está configurado para el extremo de escritorio remoto de TCP y de dos puntos (:).

        Myservice.cloudapp.NET:63133

        Para obtener más información, vea [¿Qué es un servicio de nube?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Iniciar la configuración del Administrador de Reporting Services.**

1. En **Windows Server 2012**:

1. Desde la pantalla de **Inicio** , escriba **Reporting Services** para ver una lista de aplicaciones.

1. Haga clic en **Administrador de configuración de Reporting Services** y haga clic en **Ejecutar como administrador**.

1. En **Windows Server 2008 R2**:

1. Haga clic en **Inicio**y, a continuación, haga clic en **Todos los programas**.

1. Haga clic en **Microsoft SQL Server 2016**.

1. Haga clic en **Herramientas de configuración**.

1. Haga clic en **Administrador de configuración de Reporting Services** y haga clic en **Ejecutar como administrador**.

O

1. Haga clic en **Inicio**.

1. En el cuadro de diálogo **Buscar programas y archivos** escriba **reporting services**. Si la máquina virtual está ejecutando Windows Server 2012, escriba **reporting services** en la pantalla de inicio de Windows Server 2012.

1. Haga clic en **Administrador de configuración de Reporting Services** y haga clic en **Ejecutar como administrador**.

    ![buscar Administrador de configuración de ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar Reporting Services

**Cuenta de servicio y URL de servicio web:**

1. Compruebe que el **Nombre del servidor** es el nombre del servidor local y haga clic en **Conectar**.

1. Anote el **Nombre de base de datos del servidor de informes**de en blanco. Se crea la base de datos cuando se complete la configuración.

1. Compruebe que el **Informe de estado del servidor** es **iniciado**. Si desea comprobar que el servicio en el administrador del servidor de Windows, el servicio es el servicio de Windows de **SQL Server Reporting Services** .

1. Haga clic en la **Cuenta de servicio** y cambiar la cuenta según sea necesario. Si se utiliza la máquina virtual en un entorno de dominio no combinado, la cuenta del **servidor de informes** es suficiente. Para obtener más información sobre la cuenta de servicio, consulte la [Cuenta de servicio](https://msdn.microsoft.com/library/ms189964.aspx).

1. En el panel izquierdo, haga clic en **Dirección URL del servicio Web** .

1. Haga clic en **Aplicar** para configurar los valores predeterminados.

1. Tenga en cuenta las **direcciones URL del servicio de informe servidor Web**. Tenga en cuenta que el puerto TCP predeterminado es 80 y es parte de la dirección URL. En un paso posterior, se crea un extremo de máquina Virtual de Microsoft Azure para el puerto.

1. En el panel de **resultados** , compruebe las acciones que se ha completado correctamente.

**Base de datos:**

1. En el panel izquierdo, haga clic en **base de datos** .

1. Haga clic en **Cambiar base de datos**.

1. Compruebe que está seleccionado **crear una nueva base de datos de servidor de informes** y, a continuación, haga clic en siguiente.

1. Compruebe el **Nombre del servidor** y haga clic en **Probar conexión**.

1. Si el resultado es la **conexión de prueba se realizó correctamente**, haga clic en **Aceptar** y, a continuación, haga clic en **siguiente**.

1. Observe el nombre de la base de datos es el **servidor de informes** y el **modo de servidor de informes** es **nativo** , a continuación, haga clic en **siguiente**.

1. En la página **credenciales** , haga clic en **siguiente** .

1. En la página **Resumen** , haga clic en **siguiente** .

1. Haga clic en **siguiente** en la página de **progreso y finalización** .

**Web dirección URL del Portal o dirección URL del Administrador de informes de 2012 y 2014:**

1. Haga clic en la **Dirección URL del Portal de Web**, o **El Administrador de informes** de 2014 y 2012, en el panel izquierdo.

1. Haga clic en **Aplicar**.

1. En el panel de **resultados** , compruebe las acciones que se ha completado correctamente.

1. Haga clic en **Salir**.

Para obtener información sobre los permisos de servidor de informes, consulte [Conceder permisos en un servidor de informes de modo nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Vaya al administrador de informes local

Para comprobar la configuración, vaya a Administrador de informes en la máquina virtual.

1. En la máquina virtual, inicie Internet Explorer con privilegios de administrador.

1. Vaya a http://localhost/reports en la máquina virtual.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para conectar con el portal web remoto o administrador de informes de 2014 y 2012

Si desea conectar con el portal web o el Administrador de informes para 2014 y 2012, en la máquina virtual desde un equipo remoto, cree una nueva máquina virtual extremo TCP. De forma predeterminada, el servidor de informes escucha para las solicitudes HTTP en el **puerto 80**. Si configura las direcciones URL de servidor de informes para utilizar un puerto diferente, debe especificar el número de puerto en las siguientes instrucciones.

1. Crear un extremo de la máquina Virtual de puerto TCP 80. Para obtener más información, vea la sección de [puntos finales de máquina Virtual y puertos del Firewall](#virtual-machine-endpoints-and-firewall-ports) de este documento.

1. Abrir el puerto 80 en firewall de la máquina virtual.

1. Desplácese hasta el portal web o el Administrador de informes, usando la máquina Virtual de Azure **Nombre DNS** como nombre del servidor en la dirección URL. Por ejemplo:

    **Servidor de informes**: http://uebi.cloudapp.net/reportserver  **portal Web**: http://uebi.cloudapp.net/reports

    [Configurar un Firewall para el acceso al servidor de informes](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Crear y publicar informes en la máquina Virtual de Azure

En la tabla siguiente resume algunas de las opciones disponibles para publicar informes existentes desde un equipo local al servidor de informes alojada en la máquina Virtual de Microsoft Azure:

- **Generador de informes**: la máquina virtual incluye el clic-una vez una versión de Microsoft SQL Server Report Builder para SQL 2014 y 2012. Para iniciar registrar el tiempo de generador el primero en la máquina virtual con 2016 SQL:

    1. Inicie el explorador con privilegios de administrador.

    1. Desplácese hasta el portal web, en la máquina virtual y seleccione el icono de **descarga** en la esquina superior derecha.
    
    1. Seleccione el **Generador de informes**.

    Para obtener más información, vea [Iniciar el generador de informes](https://msdn.microsoft.com/library/ms159221.aspx).

- **Herramientas de datos de SQL Server**: VM: herramientas de datos de SQL Server está instalada en la máquina virtual y puede utilizarse para crear **Proyectos de servidor de informes** e informes en la máquina virtual. Herramientas de datos de SQL Server pueden publicar los informes en el servidor de informes en la máquina virtual.

- **SQL Server Data Tools: remoto**: en el equipo local, crear un proyecto de Reporting Services en herramientas de datos de SQL Server que contiene los informes de Reporting Services. Configurar el proyecto para conectarse a la dirección URL del servicio web.

    ![propiedades del proyecto ssdt para el proyecto SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Crear una. Que contiene los informes y, a continuación, carga y adjuntar la unidad de disco duro de disco duro virtual.

    1. Crear una. Unidad de disco duro de disco duro virtual en el equipo local que contiene los informes.

    1. Crear e instalar un certificado de administración.

    1. Cargue el archivo de disco duro virtual en Azure mediante el cmdlet Add-AzureVHD [crear y cargar un disco duro virtual de Windows Server en Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Conecte el disco en la máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar otras características y servicios de SQL Server

Para instalar los servicios de SQL Server adicionales, como Analysis Services en modo de tabla, ejecute al Asistente para la instalación SQL server. Los archivos de instalación se encuentran en el disco local de la máquina virtual.

1. Haga clic en **Inicio** y, a continuación, haga clic en **Todos los programas**.

1. Haga clic en **Microsoft SQL Server 2016**, **2014 de Microsoft SQL Server** o **Microsoft SQL Server 2012** y, a continuación, haga clic en **Herramientas de configuración**.

1. Haga clic en **Centro de instalación de SQL Server**.

O ejecutar C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe o C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] La primera vez que ejecute el programa de instalación de SQL Server, los archivos de instalación más se pueden descargar y requieren un reinicio de la máquina virtual y reiniciar el programa de instalación de SQL Server.
>
>Si necesita varias veces personalice la imagen seleccionada de la máquina Virtual de Microsoft Azure, considere la posibilidad de crear su propia imagen de SQL Server. Habilitar la función de SysPrep de servicios de análisis con SQL Server 2012 SP1 CU2. Para obtener más información, consulte [Consideraciones para instalar SQL Server utilizar SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) y [Soporte técnico de Sysprep para Roles del servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar el modo Tabular de Analysis Services

Los pasos descritos en esta sección **resumir** la instalación de modo tabular de Analysis Services. Para obtener más información, consulte lo siguiente:

- [Instalar Analysis Services en modo Tabular](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modelado tabular (Adventure Works Tutorial)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar el modo Tabular de Analysis Services:**

1. En el Asistente para la instalación de SQL Server, haga clic en **instalación** en el panel izquierdo y, a continuación, haga clic en **instalación independiente de nuevo SQL server o agregar características a una instalación existente**.

    - Si ve la **Buscar carpeta**, vaya a c:\SQLServer_13.0_full, c:\SQLServer_12.0_full o c:\SQLServer_11.0_full y, a continuación, haga clic en **Aceptar**.

1. En la página de actualizaciones de producto, haga clic en **siguiente** .

1. En la página **Tipo de instalación** , seleccione **realizar una nueva instalación de SQL Server** y haga clic en **siguiente**.

1. En la página de **La función de instalación** , haga clic en **Características de instalación de SQL Server**.

1. En la página de **Selección de características** , haga clic en **Analysis Services**.

1. En la página **Configuración de la instancia** , escriba un nombre descriptivo, como **Tabular** en los cuadros de texto **Con el nombre de instancia** y el **Identificador de instancia** .

1. En la página de **Configuración de Analysis Services** , seleccione **Modo Tabular**. Agregar al usuario actual a la lista de permisos administrativos.

1. Complete y cierre al Asistente para la instalación de SQL Server.

## <a name="analysis-services-configuration"></a>Configuración de Analysis Services

### <a name="remote-access-to-analysis-services-server"></a>Acceso remoto al servidor de Analysis Services

Servidor de Analysis Services solo es compatible con la autenticación de windows. Para obtener acceso a Analysis Services remotamente desde aplicaciones cliente, como SQL Server Management Studio o herramientas de datos de SQL Server, la máquina virtual debe estar unido al dominio local, con la red Virtual de Azure. Para obtener más información, vea [Red Virtual de Azure](../virtual-network/virtual-networks-overview.md).

Escucha de puerto TCP **2383**una **instancia predeterminada** de Analysis Services. Abra el puerto en el firewall de máquinas virtuales de Windows. Una instancia con nombre agrupada de Analysis Services también escucha en el puerto **2383**.

Para un **nombre de instancia** de Analysis Services, el servicio SQL Server Browser se requiere para administrar el acceso de puerto. La configuración predeterminada del explorador de SQL Server es el puerto **2382**.

En el firewall máquinas virtuales de Windows, abra puerto **2382** y cree un estático con el nombre de puerto de la instancia de Analysis Services.

1. Para comprobar los puertos que ya están en uso en la máquina virtual y qué proceso está utilizando los puertos, ejecute el siguiente comando con privilegios de administrador:

        netstat /ao

1. Usar SQL Server Management Studio para crear un estático Analysis Services denominada puerto de instancia actualizando 'Puerto' valor de AS tabular propiedades generales de la instancia. Para obtener más información, consulte la "usar un puerto fijo para una predeterminada o instancia con nombre" en [Configurar el Firewall de Windows para permitir el acceso de servicios de análisis](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Reinicie la instancia tabular del servicio de Analysis Services.

Para obtener más información, vea la sección de **puntos finales de máquina Virtual y puertos del Firewall** de este documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Extremos de máquina virtual y puertos del Firewall

Esta sección resume extremos de máquina Virtual de Microsoft Azure para crear y puertos para abrir en los servidores de seguridad de la máquina virtual. La siguiente tabla resume el crear extremos para los puertos **TCP** y los puertos para abrir en el firewall de máquinas virtuales de Windows.

- Si está usando una única VM y los dos elementos siguientes son verdaderos, no es necesario crear extremos de máquina virtual y no es necesario abrir los puertos del Firewall de la máquina virtual.

    - No se conecta remotamente a las características de SQL Server en la máquina virtual. Establecer una conexión a Escritorio remoto VM y obtener acceso a las características de SQL Server localmente en la máquina virtual no se considera una conexión remota a las características de SQL Server.

    - No unirse a la máquina virtual a un dominio local a través de la red Virtual de Azure u otra solución de túnel de VPN.

- Si la máquina virtual no está unida a un dominio pero desea remotamente conectarse a las características de SQL Server en máquina virtual:

    - Abra los puertos en el firewall en la máquina virtual.

    - Crear extremos de máquina virtual para los puertos indicados (*).

- Si la máquina virtual está unida a un dominio mediante un túnel VPN como red Virtual de Azure, los extremos no son necesarios. Sin embargo, abra los puertos en el firewall en la máquina virtual.

  	|Puerto|Tipo|Descripción|
|---|---|---|
|**80**|TCP|Servidor de informes de acceso remoto (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP|Explorador de SQL Server. Esto es necesario cuando la máquina virtual de unirse a un dominio.|
|**2382**|TCP|Explorador de SQL Server.|
|**2383**|TCP|Instancia predeterminada de SQL Server Analysis Services y agrupadas instancias con nombre.|
|**Definidas por el usuario**|TCP|Crear un estático Analysis Services con el nombre de puerto de la instancia de un número de puerto que elija y, a continuación, desbloquear el número de puerto en el firewall.|

Para obtener más información sobre la creación de extremos, consulte lo siguiente:

- Crear extremos:[cómo configurar los extremos de una máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server: Vea la sección "Configuración completa los pasos para conectarse a la máquina virtual con SQL Server Management Studio" de [aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

El siguiente diagrama muestra los puertos para abrir en el firewall VM para permitir el acceso remoto a las características y componentes de la máquina virtual.

![puertos abrir para aplicaciones de inteligencia empresarial en máquinas virtuales de Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos

- Revise la directiva de soporte técnico de software de servidor de Microsoft utilizada en el entorno de máquina Virtual de Azure. El tema siguiente resume la compatibilidad con características como BitLocker, conmutación y equilibrio de carga de red. [Soporte de software de servidor de Microsoft para Azure máquinas virtuales de Windows](http://support.microsoft.com/kb/2721672).

- [SQL Server en máquinas virtuales de Windows Azure Introducción](virtual-machines-windows-sql-server-iaas-overview.md)

- [Máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [Cómo adjuntar un disco de datos a una máquina Virtual](virtual-machines-windows-classic-attach-disk.md)

- [Migrar una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md)

- [Determinar el modo de servidor de una instancia de Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modelos multidimensionales (Adventure Works Tutorial)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centro de documentación de Azure](https://azure.microsoft.com/documentation/)

- [Uso de Power BI en un entorno híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Enviar comentarios e información de contacto a través de Microsoft SQL Server conectarse](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenido de la Comunidad

- [Administración de la base de datos SQL Azure con PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
