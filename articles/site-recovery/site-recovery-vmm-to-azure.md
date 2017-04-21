<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM en Azure con la recuperación de sitios con el portal de Azure | Microsoft Azure"
    description="Describe cómo implementar Azure recuperación de sitio para coordinar replicación, migración tras error y recuperación de máquinas virtuales de Hyper-V en nubes de VMM en Azure con el portal de Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en Azure con recuperación de sitio de Azure en el portal de Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-azure.md)
- [Azure clásica](site-recovery-vmm-to-azure-classic.md)
- [Administrador de recursos de PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Clásico de PowerShell](site-recovery-deploy-with-powershell.md)

Bienvenido a Azure sitio recuperación! Utilice este artículo si desea replicar máquinas virtuales de Hyper-V de local administrado en nubes de System Center Virtual Machine Manager (VMM) a Azure con la recuperación de sitios de Azure en el portal de Azure.

> [AZURE.NOTE]Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model
> ) para crear y trabajar con recursos: Administrador de recursos de Azure y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación.


Recuperación de sitio de Azure en el portal de Azure proporciona varias características nuevas:

- En el portal de Azure los servicios de copia de seguridad de Azure y Azure sitio recuperación se combinan en un único depósito de servicios de recuperación, para que puedan configurar y administrar continuidad empresarial y recuperación (BCDR) desde una única ubicación. Un panel unificado permite supervisar y administrar operaciones en los sitios local y la nube pública de Azure.
- Los usuarios con suscripciones a Azure aprovisionados con el programa de proveedor de soluciones de nube (CSP) ahora pueden administrar operaciones de recuperación de sitio en el portal de Azure.
- Recuperación de sitio en el portal de Azure puede replicar máquinas a cuentas de administrador de recursos de Azure almacenamiento. Migración tras error, recuperación del sitio crea máquinas virtuales basadas en el Administrador de recursos en Azure.
- Recuperación de sitio sigue admiten la replicación de cuentas de almacenamiento clásico. Migración tras error, recuperación del sitio crea máquinas virtuales con el modelo clásico.


Después de leer este artículo, publique los comentarios en la parte inferior de los comentarios de Disqus. Formular preguntas técnicas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia BCDR que determina cómo mantenerse ejecución y está disponible durante el tiempo de inactividad planificado y aplicaciones, cargas de trabajo y datos y recuperación tan pronto como sea posible a las condiciones de trabajo normal. La estrategia BCDR debe mantener datos empresariales seguros y recuperables y garantizar que las cargas de trabajo permanecen continuamente disponibles cuando se produce desastre.

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR por la organización replicación de servidores físicos de local y máquinas virtuales en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en su ubicación primaria, conmutar a la ubicación secundaria para mantener las aplicaciones y cargas de trabajo disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales. Más información en [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)

Este artículo proporciona toda la información que necesita replicar máquinas virtuales de Hyper-V en nubes de VMM en Azure local. Incluye una introducción a la arquitectura, planificación de la información y los pasos de implementación para configurar Azure, servidores locales, opciones de replicación y planificación de capacidad. Después de configurar la infraestructura que puede habilitar la replicación en equipos que desea proteger y comprobar que migración tras error funciona.


## <a name="business-advantages"></a>Ventajas de la empresa

- Recuperación de sitio proporciona protección fuera del sitio para las cargas de trabajo de la empresa y aplicaciones que se ejecutan en máquinas virtuales de Hyper-V.
- El portal de servicios de recuperación proporciona una única ubicación para configurar, administrar y supervisar la replicación, la migración tras error y la recuperación.
- Fácilmente puede ejecutar migraciones tras error de la infraestructura local a Azure y recuperación (restaurar) de Azure a los servidores de host de Hyper-V en su sitio local.
- Puede configurar los planes de recuperación con varios equipos para que las cargas de trabajo de la aplicación en niveles conmutan entre sí.


## <a name="scenario-architecture"></a>Arquitectura de escenario


Estos son los componentes del escenario:

- **Servidor VMM**: un servidor VMM local con uno o más nubes.
- **Host de Hyper-V o clúster**: servidores de host de Hyper-V o agrupaciones administran en nubes VMM.
- **Proveedor de servicios de recuperación de sitio de Azure y agente de recuperación de servicios**: durante la implementación instala el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM y el agente de servicios de recuperación de Microsoft Azure en servidores de host de Hyper-V. El proveedor de servicios en el servidor VMM se comunica con sitio de recuperación sobre HTTPS 443 para replicar orquestación. El agente de en el servidor de host de Hyper-V aplica datos a almacenamiento de Azure sobre HTTPS 443 de forma predeterminada.
- **Azure**: necesitará una suscripción de Azure, una cuenta de almacenamiento de Azure para almacenar replicar datos y una red virtual Azure para que las máquinas virtuales de Azure está conectadas a una red después de la migración.

![Topología de E2A](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Requisitos previos de Azure

Aquí es lo que necesita en Azure para implementar este escenario.

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Se necesita una cuenta de [Microsoft Azure](http://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**Almacenamiento de Azure** | Se necesita una cuenta de almacenamiento de Azure estándar para almacenar datos duplicados. Puede usar una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos son resistentes si se produce una interrupción regional, o si no se puede recuperar el área principal. [Más información](../storage/storage-redundancy.md). La cuenta debe estar en la misma región como depósito de servicios de recuperación.<br/><br/>Almacenamiento de Premium no es compatible.<br/><br/> Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM se crean cuando se produce error. <br/><br/> [Lea acerca de](../storage/storage-introduction.md) Almacenamiento de Azure.
**Red de Azure** | Necesita una red virtual Azure que conecta máquinas virtuales de Azure cuando se produce error. La red debe estar en la misma región como depósito de servicios de recuperación.

## <a name="on-premises-prerequisites"></a>Requisitos previos de local

Esto es lo necesita en local

**Requisito previo** | **Detalles**
--- | ---
**VMM**| Uno o más servidores VMM ejecutando en System Center 2012 R2. Cada servidor VMM debe tener uno o más nubes configurados. Debe contener una nube:<br/><br/> Uno o varios grupos de host VMM.<br/><br/> Uno o varios servidores de host de Hyper-V o grupos de cada grupo de host.<br/><br/>[Más información](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sobre la configuración de nubes de VMM.
**Hyper-V** | Servidores de host de Hyper-V deben ejecutar al menos **Windows Server 2012 R2** con la función Hyper-V o **Microsoft Hyper-V Server 2012 R2** y han instalado las últimas actualizaciones.<br/><br/> Un servidor de Hyper-V debe contener uno o más máquinas virtuales.<br/><br/> Un servidor de host de Hyper-V o clúster que incluya máquinas virtuales que desea replicar debe administrarse en una nube VMM.<br/><br/>Servidores Hyper-V deben estar conectados a Internet, directa o a través de un servidor proxy.<br/><br/>Servidores de Hyper-V deben tener correcciones mencionadas en el artículo [2961977](https://support.microsoft.com/kb/2961977) instalado.<br/><br/>Servidores de host de Hyper-V necesitan acceso a internet para replicación de datos en Azure.
**Proveedor y agente** | Durante la implementación de Azure sitio recuperación deberá instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM y el agente de servicios de recuperación de hosts Hyper-V. El proveedor de servicios y el agente necesitan conectarse a Azure en internet directamente o a través de un servidor proxy. Tenga en cuenta que un servidor proxy de HTTPS no es compatible. El servidor proxy en el servidor VMM y hosts Hyper-V debe permitir el acceso a: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Si tiene reglas de firewall basados en direcciones IP en el servidor VMM, compruebe que las reglas de permitan la comunicación a Azure. Debe permitir el puerto HTTPS (443) y los [Intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) .<br/><br/>Permitir que los intervalos de direcciones IP para la región de su suscripción de Azure y oeste de Estados Unidos.<br/><br/>Además. el servidor proxy en el servidor VMM necesita obtener acceso a https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Requisitos previos del equipo protegido


**Requisito previo** | **Detalles**
--- | ---
**Máquinas virtuales protegidas** | Antes de conmutar una máquina virtual, asegúrese de que el nombre que se ha asignado a la máquina virtual de Azure cumple con los [requisitos previos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Puede modificar el nombre después de que se ha habilitado la replicación de la máquina virtual. <br/><br/> Capacidad de disco individuales en equipos protegidas no debería ser más de 1023 GB. Una máquina virtual puede tener hasta 16 discos (así hasta 16 TB).<br/><br/> Compartir invitado disco clústeres no se admiten.<br/><br/> Unificada de interfaz de Firmware Extensible (UEFI) / inicio Extensible Interface(EFI) Firmware no es compatible.<br/><br/> Si el origen de la máquina virtual tiene el equipo NIC se convierte a una única NIC después de la migración a Azure.<br/><br/>Proteger máquinas virtuales con Linux con una dirección IP estática no es compatible.

## <a name="prepare-for-deployment"></a>Prepararse para su implementación

Prepararse para su implementación que necesita:

1. [Configurar una red de Azure](#set-up-an-azure-network) en la que se encuentran máquinas virtuales de Azure después de la migración.
2. [Configurar una cuenta de Azure almacenamiento](#set-up-an-azure-storage-account) de datos duplicados.
4. [Preparar el servidor VMM](#prepare-the-vmm-server) para su implementación de recuperación de sitios.
5. [Prepararse para la asignación de red](#prepare-for-network-mapping). Configurar redes de modo que puede configurar la asignación de red durante la implementación de recuperación de sitios.

### <a name="set-up-an-azure-network"></a>Configurar una red de Azure

Se necesita una red Azure para que las máquinas virtuales de Azure creadas después de la migración tras error se conectará a él.

- La red debe estar en la misma región como la de implementar la cámara de servicios de recuperación.
- Dependiendo del modelo de recursos que desea usar para error sobre máquinas virtuales de Azure, deberá configurar la red de Azure en [modo de administrador de recursos](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o en [modo clásico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Se recomienda que configurar una red antes de empezar. Si no, debe hacerlo durante la implementación de recuperación de sitios.

> [AZURE.NOTE] [Migración de redes](../resource-group-move-resources.md) entre los grupos de recursos en la misma suscripción o a través de suscripciones no es compatible con redes usadas para implementar el sitio de recuperación.


### <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

- Tendrá una cuenta de almacenamiento de Azure estándar para almacenar datos duplicados en Azure. La cuenta debe estar en la misma región como depósito de servicios de recuperación.
- Dependiendo del modelo de recursos que desea usar para error sobre máquinas virtuales de Azure, configurar una cuenta en [modo de administrador de recursos](../storage/storage-create-storage-account.md) o en [modo clásico](../storage/storage-create-storage-account-classic-portal.md).
- Se recomienda configurar una cuenta antes de empezar. Si no, debe hacerlo durante la implementación de recuperación de sitios.

> [AZURE.NOTE] No se admite la [migración de cuentas de almacenamiento](../resource-group-move-resources.md) en grupos de recursos en la misma suscripción o en suscripciones para las cuentas de almacenamiento usadas para implementar el sitio de recuperación.

### <a name="prepare-the-vmm-server"></a>Preparar el servidor VMM

- Asegúrese de que el servidor VMM cumple con los [requisitos previos](#on-premises-prerequisites).
- Durante la implementación de recuperación del sitio, puede especificar que todas las nubes en un servidor VMM deben estar disponibles en el portal de Azure. Si solo desea nubes específicos que aparezca en el portal, puede habilitar a esta configuración en la nube en la consola de administración VMM.


### <a name="prepare-for-network-mapping"></a>Prepararse para la asignación de red

Necesita configurar la asignación de red durante la implementación de recuperación de sitios. Mapas de asignación de red entre el origen de VMM VM redes y objetivo son las redes Azure para habilitar las siguientes acciones:

- Máquinas que conmutar en la misma red pueden conectarse a otras, incluso si no está error sobre en la misma forma o en el mismo plan de recuperación.
- Si una puerta de enlace de red está configurado en el destino de red Azure, máquinas virtuales de Windows Azure puede conectarse a máquinas virtuales de local.
- Para configurar una red asignación aquí es lo que necesita preparar:

    - Asegúrese de que máquinas virtuales en el servidor de host de Hyper-V de origen están conectadas a una red de VM VMM. Red debe vincularse a una red lógica que esté asociada a la nube.
    - Una red de Azure descrito [anteriormente](#set-up-an-azure-network)

- [Más información](site-recovery-network-mapping.md) acerca de cómo funciona la asignación de red.


## <a name="create-a-recovery-services-vault"></a>Crear un depósito de servicios de recuperación

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo** > **administración** > **Servicios de recuperación**. También puede hacer clic en **Examinar** > **Servicios de recuperación de** depósitos > **Agregar**.

    ![Nuevo depósito](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. En **nombre**, especifique un nombre descriptivo para identificar la cámara. Si tiene más de una suscripción, seleccione uno de ellos.
4. [Crear un grupo de recursos](../resource-group-template-deploy-portal.md), o seleccione uno existente. Especificar una región de Azure. Máquinas se reflejarán en esta área. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si desea obtener acceso rápidamente a la cámara del panel, haga clic en **Anclar al panel** > **Crear depósito**.

    ![Nuevo depósito](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

El nuevo depósito aparece en el **panel** > **todos los recursos**y en los principales módulos de **Servicios de recuperación de depósitos** .

## <a name="getting-started"></a>Introducción

Recuperación de sitio proporciona una introducción a experiencia que le ayude a implementar tan rápido como sea posible. Introducción a comprueba los requisitos previos y le guiará por los pasos de implementación en el orden correcto de recuperación de sitios.

En Introducción a seleccionar el tipo de equipos desea replicar y, a continuación, en la que desee replicar en. Configure los servidores locales, cuentas de almacenamiento de Azure y redes. Crear directivas de replicación y planear la capacidad. Una vez que haya configurado su infraestructura de habilitar la replicación de máquinas virtuales. Puede ejecutar migraciones tras error para equipos específicos o crear planes de recuperación para conmutar varios equipos.

Comenzar introducción, elija cómo desea implementar el sitio de recuperación. El flujo de introducción cambia ligeramente dependiendo de los requisitos de replicación.



## <a name="step-1-choose-your-protection-goals"></a>Paso 1: Seleccionar los objetivos de protección

Seleccione lo que desea replicar y donde desea replicar en.

1. En el módulo de **Servicios de recuperación de depósitos** , seleccione su cámara y haga clic en **configuración**.
2. En la **Introducción** , haga clic en el **Sitio de recuperación** > **paso 1: preparar la infraestructura** > **objetivo de protección**.

    ![Elija los objetivos](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. En el **objetivo de protección** seleccione **A Azure**y seleccione **Sí, con Hyper-V**. Seleccione **Sí** para confirmar que está usando VMM para administrar hosts Hyper-V y el sitio de recuperación. A continuación, haga clic en **Aceptar**.

    ![Elija los objetivos](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Paso 2: Configurar el entorno de origen

Instalar el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM y registrar el servidor en la cámara. Instalar al agente de servicios de recuperación de Azure en hosts Hyper-V.

1. Haga clic en **paso 2: preparar la infraestructura de** > **origen**.

    ![Configurar origen](./media/site-recovery-vmm-to-azure/set-source1.png)

2. **Preparar origen** haga clic en **+ VMM** para agregar un servidor VMM.

    ![Configurar origen](./media/site-recovery-vmm-to-azure/set-source2.png)

3. En la comprobación de módulo de **Agregar servidor** **servidor de System Center VMM** aparece en **tipo de servidor** y que el servidor VMM cumple los [requisitos de la dirección URL y los requisitos previos](#on-premises-prerequisites).
4. Descargar el archivo de instalación del proveedor de servicios de recuperación de sitio de Azure.
5. Descargue la clave de registro. Es necesario al ejecutar la configuración. La clave es válida para cinco días después de que genere.

    ![Configurar origen](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Instale el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurar el proveedor de servicios de recuperación de sitio de Azure

1.  Ejecutar al proveedor de archivo de configuración.
2. En **Microsoft Update** puede participar en actualizaciones para que se instalan las actualizaciones de proveedor de acuerdo con la directiva de Microsoft Update.
3. En la **instalación**, acepte o modifique la ubicación predeterminada de la instalación de proveedor y haga clic en **instalar**.

    ![Ubicación de instalación](./media/site-recovery-vmm-to-azure/provider2.png)

4. Haga clic en **registrar** para registrar el servidor VMM en la cámara cuando finaliza la instalación.
5. En la página **Configuración de cámara** , haga clic en **Examinar** para seleccionar el archivo de clave de la cámara. Especificar la suscripción de recuperación de sitios de Azure y el nombre de la cámara.

    ![Registro de servidor](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. En la **Conexión a Internet**, especifique cómo el proveedor se ejecuta en el servidor VMM se conectará al sitio de recuperación a través de internet.

    - Si desea que el proveedor para conectar directamente seleccione **Conectar directamente a la recuperación de sitios de Azure sin un servidor proxy**.
    - Si el proxy existente requiere autenticación, o desea usar un **Conectar a Azure sitio recuperación mediante un servidor proxy**, seleccione proxy personalizado.
    - Si utiliza a un proxy personalizado, especifique la dirección y puerto credenciales.
    - Si usa a un servidor proxy debe han permitido ya las direcciones URL que se describe en [los requisitos previos](#on-premises-prerequisites).
    - Si usa a un proxy personalizado una cuenta el VMM (DRAProxyAccount) se crearán automáticamente con las credenciales de proxy especificado. Configurar el servidor proxy para que esta cuenta puede autenticar correctamente. En la consola VMM se puede modificar la configuración de cuenta el VMM. En **configuración**, expanda **seguridad** > **Cuentas de ejecutar**y a continuación, modifique la contraseña para DRAProxyAccount. Debe reiniciar el servicio VMM para que esta configuración surta efecto.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Aceptar o modificar la ubicación de un certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si se habilita el cifrado de datos para una nube protegido por Azure en el portal de recuperación de sitios de Azure. Proteger este certificado. Cuando se ejecuta un error en Azure tendrá que descifrar, si está habilitado el cifrado de datos.


8. En **nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en la cámara. En una configuración de clúster, especifique el nombre de función de clúster VMM.
9. Habilitar los **metadatos de nube de sincronización** si desea sincronizar metadatos para todas las nubes en el servidor VMM con la cámara. Esta acción solo debe ocurrir una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar esta configuración desactivada y sincronizar cada nube individualmente en las propiedades de la nube en la consola VMM. Haga clic en **registrar** para completar el proceso.

    ![Registro de servidor](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Se inicia el registro. Cuando finalice el registro, el servidor se muestra en la **configuración de** > módulo de**servidores** de la cámara.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Instalación de la línea de comandos para el proveedor de servicios de recuperación de sitio de Azure

El proveedor de servicios de recuperación de sitio de Azure se puede instalar desde la línea de comandos. Este método puede usarse para instalar al proveedor en Server Core de Windows Server 2012 R2.

1. Descargue la clave de registro y archivos de instalación de proveedor a una carpeta. Por ejemplo, C:\ASR.
2. Desde un símbolo del sistema con privilegios elevados, ejecute los comandos para extraer al instalador de proveedor:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Ejecute este comando para instalar los componentes:

            C:\ASR> setupdr.exe /i

4. A continuación, ejecute estos comandos para registrar el servidor de la cámara:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Donde:

- **/Credentials**: parámetro obligatorio que especifica donde se encuentra el archivo de clave de registro.  
- **/FriendlyName**: parámetro obligatorio para que el nombre del servidor de host de Hyper-V que aparece en el portal de recuperación de sitios de Azure.
- - **/EncryptionEnabled**: parámetros opcionales cuando está replicar máquinas virtuales de Hyper-V en VMM nubes en Azure. Especifique si desea cifrar máquinas virtuales en Azure (al cifrado de rest). Asegúrese de que el nombre del archivo tiene la extensión **.pfx** . Cifrado está desactivada de forma predeterminada.
- **/proxyaddress**: parámetro opcional que especifica la dirección del servidor proxy.
- **/proxyPort**: parámetro opcional que especifica el puerto del servidor proxy.
- **/ proxyusername**: parámetro opcional que especifica el nombre de usuario de proxy (si el servidor proxy requiere autenticación).
- **/ proxypassword**: parámetro opcional que especifica la contraseña para autenticar con el servidor proxy (si el servidor proxy requiere autenticación).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Instalar al agente de servicios de recuperación de Azure en hosts Hyper-V

1. Después de que ha configurado el proveedor, debe descargar el archivo de instalación para el agente de servicios de recuperación de Azure. Ejecutar el programa de instalación en cada servidor Hyper-V en la nube VMM.

    ![Sitios de Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. En la página **Comprobación de requisitos previos** , haga clic en **siguiente**. Los requisitos previos que faltan se instalará automáticamente.

    ![Agente de servicios de recuperación de los requisitos previos](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. En la página **Configuración de la instalación** , acepte o modifique la ubicación de instalación y la ubicación en caché. Puede configurar la memoria caché en una unidad que tenga al menos 5 GB de almacenamiento disponible pero se recomienda una unidad de caché con 600 GB o más de espacio libre. A continuación, haga clic en **instalar**.
4. Una vez completada la instalación, haga clic en **Cerrar** para finalizar.

    ![Registrar MARS agente](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Instalación de la línea de comandos para el agente de servicios de recuperación de sitio de Azure

Puede instalar al agente de servicios de recuperación de Microsoft Azure de línea de comandos con el comando siguiente:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configurar el acceso de proxy de internet para el sitio recuperación de hosts Hyper-V

El agente de servicios de recuperación que se ejecuta en hosts Hyper-V necesita acceso a internet a Azure para la replicación de la máquina virtual. Si está teniendo acceso a internet a través de un servidor proxy, configurarlo como sigue:

1. Abra el complemento MMC de copias de seguridad de Microsoft Azure en el host de Hyper-V. De forma predeterminada, un método abreviado para la copia de seguridad de Microsoft Azure está disponible en el escritorio o en Agent\bin\wabadmin de servicios de recuperación de C:\Program Files\Microsoft Azure.
2. En el complemento, haga clic en **Cambiar las propiedades**.
3. En la pestaña **Configuración de Proxy** , especifique la información del servidor proxy.

    ![Registrar MARS agente](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Asegúrese de que el agente puede llegar a las direcciones URL que se describe en los [requisitos previos](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Paso 3: Configurar el entorno de destino

Especificar la cuenta de almacenamiento de Azure que se usará para la replicación y la red de Azure al que se conectará máquinas virtuales de Azure después de la migración.

1.  Haga clic en **Preparar infraestructura** > **destino** y seleccione la suscripción de Azure que desea usar.
2.  Especifique el modelo de implementación que desea usar para máquinas virtuales después de la migración.
3.  Recuperación de sitio comprueba que tiene una o varias cuentas de almacenamiento de Azure compatible y redes.

    ![Almacenamiento de información](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Si no ha creado una cuenta de almacenamiento y desea crear uno mediante el Administrador de recursos, haga clic en **+ cuenta de almacenamiento** para hacer que en línea.  En el módulo de **crear cuenta de almacenamiento** , especifique un nombre de cuenta, el tipo, la suscripción y la ubicación. La cuenta debe estar en la misma ubicación que la cámara de servicios de recuperación.

    ![Almacenamiento de información](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Tenga en cuenta que:

    - Si desea crear una cuenta de almacenamiento mediante el modelo clásico, hacerlo en el portal de Azure. [Aprende más](../storage/storage-create-storage-account-classic-portal.md)
    - Si usa una cuenta de almacenamiento premium para los datos duplicados, debe configurar una cuenta de almacenamiento adicional de estándar para almacenar registros de replicación que capturan los cambios en curso a datos locales.

4.  Si no ha creado una red de Azure y desea crear uno mediante el Administrador de recursos haga clic en **+ red** para hacer que en línea. En el módulo de **crear una red virtual** especifique un nombre de red, intervalo de direcciones, detalles de subred, suscripción y ubicación. La red debe estar en la misma ubicación que la cámara de servicios de recuperación.

    ![Red](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Si desea crear una red de uso del modelo clásico puede hacer en el portal de Azure. [Más información](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurar la asignación de red

- [Lectura](#prepare-for-network-mapping) ¿es un breve resumen de la asignación de red. [Lea este](site-recovery-network-mapping.md) para obtener una explicación más en profundidad.
- Compruebe que máquinas virtuales en el servidor VMM está conectados a una red de la máquina virtual y si ha creado al menos una red virtual Azure. Varias redes VM pueden asignarse a una sola red de Azure.

Configurar asignación como sigue:

1. En la **configuración de** > **Infraestructura del sitio de recuperación** > **asignaciones de red** > de**Asignación de red**, haga clic en el icono **+ asignación de red** .

    ![Asignación de red](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. En la **asignación de red agregar** , seleccione el servidor de origen VMM y **Azure** como destino.
3. Compruebe que la suscripción y el modelo de implementación después de la migración.
4. En la **red de origen**, seleccione la red de máquina virtual de origen local que quiere asignar desde la lista asociada con el servidor VMM.
5. En la **red de destino**, seleccione la red de Azure en qué réplica máquinas virtuales de Azure se encuentran cuando se crean. A continuación, haga clic en **Aceptar**.

    ![Asignación de red](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Aquí es lo que ocurre cuando se inicia la asignación de red:

- Máquinas virtuales existentes en la red de la máquina virtual de origen están conectadas a la red de destino cuando comienza la asignación. Nuevas máquinas virtuales conectadas a la red de la máquina virtual de origen están conectadas a la red de Azure asignada cuando se produce la replicación.
- Si modifica una asignación de red existente, máquinas virtuales de réplica se conectará con la nueva configuración.
- Si la red de destino tiene varias subredes y una de esas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, a continuación, la máquina virtual de réplica se conectará a esa subred de destino después de la migración.
- Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.



## <a name="step-4-set-up-replication-settings"></a>Paso 4: Configurar las opciones de replicación


1. Para crear una nueva directiva de replicación, haga clic en **Preparar infraestructura** > **Opciones de replicación** > **+ crear y asociar**.

    ![Red](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. En **crear y asociar directiva**, especifique un nombre de la directiva.
3. En la **frecuencia de copia**, especifique la frecuencia con la que desea duplicar datos de delta tras la réplica inicial (cada 30 segundos, 5 o 15 minutos).
4. En **el punto de recuperación retención**, especificar en horas cuánto que será la ventana de retención para cada punto de recuperación. Equipos protegidas se pueden recuperar a cualquier punto dentro de una ventana.
6. En la **frecuencia de instantáneas coherentes para la aplicación**, especifique la frecuencia con (1-12 horas) puntos de recuperación que contiene Dr será creado. Hyper-V usa dos tipos de instantáneas: una instantánea estándar que ofrece una instantánea incremental de la máquina virtual completa y una instantánea consistente con las aplicaciones que toma una instantánea de punto en el tiempo de los datos de aplicación en la máquina virtual. Dr use el servicio de copia sombra de volumen (VSS) para garantizar que las aplicaciones estén en un estado coherente cuando se toma la instantánea. Tenga en cuenta que si habilita DR, afectará al rendimiento de las aplicaciones que se ejecutan en máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicional que configura.
3. **Hora de inicio de la replicación inicial**, indican cuándo iniciar la replicación inicial. La replicación se produce sobre su ancho de banda de internet, por lo que desea programar fuera de sus horas de disponibilidad.
5. En **cifrar datos almacenados en Azure**, especifique si desea cifrar datos del resto de almacenamiento de Azure. A continuación, haga clic en **Aceptar**.

    ![Directiva de replicación](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Cuando se crea una nueva directiva automáticamente está asociada con la nube VMM. Haga clic en **Aceptar**. Puede asociar adicionales nubes de VMM (y las máquinas virtuales de ellos) a esta directiva de replicación de la **configuración de** > **replicación** > nombre de la directiva > **Asociar la nube de VMM**.

    ![Directiva de replicación](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Paso 5: Planear la capacidad

Ahora que ya tiene su basic infraestructura configurada por el usuario puede pensar planeamiento de capacidad y averiguar si necesita recursos adicionales.

Recuperación de sitio proporciona un planificador de capacidad para ayudarle a asignar los recursos adecuados para su entorno de origen, los componentes de recuperación de sitio, redes y almacenamiento. Puede ejecutar el organizador en el modo rápido para realizar cálculos en función de un número medio de máquinas virtuales, discos y almacenamiento o en el modo detallado en la que deberá introduce las cifras en el nivel de carga de trabajo. Antes de empezar necesitará:

- Recopilar información acerca de su entorno de replicación, incluidos VM, discos por máquinas virtuales y almacenamiento por disco.
- Estimar la tasa cambio (renovación) diarias que tendrá datos duplicados. Puede usar el [Organizador de la capacidad de réplica de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para ayudarle a hacerlo.

1.  Haga clic en **Descargar** para descargar la herramienta y ejecútelo. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2.  Cuando haya terminado seleccione **Sí** en **¿ha ejecutado el programador de capacidad**?

    ![Planificación de capacidad](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Consideraciones de ancho de banda de red

Puede usar la herramienta de planificación de capacidad para calcular el ancho de banda que necesario para la replicación (replicación inicial y, a continuación, delta). Para controlar la cantidad de uso de ancho de banda de replicación tiene varias opciones:

- **Limitar ancho de banda**: tráfico de Hyper-V que se aplica a un sitio secundario pasa a través de un host de Hyper-V específico. También puede limitar el ancho de banda en el servidor de host.
- **Ajustar el ancho de banda**: puede influir en el ancho de banda que se utiliza para la replicación con un par de claves de registro.

#### <a name="throttle-bandwidth"></a>Limitar ancho de banda

1. Abra el complemento MMC de copias de seguridad de Microsoft Azure en el servidor de host de Hyper-V. De forma predeterminada, un método abreviado para la copia de seguridad de Microsoft Azure está disponible en el escritorio o en Agent\bin\wabadmin de servicios de recuperación de C:\Program Files\Microsoft Azure.
2. En el complemento, haga clic en **Cambiar las propiedades**.
3. En la pestaña **regulación** seleccione **Habilitar límite para operaciones de copia de seguridad de uso de ancho de banda de internet**y establecer los límites de trabajo y no-trabajo de horas. Rangos válidos son entre 512 a 102 Mbps por segundo.

    ![Limitar ancho de banda](./media/site-recovery-vmm-to-azure/throttle2.png)

También puede usar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para establecer el límite. Este es un ejemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Conjunto OBMachineSetting NoThrottle** indica que no se requiere ninguna limitación.


#### <a name="influence-network-bandwidth"></a>Influir en el ancho de banda de red

El valor de registro **UploadThreadsPerVM** controla el número de subprocesos que se usan para la transferencia de datos (replicación inicial o delta) de un disco. Un valor más alto aumenta el ancho de banda de red que utiliza para la replicación. El valor de registro **DownloadThreadsPerVM** especifica el número de subprocesos utilizados para transferir datos durante la recuperación.

1. En el registro navegue **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Backup\Replication de Azure**.

    - Modificar el valor **UploadThreadsPerVM** (o cree la clave, si no existe) para controlar los subprocesos utilizados para la replicación de disco.
    - Modificar el valor **DownloadThreadsPerVM** (o cree la clave, si no existe) para controlar los subprocesos utilizados para el tráfico de recuperación de Azure.
2. El valor predeterminado es 4. En una red "overprovisioned", se deben cambiar estas claves de registro de los valores predeterminados. El máximo es 32. Supervisar el tráfico para optimizar el valor.

## <a name="step-6-enable-replication"></a>Paso 6: Habilitar la replicación

Habilitar ahora la replicación de la siguiente manera:

1. Haga clic en **paso 2: replicar aplicación** > **origen**. Después de que se ha habilitado la replicación por primera vez haga clic en **+ replicar** en el depósito al habilitar la replicación de equipos adicionales.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. En el módulo de **origen** > Seleccionar el servidor VMM y la nube en el que se encuentran los hosts Hyper-V. A continuación, haga clic en **Aceptar**.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. En **destino** , seleccione la suscripción, modelo de implementación de migración tras error de entrada y la cuenta de almacenamiento que se usa para los datos duplicados.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Seleccione la cuenta de almacenamiento que desee usar. Si desea usar una cuenta de almacenamiento diferentes a los tiene, puede [crear uno](#set-up-an-azure-storage-account). Para crear un almacenamiento cuenta mediante el modelo de administrador de recursos, haga clic en **Crear nuevo**. Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo que [en el portal de Azure](../storage/storage-create-storage-account-classic-portal.md). A continuación, haga clic en **Aceptar**.
5. Seleccione la red Azure y subred al que se conectará máquinas virtuales de Azure cuando haya terminado después de la migración. Seleccione **Configurar ahora para equipos seleccionados** para aplicar la configuración de red a todos los equipos que seleccione para la protección. Seleccione **configurar más adelante** para seleccionar la red Azure por máquina. Si desea usar una red distinta de los tiene puede [crear uno](#set-up-an-azure-network). Para crear una red con el modelo de administrador de recursos, haga clic en **Crear nuevo**. Si desea crear una red de uso del modelo clásico puede hacer que [en el portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Seleccione una subred si procede. A continuación, haga clic en **Aceptar**.
6. En **máquinas virtuales** > **Seleccione máquinas virtuales de Windows** , haga clic en y seleccione cada equipo que desea replicar. Solo puede seleccionar equipos para el que se puede habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. En las **Propiedades** > **configurar las propiedades**, seleccione el sistema operativo para las VM seleccionadas y el disco de sistema operativo. A continuación, haga clic en **Aceptar**. Puede establecer propiedades adicionales más adelante.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. En **Opciones de replicación** > **establecer la configuración de replicación**, seleccione la directiva de replicación que desea aplicar para las VM protegidas. A continuación, haga clic en **Aceptar**. Puede modificar la directiva de replicación de la **configuración** > **las políticas de replicación** > nombre de la directiva > **Editar configuración**. Cambios que aplique se usan para equipos que ya están replicar y nuevos equipos.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Puede seguir el progreso de la tarea de **Habilitar protección** en la **configuración de** > **trabajos** > **trabajos de recuperación de sitios**. Después de que el trabajo de **Protección finalizar** ejecuta el equipo está listo para migración tras error.

### <a name="view-and-manage-vm-properties"></a>Ver y administrar propiedades VM

Le recomendamos que compruebe las propiedades de la máquina de origen. Recuerde que el nombre de Azure VM debe cumplir con los [requisitos de la máquina virtual de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Haga clic en **configuración** > **Elementos protegidos** > **Elementos replicar** > y seleccione el equipo para ver sus detalles.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. En las **Propiedades** puede ver información de replicación y migración tras error de la máquina virtual.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. En la **red y calcular** > **calcular propiedades** que puede especificar el tamaño de nombre y de destino de la máquina virtual de Azure. Modifique el nombre para cumplir con [los requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) si necesita. También puede ver y modificar información acerca de la red de destino, la subred y la dirección IP que se ha asignado a la máquina virtual de Azure. Tenga en cuenta lo siguiente:

    - Puede establecer la dirección IP de destino. Si no proporciona una dirección, el error sobre equipo usará DHCP. Si establece una dirección que no está disponible en la migración tras error, se producirá un error en la migración tras error. La misma dirección IP de destino puede utilizarse para probar migración tras error si la dirección está disponible en la red de migración tras error de prueba.
    - El número de adaptadores de red se indica mediante el tamaño que especifique para la máquina virtual de destino, como sigue:

        - Si el número de adaptadores de red en el equipo de origen es menor o igual que el número de adaptadores permitida para el tamaño del equipo de destino, el destino tendrá el mismo número de adaptadores como el origen.
        - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, a continuación, se utiliza el tamaño máximo de destino.
        - Por ejemplo, si un equipo de origen tiene dos adaptadores de red y el tamaño del equipo de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores, pero el tamaño de destino compatibles solo es compatible con una el equipo de destino tendrá sólo un adaptador.     
        - Si la máquina virtual tiene varios adaptadores de red se conectará a la misma red.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  En **discos** puede ver el sistema operativo y los discos de datos en la máquina virtual que se van a replicar.



## <a name="step-7-test-your-deployment"></a>Paso 7: Comprobar la implementación

Para probar la implementación puede ejecutar un error de prueba de una sola máquina virtual o un plan de recuperación que contiene una o más máquinas virtuales.


### <a name="prepare-for-failover"></a>Prepararse para la migración tras error

- Para ejecutar un error de prueba, le recomendamos que cree una nueva red Azure que ha aislado de la red de producción Azure (es el comportamiento predeterminado cuando se crea una nueva red en Azure). [Más información](site-recovery-failover.md#run-a-test-failover) acerca de cómo ejecutar migraciones tras error de prueba.
- Para obtener el mejor rendimiento al conmutar a Azure, instale al agente de Azure en el equipo protegido. Hace arrancar de forma más rápida y ayuda para solucionar problemas. Instalar al agente [Linux](https://github.com/Azure/WALinuxAgent) o [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para probar la implementación necesita una infraestructura de la máquina duplicada para que funcione como se esperaba. Si desea probar DNS y Active Directory puede crear una máquina virtual como un controlador de dominio con el DNS y replicar esto en Azure mediante la recuperación de sitio de Azure. Lea más en [Consideraciones de migración tras error de prueba de Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Si desea ejecutar una migración tras error planificado en lugar de un error de prueba tenga en cuenta lo siguiente:

    - Si es posible debe apagar equipos principales antes de ejecutar una migración tras error no planeado. Esto garantiza que no tiene el origen y la réplica máquinas que se ejecutan al mismo tiempo.
    - Al ejecutar una migración tras error planificado detiene la replicación de datos de equipos principales para cualquier delta de datos no se transferirán después de que comience una migración tras error no planeado. Además si ejecuta una migración tras error planificado en un plan de recuperación se ejecutará hasta que haya terminado, incluso si se produce un error.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Prepararse para conectarse a máquinas virtuales de Azure después de la migración

Si desea conectarse a máquinas virtuales de Azure con RDP después de la migración, asegúrese de que hacer lo siguiente:

**En el equipo local antes de la migración tras error**:

- Acceso a través de internet habilitar RDP, asegúrese de que se agregan las reglas TCP y UDP para el **público**y asegúrese de que está permitido RDP en el **Firewall de Windows** -> **características y aplicaciones permitidos** para todos los perfiles.
- Acceso a través de una conexión de sitio a sitio habilitar RDP en el equipo y asegúrese de que está permitido RDP en el **Firewall de Windows** -> **permitidos aplicaciones y características** de redes **privadas** y **dominio** .
- Instalar el [agente de Azure VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) en el equipo local.
- Asegúrese de que la directiva de SAN del sistema operativo se establece en OnlineAll. [Aprende más]( https://support.microsoft.com/kb/3031135)
- Desactivar el servicio IPSec antes de ejecutar la migración tras error.

**En la VM Azure después de la migración**:

- Agregar un extremo público para el protocolo RDP (puerto 3389) y especifique las credenciales de inicio de sesión.
- Asegúrese de que no tiene las directivas de dominio que impiden la conexión a una máquina virtual usando una dirección pública.
- Intente conectarse. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual. Para obtener más sugerencias de solución de problemas, lea este [artículo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Si desea tener acceso a una máquina virtual de Azure ejecutan Linux después de la migración con un Secure Shell cliente (ssh), haga lo siguiente:

**En el equipo local antes de la migración tras error**:

- Asegúrese de que el servicio de Secure Shell en la máquina virtual de Azure está establecido para iniciarse automáticamente en el inicio del sistema.
- Compruebe que las reglas de firewall permiten una conexión SSH a él.

**En la VM Azure después de la migración**:

- Las reglas de grupo de seguridad de red de error sobre VM y la subred Azure al que está conectado debe permitir las conexiones entrantes al puerto SSH.
- Debe crear un extremo del público para permitir las conexiones entrantes en el SSH (puerto TCP 22 de forma predeterminada).
- Si la máquina virtual se accede a través de una conexión VPN (ruta Express o VPN de sitio a sitio) el cliente puede utilizarse para conectarse directamente a la máquina virtual sobre SSH.


### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

Para ejecutar prueba migración tras error no lo siguiente:

1. Conmutar una única VM en la **configuración de** > **Replicar elementos**, haga clic en la máquina virtual > **+ migración tras error de prueba**.
2. Conmutar un plan de recuperación, en la **configuración de** > de**Planes de recuperación**, haga clic en el plan > **Migración tras error de prueba**. Para crear una recuperación plan [siga estas instrucciones](site-recovery-create-recovery-plans.md).

3. **Prueba de migración tras error** de seleccionar la red Azure al que se conectan máquinas virtuales de Azure después de que se produce error.
4. Haga clic en **Aceptar** para comenzar la migración tras error. Puede realizar el seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo de **Migración tras error de prueba** en la **configuración de** > **trabajos de recuperación de sitios**.
5. Cuando la migración tras error alcanza la fase de **pruebas completado** , haga lo siguiente:

    1. Ver la máquina virtual de réplica en el portal de Azure. Compruebe que la máquina virtual se inicia correctamente.
    2. Si está configurado para máquinas virtuales de acceso de su red local, puede iniciar una conexión a Escritorio remoto en la máquina virtual.
    3. Haga clic en **completado la prueba** para finalizarlo.
    4. Haga clic en **notas** para grabar y guardar las observaciones asociadas con la migración tras error de prueba.
    5. Haga clic en **la prueba de migración tras error está completa**. Limpiar el entorno de prueba para apagar y eliminar la máquina virtual de prueba automáticamente.
    6. En esta fase se eliminan todos los elementos o máquinas virtuales creadas automáticamente por sitio de recuperación durante la migración tras error de prueba. No se eliminan los elementos adicionales que haya creado para la migración tras error de prueba.

    > [AZURE.NOTE] Si un error de prueba continúa más de dos semanas, se completa en vigor.

6. Una vez completada la migración tras error también debería poder ver la réplica Azure equipo aparezca en el portal de Azure > **máquinas virtuales de Windows**. Debe asegurarse de que la máquina virtual es el tamaño apropiado, que ha conectado a la red apropiada, y que se está ejecutando.
7. Si se [preparado para conexiones después de la migración](#prepare-to-connect-to-Azure-VMs-after-failover) debería poder conectarse a la máquina virtual de Azure.


## <a name="monitor-your-deployment"></a>Supervisar la implementación

Esto es cómo puede supervisar el estado, estado y opciones de configuración para la implementación de recuperación de sitios:

1. Haga clic en el nombre de la cámara para tener acceso al panel de **Essentials** . En este panel puede trabajos, estado de la replicación, planes de recuperación, el estado del servidor y eventos de recuperación del sitio.  Puede personalizar Essentials para mostrar los mosaicos y los diseños más útiles, que incluye el estado de otros depósitos de copia de seguridad y recuperación del sitio.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. En el icono de **estado** que puede supervisar servidores de sitio (servidores VMM o configuración) que se producen problemas y los eventos generados por el sitio de recuperación en las últimas 24 horas.
3. Puede administrar y supervisar la replicación en los **Elementos replicar**, **Planes de recuperación**, y mosaicos de **Trabajos de recuperación de sitios** . Puede explorar en profundidad trabajos en la **configuración de** -> **trabajos** -> **Trabajos de recuperación de sitios**.


## <a name="next-steps"></a>Pasos siguientes

Después de la implementación está configurado y en ejecución, [Obtenga más](site-recovery-failover.md) información sobre los distintos tipos de migración tras error.
