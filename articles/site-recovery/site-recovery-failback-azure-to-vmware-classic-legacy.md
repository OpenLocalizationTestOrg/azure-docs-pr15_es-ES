<properties 
   pageTitle="Un error atrás máquinas virtuales de VMware y servidores físicos de Azure a VMware (heredado) | Microsoft Azure" 
   description="En este artículo se describe cómo conmutar una máquina virtual VMware que se han duplica en Azure con la recuperación de sitio de Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Un error atrás máquinas virtuales de VMware y servidores físicos de Azure a VMware con la recuperación de sitio de Azure (heredado)

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-failback-azure-to-vmware.md)
- [Portal de clásico de Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal clásica Azure (heredado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)

## <a name="overview"></a>Información general

En este artículo se describe cómo errores atrás máquinas virtuales de VMware y servidores físicos de Windows o Linux de Azure a su sitio local después de que haya replicar desde el sitio local en Azure.

>[AZURE.NOTE] En este artículo se describe un escenario heredado. Solo debe utilizar las instrucciones de este artículo si replicar en Azure con [estas instrucciones heredadas](site-recovery-vmware-to-azure-classic-legacy.md). Si configurar replicación usando la [implementación mejoradas](site-recovery-vmware-to-azure-classic-legacy.md) , siga las instrucciones de [este artículo](site-recovery-failback-azure-to-vmware-classic.md) para conmutar. 


## <a name="architecture"></a>Arquitectura

Este diagrama representa el escenario conmutación y. Las líneas azules son las conexiones usadas durante la migración tras error. Las líneas rojas son las conexiones usadas durante la recuperación. Las líneas con flechas que se vaya a través de internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Antes de empezar 

- Debe no ha sobre su máquinas virtuales de VMware o servidores físicos y deben ejecutarse en Azure.
- Tenga en cuenta que solo puede fallar atrás máquinas virtuales de VMware y Windows/Linux servidores físicos de Azure máquinas virtuales de VMware en el sitio principal local.  Si se está experimentando problemas volver una máquina física, migración tras error a Azure convertirá a una máquina virtual de Azure y recuperación para VMware convertirá a una VM VMware.

Le mostramos cómo configurar recuperación:

1. **Configurar los componentes de recuperación**: deberá configurar un servidor vContinuum local y elija en el servidor de configuración VM en Azure. También deberá configurar un servidor de procesos como una máquina virtual de Azure para enviar datos al servidor de destino principal local. Registrar el servidor de proceso con el servidor de configuración que controla la migración tras error. Instalar a un servidor de destino principal local. Si necesita un servidor de destino principal de Windows se configura automáticamente al instalar vContinuum. Si necesita Linux debe configurarla manualmente en un servidor distinto.
2. **Activar la protección y recuperación**: después de configurar los componentes, en vContinuum tendrá que activar la protección de error sobre máquinas virtuales de Azure. Deberá ejecutar una comprobación de disponibilidad de las máquinas virtuales y ejecutar un error de Azure a su sitio local. Cuando termine de recuperación proteger equipos locales para que comiencen replicar en Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Paso 1: Instalar vContinuum local

Debe instalar a un servidor de vContinuum local y elija en el servidor de configuración.

1.  [Descargar vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Descargar la versión [vContinuum actualizar](http://go.microsoft.com/fwlink/?LinkID=533813) .
3. Instalar la última versión de vContinuum. En la página **principal** , haga clic en **siguiente**.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  En la primera página del asistente, especifique la dirección IP del servidor CX y el puerto del servidor CX. Seleccione **usar HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Busque la dirección IP de servidor de configuración en la pestaña **panel** del servidor de configuración VM en Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Busque al servidor de configuración puerto público HTTPS en la pestaña **extremos** de la máquina virtual del servidor de configuración en Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. En la página **Detalles de la frase de contraseña de CS** especifique la contraseña que anotó hacia abajo cuando se registra el servidor de configuración. Si no recuerda compruebe **C:\\archivos de programa (x86)\\InMage sistemas\\privada\\connection.passphrase** en el servidor de configuración de máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  En la página **Seleccionar ubicación de destino** especifique donde desea instalar al servidor de vContinuum y haga clic en **instalar**.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Una vez finalizada la instalación, puede iniciar vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Paso 2: Instalar a un servidor de proceso de Azure 

Debe instalar a un servidor de procesos en Azure para que las máquinas virtuales en Azure pueden enviar los datos a un servidor de destino principal local. 

1.  En la página de **Configuración de servidores** en Azure, seleccione esta opción para agregar un nuevo servidor de proceso.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especificar un servidor de proceso nombre y un nombre y una contraseña para conectarse a la máquina virtual como administrador. Seleccione el servidor de configuración a la que desea registrar el servidor de procesos. Debe ser el mismo servidor que se usa para proteger y un error sobre sus máquinas virtuales.
3.  Especifique la red de Azure en la que se debe implementar el servidor de procesos. Debe ser la misma red que el servidor de configuración. Especificar una única subred dirección seleccionada de IP y comenzar su implementación.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Un trabajo se activa para implementar el servidor de procesos.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Después de que el servidor de proceso se implementa en Azure que puede iniciar sesión en el servidor con las credenciales que especificó. Registrar el servidor de procesos de la misma manera que haya registrado el servidor de procesos en local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Los servidores registrados durante la recuperación no estará visibles en propiedades VM en el sitio de recuperación. Sólo están visibles en la ficha **servidores** del servidor de configuración a los que está registrados. Este proceso puede demorarse unos 10 y 15 minutos hasta que el servidor de procesos aparece en la pestaña.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Paso 3: Instalar un servidor de destino principal local

Según el sistema operativo de máquinas virtuales de origen, debe instalar una Linux o un servidor de destino principal de Windows local.

### <a name="deploy-a-windows-master-target-server"></a>Implementar un servidor de destino principal de Windows

Un destino de patrón de Windows ya está incluido con la instalación de vContinuum. Cuando instala el vContinuum, un servidor principal también implementado en el mismo equipo y registrado en el servidor de configuración.

1.  Para comenzar la implementación, crear una vacía del equipo local en el host ESX en el que desea recuperar las máquinas virtuales de Azure.

2.  Asegúrese de que hay al menos dos discos adjuntados a la máquina virtual: uno se usa para el sistema operativo y la otra se usa para la unidad de retención.

3.  Instalar el sistema operativo.

4.  Instalar el vContinuum en el servidor. Esto también completa la instalación del servidor de destino principal.

### <a name="deploy-a-linux-master-target-server"></a>Implementar un servidor de destino maestra Linux

1.  Para comenzar la implementación, crear una vacía del equipo local en el host ESX en el que desea recuperar las máquinas virtuales de Azure.

2.  Asegúrese de que hay al menos dos discos adjuntados a la máquina virtual: uno se usa para el sistema operativo y la otra se usa para la unidad de retención.

3.  Instalar el sistema operativo Linux. El sistema de destino maestra Linux no debe utilizar LVM raíz o retención espacios de almacenamiento. Un servidor de destino maestra Linux está configurado para evitar la detección de particiones o discos LVM de forma predeterminada.
4.  Particiones que puede crear:

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Llevar a cabo el a continuación pasos posteriores a la instalación antes de comenzar la instalación de destino principal.


#### <a name="post-os-installation-steps"></a>Registrar los pasos de instalación de sistema operativo

Para obtener el ID de SCSI para cada uno de SCSI en el disco duro en un equipo virtual Linux, habilite el parámetro "disco. EnableUUID = TRUE "como sigue:

1. Apagar la máquina virtual.
2. Haga clic en la entrada VM en el panel izquierdo > **Editar configuración**.
3. Haga clic en la pestaña **Opciones** . Seleccione **avanzadas\>elemento General** > **Parámetros de configuración**. La opción de **Parámetros de configuración** solo está disponible cuando se apaga el equipo.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Comprueba si una fila con **disco. EnableUUID** existe. Si y se establece en **False** , a continuación, establece en **True (no entre mayúsculas y minúsculas).** Si existe y se establece en true, haga clic en **Cancelar** y probar el comando SCSI dentro del sistema operativo invitado después de arrancar hacia arriba. Si no existe haga clic en **Agregar fila**.
5. Agregar disco. EnableUUID en la columna **nombre** . Establezca su valor como TRUE. No agregar los valores anteriores junto con comillas dobles.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Descargar e instalar los paquetes adicionales

Nota: Asegúrese de que el sistema tiene conexión a internet antes de descargar e instalar los paquetes adicionales.

\#yum instalar -y xfsprogs perl lsscsi rsync wget kexec-herramientas

Este comando descarga estos 15 paquetes desde repositorio de CentOS 6.6 y los instala:

BC-1.06.95-1.el6.x86\_64. rpm

busybox-1.15.1-20.el6.x86\_64. rpm

elfutils-bibliotecas-0.158-3.2.el6.x86\_64. rpm

kexec-herramientas-2.0.0-280.el6.x86\_64. rpm

lsscsi-0,23-2.el6.x86\_64. rpm

2.03 de lzo de 3.1.el6\_5.1.x86\_64. rpm

Perl-5.10.1-136.el6\_6.1.x86\_64. rpm

Perl-módulo-acoplable-3.90-136.el6\_6.1.x86\_64. rpm

Perl caja de escape 1.04 136.el6\_6.1.x86\_64. rpm
    
Perl caja Simple 3.13 136.el6\_6.1.x86\_64. rpm

Perl-bibliotecas-5.10.1-136.el6\_6.1.x86\_64. rpm

Perl-versión-0.77-136.el6\_6.1.x86\_64. rpm

rsync-3.0.6-12.el6.x86\_64. rpm

divertidos 1.1.0-1.el6.x86\_64. rpm

1.12-wget-5.el6\_6.1.x86\_64. rpm

Nota: Si el equipo de origen usa el sistema de archivos Reiser o XFS para el dispositivo de inicio o raíz, a continuación, paquetes siguientes deben descargar e instalar en Linux destino principal antes de la protección.

\#CD/usr/local

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#rpm - ivh kmod-reiserfs-0,0-1.el6.elrepo.x86\_rpm 64.-utilidades reiserfs-1.el6.elrepo.x86 3.6.21\_64. rpm

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#rpm - ivh xfsprogs-3.1.1-16.el6.x86\_64. rpm

#### <a name="apply-custom-configuration-changes"></a>Aplicar cambios de configuración personalizada

Antes de aplicar estos cambios Asegúrese de que haya completado la sección anterior, a continuación, siga estos pasos:


1. Copiar al binario RHEL 6-64 unificada agente en el sistema operativo recién creado.

2. Ejecutar este comando para descomprima el binario: **alquitrán - zxvf \<nombre de archivo\> **

3. Ejecutar este comando para conceder permisos: \# **chmod 755./ApplyCustomChanges.sh**

4. Ejecutar la secuencia de comandos: ** \# ./ApplyCustomChanges.sh**. Ejecute el script solo una vez en el servidor. Reinicie el servidor después de que se ejecuta la secuencia de comandos.



### <a name="install-the-linux-server"></a>Instalar al servidor Linux


1. [Descargar](http://go.microsoft.com/fwlink/?LinkID=529757) el archivo de instalación.
2. Copie el archivo en la máquina virtual de destino de patrón de Linux mediante una herramienta de cliente de sftp de su elección. También puede iniciar sesión en la máquina virtual de destino principal de Linux y usar wget para descargar el paquete de instalación desde el vínculo proporcionado
3. Inicie sesión en Linux destino maestro servidor máquina virtual usando un ssh cliente de su elección
4. Si está conectado a la red de Azure en el que implementa el servidor de destino maestra Linux a través de una conexión VPN, a continuación, use la dirección IP interna del servidor que puede encontrar en la pestaña **panel** de máquina virtual y puerto 22 para conectar con el servidor mediante Secure Shell de destino maestra Linux.
5. Si va a conectarse al servidor de destino maestra Linux sobre una conexión a internet pública usar dirección IP virtual pública del servidor de destino maestra Linux (desde la ficha de **panel** máquinas virtuales) y el extremo del público para ssh iniciar sesión en la servder Linux.
6. Extraer los archivos desde el archivo de tar comprimido con gzip Linux destino maestro servidor instalador ejecutando: *"alquitrán – xvzf Microsoft ASR\_UA\_8.2.0.0\_RHEL6 64\"* desde el directorio que contiene el archivo del instalador.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Si extrae el instalador cambian archivos en un directorio diferente al directorio para que el contenido de la alquitrán archiva se han extraído. Desde esta ruta de acceso del directorio ejecutar "sudo. / install.sh".

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Cuando se le pida que elija un rol principal seleccione **2 (patrón de destino)**. Deje el resto de opciones de instalación interactivos en sus valores predeterminados.
9. Espere a que la instalación para continuar y la interfaz de configuración de Host aparezca. La configuración de Host para el servidor de sarget maestro Linux es una utilidad de línea de comandos. No cambiar el tamaño de la ssh ventana de la utilidad de cliente. Use las teclas de dirección para seleccionar la opción **Global** y presione ENTRAR en el teclado.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. En el campo **IP** , escriba la dirección IP interna del servidor de configuración (que puede encontrar en la pestaña **panel** del servidor de configuración VM) y presione ENTRAR. En **puerto** , escriba **22** y presione ENTRAR. 
11.  Dejar de **Usar HTTPS** como **Sí**y presione ENTRAR.
12.  Escriba la contraseña que se ha generado en el servidor de configuración. Si está usando a un cliente MASILLA desde un equipo Windows para ssh a la máquina virtual de Linux puede utilizar MAYÚS+INSERT para pegar el contenido del Portapapeles. Copiar la frase de contraseña al Portapapeles local mediante Ctrl-C y use MAYÚS+ENTRAR para pegarlo. Presione ENTRAR.
13.  Utilice la tecla flecha derecha para desplazarse para salir y presione ENTRAR. Espere a completar instalación.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si por algún motivo no pudo registrar el servidor de destino maestra Linux en el servidor de configuración puede hacer lo vuelva a ejecutar el host de utilidad de configuración de /usr/local/ASR/Vx/bin/hostconfigcli (que primero deberá configurar permisos de acceso a este directorio ejecutando chmod como un usuario avanzado).


#### <a name="validate-master-target-registration"></a>Validar registro de destino principal

Puede validar que el servidor de destino principal se registró correctamente en el servidor de configuración de cámara de recuperación de sitios de Azure > **Configuración del servidor** > **Detalles del servidor**.

>[AZURE.NOTE] Después de registrar el servidor de destino principal, si recibe errores de configuración que se hayan eliminado la máquina virtual de Azure o que extremos no están configurados correctamente, es porque aunque el dndpoints Azure detecta la configuración de destino principal cuando se implementa el patrón de destino en Azure, no es true para un local maestro destino server local. Esto no afecta a la recuperación y puede pasar por alto estos errores. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Paso 4: Proteger los equipos virtuales al sitio local

Es necesario proteger máquinas virtuales al sitio local antes de que se produce de nuevo.

### <a name="before-you-begin"></a>Antes de empezar

Cuando una máquina virtual es un error sobre Azure, agrega una unidad adicional temporal para el archivo de página. Se trata de una unidad adicional que no se requiere habitualmente por el error sobre VM puesto que ya dispone de una unidad dedicada para la página. Antes de comenzar el inverso protección de los equipos virtuales, debe asegurarse de que se desconecta la unidad para que no se ha protegen. Haga lo siguiente como sigue:

1.  Abra Administración de equipos y seleccione Administración de almacenamiento para que muestra los discos en línea y conectados al equipo.
2.  Seleccione el disco temporal conectado al equipo y elija ponerlo sin conexión. 

### <a name="protect-the-vms"></a>Proteger las máquinas virtuales

1. En el portal de Azure Active los Estados de la máquina virtual para asegurarse de que está no pudo sobre.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Iniciar vContinuum en su equipo.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Haga clic en **Nueva protección** y seleccione el tipo de sistema de la operación, el 

4.  En la nueva ventana que abrir seleccione el **tipo de sistema operativo** > **Obtener detalles** de las máquinas virtuales que desee recuperar. En **Detalles del servidor principal**, identifique y seleccione las máquinas virtuales que desea proteger. Máquinas virtuales aparecen en el nombre de host de vCenter que estaban antes de la migración tras error.
5.  Cuando seleccione una máquina virtual para proteger (y ya no haya sobre Azure) una ventana emergente proporciona dos entradas para la máquina virtual. Esto es porque el servidor de configuración detecta dos instancias de los equipos virtuales registrados. Debe quitar la entrada de la VM local de modo que puede proteger la máquina virtual correcta. Para identificar la entrada correcta de Azure VM aquí, puede iniciar sesión en la máquina virtual de Azure y vaya a C:\Program Files (x86) \Microsoft Azure Site Recovery\Application Data\etc. En el archivo drscout.conf, identifique el identificador de host. En el cuadro de diálogo vContinuum, mantenga la entrada para la que encuentra el identificador de host en la máquina virtual. Eliminar todas las demás entradas. Para seleccionar la máquina virtual correcta, puede hacer referencia a su dirección IP. La IP dirección rango local será la VM local.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. En el servidor de vCenter dejar la máquina virtual. También puede eliminar las VM local.
7. A continuación, especifique el servidor de MT de local a la que desea proteger las máquinas virtuales. Para ello, conectar con el servidor de vCenter al que desea conmutar.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Seleccione el servidor de destino maestra basado en el host a la que desea recuperar la máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Proporciona la opción de replicación para cada uno de los equipos virtuales. Para ello debe seleccionar el almacén de datos del lado de recuperación a la que se recuperarán las máquinas virtuales. La tabla resumen las distintas opciones que debe proporcionar para cada máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Opción** | **Opción valor recomendado**
    ---|---
    Dirección IP del servidor de procesos | Seleccione el servidor de proceso implementado en Azure
    Tamaño de retención en MB| 
    Valor de retención | 1
    Días y horas | Días
    Intervalo de coherencia | 1
    Seleccione el almacén de datos de destino | El almacén de datos disponible en el sitio de recuperación. El almacén de datos debe tiene suficiente espacio y estén disponibles al host ESX en el que desea restaurar la máquina virtual.


10. Configurar las propiedades que va a adquirir la máquina virtual después de la migración al sitio local. En la tabla siguiente se resumen las propiedades.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **(Propiedad)** | **Detalles**
    ---|---
    Configuración de red| Para cada adaptador de red detectado, selecciónelo y haga clic en **cambiar** para configurar la dirección IP de recuperación para la máquina virtual. 
    Configuración de hardware| Especificar la CPU y la memoria para la máquina virtual. Configuración se puede aplicar a todas las máquinas virtuales que intenta proteger. Para identificar los valores correctos para la CPU y la memoria, puede consultar el tamaño de la función máquinas virtuales de IAAS y ver el número de núcleos y memoria asignada.
    Nombre para mostrar | Después de recuperación a local, puede cambiar el nombre de los equipos virtuales como aparecerán en el inventario vCenter. El nombre predeterminado es el nombre de host de máquina virtual PC. Para identificar el nombre de la máquina virtual, puede hacer referencia a la lista VM en el grupo de protección.
    Configuración de NAT | Se explica en detalle

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Establecer la configuración de NAT

1. Para habilitar la protección de los equipos virtuales, dos canales de comunicación necesitan establecer. El primer canal está entre la máquina virtual y el servidor de procesos. Este canal recopila los datos de la máquina virtual y lo envía al servidor de procesos que envía los datos al servidor de destino principal. Si el servidor de procesos y la máquina virtual que se desea proteger se encuentran en la misma red virtual Azure no necesita usar la configuración de NAT. En caso contrario, especifique la configuración de NAT. Ver la dirección IP pública del servidor de proceso de Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. El segundo canal está entre el servidor y el servidor de destino principal. La opción usar NAT o no depende de si está usando una conexión VPN según o comunicarse a través de internet. No seleccione NAt si usa VPN, pero solo si usa una conexión a internet.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Si todavía no lo ha eliminado las máquinas virtuales de local según lo especificado y el almacén de datos que no se puede volver todavía contiene el VMDK antiguo deberá asegurarse de que la recuperación que VM obtiene creada en una ubicación nueva. Para hacer esto seleccione la configuración de **Opciones avanzadas** y especifique una carpeta alternativa para restaurar a en **Configuración de nombre de carpeta**. Deje las demás opciones con sus valores predeterminados. Aplicar la configuración de nombre de carpeta a todos los servidores.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Ejecute una comprobación de preparación para asegurarse de que los equipos virtuales están listos para protegerse a local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Espere a que finalice. Si se encuentra correctamente en todas las máquinas virtuales puede especificar un nombre para el plan de protección. A continuación, haga clic en **proteger** para comenzar.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Puede supervisar el progreso en vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Después del paso **Que plan de protección de activación** finaliza puede supervisar la protección de VM en el portal de recuperación del sitio.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Puede ver el estado exacto al hacer clic en la máquina virtual y supervisar su progreso.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Preparar el plan de recuperación

Puede preparar un plan de recuperación mediante vContinuum para que la aplicación puede por error a su sitio local en cualquier momento. Estos planes de recuperación son muy similares a los planes de recuperación en el sitio de recuperación.

1.  Iniciar vContinuum y seleccione **Administrar planes** > **recuperar.** Puede ver de la lista de todos los planes que se han utilizado conmutar máquinas virtuales. Puede usar el mismo plan para recuperar.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Seleccione el plan de protección y todas las máquinas virtuales que desea recuperar en él. Al seleccionar cada VM puede ver más detalles, incluido el servidor ESX de destino y el disco de máquina virtual de origen. Haga clic en **siguiente** para iniciar al Asistente para recuperar y seleccione las máquinas virtuales que desea recuperar.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Se puede recuperar en función de varias opciones pero se recomienda usar la **Última etiqueta** y seleccione **Aplicar para todas las máquinas virtuales** para asegurarse de que se usará los datos más recientes de la máquina virtual.
4. Ejecutar la **comprobación de preparación.** Comprueba si los parámetros derecha están configurados para habilitar la recuperación de máquina virtual. Si todas las comprobaciones son correctas, haga clic en **siguiente** . Si no es así, compruebe el registro y resolver los errores.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  En **Configuración de la máquina virtual de** asegurarse de que la configuración de recuperación correctamente. Puede cambiar la configuración de la máquina virtual si necesita.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Revise la lista de máquinas virtuales que se recuperarán y especifique un orden de recuperación. Tenga en cuenta que las máquinas virtuales se muestran con el nombre de host del equipo. Puede ser difícil asignar el nombre de host del equipo a la máquina virtual.
Para asignar los nombres, vaya a los equipos virtuales **paneles** en Azure y compruebe el nombre de host de máquina virtual.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Especifique un nombre de plan y seleccione **recuperar más adelante**. Se recomienda recuperar más adelante, puesto que la protección inicial podría no estar completa. 
11. Haga clic en **recuperar** para guardar el plan o desencadenar la recuperación si ha seleccionado para recuperar ahora y más. Puede comprobar el estado de recuperación para ver si el plan de se ha guardado.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Recuperar máquinas virtuales de Windows

Después de creado el plan, puede recuperar los equipos virtuales. Antes comprobar que se han completado las máquinas virtuales de sincronización. Si Aceptar muestra el estado de la replicación se haya completado la protección y se ha alcanzado el umbral RPO. Puede comprobar el estado de las propiedades de la máquina virtual.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Desactivar las máquinas virtuales Azure antes de iniciar la recuperación. Así se garantiza que no hay ningún cerebro dividido y que los usuarios solo tendrán acceso a una copia de la aplicación. 


1.  Inicie el plan guardado. En vContinuum seleccione **Monitor** los planes. Enumera todos los planes que se han ejecutado.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Seleccione el plan de **recuperación** y haga clic en **Inicio**. Puede supervisar la recuperación. Después de han sido desactivadas máquinas virtuales al conectarse a ellos en vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Proteger a Azure después de la recuperación

Una vez completada la recuperación probablemente desea proteger las máquinas virtuales de nuevo. Haga lo siguiente como sigue:

1.  Compruebe que está trabajando máquinas virtuales local y que las aplicaciones son accesibles.
2.  En el portal de recuperación de sitios de Azure, seleccione las máquinas virtuales y elimínelas. Seleccione esta opción para deshabilitar la protección de los equipos virtuales. Así se garantiza que las máquinas virtuales más no están protegidas.
3.  En Azure eliminar el error sobre máquinas virtuales de Windows Azure
4.  Eliminar la máquina virtual antigua en vSpehere. Estas son las máquinas virtuales que no pudo previamente sobre Azure.
5.  En el portal de recuperación de sitios proteger las máquinas virtuales que conmutar por error recientemente. Una vez que están protegidas puede agregarlas a un plan de recuperación.
 
## <a name="next-steps"></a>Pasos siguientes



- [Obtenga información sobre](site-recovery-vmware-to-azure-classic.md) la replicación de máquinas virtuales de VMware y servidores físicos en Azure con la implementación mejorada.

 
