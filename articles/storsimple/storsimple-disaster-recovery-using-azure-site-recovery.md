<properties 
   pageTitle="Automatizar DR para recursos compartidos de archivos en StorSimple mediante la recuperación de sitio de Azure | Microsoft Azure"
   description="Describe los pasos y procedimientos recomendados para crear una solución de recuperación de desastres para hospedado en StorSimple almacenamiento de archivos compartidos."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solución de recuperación automática con Azure sitio recuperación para recursos compartidos de archivos alojados en StorSimple

## <a name="overview"></a>Información general

Microsoft Azure StorSimple es una solución de almacenamiento de nube híbrida que resuelve las complejidades de datos no estructurados comúnmente asociados a los recursos compartidos de archivos. StorSimple utiliza almacenamiento de nube como una extensión de la solución local y automáticamente los datos de niveles entre almacenamiento local y el almacenamiento de la nube. Integrada de protección de datos, con local y la nube instantáneas, elimina la necesidad de una infraestructura de almacenamiento desorganizados.

[Recuperación de sitio de Azure](../site-recovery/site-recovery-overview.md) es un servicio basado en Azure que ofrece capacidades de recuperación (DR) de desastres al coordinar replicación, migración tras error y recuperación de máquinas virtuales. Recuperación de sitio de Azure es compatible con un número de tecnologías de replicación de forma uniforme replicar, proteger y perfecta conmutar máquinas virtuales y aplicaciones de nubes públicas o privadas o alojados.

Con Azure sitio recuperación, replicación de máquina virtual y capacidades de instantánea StorSimple nube, puede proteger el entorno de servidor completa del archivo. En caso de una interrupción, puede usar un solo clic para recuperar los recursos compartidos de archivos de Azure en tan solo unos minutos.

Este documento explica detalladamente cómo crear una solución de recuperación de desastres para los recursos compartidos de archivos alojados en el almacenamiento de StorSimple y realizar planeada, no planeada y probar migraciones tras error usando un plan de recuperación de un solo clic. Básicamente, se muestra cómo se puede modificar el Plan de recuperación en su cámara Azure sitio recuperación para habilitar StorSimple migraciones tras error durante situaciones de desastres. Además, describe los requisitos previos y configuraciones compatibles. Este documento se supone que está familiarizado con los conceptos básicos de arquitecturas de recuperación de sitios de Azure y StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opciones de implementación de Azure sitio recuperación compatibles

Los clientes pueden implementar servidores de archivos como servidores físicos o máquinas virtuales (VM) que se ejecutan en Hyper-V o sus y, a continuación, crear recursos compartidos de archivos de volúmenes extraídos de almacenamiento de StorSimple. Recuperación de sitio de Azure puede proteger implementaciones físicas y virtuales en un sitio secundario o en Azure. Este documento trata sobre los detalles de una solución de DR con Azure como el sitio de recuperación para un servidor de archivos que VM hospedada en Hyper-V y recursos compartidos de archivos de almacenamiento de StorSimple. Otras situaciones en que el servidor de archivos VM está en una VM VMware o un equipo físico se pueden implementar de forma similar.

## <a name="prerequisites"></a>Requisitos previos

Implementar una solución de recuperación de desastres de un solo clic que usa recuperación de sitio de Azure recursos compartidos de archivos alojados en StorSimple almacenamiento tiene los siguientes requisitos previos:

-   Servidor de archivo de Windows Server 2012 R2 que VM hospedada en Hyper-V o VMware o un equipo físico local

-   StorSimple almacenamiento dispositivo local registrados con el Administrador de StorSimple de Azure

-   Dispositivo de nube StorSimple creado en el Administrador de Azure StorSimple (se conservarán en estado apagado)

-   Recursos compartidos de archivos alojados en los volúmenes configurados en el dispositivo de almacenamiento de StorSimple

-   [Depósito de servicios de recuperación de sitios de azure](../site-recovery/site-recovery-vmm-to-vmm.md) creado en una suscripción de Microsoft Azure

Además, si Azure es el sitio de recuperación, ejecute la [herramienta de evaluación de disponibilidad de la máquina Virtual de Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) en máquinas virtuales para asegurarse de que son compatibles con servicios de recuperación de sitio de Azure y máquinas virtuales de Azure.

Para evitar problemas (Esto pueden ocasionar costos más altos), asegúrese de que crear su dispositivo de nube de StorSimple, la cuenta de automatización y el almacenamiento de latencia cuentas en la misma región.

## <a name="enable-dr-for-storsimple-file-shares"></a>Habilitar DR StorSimple recursos compartidos de archivos  

Cada componente del entorno local debe estar protegidos para habilitar la replicación completa y recuperación. Esta sección describe cómo:

-   Configurar la replicación de Active Directory y DNS (opcional)

-   Usar la recuperación de sitio de Azure para habilitar la protección del servidor de archivos VM

-   Habilitar la protección de volúmenes StorSimple

-   Configurar la red

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar la replicación de Active Directory y DNS (opcional)

Si desea proteger los equipos que ejecutan Active Directory y el DNS para que estén disponibles en el sitio de DR, debe proteger explícitamente (de modo que los servidores de archivos sean accesibles después de éxito sobre con autenticación). Hay dos opciones recomendadas en función de la complejidad del entorno de local del cliente.

#### <a name="option-1"></a>Opción 1

Si el cliente tiene un pequeño número de aplicaciones, un controlador de dominio para todo el sitio local y se errores en todo el sitio, a continuación, te recomendamos que uses Azure sitio recuperación replicación para replicar la máquina del controlador de dominio en un sitio secundario (es aplicable a sitios y sitios a Azure).

#### <a name="option-2"></a>Opción 2

Si el cliente tiene una gran cantidad de aplicaciones, está ejecutando un bosque de Active Directory y se errores sobre algunas aplicaciones a la vez, se recomienda la configuración de un controlador de dominio en el sitio de DR (un sitio secundario o en Azure).

Consulte [solución de DR automatizada de Active Directory y DNS mediante la recuperación de sitios de Azure](../site-recovery/site-recovery-active-directory.md) para obtener instrucciones al poner a disposición de un controlador de dominio en el sitio de DR. Para el resto de este documento, consideraremos que un controlador de dominio está disponible en el sitio de DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Usar la recuperación de sitio de Azure para habilitar la protección del servidor de archivos VM

Este paso requiere que preparar el entorno de servidor de archivo local, crear y preparar un depósito de recuperación de sitios de Azure y habilitar la protección de archivos de la máquina virtual.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar el entorno de servidor de archivo local

1.  Establezca el **Control de cuentas de usuario** para **No notificar a nunca**. Esto es necesario para que puede utilizar las secuencias de comandos de automatización Azure para conectar los destinos iSCSI después de éxito por recuperación de sitio de Azure.

    1.  Presione la tecla Windows + Q y busque **UAC**.

    2.  Seleccione **configuración de Control de cuentas de usuario de cambio**.

    3.  Arrastre la barra a la parte inferior hacia **Notificarme nunca**.

    4.  Haga clic en **Aceptar** y, a continuación, seleccione **Sí** cuando se le solicite.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Instalar al agente de VM en cada una del máquinas virtuales de servidor de archivos. Esto es necesario para que pueda ejecutar secuencias de comandos de automatización de Azure en el error sobre máquinas virtuales.

    1.  [Descargue el agente](http://aka.ms/vmagentwin) en `C:\\Users\\<username>\\Downloads`.

    2.  Abrir Windows PowerShell en modo de administrador (ejecutar como administrador) y, a continuación, escriba el comando siguiente para ir a la ubicación de descarga:

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] Dependiendo de la versión, puede cambiar el nombre de archivo.

1.  Haga clic en **siguiente**.

2.  Acepte los **Términos del contrato** y, a continuación, haga clic en **siguiente**.

3.  Haga clic en **Finalizar**.


1.  Crear recursos compartidos de archivos con volúmenes extraídos de almacenamiento de StorSimple. Para obtener más información, consulte [usar el servicio de administrador de StorSimple para administrar volúmenes](storsimple-manage-volumes.md).

    1.  En la VM local, presione la tecla Windows + Q y busque **iSCSI**.

    2.  Seleccione el **iniciador iSCSI**.

    3.  Seleccione la pestaña **configuración** y copie el nombre del iniciador.

    4.  Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com/).

    5.  Seleccione la ficha **StorSimple** y, a continuación, seleccione el servicio de administrador de StorSimple que contiene el dispositivo físico.

    6.  Crear contenedores de volumen y, a continuación, cree volúmenes. (Estos volúmenes son para los recursos compartidos de archivos en el servidor de archivos máquinas virtuales). Copie el nombre del iniciador y asigne un nombre adecuado para los registros de Control de acceso al crear los volúmenes.

    7.  Seleccione la ficha **Configurar** y observe la dirección IP del dispositivo.

    8.  En la VM local, ir al **iniciador iSCSI** de nuevo y escriba la dirección IP en la sección conectar rápida. Haga clic en **Conectar rápida** (ahora debe estar conectado el dispositivo).

    9.  Abra el Portal de administración de Azure y seleccione la pestaña **volúmenes y dispositivos** . Haga clic en **configurar automáticamente**. Debería aparecer el volumen que acaba de crear.

    10. En el portal, seleccione la pestaña **dispositivos** y, a continuación, seleccione **crear un nuevo dispositivo Virtual.** (Este dispositivo virtual se utilizará si se produce un error). Este nuevo dispositivo virtual puede mantenerse en un estado sin conexión para evitar gastos adicionales. Para poner sin conexión el dispositivo virtual, vaya a la sección de **máquinas virtuales** en el Portal y cerrarlo.

    11. Vuelva a las VM local y abra Administración de discos (presione la tecla Windows + X y seleccione **Administración de discos**).

    12. Observará algunos discos adicionales (dependiendo del número de volúmenes que haya creado). Haga clic en el primero de ellos, seleccione **Inicializar disco**y haga clic en **Aceptar**. Haga clic en la sección **Unallocated** , seleccione **Nuevo volumen Simple**, asignar una letra de unidad y finalice al asistente.

    13. Repita el paso l para todos los discos. Ahora puede ver todos los discos en **Este equipo** en el Explorador de Windows.

    14. Use la función de archivo y servicios de almacenamiento para crear recursos compartidos de archivos en estos volúmenes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Crear y preparar un depósito de recuperación de sitios de Azure

Consulte la [documentación de recuperación de sitio de Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para empezar con la recuperación de sitio de Azure antes de proteger el servidor de archivos VM.

#### <a name="to-enable-protection"></a>Para habilitar la protección

1.  Desconectar los destinos iSCSI las VM local que desea proteger mediante la recuperación de sitio de Azure:

    1.  Presione la tecla Windows + Q y buscar **iSCSI**.

    2.  Seleccione **Configurar iniciador iSCSI**.

    3.  Desconecte el dispositivo de StorSimple que conectó anteriormente. Como alternativa, puede desactivar el servidor de archivos para unos minutos al habilitar la protección.

    > [AZURE.NOTE] Esto hará que los recursos compartidos de archivos no está disponible temporalmente

1.  [Habilitar la protección de la máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) del servidor de archivos VM desde el portal de recuperación de sitios de Azure.

2.  Cuando se inicia la sincronización inicial, puede volver a conectar el destino nuevo. Vaya al iniciador iSCSI, seleccione el dispositivo de StorSimple y haga clic en **Conectar**.

3.  Cuando finalice la sincronización y el estado de la máquina virtual está **protegida**, seleccione la máquina virtual, seleccione la ficha **Configurar** y actualice la red de la máquina virtual (es decir, la red de error sobre VM(s) ser parte del). Si la red no aparece, significa que la sincronización todavía está ocurriendo.

### <a name="enable-protection-of-storsimple-volumes"></a>Habilitar la protección de volúmenes StorSimple

Si no ha seleccionado la opción **Habilitar una copia de seguridad predeterminado para este volumen** para los volúmenes StorSimple, vaya a **Las directivas de copia de seguridad** en el servicio Administrador de StorSimple y crear una directiva de copia de seguridad adecuada para todos los volúmenes. Se recomienda que establezca la frecuencia de las copias de seguridad en el objetivo de punto de recuperación (RPO) que le gustaría ver de la aplicación.

### <a name="configure-the-network"></a>Configurar la red

El servidor de archivos VM, configurar opciones de red de recuperación de sitios de Azure para que las redes VM se adjuntan a la red de DR correcta después de la migración.

Puede seleccionar la máquina virtual en la **Nube de VMM** o el **Grupo de protección** para establecer la configuración de red, tal como se muestra en la siguiente ilustración.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Crear un plan de recuperación

Puede crear un plan de recuperación de ASR para automatizar el proceso de migración tras error de los recursos compartidos de archivos. Si se produce una interrupción, puede hacer que aparezca el recursos compartidos de archivos en unos minutos con un solo clic. Para habilitar esta automatización, necesitará una cuenta de Azure automatización.

#### <a name="to-create-the-account"></a>Para crear la cuenta

1.  Vaya al portal clásico Azure y vaya a la sección de **automatización** .

1.  Crear una nueva cuenta de automatización. Mantenerlo en el misma geo o la región en la que se crearon las cuentas de almacenamiento y el dispositivo de nube de StorSimple.

2.  Haga clic en **nuevo** &gt; **Servicios de aplicación** &gt; **automatización** &gt; **Runbook** &gt; **Desde la Galería** de importar todos los runbooks necesarios a la cuenta de automatización.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Agregar el siguiente runbooks desde el panel **De recuperación** en la Galería:

    -   Conmutar StorSimple contenedores de volumen

    -   Limpiar de volúmenes StorSimple después de probar migración tras error (TFO)

    -   Montaje de volúmenes en StorSimple dispositivo después de la migración

    -   Inicie el dispositivo Virtual de StorSimple

    -   Desinstalar extensión de scripts personalizados en Azure VM

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Publicar las secuencias de comandos seleccionando runbook en la cuenta de automatización y vaya a la pestaña de **autor** . Después de este paso, la ficha **Runbooks** aparecerá como sigue:

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  En la cuenta de automatización, vaya a la pestaña **activos** , haga clic en **Agregar configuración** &gt; **Agregar credenciales**y agregar sus credenciales de Azure – name AzureCredential activo.

    Use las credenciales de Windows PowerShell. Debe ser una credencial que contiene un nombre de usuario de identificador de organización y una contraseña con acceso a esta suscripción Azure y con autenticación multifactor deshabilitado. Esto es necesario para autenticar en nombre del usuario durante las migraciones tras error y para que aparezca el volúmenes de servidor de archivos en el sitio de DR.

1.  En la cuenta de automatización, seleccione la ficha de **activos** y, a continuación, haga clic en **Agregar configuración** &gt; **Agregar variable** y agregue las siguientes variables. Puede elegir cifrar estos activos. Estas variables son específicos de plan de recuperación. Si la recuperación previsto (que se creará en el paso siguiente) nombre es el plan de pruebas y luego las variables deben ser StorSimRegKey del plan de pruebas, plan de pruebas AzureSubscriptionName y así sucesivamente.

    -   *RecoveryPlanName* **-StorSimRegKey**: la clave de registro para el servicio Administrador de StorSimple.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: el nombre de la suscripción de Azure.

    -   *RecoveryPlanName* **-ResourceName**: el nombre del recurso StorSimple que tiene el dispositivo de StorSimple.

    -   *RecoveryPlanName* **Nombre de dispositivo-**: el dispositivo que tiene el error.

    -   *RecoveryPlanName* **-TargetDeviceName**: el dispositivo de nube de StorSimple en la que los contenedores son tras error.

    -   *RecoveryPlanName* **-VolumeContainers**: una cadena de valores separados por comas de contenedores de volumen presentes en el dispositivo que necesita tras error; Por ejemplo, volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: el nombre de servicio del dispositivo de destino (Esto se puede encontrar en la sección de **Máquina Virtual** : el nombre del servicio es el mismo que el nombre DNS).

    -   *RecoveryPlanName* **-StorageAccountName**: el nombre de la cuenta de almacenamiento en el que se almacenará la secuencia de comandos (que se ejecuten en el error sobre VM). Puede ser cualquier cuenta de almacenamiento que tiene espacio para almacenar la secuencia de comandos temporalmente.

    -   *RecoveryPlanName* **-StorageAccountKey**: la tecla de acceso para la cuenta de almacenamiento anteriores.

    -   *RecoveryPlanName* **-ScriptContainer**: el nombre del contenedor en el que se almacenará la secuencia de comandos en la nube. Si no existe el contenedor, se creará.

    -   *RecoveryPlanName* **-VMGUIDS**: tras proteger una máquina virtual, recuperación de sitios de Azure asigna cada VM un identificador único que proporciona los detalles de los errores sobre VM. Para obtener el VMGUID, seleccione la ficha de **Servicios de recuperación** y, a continuación, haga clic en **Elemento protegido** &gt; **Grupos de protección** &gt; **máquinas** &gt; **Propiedades**. Si tiene varias VM, a continuación, agregue los GUID como una cadena de valores separados por comas.

    -   *RecoveryPlanName* **-AutomationAccountName** : el nombre de la cuenta de automatización en el que haya agregado la runbooks y los activos.

    Por ejemplo, si el nombre del plan de recuperación de fileServerpredayRP, a continuación, la ficha **activos** debería aparecer como sigue después de agregar todos los activos.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Vaya a la sección de **Servicios de recuperación** y seleccione el depósito de recuperación de sitios de Azure que creó anteriormente.

2.  Seleccione la ficha **Planes de recuperación** y crear un nuevo plan de recuperación como sigue:

    una.  Especifique un nombre y seleccione el **Grupo de protección**de correspondiente.

    b.  Seleccione las máquinas virtuales del grupo de protección que desea incluir en el plan de recuperación.

    c.  Después de la recuperación se crea el plan, selecciónela para abrir la vista de personalización del plan de recuperación.

    d.  Seleccione **apagado de todos los grupos**, haga clic en **secuencia de comandos**y elija **Agregar una secuencia de comandos principal antes de cerrar todos los de grupo**.

    e.  Seleccione la cuenta de automatización (en el que agregó el runbooks) y, a continuación, seleccione **un error sobre StorSimple volumen contenedores** runbook.

    f.  Haga clic en **grupo 1: iniciar**, elija **máquinas virtuales**y agregar las máquinas virtuales que deban proteger en el plan de recuperación.

    g.  Haga clic en **grupo 1: iniciar**, elija la **secuencia de comandos**y agregar los siguientes scripts en orden como pasos **después del 1 de grupo** .

    - Inicio StorSimple-Virtual dispositivo runbook
    - Un error sobre StorSimple volumen contenedores runbook
    - Montaje volúmenes después failover runbook
    - Desinstalar personalizado-script-extensión runbook

1.  Agregar una acción manual después de las secuencias de comandos de 4 anteriores en la misma **grupo 1: pasos posteriores a la** sección. Esta acción es el punto en el que se puede comprobar que todo funciona correctamente. Esta acción es necesario agregar solo como parte de la prueba de migración tras error (tan solo la **Prueba de migración tras error** casilla).

2.  Después de la acción manual, agregue la secuencia de comandos de limpieza con el mismo procedimiento que ha usado para las demás runbooks. Guarde el plan de recuperación.

    > [AZURE.NOTE] Cuando se ejecuta un error de prueba, debe comprobar todo en el paso de acción manual porque se eliminarán los volúmenes StorSimple que ha tenían clonar en el dispositivo de destino como parte de la limpieza una vez completada la acción manual.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Realizar una migración tras error de prueba

Consulte la Guía de [Solución de Active Directory DR](../site-recovery/site-recovery-active-directory.md) complementaria para consideraciones específicas de Active Directory durante la migración tras error de prueba. La instalación local no afectan en absoluto cuando se produce el error de prueba. Los volúmenes de StorSimple que se han adjuntado a la VM local se clonarán en el dispositivo de nube StorSimple en Azure. Se abre una máquina virtual para fines de pruebas en Azure y los volúmenes duplicados se adjuntan a la máquina virtual.

#### <a name="to-perform-the-test-failover"></a>Para realizar la migración tras error de prueba

1.  En el portal de clásico Azure, seleccione su depósito de recuperación de sitio.

1.  Haga clic en el plan de recuperación creado para el servidor de archivos VM.

2.  Haga clic en **Probar migración tras error**.

3.  Seleccione la red virtual para iniciar el proceso de migración tras error de prueba.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Cuando el entorno secundario hacia arriba, puede realizar su validaciones.

2.  Una vez completadas las validaciones, haga clic en **Las validaciones completada**. El entorno de prueba de migración tras error desaparecerá y se puede completar la operación de TFO.

## <a name="perform-an-unplanned-failover"></a>Realizar una migración tras error planificado

Durante una migración tras error planificado, los volúmenes StorSimple se ha podido sobre el dispositivo virtual, una réplica VM se actualizará en Azure y los volúmenes se adjuntan a la máquina virtual.

#### <a name="to-perform-an-unplanned-failover"></a>Para realizar una migración tras error planificado

1.  En el portal de clásico Azure, seleccione su depósito de recuperación de sitio.

1.  Haga clic en el plan de recuperación creado para el servidor de archivos de máquina virtual.

2.  Haga clic en **migración tras error** y, a continuación, seleccione **No planeado migración tras error**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Seleccione la red de destino y, a continuación, haga clic en el icono de verificación ✓ para iniciar el proceso de migración tras error.

## <a name="perform-a-planned-failover"></a>Realizar una migración tras error planeada

Durante un error planeado, el local servidor de archivos que VM se cierra correctamente y una nube se toma instantáneas de copia de seguridad de los volúmenes de dispositivo StorSimple. Los volúmenes StorSimple se ha podido sobre el dispositivo virtual, se abre una réplica VM en Azure y los volúmenes se adjuntan a la máquina virtual.

#### <a name="to-perform-a-planned-failover"></a>Para realizar una migración tras error planeada

1.  En el portal de clásico Azure, seleccione su depósito de recuperación de sitio.

1.  Haga clic en el plan de recuperación creado para el servidor de archivos VM.

2.  Haga clic en **migración tras error** y, a continuación, seleccione **Planeado de migración tras error**.

3.  Seleccione la red de destino y, a continuación, haga clic en el icono de verificación ✓ para iniciar el proceso de migración tras error.

## <a name="perform-a-failback"></a>Realizar una recuperación

Durante una recuperación contenedores de volumen de StorSimple son error sobre volver al dispositivo físico después de que se realiza una copia de seguridad.

#### <a name="to-perform-a-failback"></a>Para realizar una recuperación

1.  En el portal de clásico Azure, seleccione su depósito de recuperación de sitio.

1.  Haga clic en el plan de recuperación creado para el servidor de archivos VM.

2.  Haga clic en **migración tras error** y seleccione **planeado migración tras error** o **no planeado migración tras error**.

3.  Haga clic en **Cambiar dirección**.

4.  Seleccione las opciones de creación de VM y sincronización de los datos adecuados.

5.  Haga clic en el icono de verificación ✓ para iniciar el proceso de recuperación.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Prácticas recomendadas

### <a name="capacity-planning-and-readiness-assessment"></a>Evaluación de preparación y planificación de capacidad


#### <a name="hyper-v-site"></a>Sitio de Hyper-V

Use la [herramienta de planificación de capacidad de usuario](http://www.microsoft.com/download/details.aspx?id=39057) para diseñar el servidor, el almacenamiento y la infraestructura de red de su entorno de réplica Hyper-V.

#### <a name="azure"></a>Azure

Puede ejecutar la [herramienta de evaluación de disponibilidad de la máquina Virtual de Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) en máquinas virtuales para asegurarse de que son compatibles con servicios de recuperación de sitio de Azure y máquinas virtuales de Azure. La herramienta de evaluación de preparación verifica configuraciones de máquina virtual y avisa cuando las configuraciones no son compatibles con Azure. Por ejemplo, emite una advertencia si una unidad C: es mayor que 127 GB.


Planificación de capacidad se compone de al menos dos procesos importantes:

-   Asignación local máquinas virtuales de Hyper-V para tamaños de Azure VM (como A6, A7, A8 y A9).

-   Determinar el ancho de banda de Internet necesario.

## <a name="limitations"></a>Limitaciones

- Actualmente, solo 1 dispositivo StorSimple puede conmutar por error (en una sola aplicación de nube de StorSimple). El escenario de un servidor de archivos que se extiende por varios dispositivos de StorSimple no es compatible todavía.

- Si recibe un error al habilitar la protección de una máquina virtual, asegúrese de que ha desconectado los destinos iSCSI.

- Todos los contenedores de volumen agrupados debido a las directivas de copia de seguridad que abarcan contenedores de volumen error sobre juntas.

- Error sobre todos los volúmenes en los contenedores de volumen que haya elegido.

- Volúmenes que llegan más de 64 TB no pueden conmutar por error porque la capacidad máxima de un dispositivo StorSimple nube es 64 TB.

- Si se produce un error en la migración tras error planificado y y las máquinas virtuales se crean en Azure, a continuación, no limpiar las máquinas virtuales. En su lugar, realizar una recuperación. Si elimina las máquinas virtuales, a continuación, las VM local no pueden activarse nuevamente.

- Después de una migración tras error, si no puede ver los volúmenes, vaya a las máquinas virtuales, abra Administración de discos, volver a examinar los discos y, a continuación, conectarlos.

- En algunos casos, las letras de unidad en el sitio de DR posible diferentes a letras local. Si es así, debe corregir manualmente el problema una vez finalizada la migración tras error.

- Autenticación multifactor debe estar deshabilitada para la credencial Azure que aparece en la cuenta de automatización como activo. Si esta autenticación no está deshabilitada, no se permitirá secuencias de comandos que se ejecute automáticamente y se producirá un error en el plan de recuperación.

- Tiempo de espera de trabajo de migración tras error: secuencia de comandos de la StorSimple mostrarán el tiempo de espera si la migración tras error de contenedores de volumen tarda más tiempo que el límite de recuperación de sitios de Azure por script (actualmente 120 minutos).

- Tiempo de espera de trabajo de copia de seguridad: script StorSimple el tiempo de espera si la copia de seguridad de volúmenes tarda más tiempo que el límite de recuperación de sitios de Azure por script (actualmente 120 minutos).
 
    > [AZURE.IMPORTANT] Ejecutar la copia de seguridad manualmente desde el portal de Azure y, a continuación, ejecute de nuevo el plan de recuperación.

- Clonar tiempo de espera de trabajo: StorSimple el script agota el tiempo si clonar de volúmenes tarda más tiempo que el límite de recuperación de sitios de Azure por script (actualmente 120 minutos).

- Error de sincronización de tiempo: StorSimple las secuencias de comandos de errores que indica que las copias de seguridad no pudieron aunque es correcta en el portal de la copia de seguridad. Una posible causa posible que la hora del dispositivo StorSimple puede estar sincronizado con la hora actual en la zona horaria.
 
    > [AZURE.IMPORTANT] Sincronizar la hora del dispositivo con la hora actual en la zona horaria.

- Error de migración tras error del dispositivo: StorSimple el script puede producir un error si hay un dispositivo de migración tras error cuando se ejecuta el plan de recuperación.
    
    > [AZURE.IMPORTANT] Una vez completada la migración tras error del dispositivo, vuelva a ejecutar el plan de recuperación.

## <a name="summary"></a>Resumen

Con la recuperación de sitios de Azure, puede crear un completo automatizado plan de recuperación para un servidor de archivos VM tener hospedadas en StorSimple almacenamiento de archivos compartidos. Puede iniciar la migración tras error en pocos segundos desde cualquier lugar en el caso de una interrupción y obtener la aplicación marcha en unos minutos.
