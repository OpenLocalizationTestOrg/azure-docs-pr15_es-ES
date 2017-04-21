<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM en Azure | Microsoft Azure"
    description="Este artículo describe cómo replicar máquinas virtuales de Hyper-V en hosts Hyper-V que se encuentran en nubes de System Center VMM en Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Administrador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clásica](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clásica](site-recovery-deploy-with-powershell.md)



El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md).

## <a name="overview"></a>Información general

En este artículo se describe cómo implementar la recuperación de sitios para replicar máquinas virtuales de Hyper-V en servidores de host de Hyper-V que provienen de nubes privadas de VMM en Azure.

El artículo incluye los requisitos previos para el escenario y muestra cómo configurar un depósito de recuperación del sitio, obtener el proveedor de recuperación del sitio de Azure instalado en el servidor de origen VMM, registrar el servidor en la caja fuerte, agregar una cuenta de almacenamiento de Azure, instale al agente de servicios de recuperación de Azure en servidores de host de Hyper-V, establecer la configuración de protección de nubes VMM que se aplicará a todos los equipos virtuales protegidos y, a continuación, habilitar la protección de esas máquinas virtuales. Finalizar configuración probando la migración tras error para asegurarse de que todo funciona según lo esperado.

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitectura

![Arquitectura](./media/site-recovery-vmm-to-azure-classic/topology.png)

- El proveedor de servicios de recuperación de Azure sitio está instalado en el VMM durante la implementación de recuperación del sitio y el servidor VMM está registrado en la cámara de recuperación del sitio. El proveedor se comunica con el sitio recuperación manejar orquestación replicación.
- El agente de servicios de recuperación de Azure está instalado en servidores de host de Hyper-V durante la implementación de recuperación de sitios. Controla la replicación de datos con el almacenamiento de Azure.


## <a name="azure-prerequisites"></a>Requisitos previos de Azure

Aquí es lo que tiene en Azure.

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Tendrá una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**Almacenamiento de Azure** | Tendrá una cuenta de almacenamiento de Azure para almacenar datos duplicados. Los datos duplicados se almacenan en el almacenamiento de Azure y Azure VM está terminadas cuando se produce error. <br/><br/>Se necesita una [cuenta de almacenamiento geo redundantes estándar](../storage/storage-redundancy.md#geo-redundant-storage). La cuenta debe en la misma región como el servicio de recuperación de sitio y estar asociadas a la misma suscripción. Tenga en cuenta que la replicación a cuentas de almacenamiento premium no es compatible actualmente y no debe usarse.<br/><br/>[Lea acerca de](../storage/storage-introduction.md) Almacenamiento de Azure.
**Red de Azure** | Necesitará una red virtual Azure que se conectará máquinas virtuales de Azure cuando se produce error. La red virtual Azure debe estar en la misma región como depósito de recuperación del sitio.

## <a name="on-premises-prerequisites"></a>Requisitos previos de local

Esto es lo tiene en local.

**Requisito previo** | **Detalles**
--- | ---
**VMM** | Necesitará al menos un servidor VMM implementado como un servidor físico o virtual independiente o como un clúster virtual. <br/><br/>El servidor VMM debe estar ejecutándose System Center 2012 R2 con las últimas actualizaciones acumulativas.<br/><br/>Necesitará al menos una nube configurada en el servidor VMM.<br/><br/>La nube de origen que desea proteger debe contener uno o varios grupos de host VMM.<br/><br/>Más información sobre la configuración de nubes de VMM en [Tutorial: crear nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) en el blog de Keith Mayer.
**Hyper-V** | Necesitará uno o varios servidores de host de Hyper-V o clústeres en la nube VMM. Debe tener el servidor host y máquinas virtuales de uno o más. <br/><br/>El servidor Hyper-V debe estar ejecutándose al menos **Windows Server 2012 R2** con la función Hyper-V o **Microsoft Hyper-V Server 2012 R2** y han instalado las últimas actualizaciones.<br/><br/>Cualquier servidor Hyper-V que contiene máquinas virtuales que desea proteger debe estar ubicado en una nube VMM.<br/><br/>Si está ejecutando Hyper-V en una nota de clúster ese agente de clúster no se crea automáticamente si tiene un clúster de basado en la dirección IP estático. Deberá configurar al agente de clúster manualmente. [Más información](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) en la entrada de blog de Aidan Finn.
**Máquinas protegidos** | Máquinas virtuales que desea proteger deben cumplir [los requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Requisitos previos de asignación de red
Cuando proteger máquinas virtuales de mapas de asignación de red Azure entre redes VM en el servidor VMM de origen y destino Azure redes para habilitar las siguientes acciones:

- Todos los equipos de la migración tras error en la misma red puede conectar entre sí, con independencia de qué plan de recuperación están en.
- Si una puerta de enlace de red está configurado en el destino de red Azure, máquinas virtuales puede conectarse a otras máquinas virtuales de local.
- Si no configurar red asignación máquinas virtuales solo que conmutar en el mismo plan de recuperación podrán conectar entre sí después de la migración a Azure.

Si desea implementar la asignación de red necesita lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen debe estar conectadas a una red VM. Red debe vincularse a una red lógica que esté asociada a la nube.
- Una red de Azure al que puede conectarse replicadas máquinas virtuales después de la migración. En el momento de la migración tras error, seleccione esta red. La red debe estar en la misma región como su suscripción de recuperación de sitios de Azure.

Prepararse para la asignación de red como sigue:

1. [Obtenga información sobre](site-recovery-network-mapping.md) requisitos de asignación de red.
2. Preparar redes de máquina virtual en VMM:

    - [Configurar redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurar redes de máquina virtual](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Paso 1: Crear un depósito de recuperación de sitios

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) del servidor VMM que desea registrar.
2. Haga clic en **Servicios de datos** > **Servicios de recuperación** > **depósito de recuperación del sitio**.
3. Haga clic en **Crear nuevo** > **crear rápido**.
4. En **nombre**, escriba un nombre descriptivo para identificar la cámara.
5. En la **región**, seleccione la región geográfica de la cámara. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Haga clic en **Crear depósito**.

    ![Nuevo depósito](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Compruebe la barra de estado para confirmar que la cámara se creó correctamente. La cámara se mostrarán como **activo** en la página principal de servicios de recuperación.

## <a name="step-2-generate-a-vault-registration-key"></a>Paso 2: Generar una clave de registro de cámara

Generar una clave de registro de la cámara. Después de descargar el proveedor de servicios de recuperación de sitio de Azure e instalarlo en el servidor VMM, deberá usar esta clave para registrar el servidor VMM en la cámara.

1. En la página **Servicios de recuperación** , haga clic en la cámara para abrir la página de inicio rápido. También se pueden abrir en cualquier momento mediante el icono de inicio rápido.

    ![Icono de inicio rápido](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. En la lista desplegable, seleccione **entre un sitio VMM local y Microsoft Azure**.
3. En **Servidores de VMM preparar**, haga clic en **Generar clave de registro** . El archivo de clave se genera automáticamente y es válido durante 5 días después de que se genera. Si no está acceder al portal de Azure desde el servidor VMM, que tendrá que copiar este archivo en el servidor.

    ![Clave de registro](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Paso 3: Instalar el proveedor de servicios de recuperación de sitio de Azure

1. En el **Inicio rápido**de > **VMM preparar servidores**, haga clic en **Descargar Microsoft Azure sitio recuperación de proveedor para la instalación en servidores VMM** para obtener la última versión del archivo de instalación de proveedor.
2. Ejecutar este archivo en el servidor VMM de origen.

    >[AZURE.NOTE] Si VMM se implementa en un clúster y va a instalar el proveedor para la primera vez instalar en un nodo activo y finalizar la instalación para registrar el servidor VMM en la cámara. A continuación, instale al proveedor en el resto de los nodos. Tenga en cuenta que si está actualizando el proveedor deberá actualizar en todos los nodos porque deben todos ejecutarse la misma versión de proveedor.

3. El instalador comprueba prerequirements y solicita permiso para detener el servicio VMM para comenzar la instalación del proveedor. El servicio de VMM se se reinicia automáticamente cuando finaliza la instalación. Si va a instalar en un clúster VMM que le pedirá que detenga el rol de clúster.

4. En **Microsoft Update** puede participar en actualizaciones. Con este valor habilitado a proveedor actualizaciones se instalarán según la directiva de Microsoft Update.

    ![Actualizaciones de Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  La ubicación de instalación para el proveedor se establece en ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual máquina Manager\bin**. Haga clic en **instalar**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Después de instala el proveedor haga clic en **registrar** para registrar el servidor en la cámara.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. En **nombre de depósito**, compruebe el nombre del depósito en el que se registrará el servidor. Haga clic en *siguiente*.

    ![Registro de servidor](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. En la **Conexión a Internet** , especifique cómo se conecta el proveedor se ejecuta en el servidor VMM a Internet. Seleccione **Conectar con la configuración de proxy existente** para usar la configuración predeterminada de la conexión de Internet configurada en el servidor.

    ![Configuración de Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

    - Si desea usar a un proxy personalizado debe configurarlo antes de instalar el proveedor de servicios. Cuando configure la configuración de proxy personalizada se ejecuta una prueba para comprobar la conexión de proxy.
    - Si utiliza a un proxy personalizado o su proxy predeterminado requiere autenticación que deberá especificar los detalles de proxy, incluida la dirección del proxy y el puerto.
    - Las siguientes direcciones URL deben ser accesible desde el servidor VMM y hosts de Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Permitir que las direcciones IP que se describe en [Intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y HTTPS protocolo (443). Tendrá que los intervalos de IP de la lista de notas de la región de Azure que planea usar y de Estados Unidos oeste.
    - Si usa a un proxy personalizado una cuenta el VMM (DRAProxyAccount) se crearán automáticamente con las credenciales de proxy especificado. Configurar el servidor proxy para que esta cuenta puede autenticar correctamente. En la consola VMM se puede modificar la configuración de cuenta el VMM. Para ello, abra el área de trabajo de **configuración** , expanda **seguridad**y, a continuación, haga clic en **Ejecutar como cuentas**y modifique la contraseña DRAProxyAccount. Debe reiniciar el servicio VMM para que esta configuración surta efecto.


8. En **Clave de registro**, seleccione la clave que descargó de recuperación de sitios de Azure y ha copiado en el servidor VMM.


10.  La configuración de cifrado solo se usa cuando está replicar máquinas virtuales de Hyper-V en nubes de VMM en Azure. Si va a duplicar en un sitio secundario no se utiliza.

11.  En **nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en la cámara. En una configuración de clúster, especifique el nombre de función de clúster VMM.
12.  En **los metadatos de nube de sincronizar** , seleccione si desea sincronizar metadatos para todas las nubes en el servidor VMM con la cámara. Esta acción solo debe ocurrir una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar esta configuración desactivada y sincronizar cada nube individualmente en las propiedades de la nube en la consola VMM.

13.  Haga clic en **siguiente** para completar el proceso. Después de registrarse, recuperación de sitios de Azure recupera metadatos desde el servidor VMM. El servidor se muestra en la pestaña **Servidores VMM** en la página de **los servidores** de la cámara.

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Después de registrarse, recuperación de sitios de Azure recupera metadatos desde el servidor VMM. El servidor se muestra en la pestaña **Servidores VMM** en la página de **los servidores** de la cámara.

### <a name="command-line-installation"></a>Instalación de la línea de comandos

También se puede instalar el proveedor de servicios de recuperación de Azure sitio mediante la siguiente línea de comandos. Este método puede usarse para instalar al proveedor en un núcleo de servidor de Windows Server 2012 R2.

1. Descargue la clave de registro y archivos de instalación de proveedor a una carpeta. Por ejemplo: C:\ASR.
2. Detener el servicio Administrador de máquina Virtual de System Center
3. Desde un símbolo del sistema con privilegios elevados, extraer al instalador de proveedor con estos comandos:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale al proveedor de la siguiente manera:

        C:\ASR> setupdr.exe /i

5. Registrar el proveedor como sigue:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Donde parámetros son los siguientes:

 - **/Credentials** : parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro  
 - **/FriendlyName** : parámetro obligatorio para que el nombre del servidor de host de Hyper-V que aparece en el portal de recuperación de sitios de Azure.
 - **/EncryptionEnabled** : parámetro opcional para especificar si desea cifrado sus máquinas virtuales en Azure (al cifrado de rest). El nombre de archivo debería tener la extensión **.pfx** .
 - **/proxyaddress** : parámetro opcional que especifica la dirección del servidor proxy.
 - **/proxyPort** : parámetro opcional que especifica el puerto del servidor proxy.
 - **/ proxyusername** : parámetro opcional que especifica el nombre de usuario de proxy.
 - **/ proxypassword** : parámetro opcional que especifica la contraseña de proxy.  


## <a name="step-4-create-an-azure-storage-account"></a>Paso 4: Crear una cuenta de almacenamiento de Azure

1. Si no tiene una cuenta de almacenamiento de Azure, haga clic en **Agregar una cuenta de almacenamiento de Azure** para crear una cuenta.
2. Cree una cuenta con la replicación de geo habilitada. Debe en la misma región como el servicio de recuperación de sitios de Azure y estar asociadas a la misma suscripción.

    ![Cuenta de almacenamiento](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] No se admite la [migración de cuentas de almacenamiento](../resource-group-move-resources.md) en grupos de recursos en la misma suscripción o en suscripciones para las cuentas de almacenamiento usadas para implementar el sitio de recuperación.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Paso 5: Instalar al agente de servicios de recuperación de Azure

Instalar al agente de servicios de recuperación de Azure en cada servidor de host de Hyper-V en la nube VMM.

1. Haga clic en **Inicio rápido** > **agente de servicios de recuperación de descargar Azure sitio e instale en hosts** para obtener la última versión del archivo de instalación de agente.

    ![Instalar a agente de recuperación de servicios](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Ejecute el archivo de instalación en cada servidor de host de Hyper-V.
3. En la página de **Comprobación de requisitos previos** , haga clic en **siguiente**. Los requisitos previos que faltan se instalará automáticamente.

    ![Agente de servicios de recuperación de los requisitos previos](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. En la página **Configuración de la instalación** , especifique dónde desea instalar al agente y seleccione la ubicación en caché en el que se instalará metadatos de copia de seguridad. A continuación, haga clic en **instalar**.
5. Una vez que termine la instalación, haga clic en **Cerrar** para completar al asistente.

    ![Registrar MARS agente](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Instalación de la línea de comandos

También puede instalar al agente de servicios de recuperación de Microsoft Azure desde la línea de comandos con este comando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Paso 6: Configurar nube configuración de protección

Después de que el servidor VMM está registrado, puede configurar la configuración de protección de la nube. Ha activado la opción **sincronizar datos de nube con la cámara** cuando instala el proveedor para que todas las nubes en el servidor VMM aparecerá en la pestaña <b>Elementos protegidos</b> de la cámara.

![Nube publicado](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. En la página de inicio rápido, haga clic en **Configurar la protección de nubes VMM**.
2. En la pestaña **Elementos protegidos** , haga clic en la nube que desea configurar y vaya a la pestaña **configuración** .
3. En **destino** , seleccione **Azure**.
4. En la **Cuenta de almacenamiento** seleccione la cuenta de almacenamiento de Azure que utiliza para la replicación.
5. Establecer **cifrar los datos almacenados** en **desactivado**. Esta opción especifica que se deben cifrar datos replicada entre el sitio local y Azure.
6. En la **frecuencia de copia** deje el valor predeterminado. Este valor especifica con qué frecuencia se deben sincronizar los datos entre ubicaciones de origen y destino.
7. En **puntos de recuperación de conservar**, deje el valor predeterminado. Con un valor predeterminado de cero, el último punto recuperación para una máquina virtual principal se almacena en un servidor de host de réplica.
8. **Frecuencia de Dr**, deje el valor predeterminado. Este valor especifica la frecuencia con que crear instantáneas. Instantáneas utilizan el servicio de copia sombra de volumen (VSS) para garantizar que las aplicaciones estén en un estado coherente cuando se toma la instantánea.  Si establece un valor, asegúrese de que es menor que el número de puntos de recuperación adicional que configura.
9. En **hora de inicio de la replicación**, especifique cuándo debe comenzar la replicación inicial de datos de Azure. Se usará la zona horaria del servidor de host de Hyper-V. Recomendamos que programe la replicación inicial durante las horas.

    ![Configuración de la replicación de nube](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Después de guardar la configuración de un trabajo se creará y se puede supervisar en la ficha **trabajos** . Todos los servidores de host de Hyper-V en la nube de origen VMM se configurarán para la replicación.

Después de guardar, se puede modificar la configuración de nube en la ficha **Configurar** . Para modificar la ubicación de destino o la cuenta de almacenamiento de destino debe quitar la configuración de la nube y a continuación, vuelva a configurar la nube. Tenga en cuenta que, si cambia la cuenta de almacenamiento el cambio se aplicará únicamente para máquinas virtuales que están habilitados para la protección después de que se ha modificado la cuenta de almacenamiento. Máquinas virtuales existentes no se migran a la nueva cuenta de almacenamiento.

## <a name="step-7-configure-network-mapping"></a>Paso 7: Configurar la asignación de red
Antes de comenzar la asignación de red Compruebe que máquinas virtuales en el servidor de origen VMM están conectados a una red de la máquina virtual. Además, cree una o varias redes de Azure virtuales. Tenga en cuenta que se pueden asignar varias redes VM a una única red de Azure.

1. En la página de inicio rápido, haga clic en **mapa de redes**.
2. En la pestaña de **redes** , en la **ubicación de origen**, seleccione el servidor VMM de origen. En la **ubicación de destino** seleccione Azure.
3. En redes de **origen** se muestran una lista de redes VM asociado con el servidor VMM. En redes de **destino** , se muestran las redes Azure asociadas con la suscripción.
4. Seleccione la red de la máquina virtual de origen y haga clic en **mapa**.
5. En la página **Seleccionar una red de destino** , seleccione el destino Azure red que desea usar.
6. Haga clic en la marca de verificación para completar el proceso de asignación.

    ![Configuración de la replicación de nube](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Después de guardar la configuración de un trabajo se inicia realizar un seguimiento del progreso de la asignación y se puede supervisar en la ficha trabajos. Cualquier réplica máquinas virtuales que corresponden a la red de la máquina virtual de origen se conectará al destino Azure redes. Nuevas máquinas virtuales que están conectados a la red de la máquina virtual de origen se conectará a la red de Azure asignada después de la replicación. Si modifica una asignación de una nueva red existente, máquinas virtuales de réplica se conectará con la nueva configuración.

Tenga en cuenta que si la red de destino tiene varias subredes y una de esas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen y luego la máquina virtual de réplica se conectará a esa subred de destino después de la migración. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

> [AZURE.NOTE] [Migración de redes](../resource-group-move-resources.md) entre los grupos de recursos en la misma suscripción o a través de suscripciones no es compatible con redes usadas para implementar el sitio de recuperación.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Paso 8: Habilitar la protección de máquinas virtuales

Después de servidores, nubes y redes están configuradas correctamente, puede habilitar la protección de máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

- Máquinas virtuales de Windows debe cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Para habilitar la protección del sistema operativo y el sistema operativo propiedades del disco deben estar establecidas para la máquina virtual. Cuando se crea una máquina virtual en VMM utilizando una plantilla de máquina virtual puede establecer la propiedad. También puede establecer las propiedades de máquinas virtuales existentes en las pestañas **General** y **Configuración de Hardware** de las propiedades de la máquina virtual. Si no establece estas propiedades en VMM podrá configurarlas en el portal de recuperación de sitios de Azure.

    ![Crear la máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modificar las propiedades de la máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Para habilitar la protección, en la pestaña de **máquinas virtuales** en la nube en la que se encuentra la máquina virtual, haga clic en **Habilitar protección** > **Agregar máquinas virtuales**.
2. En la lista de máquinas virtuales en la nube, seleccione el que desea proteger.

    ![Habilitar la protección de máquina virtual](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Realizar un seguimiento del progreso de la acción de **Habilitar protección** en la pestaña **tareas** , incluida la replicación inicial. Después de que se ejecuta el trabajo de **Protección de finalizar** la máquina virtual está lista para la migración tras error. Después de habilita protección y se duplican máquinas virtuales, podrá verlos en Azure.


    ![Trabajo de protección de máquina virtual](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Compruebe las propiedades de la máquina virtual y modificar según sea necesario.

    ![Comprobar máquinas virtuales de Windows](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. En la pestaña **Configurar** las propiedades de la máquina virtual se pueden modificar las siguientes propiedades de red.





- **Número de adaptadores de red de la máquina virtual de destino** : el número de adaptadores de red viene determinada por el tamaño que especifique para la máquina virtual de destino. Compruebe [las especificaciones de tamaño de máquina virtual](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) para el número de adaptadores compatibles con el tamaño de la máquina virtual. Cuando se modifica el tamaño de una máquina virtual y guardar la configuración, cambiará el número de adaptador de red al abrir la próxima vez que **Configurar** página. El número de adaptadores de red de máquinas virtuales de destino es el número mínimo de adaptadores de red de la máquina virtual de origen y el número máximo de adaptadores de red compatibles con el tamaño de la máquina virtual elegido, como sigue:

    - Si el número de adaptadores de red en el equipo de origen es menor o igual que el número de adaptadores permitida para el tamaño del equipo de destino, el destino tendrá el mismo número de adaptadores como el origen.
    - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para que se utilizará el tamaño de destino, a continuación, el tamaño máximo de destino.
    - Por ejemplo, si un equipo de origen tiene dos adaptadores de red y el tamaño del equipo de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores, pero el tamaño de destino compatibles solo es compatible con una el equipo de destino tendrá sólo un adaptador.    

- **Red de la máquina virtual de destino** : la red a la que se conecta la máquina virtual depende de la asignación de red de la red de la máquina virtual de origen. Si la máquina virtual de origen tiene más de un adaptador de red y redes de origen se asignan a diferentes redes de destino, necesitará elegir entre una de las redes de destino.
- **Subred de cada adaptador de red** - para cada adaptador de red puede seleccionar la subred a la que el error al conectarse a través de máquina virtual.
- **Dirección IP de destino** : si el adaptador de red de la máquina virtual de origen está configurado para usar una dirección IP estática, a continuación, puede proporcionar la dirección IP de la máquina virtual de destino. Usar esta característica conservar la dirección IP de una máquina virtual de origen después de un error. Si no se proporciona ninguna dirección IP cualquier dirección IP disponible se expresa al adaptador de red en el momento de la migración tras error. Si se especifica la dirección IP de destino, pero ya está usando otra máquina virtual que se ejecuta en Azure provocarán migración tras error.  

    ![Modificar las propiedades de red](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] No se admiten máquinas virtuales de Linux con la dirección IP estática.

## <a name="test-the-deployment"></a>Probar la implementación

Para probar la implementación puede ejecutar un error de prueba para una sola máquina virtual, o crear un plan de recuperación que consta de varios equipos virtuales y ejecutar un error de prueba para el plan.  

Migración de prueba tras error simula su mecanismo de migración y recuperación en una red aislada. Tenga en cuenta que:

- Si desea conectarse a la máquina virtual en Azure con Escritorio remoto después del error, Habilitar conexión a Escritorio remoto en la máquina virtual antes de ejecutar la migración tras error de prueba.
- Después de la migración deberá usar una dirección IP pública para conectarse a la máquina virtual en Azure mediante Escritorio remoto. Si desea hacerlo, asegúrese de que no tiene las directivas de dominio que impiden la conexión a una máquina virtual usando una dirección pública.

>[AZURE.NOTE] Para obtener el mejor rendimiento al realizar una migración tras error en Azure, asegúrese de que ha instalado al agente de Azure en el equipo protegido. Esto ayuda de inicio de forma más rápida y también ayuda en diagnóstico en caso de problemas. Puede ser agente de Linux se encuentra [aquí](https://github.com/Azure/WALinuxAgent) - y Windows agente puede encontrarse [aquí](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Crear un plan de recuperación

1. En la pestaña **Planes de recuperación** , agregue un nuevo plan. Especifique un nombre, **VMM** en **tipo de origen**, y el servidor de origen VMM de **origen**, el destino se Azure.

    ![Crear el plan de recuperación](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. En la página **Seleccionar máquinas virtuales** , seleccione máquinas virtuales de Windows para agregar el plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado de plan de recuperación, grupo 1. Se ha probado un máximo de 100 máquinas virtuales de un plan de recuperación única.

- Si desea comprobar las propiedades de la máquina virtual antes de agregarlas al plan, haga clic en la máquina virtual en la página de propiedades de la nube donde encuentre. También puede configurar las propiedades de la máquina virtual en la consola VMM.
- Todos los equipos virtuales que se muestran se han habilitado para la protección. La lista incluye dos máquinas virtuales que están habilitados para protección y replicación inicial se ha completado y los que están habilitados para la protección con inicial replicación pendientes. Solo los equipos virtuales con replicación inicial completado puede conmutar como parte de un plan de recuperación.

    ![Crear el plan de recuperación](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Después de haber creado un plan de recuperación aparece en la ficha **Planes de recuperación** . También puede agregar [automatización Azure runbooks](site-recovery-runbook-automation.md) el plan de recuperación para automatizar acciones durante la migración tras error.

### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

Hay dos formas para ejecutar un error de prueba en Azure.

- **Probar la migración tras error sin una red Azure**: este tipo de migración tras error de prueba comprueba que la máquina virtual viene correctamente en Azure. La máquina virtual no se pueden conectar a cualquier red Azure después de la migración.
- **Probar la migración tras error con una red de Azure**: este tipo de controles de migración tras error que el entorno de replicación completa procede como se esperaba y que no se pudo sobre las máquinas virtuales se conectará con el destino especificado red Azure. Para el control de subred para probar la subred de la máquina virtual de prueba de migración tras error se averiguar en función de la subred de la máquina virtual de réplica. Esto es diferente a la replicación normal cuando la subred de una máquina virtual de réplica se basa en la subred de la máquina virtual de origen.

Si desea ejecutar un error de prueba para una máquina virtual habilitada para la protección de Azure sin especificar una red de destino Azure no es necesario preparar nada. Para ejecutar un error de prueba con un destino de red Azure tendrá que crear una nueva red Azure que ha aislado de la red de producción Azure (comportamiento predeterminado cuando se crea una nueva red en Azure). Observe cómo [ejecutar un error de prueba](site-recovery-failover.md#run-a-test-failover) para obtener más detalles.


También deberá configurar la infraestructura de la máquina virtual replicada funciona según lo previsto. Por ejemplo, una máquina virtual con el controlador de dominio y DNS se pueden replicar en Azure mediante la recuperación de sitio de Azure y pueden crearse en la red de prueba mediante la prueba de migración tras error. Mire la sección [Consideraciones de migración tras error de active directory de prueba](site-recovery-active-directory.md#considerations-for-test-failover) para obtener más detalles.

Para ejecutar un migración tras error de prueba, realice lo siguiente:

1. En la pestaña **Planes de recuperación** , seleccione el plan y haga clic en **Prueba de migración tras error**.
2. En la página **Confirmar prueba Failover** seleccione **Ninguno** o una red de Azure específica.  Tenga en cuenta que si selecciona ninguna la migración tras error de prueba deberá comprobar que la máquina virtual correctamente replicarse en Azure pero no comprueba la configuración de red de replicación.

    ![Ninguna red](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Si el cifrado de datos está habilitado para la nube, en la **Clave de cifrado** , seleccione el certificado que fue emitido durante la instalación del proveedor en el servidor VMM, cuando activa la opción para habilitar el cifrado de datos para una nube.
4. En la ficha **trabajos** puede seguir el progreso de la migración tras error. También debería poder ver la réplica de prueba de máquina virtual en el portal de Azure. Si está configurado para máquinas virtuales de acceso de su red local, puede iniciar una conexión a Escritorio remoto en la máquina virtual.
5. Cuando la migración tras error alcanza la fase de **pruebas completado** , haga clic en **Probar completa** para terminar la migración tras error de prueba. Puede desglosar la pestaña de **trabajo** para realizar un seguimiento del estado y progreso de la migración tras error y realizar las acciones que son necesarias.
6. Después de la migración podrá ver la réplica de prueba de máquina virtual en el portal de Azure. Si está configurado para máquinas virtuales de acceso de su red local, puede iniciar una conexión a Escritorio remoto en la máquina virtual. Haga lo siguiente:

    1. Compruebe que los equipos virtuales se inicie correctamente.
    2. Si desea conectarse a la máquina virtual en Azure con Escritorio remoto después del error, Habilitar conexión a Escritorio remoto en la máquina virtual antes de ejecutar la migración tras error de prueba. También deberá agregar un extremo RDP en la máquina virtual. Puede aprovechar un [Azure automatización Runbooks](site-recovery-runbook-automation.md) para hacerlo.
    3. Después de migración tras error si utiliza una dirección IP pública para conectarse a la máquina virtual en Azure con Escritorio remoto, asegúrese de no tiene las directivas de dominio que impiden la conexión a una máquina virtual usando una dirección pública.

7.  Una vez completada la prueba, haga lo siguiente:
    - Haga clic en **la prueba de migración tras error está completa**. Limpiar el entorno de prueba para apagar automáticamente y elimine las máquinas virtuales de prueba.
    - Haga clic en **notas** para grabar y guardar las observaciones asociadas con la migración tras error de prueba.

>

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre [la configuración de los planes de recuperación](site-recovery-create-recovery-plans.md) y [migración tras error](site-recovery-failover.md).
