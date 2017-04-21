<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario de VMM | Microsoft Azure"
    description="Este artículo describe cómo replicar máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario de VMM con la recuperación de sitio de Azure."
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
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario de VMM

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-vmm.md)
- [Portal clásica](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Administrador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)

## <a name="overview"></a>Información general

Este artículo describe cómo replicar máquinas virtuales de Hyper-V en servidores de host de Hyper-V se administran en nubes de VMM sitio VMM secundario mediante la recuperación de sitio de Azure.

El artículo incluye los requisitos previos, se muestra cómo configurar un depósito de recuperación del sitio, instalar el proveedor de servicios de recuperación de sitio de Azure en origen VMM servidores de destino registrar los servidores de la caja fuerte, establecer la configuración de protección de nubes de VMM y, a continuación, habilitar la protección de máquinas virtuales de Hyper-V. Finalizar configuración probando la migración tras error para asegurarse de que todo funciona según lo esperado.

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitectura

La imagen siguiente muestra los canales de comunicación diferentes y puertos usados por Azure sitio recuperación de orquestación y replicación

![Topología de E2E](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Antes de empezar

Asegúrese de que tiene estos requisitos previos en su lugar:

**Requisitos previos** | **Detalles**
--- | ---
**Azure**| Se necesita una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.
**VMM** | Se necesita al menos un servidor VMM.<br/><br/>Debe ejecutar al menos el servidor VMM System Center 2012 SP1 con las últimas actualizaciones acumulativas.<br/><br/>Si desea configurar la protección con un solo servidor VMM, necesita al menos dos nubes configurados en el servidor.<br/><br/>Si desea implementar la protección con dos servidores VMM, cada servidor debe tener al menos una nube configurado en el servidor VMM principal que desea proteger y a continuación, una nube configurado en el servidor VMM secundario que desee usar para la protección y recuperación<br/><br/>Todas las nubes VMM deben tener el perfil de la función Hyper-V.<br/><br/>La nube de origen que desea proteger debe contener uno o varios grupos de host VMM.<br/><br/>Más información sobre la configuración de nubes de VMM en [Tutorial: crear nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) en el blog de Keith Mayer.
**Hyper-V** | Necesita uno o más servidores de host de Hyper-V en los grupos de host VMM principales y secundarios y uno o varios equipos virtuales en cada servidor de host de Hyper-V.<br/><br/>Los servidores de Hyper-V host y de destino deben ejecutar al menos Windows Server 2012 con la función Hyper-V y han instalado las últimas actualizaciones.<br/><br/>Cualquier servidor Hyper-V que contiene máquinas virtuales que desea proteger debe estar ubicado en una nube VMM.<br/><br/>Si está ejecutando Hyper-V en un clúster, tenga en cuenta que agente de clúster no se crea automáticamente si tiene un clúster de basado en la dirección IP estático. Debe configurar al agente de clúster manualmente. [Más información](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) en la entrada de blog de Aidan Finn.
**Asignación de red** | Puede configurar la asignación de red para asegurarse de que replicada máquinas virtuales óptima se ubican en servidores de host de Hyper-V secundarios después de la migración y que pueden conectar a redes VM correspondientes. Si no establece la asignación de red, réplica máquinas virtuales no conectada a una red después de la migración.<br/><br/>Para configurar la asignación de red durante la implementación, asegúrese de que los equipos virtuales en el servidor de host de origen Hyper-V está conectados a una red VMM VM. Red debe vincularse a una red lógica que esté asociada a la nube. < br /<br/>La nube de destino en el servidor VMM secundario que usar para la recuperación debe tener configurada una red VM correspondiente y por separado debe estar vinculado a una red lógica correspondiente que esté asociada a la nube de destino.<br/><br/>[Más información](site-recovery-network-mapping.md) acerca de la asignación de red.
**Asignación de almacenamiento** | De forma predeterminada cuando se duplica una máquina virtual en un servidor de host de Hyper-V de origen a un servidor de host de destino Hyper-V, los datos duplicados se almacenan en la ubicación predeterminada que se indica el Hyper-V para host de destino en el Administrador de Hyper-V. Para tener más control sobre dónde se almacenan los datos duplicados, puede configurar la asignación de almacenamiento<br/><br/> Para configurar la asignación de almacenamiento, debe configurar clasificaciones de almacenamiento en el origen y destino en servidores VMM antes de comenzar la implementación. [Más información](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Paso 1: Crear un depósito de recuperación de sitios

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) del servidor VMM que desea registrar.

2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Depósito de recuperación del sitio**.

3. Haga clic en **Crear nuevo** > **crear rápido**.

4. En **nombre**, escriba un nombre descriptivo para identificar la cámara.

5. En la **región** , seleccione la región geográfica de la cámara. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Haga clic en **Crear depósito**.

    ![Crear depósito](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

En la barra de estado, compruebe que la cámara se ha creado. La cámara se mostrarán como **activo** en la página principal de servicios de recuperación.

## <a name="step-2-generate-a-vault-registration-key"></a>Paso 2: Generar una clave de registro de cámara

Generar una clave de registro de la cámara. Después de descargar el proveedor de servicios de recuperación de sitio de Azure e instalarlo en el servidor VMM, deberá usar esta clave para registrar el servidor VMM en la cámara.

1. En la página **Servicios de recuperación** , haga clic en la cámara para abrir la página de inicio rápido. También se pueden abrir en cualquier momento mediante el icono de inicio rápido.

    ![Icono de inicio rápido](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. En la lista desplegable, seleccione **entre los dos sitios VMM local**.
3. **Preparar los servidores VMM**, haga clic en **Generar archivo de clave de registro**. El archivo de clave se genera automáticamente y es válido durante 5 días después de que se genera. Si no está acceder al portal de Azure desde el servidor VMM debe copiar este archivo en el servidor.

    ![Clave de registro](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Paso 3: Instalar el proveedor de servicios de recuperación de sitio de Azure

4. En la página de **Inicio rápido** , en **servidores de VMM preparar**, haga clic en **Descargar Microsoft Azure sitio recuperación de proveedor para la instalación en servidores VMM** para obtener la última versión del archivo de instalación de proveedor.

2. Ejecutar este archivo en el servidor VMM de origen.

    >[AZURE.NOTE] Si VMM se implementa en un clúster y va a instalar el proveedor para la primera vez instalar en un nodo activo y finalizar la instalación para registrar el servidor VMM en la cámara. A continuación, instale al proveedor en el resto de los nodos. Tenga en cuenta que, si está actualizando el proveedor debe actualizar en todos los nodos porque deben todos ejecutarse la misma versión de proveedor.

3. El programa de instalación hace unos permisos de **Comprobación de requisitos anteriores** y las solicitudes para detener el servicio VMM para comenzar la instalación del proveedor. El servicio de VMM se se reinicia automáticamente cuando finaliza la instalación. Si va a instalar en un clúster VMM que le pedirá que detenga el rol de clúster.

4. En **Microsoft Update** puede participar en actualizaciones. Con este valor habilitado a proveedor actualizaciones se instalarán según la directiva de Microsoft Update.

    ![Actualizaciones de Microsoft](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. Se establece la ubicación de instalación en ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual máquina Manager\bin**. Haga clic en el botón Instalar para iniciar la instalación del proveedor.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Después de instala el proveedor haga clic en **registrar** para registrar el servidor en la cámara.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
9. En **nombre de depósito**, compruebe el nombre del depósito en el que se registrará el servidor. Haga clic en *siguiente*.

    ![Registro de servidor](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. En la **Conexión a Internet** , especifique cómo se conecta el proveedor se ejecuta en el servidor VMM a Internet. Seleccione **Conectar con la configuración de proxy existente** para usar la configuración predeterminada de la conexión de Internet configurada en el servidor.

    ![Configuración de Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Si desea usar a un proxy personalizado debe configurarlo antes de instalar el proveedor de servicios. Cuando configure la configuración de proxy personalizada se ejecuta una prueba para comprobar la conexión de proxy.
    - Si utiliza a un proxy personalizado o su proxy predeterminado requiere autenticación que debe especificar los detalles de proxy, incluida la dirección del proxy y el puerto.
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

13.  Haga clic en **siguiente** para completar el proceso. Después de registrarse, recuperación de sitios de Azure recupera metadatos desde el servidor VMM. El servidor se muestra en **Los servidores de VMM** > **servidores** de la cámara.

    ![Servidores](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Instalación de la línea de comandos

El proveedor de servicios de recuperación de Azure sitio también se puede instalar desde la línea de comandos. Este método puede usarse para instalar al proveedor en un núcleo de servidor de Windows Server 2012 R2.

1. Descargue la clave de registro y archivos de instalación de proveedor a una carpeta. Por ejemplo, C:\ASR.
2. Detener el servicio de administrador de la máquina Virtual de System Center
3. Extraer al instalador de proveedor ejecutando estos comandos desde un símbolo del sistema con privilegios de **Administrador** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instalar al proveedor ejecutando:

        C:\ASR> setupdr.exe /i

5. Registrar el proveedor ejecutando:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

¿Dónde están los parámetros:

 - **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro  
 - **/FriendlyName**: parámetro obligatorio para que el nombre del servidor de host de Hyper-V que aparece en el portal de recuperación de sitios de Azure.
 - **/EncryptionEnabled**: parámetro opcional que se debe usar solo en el escenario de Azure VMM si necesita cifrado de sus máquinas virtuales al resto de Azure. Asegúrese de el nombre del archivo que proporciona tiene la extensión **.pfx** .
 - **/proxyaddress**: parámetro opcional que especifica la dirección del servidor proxy.
 - **/proxyPort**: parámetro opcional que especifica el puerto del servidor proxy.
 - **/ proxyusername**: parámetro opcional que especifica el nombre de usuario de Proxy (si el servidor proxy requiere autenticación).
 - **/ proxypassword**: parámetro opcional que especifica la contraseña para autenticar con el servidor proxy (si el servidor proxy requiere autenticación).  

## <a name="step-4-configure-cloud-protection-settings"></a>Paso 4: Configurar nube configuración de protección

Después de servidores VMM están registrados, puede configurar opciones de protección de nube. Si ha activado la opción **sincronizar datos de nube con la cámara** cuando instala el proveedor para todas las nubes en el servidor VMM aparecerá en la pestaña **Elementos protegidos** de la cámara. Si no se puede sincronizar una nube específica con la recuperación de sitio de Azure en la ficha **General** de la página de propiedades de la nube en la consola VMM.

![Nube publicado](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. En la página de inicio rápido, haga clic en **Configurar la protección de nubes VMM**.
2. En la pestaña de **Nubes de VMM** , seleccione la nube que desea configurar y vaya a la pestaña **configuración** .
3. En **destino**, seleccione **VMM**.
4. En la **ubicación de destino**, seleccione el servidor VMM in situ que administra la nube que desea usar para la recuperación.
4. En la **nube de destino**, seleccione la nube de destino que desea usar para la migración tras error de máquinas virtuales en la nube de origen. Tenga en cuenta que:

    - Le recomendamos que seleccione una nube de destino que cumpla los requisitos de recuperación para las máquinas virtuales que va a proteger.
    - Una nube sólo puede pertenecer a un par de nube única, como principal o una nube de destino.

5. En la **frecuencia de copia**, especifique con qué frecuencia se deben sincronizar datos entre ubicaciones de origen y destino de 5he. Tenga en cuenta que esta opción solo es relevante cuando el host de Hyper-V está ejecutando Windows Server 2012 R2. Para otros servidores se usa un valor predeterminado de cinco minutos.
6. En **puntos de recuperación adicionales**, especifique si desea crear puntos de recuperación adicionales. El valor predeterminado cero indica que el último punto recuperación para una máquina virtual principal se almacena en un servidor de host de réplica. Observe que la habilitación de varios puntos de recuperación requiere almacenamiento adicional para las instantáneas que se almacenan en cada punto de recuperación. De forma predeterminada, los puntos de recuperación se crean cada hora, por lo que cada punto de recuperación contiene el valor de la hora de datos. El valor de punto de recuperación se asigna para la máquina virtual en la consola VMM no debe ser menor que el valor que va a asignar en la consola de recuperación de sitio de Azure.
7. **Frecuencia de Dr**, especifique la frecuencia con que crear instantáneas coherentes de aplicación. Hyper-V usa dos tipos de instantáneas: una instantánea estándar que ofrece una instantánea incremental de la máquina virtual completa y una instantánea consistente con las aplicaciones que toma una instantánea de punto en el tiempo de los datos de aplicación en la máquina virtual. Dr use el servicio de copia sombra de volumen (VSS) para garantizar que las aplicaciones estén en un estado coherente cuando se toma la instantánea. Tenga en cuenta que si habilita DR, afectará al rendimiento de las aplicaciones que se ejecutan en máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicional que configura.

    ![Configurar las opciones de protección](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. En la **compresión de transferencia de datos**, especifique si se deben comprimir datos duplicados que se transfieren.
9. En **autenticación**, especifique cómo se autentica el tráfico entre los servidores de host de Hyper-V principal y recuperación. Seleccione HTTPS a menos que tiene un entorno de Kerberos configurado. Recuperación de sitio de Azure configurará automáticamente certificados para la autenticación HTTPS. No es necesaria ninguna configuración manual. Si selecciona Kerberos, se usará un vale Kerberos para la autenticación de los servidores de host. De forma predeterminada, se abrirá puerto 8083 y 8084 (para certificados) en el Firewall de Windows en los servidores de host de Hyper-V. Tenga en cuenta que esta opción solo es relevante para servidores de host de Hyper-V ejecutar en Windows Server 2012 R2.
10. En **puerto**, modifique el número de puerto en el que los equipos de host de origen y destino escuchan tráfico de replicación. Por ejemplo, puede modificar la configuración si desea aplicar la calidad de servicio (QoS) red ancho de banda para el tráfico de replicación. Compruebe que cualquier otra aplicación no está utilizando el puerto y de que está abierto en la configuración del firewall.
11. En el **método de replicación**, especifique cómo la replicación inicial de los datos de origen a ubicaciones de destino se tratará, antes de que empiece la replicación normal:

    - **En red**: copiar datos a través de la red puede llevar mucho tiempo y recursos. Se recomienda que use esta opción si la nube contiene máquinas virtuales con relativamente pequeño disco duro virtual, y si el sitio principal está conectado al sitio secundario sobre una conexión con ancho de banda ancha. Puede especificar que debe iniciar inmediatamente la copia o seleccione un período de tiempo. Si utiliza la replicación de la red, se recomienda prográmelo durante las horas.
    - **Sin conexión**, este método especifica que la replicación inicial se realizará con medios externos. Es útil si desea evitar la degradación del rendimiento de la red o para ubicaciones remotas geográfico. Para usar este método especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Al habilitar la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Enviar al sitio de destino y copiar a la ubicación de importación. El sistema copia la información importada a las máquinas virtuales de réplica.

12. Seleccione **Eliminar Máquina Virtual de réplica** para especificar que se debe eliminar la máquina virtual de réplica si detener la protección de la máquina virtual seleccionando la opción **eliminar la protección de la máquina virtual** en la pestaña máquinas virtuales de las propiedades de la nube. Con esta configuración habilitada, cuando se deshabilita la protección de la máquina virtual se quita de recuperación de sitios de Azure, se quita la configuración de recuperación de sitio para la máquina virtual en la consola VMM y se elimina la réplica.

    ![Configurar las opciones de protección](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Después de guardar la configuración de un trabajo se creará y se puede supervisar en la ficha **trabajos** . Todos los servidores de host de Hyper-V en la nube de origen VMM se configurarán para la replicación. En la ficha **Configurar** , se puede modificar la configuración de la nube. Si desea modificar la nube de ubicación o de destino de destino debe quitar la configuración de la nube y, a continuación, vuelva a configurar la nube.

### <a name="prepare-for-offline-initial-replication"></a>Prepararse para la replicación inicial sin conexión

Debe realizar las siguientes acciones para prepararse para la replicación inicial sin conexión:

- En el servidor de origen, deberá especificar una ubicación de la ruta de acceso desde la que se producirá la exportación de datos. Asignar Control total para NTFS y permisos compartidos con el servicio VMM en la ruta de exportación. En el servidor de destino, deberá especificar una ubicación de la ruta de acceso desde la que se produzca la importación de datos. Asignar los mismos permisos en esta ruta de acceso de importación.
- Si se comparte la ruta de acceso de importación o exportación, asignar la pertenencia a administrador, usuario avanzado, operador de impresión u operador de servidor para la cuenta de servicio VMM en el equipo remoto en el que se encuentra la compartida.
- Si está utilizando las cuentas Ejecutar como agregar hosts, en las rutas de acceso de importación y exportación, asignar lectura y permisos de escritura a las cuentas Ejecutar como en VMM.
- Los recursos compartidos de importar y exportar no deben estar en cualquier equipo que se utiliza como un servidor de host Hyper-V, porque la configuración de bucle no es compatible con Hyper-V.
- En Active Directory, en cada Hyper-V server host que contiene máquinas virtuales que desea proteger, habilitar y configurar la delegación restringida para confiar en los equipos remotos en que las rutas de acceso de importación y exportación se encuentran, como sigue:
    1. En el controlador de dominio, abra **equipos y usuarios de Active Directory**.
    2. En el árbol de consola, haga clic en **nombre de dominio** > **equipos**.
    3. Haga clic en el nombre del servidor de host de Hyper-V > **Propiedades**.
    4. En la ficha **delegación** , haga clic en T**óxido este equipo para la delegación sólo a servicios especificados**.
    5. Haga clic en **usar cualquier protocolo de autenticación**.
    6. Haga clic en **Agregar** > **usuarios y equipos**.
    7. Escriba el nombre del equipo que hospeda la ruta de exportación > **Aceptar**. En la lista de servicios disponibles, mantenga presionada la tecla CTRL y haga clic en **cifs** > **Aceptar**. Repita los pasos en el nombre del equipo que aloja la ruta de acceso de importación. Repita según sea necesario para servidores de host de Hyper-V adicionales.

## <a name="step-5-configure-network-mapping"></a>Paso 5: Configurar la asignación de red
1. En la página de inicio rápido, haga clic en **mapa de redes**.
2. Seleccione el servidor VMM origen desde la que desea asignar redes y, a continuación, el servidor VMM de destino a la que se asignarán las redes. Se muestra la lista de origen y sus redes de destino asociado. Se muestra un valor en blanco para redes que no están asignadas actualmente.
3. Seleccione una red en la **red en origen** > **mapa**. El servicio detecta las redes VM en el servidor de destino y los muestra. Haga clic en el icono de información junto a los nombres de red de origen y destino para ver las subredes para cada red.

    ![Configurar la asignación de red](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. En el cuadro de diálogo Seleccione una de las redes VM desde el servidor VMM de destino.

    ![Seleccione una red de destino](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Cuando se selecciona una red de destino, se muestran las nubes protegidas que usan la red de origen. También se muestran las redes de destino disponibles que están asociadas a las nubes que se utiliza para la protección. Le recomendamos que seleccione una red de destino que está disponible para todas las nubes que se usa para la protección. O bien, también puede ir al servidor de VMM y modificar las propiedades de la nube para agregar la red lógica correspondiente a la red de la máquina virtual que desea elegir.
6. Haga clic en la marca de verificación para completar el proceso de asignación. Se inicia un trabajo realizar un seguimiento del progreso de la asignación. Puede verlo en la ficha **trabajos** .


## <a name="step-6-configure-storage-mapping"></a>Paso 6: Configurar la asignación de almacenamiento
De forma predeterminada cuando se duplica una máquina virtual en un servidor de host de Hyper-V de origen a un servidor de host de destino Hyper-V, los datos duplicados se almacenan en la ubicación predeterminada que se indica el Hyper-V para host de destino en el Administrador de Hyper-V. Para tener más control sobre dónde se almacenan los datos duplicados, puede configurar las asignaciones de almacenamiento como sigue:



1. Definir las clasificaciones de almacenamiento en servidores VMM de origen y de destino. [Más información](https://technet.microsoft.com/library/gg610685.aspx). Clasificaciones deben estar disponibles para los servidores de host de Hyper-V en nubes de origen y destino. Clasificaciones no es necesario tener el mismo tipo de almacenamiento. Por ejemplo, se puede asignar una clasificación de origen que contiene los recursos compartidos SMB a una clasificación de destino que contiene CSVs.
2. Una vez clasificaciones de puede crear asignaciones. Para ello, en la página de **Inicio rápido** > **asignar almacenamiento**.
3. Haga clic en la ficha **almacenamiento** > **asignar clasificaciones de almacenamiento**.
4. En la pestaña **asignar clasificaciones de almacenamiento** , seleccione clasificaciones en el origen y destino en servidores VMM. Guardar la configuración.

    ![Seleccione una red de destino](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Paso 7: Habilitar la protección de máquina virtual
Después de servidores, nubes y redes están configuradas correctamente, puede habilitar la protección de máquinas virtuales en la nube.

1. En la pestaña **máquinas virtuales** en la nube en que se encuentra la máquina virtual, haga clic en **Habilitar protección** > **Agregar máquinas virtuales**.
2. En la lista de máquinas virtuales en la nube, seleccione el que desea proteger.

    ![Habilitar la protección de máquina virtual](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Realizar un seguimiento del progreso de la acción de Habilitar protección en la pestaña **tareas** , incluida la replicación inicial. Después de que se ejecuta el trabajo de protección de finalizar la máquina virtual está lista para la migración tras error. Después de habilita protección y se duplican máquinas virtuales, podrá verlos en Azure.

    ![Trabajo de protección de máquina virtual](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] También puede habilitar la protección de máquinas virtuales de la consola VMM. Haga clic en **Habilitar protección** en la barra de herramientas en la ficha de **Recuperación de sitio de Azure** en las propiedades de la máquina virtual.

### <a name="on-board-existing-virtual-machines"></a>Integrada máquinas virtuales existentes

Si tiene máquinas virtuales de VMM que va a replicar con Hyper-V réplica necesitará incorporado ellos para la protección de recuperación de sitios de Azure como sigue:

1. Compruebe que dispone de nubes primario y secundarios. Asegúrese de que el servidor de Hyper-V hospeda la máquina virtual existente se encuentra en la nube principal y que el servidor de Hyper-V la máquina virtual de réplica de hospedaje se encuentra en la nube secundaria. Asegúrese de que ha configurado la configuración de protección de las nubes. La configuración debe coincidir con los que están configuradas para Hyper-V réplica. En caso contrario, la replicación de máquina virtual podría no funcionar según lo esperado.
2. A continuación, habilitar la protección de la máquina virtual principal. Azure VMM y la recuperación de sitio se asegurarán de que se detecta el mismo host de réplica y máquina virtual y recuperación de sitio de Azure se reutilizar y restablecer la replicación usando la configuración durante la configuración de la nube.


## <a name="test-your-deployment"></a>Probar la implementación

Para probar la implementación puede ejecutar un error de prueba para una sola máquina virtual, o crear un plan de recuperación que consta de varios equipos virtuales y ejecutar un error de prueba para el plan.  Migración de prueba tras error simula su mecanismo de migración y recuperación en una red aislada.

### <a name="create-a-recovery-plan"></a>Crear un plan de recuperación

1. En la pestaña **Planes de recuperación** , haga clic en **Crear el Plan de recuperación**.
2. Especifique un nombre para el plan de recuperación y servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales que están habilitados para la migración y recuperación. Seleccione **Hyper-V** para ver solo las nubes que se han configurado para la replicación de Hyper-V.

    ![Crear el plan de recuperación](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. En **Seleccionar Máquina Virtual**, seleccione los grupos de replicación. Todas las máquinas virtuales de Windows asociados con el grupo de replicación se seleccionará y se agrega al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado de plan de recuperación, grupo 1. Puede agregar más grupos si es necesario. Tenga en cuenta que, una vez máquinas virtuales de replicación se iniciará según el orden de los grupos de plan de recuperación.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Después de una recuperación de plan se creó, aparece en la lista en la pestaña de **Planes de recuperación** .

###<a name="run-a-test-failover"></a>Ejecutar un error de prueba

1. En la pestaña **Planes de recuperación** , seleccione el plan y haga clic en **Prueba de migración tras error**.
2. En la página **Confirmar Failover de prueba** , seleccione **Ninguno**. Tenga en cuenta que con esta opción había habilitado el error sobre máquinas virtuales de réplica no estar conectado a una red. Esto comprueba que la máquina virtual falla sobre según lo esperado, pero no probar su entorno de red de replicación. Observe cómo [ejecutar un error de prueba](site-recovery-failover.md#run-a-test-failover) para obtener más detalles sobre cómo usar las opciones de red diferentes.
3. La máquina virtual de prueba se creará en el mismo host que el host en la que se encuentra la máquina virtual de réplica. Se agrega a la misma nube donde se encuentra la máquina virtual de réplica.

### <a name="run-a-recovery-plan"></a>Ejecutar un plan de recuperación
Después de la replicación de la máquina virtual de réplica que no tenga una dirección IP que es la misma que la dirección IP de la máquina virtual principal. Máquinas virtuales se actualizará el servidor DNS que están usando una vez que empiezan. También puede agregar una secuencia de comandos para actualizar el servidor DNS para garantizar una actualización oportuna.

#### <a name="script-to-retrieve-the-ip-address"></a>Secuencia de comandos para recuperar la dirección IP
Ejecute esta secuencia de comandos de ejemplo para recuperar la dirección IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Secuencia de comandos para actualizar DNS
Ejecute esta secuencia de comandos de ejemplo para actualizar DNS, que especifica la dirección IP que ha recuperado mediante el script de ejemplo anterior.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Información de privacidad de recuperación de sitios

Esta sección proporciona información adicional de la declaración de privacidad para el sitio recuperación de Microsoft Azure servicio (""). Para ver la declaración de privacidad para los servicios de Microsoft Azure, consulte la [Declaración de privacidad de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Característica: el registro**

- **Qué hace**: registra el servidor con el servicio de modo que se puede proteger máquinas virtuales de Windows
- **Información recopilada**: después de registrar el servicio recopila, procesa y transmite información de administración de certificados desde el servidor VMM que se hayan designado para proporcionar recuperación usando el nombre de servicio del servidor VMM y el nombre de máquina virtual nubes en su servidor VMM.
- **Uso de la información**:
    - Certificado de administración: se usa para ayudar a identificar y autenticar el servidor VMM registrado para obtener acceso al servicio. El servicio utiliza la parte de la clave pública del certificado para proteger un símbolo que solo el servidor VMM registrado puede tener acceso a. El servidor debe utilizar este token para tener acceso a las características del servicio.
    - Nombre del servidor VMM: nombre del servidor VMM el es necesaria para identificar y comunicarse con el servidor VMM apropiado en el que se encuentran las nubes.
    - Nombres del servidor VMM en la nube: el nombre de la nube es necesario cuando se usa la nube de servicio de emparejamiento de unpairing característica se describe a continuación. Cuando decide emparejar su nube desde un centro de datos principal con otro nube en el centro de recuperación de datos, se muestran los nombres de todas las nubes desde el centro de recuperación de datos.

- **Opción**: esta información es una parte esencial del proceso de registro de servicio, ya que ayuda a usted y el servicio para identificar el servidor VMM para los que desea proporcionar protección de recuperación de sitios de Azure para identificar el servidor VMM registrado correcto. Si no desea enviar esta información para el servicio, no utilice este servicio. Si registrar su servidor y más tarde desea anular su registro, puede hacerlo eliminando la información del servidor VMM desde el portal de servicio (que es el portal de Azure).

**Característica: Habilitar la protección de recuperación de sitios de Azure**

- **Qué hace**: el proveedor de recuperación de sitio de Azure instalado en el servidor VMM es el conducto para comunicarse con el servicio. El proveedor es una biblioteca de vínculos dinámicos (DLL) hospedada en el proceso VMM. Después de instala el proveedor, obtiene habilitada la característica "Recuperación de centro de datos" en la consola del administrador VMM. Cualquier máquinas virtuales de nuevas o existentes en una nube puede habilitar una propiedad denominada "Recuperación de centro de datos" para ayudar a proteger la máquina virtual. Cuando se establece esta propiedad, el proveedor envía el nombre y el identificador de la máquina virtual para el servicio. Está habilitada la protección virtual tecnología de replicación de Windows Server 2012 o Windows Server 2012 R2 Hyper-V. Los datos de la máquina virtual replicarse desde un host de Hyper-V a otro (normalmente se encuentra en un centro de datos diferente "recuperación").

- **Información recopilada**: el servicio recopila, procesa y transmite los metadatos de la máquina virtual, que incluye el nombre, identificador, una red virtual y el nombre de la nube a la que pertenece.

- **Uso de la información**: el servicio usa la información anterior para rellenar la información de la máquina virtual en el portal de servicio.

- **Opción**: Esto es una parte esencial del servicio y no se puede desactivar. Si no desea enviar al servicio de esta información, no activar la protección de recuperación de sitios de Azure para cualquier máquinas virtuales. Tenga en cuenta que todos los datos enviados por el proveedor del servicio se envía a través de HTTPS.

**Función: Plan de recuperación**

- **Qué hace**: esta característica le ayuda a crear un plan de orquestación para el centro de datos de "recuperación". Puede definir el orden en que se debe iniciar las máquinas virtuales o un grupo de máquinas virtuales en el sitio de recuperación. También puede especificar las secuencias de comandos automatizadas para ejecutar o manual de cualquier acción que se realicen en el momento de la recuperación para cada máquina virtual. Normalmente se activa la migración tras error (que se describe en la siguiente sección) en el nivel de Plan de recuperación de recuperación coordinada.

- **Información recopilada**: el servicio recopila, procesa y transmite metadatos para el plan de recuperación, incluidos los metadatos de máquina virtual y metadatos de los scripts de automatización y notas de acción manual.

- **Uso de la información**: los metadatos descrito anteriormente se usan para generar el plan de recuperación en el portal de servicio.

- **Opción**: Esto es una parte esencial del servicio y no se puede desactivar. Si no desea enviar al servicio de esta información, no crear planes de recuperación en este servicio.

**Función: Asignación de red**

- **Qué hace**: esta característica le permite asignar la información de la red desde el centro de datos principal para el centro de recuperación de datos. Cuando se recuperan los equipos virtuales en el sitio de recuperación, esta asignación ayuda a establecer la conectividad de red para ellos.

- **Información recopilada**: como parte de la característica de asignación de red, el servicio recopila, procesa y transmite los metadatos de las redes lógicas para cada sitio (principal y centro de datos).

- **Uso de la información**: el servicio usa los metadatos para rellenar el portal de servicio donde puede asignar la información de red.

- **Opción**: Esto es una parte esencial del servicio y no se puede desactivar. Si no desea enviar al servicio de esta información, no use la característica de asignación de red.

**Característica: Migración tras error - planeada, no planeada, prueba**

- **Qué hace**: esta característica le ayuda a los errores de una máquina virtual de un centro de datos administrados VMM a otro centro de datos administrados VMM. La acción de migración tras error se activa por el usuario en su portal de servicio. Posibles motivos para un error incluyen un evento no planificado (por ejemplo, en el caso de un disaster0 natural; un evento planificado (por ejemplo Centro de datos de equilibrio de carga); un error de prueba (por ejemplo un ensayo de plan de recuperación).

El proveedor de servicios en el servidor VMM Obtiene una notificación del evento del servicio y ejecute una acción de migración tras error en el host de Hyper-V a través de interfaces VMM. Errores reales de los de la máquina virtual de un host de Hyper-V a otra (normalmente se ejecuta en un centro de datos diferente "recuperación") se administran mediante la tecnología de replicación de Windows Server 2012 o Windows Server 2012 R2 Hyper-V. Una vez completada la migración tras error, el proveedor de servicios instalado en el servidor VMM del centro de datos de "recuperación" envía la información de éxito al servicio.

- **Información recopilada**: el servicio usa la información anterior para rellenar el estado de la información de acción de migración tras error en el portal de servicio.

- **Uso de la información**: el servicio usa la información anterior como sigue:

    - Certificado de administración: se usa para ayudar a identificar y autenticar el servidor VMM registrado para obtener acceso al servicio. El servicio utiliza la parte de la clave pública del certificado para proteger un símbolo que solo el servidor VMM registrado puede tener acceso a. El servidor debe utilizar este token para tener acceso a las características del servicio.
    - Nombre del servidor VMM: nombre del servidor VMM el es necesaria para identificar y comunicarse con el servidor VMM apropiado en el que se encuentran las nubes.
    - Nombres del servidor VMM en la nube: el nombre de la nube es necesario cuando se usa la nube de servicio de emparejamiento de unpairing característica se describe a continuación. Cuando decide emparejar su nube desde un centro de datos principal con otro nube en el centro de recuperación de datos, se muestran los nombres de todas las nubes desde el centro de recuperación de datos.

- **Opción**: Esto es una parte esencial del servicio y no se puede desactivar. Si no desea enviar al servicio de esta información, no use este servicio.

## <a name="next-steps"></a>Pasos siguientes

Una vez haya ejecutar una migración tras error de prueba para comprobar que su entorno funciona según lo esperado, [Obtenga más información sobre](site-recovery-failover.md) distintos tipos de migraciones tras error.
