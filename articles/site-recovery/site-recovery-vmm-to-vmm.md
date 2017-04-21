<properties
    pageTitle="Replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario de VMM con el portal de Azure | Microsoft Azure"
    description="Describe cómo implementar Azure recuperación de sitio para coordinar replicación, migración tras error y recuperación de máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario de VMM con el portal de Azure."
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

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario de VMM con el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-vmm.md)
- [Portal clásica](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Administrador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bienvenido a Azure sitio recuperación! Utilice este artículo si desea replicar máquinas virtuales de Hyper-V de local administrado en nubes de System Center Virtual Machine Manager (VMM) a un sitio secundario. En este artículo se describe cómo configurar la replicación usando la recuperación de sitio de Azure en el portal de Azure.

> [AZURE.NOTE] Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos: Administrador de recursos de Azure y clásica. Azure también tiene dos portales – el portal clásico Azure que admita el modelo de implementación clásica y el portal de Azure con soporte para ambos modelos de implementación.


Recuperación de sitio de Azure en el portal de Azure proporciona varias características nuevas:

- En la Azure portal, la copia de seguridad de Azure y los servicios de recuperación de sitios de Azure se combinan en un único depósito de servicios de recuperación, para que puedan configurar y administrar continuidad empresarial y recuperación (BCDR) desde una única ubicación. Un panel unificado permite supervisar y administrar operaciones en los sitios local y la nube pública de Azure.
- Los usuarios con suscripciones a Azure aprovisionados con el programa de proveedor de soluciones de nube (CSP) ahora pueden administrar operaciones de recuperación de sitio en el portal de Azure.


Después de leer este artículo, publique los comentarios en la parte inferior de los comentarios de Disqus. Formular preguntas técnicas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia BCDR que determina cómo mantenerse ejecución y está disponible durante el tiempo de inactividad planificado y aplicaciones, cargas de trabajo y datos y recuperación tan pronto como sea posible a las condiciones de trabajo normal. La estrategia BCDR debe mantener datos empresariales seguros y recuperables y garantizar que las cargas de trabajo permanecen continuamente disponibles cuando se produce desastre.

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR por coordinar la replicación de servidores físicos de local y máquinas virtuales en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en su ubicación primaria, conmutar a la ubicación secundaria para mantener las aplicaciones y cargas de trabajo disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales. Más información en [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)

Este artículo proporciona toda la información que necesita replicar máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario de VMM local. Incluye una introducción a la arquitectura, planificación de la información y los pasos de implementación para configurar servidores locales, opciones de replicación y planificación de capacidad. Después de configurar la infraestructura que puede habilitar la replicación en equipos que desea proteger y comprobar que migración tras error funciona.

## <a name="business-advantages"></a>Ventajas de la empresa

- Recuperación de sitio proporciona protección para las cargas de trabajo de la empresa y aplicaciones que se ejecutan en máquinas virtuales de Hyper-V mediante la replicación de a un servidor de Hyper-V secundario.
- El portal de servicios de recuperación proporciona una única ubicación para configurar, administrar y supervisar la replicación, la migración tras error y la recuperación.
- Puede ejecutar fácilmente ejecutar migraciones tras error desde su infraestructura local principal para el sitio secundario y recuperación (restaurar) desde el sitio secundario al primario.
- Puede configurar los planes de recuperación con varios equipos para que las cargas de trabajo de la aplicación en niveles conmutan entre sí.

## <a name="scenario-architecture"></a>Arquitectura de escenario

Estos son los componentes del escenario:

- **Sitio principal**: en el sitio primario, hay uno o más servidores de host de Hyper-V ejecutar máquinas virtuales de origen que desea duplicar. Estos servidores host principal se encuentran en una nube privada de VMM.
- **Sitio secundario**: en el sitio secundario, hay uno o más servidores de host de Hyper-V ejecutar máquinas virtuales de destino en los que replicar la VM principales. Estos servidores host se encuentran en una nube privada de VMM. En el servidor principal (si solo tiene un único servidor VMM) o en un servidor VMM secundario, puede ser la nube.
- **Proveedor**: durante la implementación de recuperación de sitios, instale el proveedor de servicios de recuperación de sitio de Azure en los servidores VMM y registrar los servidores en un depósito de servicios de recuperación. El proveedor se ejecuta en el servidor VMM se comunica con el sitio de recuperación por HTTPS 443 para replicar orquestación. Se produce la replicación de datos entre servidores de host de Hyper-V principal y secundario. Datos duplicados permanece dentro de redes y sitios local y no se envían a Azure. Más información sobre [privacidad](#privacy-information-for-site-recovery).

![Topología de E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Información general sobre la privacidad de datos

Esta tabla resume cómo se almacenan los datos en este escenario:
****
Acción | **Detalles** | **Datos recopilados** | **Usar** | **Obligatorio**
--- | --- | --- | --- | ---
**Registro** | Registrar un servidor VMM en un depósito de servicios de recuperación. Si más tarde desea anular el registro de un servidor, puede hacerlo eliminando la información del servidor desde el portal de Azure. | Una vez registrado un servidor VMM sitio recuperación recopila, procesos y transfiere metadatos acerca del servidor VMM y los nombres de las nubes VMM detectados por sitio de recuperación. | Los datos se utilizan para identificar y comunicarse con el servidor VMM apropiado y establecer la configuración de nubes de VMM adecuados. | Esta característica es necesaria. Si no desea enviar esta información al sitio de recuperación no debe utilizar el servicio de recuperación de sitios.
**Habilitar la replicación** | El proveedor de servicios de recuperación de Azure sitio está instalado en el servidor VMM y es el conducto para la comunicación con el servicio de recuperación de sitios. El proveedor es una biblioteca de vínculos dinámicos (DLL) hospedada en el proceso VMM. Después de instala el proveedor, obtiene habilitada la característica "Recuperación de centro de datos" en la consola del administrador VMM. Máquinas virtuales nuevas y existentes pueden habilitar esta característica Habilitar la protección de una máquina virtual. | Con esta propiedad establecida, el proveedor envía el nombre y el identificador de la máquina virtual al sitio de recuperación.  Replicación está habilitada por Windows Server 2012 o Windows Server 2012 R2 Hyper-V réplica. Los datos de la máquina virtual replicarse desde un host de Hyper-V a otro (normalmente se encuentra en un centro de datos diferente "recuperación"). | Recuperación de sitio utiliza los metadatos para rellenar la información de la máquina virtual en el portal de Azure. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información, no activar la protección de recuperación de sitio para máquinas virtuales. Tenga en cuenta que todos los datos enviados por el proveedor de servicios de recuperación de sitios se envía a través de HTTPS.
**Plan de recuperación** | Planes de recuperación de generar un plan de orquestación para el centro de recuperación de datos. Puede definir el orden en que se debe iniciar las máquinas virtuales o un grupo de máquinas virtuales en el sitio de recuperación. También puede especificar las secuencias de comandos automatizadas para ejecutar o manual de cualquier acción que realizará, en el momento de la recuperación para cada VM. Normalmente se activa la migración tras error en el nivel del plan de recuperación de recuperación coordinada. | Recuperación de sitio recopila, procesa y transmite metadatos para el plan de recuperación, incluidos los metadatos de máquina virtual y metadatos de los scripts de automatización y notas de acción manual. | Los metadatos se usan para generar el plan de recuperación en el portal de Azure. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información al sitio de recuperación, no crear planes de recuperación.
**Asignación de red** | Información de red de mapas desde el centro de datos principal para el centro de recuperación de datos. Cuando se recuperan máquinas virtuales en el sitio de recuperación, asignación de red ayuda a establecer la conectividad de red. | Recuperación de sitio recopila, procesa y transmite los metadatos de las redes lógicas para cada sitio (principal y centro de datos). | Los metadatos se utilizan para rellenar la configuración de la red para que pueda asignar la información de red. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información al sitio de recuperación, no use la asignación de red.
**Migración tras error (planeado o no planeado/prueba)** | Se produce un error en la migración tras error sobre máquinas virtuales de un centro de datos administra VMM a otro. La acción de migración tras error se activa de forma manual en el portal de Azure. | El proveedor en el servidor VMM es una notificación de la situación de error de recuperación de sitios y ejecuta una acción de migración tras error en el host de Hyper-V a través de interfaces VMM. Errores reales de los de una máquina virtual es de un host de Hyper-V a otra y controlado por Windows Server 2012 o Windows Server 2012 R2 Hyper-V réplica. Una vez completada la migración tras error, el proveedor de servicios en el servidor VMM en el centro de recuperación de datos envía información de éxito al sitio de recuperación. | Recuperación de sitio usa la información enviada a rellenar el estado de la información de acción de migración tras error en el portal de Azure. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información al sitio de recuperación, no use la migración tras error.


## <a name="azure-prerequisites"></a>Requisitos previos de Azure

Aquí es lo que necesita en Azure para implementar este escenario:

**Requisitos previos** | **Detalles**
--- | ---
**Azure**| Se necesita una cuenta de [Microsoft Azure](http://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.


## <a name="on-premises-prerequisites"></a>Requisitos previos de local

Aquí es lo que necesita en los sitios primarios y secundarios local para implementar este escenario:

**Requisitos previos** | **Detalles**
--- | ---
**VMM** | Se recomienda que implementar un servidor VMM en el sitio principal y un servidor VMM en el sitio secundario.<br/><br/> También puede [replicar entre nubes en un único servidor VMM](site-recovery-single-vmm.md). Para ello, necesita al menos dos nubes configurados en el servidor VMM.<br/><br/> Servidores VMM deben ejecutar al menos System Center 2012 SP1 con las últimas actualizaciones.<br/><br/> Cada servidor VMM debe tener uno o más nubes configurados y en todas las nubes deben tener el perfil de capacidad de Hyper-V establecer. <br/><br/>Nubes deben contener uno o varios grupos de host VMM.<br/><br/>Más información sobre la configuración de nubes de VMM en [configuración de la estructura de la nube VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), y [Tutorial: crear nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Servidores VMM tienen acceso a internet.
**Hyper-V** | Servidores Hyper-V deben ejecutar al menos Windows Server 2012 con la función Hyper-V y han instalado las últimas actualizaciones.<br/><br/> Un servidor de Hyper-V debe contener uno o más máquinas virtuales.<br/><br/>  Servidores de host de Hyper-V deben ubicarse en grupos de host de las nubes VMM primario y secundarios.<br/><br/> Si está ejecutando Hyper-V en un clúster de Windows Server 2012 R2 debe instalar la [actualización 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si está ejecutando Hyper-V en un clúster de Windows Server 2012, tenga en cuenta que agente de clúster no se crea automáticamente si tiene un clúster de basado en la dirección IP estático. Debe configurar al agente de clúster manualmente. [Obtenga más información](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Proveedor** | Durante la implementación de recuperación de sitios, instale el proveedor de servicios de recuperación de Azure sitio en servidores VMM. El proveedor se comunica con sitio de recuperación sobre HTTPS 443 para organizar la replicación. Se produce la replicación de datos entre los servidores de Hyper-V primario y secundarios en la LAN o una conexión VPN.<br/><br/> El proveedor se ejecuta en el servidor VMM necesita obtener acceso a estas direcciones URL: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Además, permiten la comunicación de firewall desde los servidores VMM a los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y permitir el protocolo HTTPS (443).

## <a name="prepare-for-deployment"></a>Prepararse para su implementación

Prepararse para su implementación que necesita:

1. [Preparar el servidor VMM](#prepare-the-vmm-server) para su implementación de recuperación de sitios.
2. [Prepararse para la asignación de red](#prepare-for-network-mapping). Configurar redes de modo que puede configurar la asignación de red.

### <a name="prepare-the-vmm-server"></a>Preparar el servidor VMM

Asegúrese de que el servidor VMM cumpla con los [requisitos previos](#on-premises-prerequisites) y puede tener acceso a las direcciones URL enumeradas.


### <a name="prepare-for-network-mapping"></a>Prepararse para la asignación de red

Mapas de asignación de red entre redes de VMM VM en los servidores VMM principales y secundarios para:

- Coloque óptima máquinas virtuales de réplica en hosts Hyper-V secundarios después de la migración.
- Conectar máquinas virtuales de réplica a redes VM correspondientes.
- Si no configura réplica de asignación de red que máquinas virtuales no se pueden conectar a cualquier red después de la migración.
- Si desea configurar red asignación durante la recuperación de sitio de implementación aquí es lo que necesita:

    - Asegúrese de que máquinas virtuales en el servidor de host de Hyper-V de origen están conectadas a una red de VM VMM. Red debe vincularse a una red lógica que esté asociada a la nube.
    - Compruebe que la nube secundaria que usar para la recuperación tiene configurada una red VM correspondiente. Red VM debe vincularse a una red lógica que está asociada a la nube secundaria.

- [Más información](site-recovery-network-mapping.md) acerca de cómo funciona la asignación de red.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Prepararse para su implementación con un solo servidor VMM

Si solo tiene un único servidor VMM pueden replicar VM en hosts Hyper-V en la nube VMM a [Azure](site-recovery-vmm-to-azure.md) o una nube de VMM secundario. Le recomendamos que la primera opción porque replicar entre nubes no está completa, pero si necesita hacerlo aquí es lo que debe hacer:

1. **Configurar VMM en una máquina virtual Hyper-V**. Al hacer esto le recomendamos que situar la instancia de SQL Server utilizada por VMM en la misma máquina virtual de. Esto ahorra tiempo como solo VM tiene que crear. Si desea usar la instancia remota de SQL Server y una interrupción se produce, necesita recuperar esa instancia antes de que se puede recuperar VMM.
2. **Asegúrese de que el servidor VMM tiene al menos dos nubes configurados**. Una nube contendrá las máquinas virtuales que desea replicar y la nube otra servirán como la ubicación secundaria. La nube que contiene las máquinas virtuales que desea proteger debe cumplir [los requisitos previos](#on-premises-prerequisites).
3. Configurar el sitio de recuperación como se describe en este artículo. Crear y registrar el servidor VMM en la caja fuerte, configurar una directiva de replicación y habilitar la replicación. Debe especificar que la replicación inicial lleve a cabo por la red.
4. Al configurar la asignación de red asignar la red de la máquina virtual para la nube principal a la red de la máquina virtual para la nube secundaria.
5. En la consola del Administrador de Hyper-V habilitar réplica Hyper-V en el host de Hyper-V que contiene la VM VMM y habilitar la replicación de la máquina virtual. Asegúrese de que no agrega la máquina virtual VMM a nubes que están protegidos por el sitio de recuperación, para asegurarse de que no reemplazar los valores de réplica de Hyper-V por sitio de recuperación.
6. Si crea los planes de recuperación para migración tras error usar el mismo servidor VMM para origen y destino.
7. Conmutar y recuperar como sigue:

    - Conmutar manualmente la VM VMM al sitio secundario mediante el Administrador de Hyper-V con un error planeado.
    - Conmutar las máquinas virtuales.
    - Después de que el principal se ha recuperado VMM VM, inicie sesión en el portal de Azure -> Servicios de recuperación de depósito y ejecutar una migración tras error planificado de las máquinas virtuales desde el sitio secundario al sitio primario.
    - Una vez completada la migración tras error no planeada, todos los recursos se pueden acceder desde el sitio primario nuevamente.


### <a name="create-a-recovery-services-vault"></a>Crear un depósito de servicios de recuperación

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo** > **administración** > **Servicios de recuperación**. También puede hacer clic en **Examinar** > **Servicios de recuperación de** depósitos > **Agregar**.

    ![Nuevo depósito](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. En **nombre** , especifique un nombre descriptivo para identificar la cámara. Si tiene más de una suscripción, seleccione uno de ellos.
4. [Crear un nuevo grupo de recursos](../resource-group-template-deploy-portal.md) o seleccione una existente y especifique una región Azure. Máquinas se reflejarán en esta área. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si desea obtener acceso rápidamente a la cámara del panel, haga clic en **Anclar al panel** > **Crear depósito**.

    ![Nuevo depósito](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

El nuevo depósito aparecerá en el **panel** > **todos los recursos**y en los principales módulos de **Servicios de recuperación de depósitos** .




## <a name="getting-started"></a>Introducción

Recuperación de sitio proporciona una introducción a experiencia que le ayude a implementar tan rápido como sea posible. Introducción a comprueba los requisitos previos y le guiará por los pasos de implementación en el orden correcto de recuperación de sitios.

En Introducción a seleccionar el tipo de equipos desea replicar y, a continuación, en la que desee replicar en. Configurar los servidores locales, crear directivas de replicación y planear la capacidad. Una vez que haya configurado su infraestructura de habilitar la replicación de máquinas virtuales. Puede ejecutar migraciones tras error para equipos específicos o crear planes de recuperación para conmutar varios equipos.

Comenzar introducción, elija cómo desea implementar el sitio de recuperación. El flujo de introducción cambia ligeramente dependiendo de los requisitos de replicación.

## <a name="step-1-choose-your-protection-goal"></a>Paso 1: Seleccionar su objetivo de protección

Seleccione lo que desea replicar y donde desea replicar en.

1. En el módulo de **Servicios de recuperación de depósitos** seleccione su cámara y haga clic en **configuración**.
2. En la **configuración de** > **Introducción** haga clic en el **Sitio de recuperación** > **paso 1: preparar la infraestructura** > **objetivo de protección**.
3. En el **objetivo de protección** seleccione **al sitio de recuperación**y seleccione **Sí, con Hyper-V**.
4. Seleccione **Sí** para indicar que usa VMM para administrar los hosts Hyper-V y a continuación, seleccione **Sí** si tiene un servidor VMM secundario. Si implementa replicación entre nubes en un único servidor VMM haga clic en **No**. A continuación, haga clic en **Aceptar**.

    ![Elija los objetivos](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Paso 2: Configurar el entorno de origen

Instalar el proveedor de servicios de recuperación de Azure sitio en servidores VMM y registrar los servidores de la cámara.

1. Haga clic en **paso 2: preparar la infraestructura de** > **origen**.

    ![Configurar origen](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. **Preparar origen** haga clic en **+ VMM** para agregar un servidor VMM.

    ![Configurar origen](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. En la comprobación de módulo de **Agregar servidor** **servidor de System Center VMM** aparece en **tipo de servidor** y que el servidor VMM cumple los [requisitos de la dirección URL y los requisitos previos](#on-premises-prerequisites).
4. Descargar el archivo de instalación del proveedor de servicios de recuperación de sitio de Azure.
5. Descargue la clave de registro. Es necesario al ejecutar la configuración. La clave es válida durante 5 días después de que genere.

    ![Configurar origen](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Instale el proveedor de servicios de recuperación de sitio de Azure en el servidor VMM.

> [AZURE.NOTE] No es necesario realizar ninguna instalación explícitamente en servidores de host de Hyper-V.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurar el proveedor de servicios de recuperación de sitio de Azure

1. Ejecutar al proveedor de archivo de configuración en cada servidor VMM. Si VMM se implementa en un clúster y va a instalar el proveedor para la primera vez instalar en un nodo activo y finalizar la instalación para registrar el servidor VMM en la cámara. A continuación, instale al proveedor en el resto de los nodos. Nodos de clúster deben ejecutarse la misma versión del proveedor.
2. El programa de instalación ejecuta comprobaciones prerequirement unas y solicita permiso para detener el servicio VMM. El servicio de VMM se se reinicia automáticamente cuando finaliza la instalación. Si va a instalar en un clúster VMM que le pedirá que detenga el rol de clúster.

2.  En **Microsoft Update** puede participar en actualizaciones para que se instalan las actualizaciones de proveedor de acuerdo con la directiva de Microsoft Update.
3. En la **instalación** acepte o modifique la ubicación predeterminada de la instalación de proveedor y haga clic en **instalar**.

    ![Ubicación de instalación](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Una vez completada la instalación haga clic en **registrar** para registrar el servidor en la cámara.

    ![Ubicación de instalación](./media/site-recovery-vmm-to-vmm/provider-register.png)

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

13.  Haga clic en **siguiente** para completar el proceso. Después de registrarse, recuperación de sitios de Azure recupera metadatos desde el servidor VMM. El servidor se muestra en la pestaña **Servidores VMM** en la página de **los servidores** de la cámara.

    ![Servidor](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Después de que el servidor está disponible en la consola de recuperación de sitios de **origen** > **Preparar origen** seleccione el servidor VMM y la nube en que se encuentra el host de Hyper-V. A continuación, haga clic en **Aceptar**.

#### <a name="command-line-installation"></a>Instalación de la línea de comandos

El proveedor de servicios de recuperación de sitio de Azure se puede instalar desde la línea de comandos. Este método puede usarse para instalar al proveedor en Server Core de Windows Server 2012 R2.

1. Descargue la clave de registro y archivos de instalación de proveedor a una carpeta. Por ejemplo, C:\ASR.
2. Detener el servicio de administrador de la máquina Virtual de System Center.
3. Desde un símbolo del sistema con privilegios elevados, ejecute los comandos para extraer al instalador de proveedor:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Ejecutar este comando para instalar el proveedor de servicios:

        C:\ASR> setupdr.exe /i

5. A continuación, ejecute estos comandos para registrar el servidor de la cámara:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

¿Dónde están los parámetros:

 - **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro  
 - **/FriendlyName**: parámetro obligatorio para que el nombre del servidor de host de Hyper-V que aparece en el portal de recuperación de sitios de Azure.
 - **/EncryptionEnabled**: parámetro opcional que utiliza solo al replicar desde VMM a Azure.
 - **/proxyaddress**: parámetro opcional que especifica la dirección del servidor proxy.
 - **/proxyPort**: parámetro opcional que especifica el puerto del servidor proxy.
 - **/ proxyusername**: parámetro opcional que especifica el nombre de usuario de Proxy (si el servidor proxy requiere autenticación).
 - **/ proxypassword**: parámetro opcional que especifica la contraseña para autenticar con el servidor proxy (si el servidor proxy requiere autenticación).  

## <a name="step-3-set-up-the-target-environment"></a>Paso 3: Configurar el entorno de destino

Seleccione el servidor VMM de destino y la nube.

1. Haga clic en **Preparar infraestructura** > **destino** y seleccione el servidor VMM de destino que desee usar.
2.  Se mostrarán nubes en el servidor que se sincronizan con el sitio de recuperación. Seleccione la nube de destino.

    ![Destino](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Paso 4: Configurar las opciones de replicación

1. Para crear una nueva réplica directiva, haga clic en **Preparar infraestructura** > **Opciones de replicación** > **+ crear y asociar**.

    ![Red](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. En **crear y asociar directiva** especifique un nombre de la directiva. El tipo de origen y destino debería **Hyper-V**.
3. En la **versión de host de Hyper-V** , seleccione el sistema operativo que se ejecuta en el host.

    > [AZURE.NOTE] La nube VMM puede contener hosts Hyper-V ejecutando diferentes versiones (admitidas) de Windows Server, pero una directiva de replicación es aplicados hosts con el mismo sistema operativo. Si tiene hosts con más de un sistema operativo versión crear directivas de replicación independiente.

4. En **tipo de autenticación** y **puerto de autenticación** especifique cómo se autentica el tráfico entre los servidores de host de Hyper-V principal y recuperación. Seleccione el **certificado** a menos que tiene un entorno Kerberos. Recuperación de sitio de Azure configurará automáticamente certificados para la autenticación HTTPS. No es necesario hacer nada manualmente. De forma predeterminada, se abrirá puerto 8083 y 8084 (para certificados) en el Firewall de Windows en los servidores de host de Hyper-V. Si selecciona **Kerberos**, se usará un vale Kerberos para la autenticación de los servidores de host. Tenga en cuenta que esta opción solo es relevante para servidores de host de Hyper-V ejecutar en Windows Server 2012 R2.
3. En **Copiar frecuencia** especifique la frecuencia con la que desea duplicar datos de delta tras la réplica inicial (cada 30 segundos, 5 o 15 minutos).
4. En **el punto de recuperación retención**, especificar en horas cuánto que será la ventana de retención para cada punto de recuperación. Equipos protegidas se pueden recuperar a cualquier punto dentro de una ventana.
6. En la **aplicación coherente frecuencia instantánea** especifique la frecuencia con (1-12 horas) puntos de recuperación que contiene Dr será creado. Hyper-V usa dos tipos de instantáneas: una instantánea estándar que ofrece una instantánea incremental de la máquina virtual completa y una instantánea consistente con las aplicaciones que toma una instantánea de punto en el tiempo de los datos de aplicación en la máquina virtual. Dr use el servicio de copia sombra de volumen (VSS) para garantizar que las aplicaciones estén en un estado coherente cuando se toma la instantánea. Tenga en cuenta que si habilita DR, afectará al rendimiento de las aplicaciones que se ejecutan en máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicional que configura.
7. En la **compresión de transferencia de datos** , especifique si se deben comprimir datos duplicados que se transfieren.
8. Seleccione **Eliminar réplica VM** para especificar que se debe eliminar la máquina virtual de réplica si deshabilita la protección de la máquina virtual de origen. Si habilita a esta configuración, cuando se deshabilita la protección para el origen de la máquina virtual se quita de la consola de recuperación de sitios, configuración de recuperación de sitio para que el VMM se quita de la consola VMM y se elimina la réplica.
3. En **el método de replicación inicial** si va a replicar por la red especifique si desea iniciar la replicación inicial o programarla. Para guardar el ancho de banda de red que desea programar fuera de sus horas de disponibilidad. A continuación, haga clic en **Aceptar**.

    ![Directiva de replicación](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Cuando se crea una nueva directiva automáticamente está asociada con la nube VMM. En **la directiva de replicación** haga clic en **Aceptar**. Puede asociar adicionales nubes de VMM (y las máquinas virtuales de ellos) a esta directiva de replicación de la **configuración de** > **replicación** > nombre de la directiva > **Asociar la nube de VMM**.

    ![Directiva de replicación](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Prepararse para la replicación inicial sin conexión

Puede realizar la replicación sin conexión para la copia de los datos iniciales. Puede preparar esto como sigue:

- En el servidor de origen, deberá especificar una ubicación de la ruta de acceso desde la que se producirá la exportación de datos. Asignar Control total para NTFS y permisos compartidos con el servicio VMM en la ruta de exportación. En el servidor de destino, deberá especificar una ubicación de la ruta de acceso desde la que se produzca la importación de datos. Asignar los mismos permisos en esta ruta de acceso de importación.
- Si se comparte la ruta de acceso de importación o exportación, asignar la pertenencia a administrador, usuario avanzado, operador de impresión u operador de servidor para la cuenta de servicio VMM en el equipo remoto en el que se encuentra la compartida.
- Si está utilizando las cuentas Ejecutar como agregar hosts, en las rutas de acceso de importación y exportación, asignar lectura y permisos de escritura a las cuentas Ejecutar como en VMM.
- Los recursos compartidos de importar y exportar no deben estar en cualquier equipo que se utiliza como un servidor de host Hyper-V, porque la configuración de bucle no es compatible con Hyper-V.
- En Active Directory, en cada Hyper-V server host que contiene máquinas virtuales que desea proteger, habilitar y configurar la delegación restringida para confiar en los equipos remotos en que las rutas de acceso de importación y exportación se encuentran, como sigue:
    1. En el controlador de dominio, abra **equipos y usuarios de Active Directory**.
    2. En el árbol de consola, haga clic en **nombre de dominio** > **equipos**.
    3. Haga clic en el nombre del servidor de host de Hyper-V > **Propiedades**.
    4. En la ficha **delegación** , haga clic en **Confiar en este equipo para la delegación sólo a servicios especificados**.
    5. Haga clic en **usar cualquier protocolo de autenticación**.
    6. Haga clic en **Agregar** > **usuarios y equipos**.
    7. Escriba el nombre del equipo que hospeda la ruta de exportación > **Aceptar**. En la lista de servicios disponibles, mantenga presionada la tecla CTRL y haga clic en **cifs** > **Aceptar**. Repita los pasos en el nombre del equipo que aloja la ruta de acceso de importación. Repita según sea necesario para servidores de host de Hyper-V adicionales.


### <a name="configure-network-mapping"></a>Configurar la asignación de red

Configurar la asignación de red entre las redes de origen y destino.

- ¿Es [lectura](#prepare-for-network-mapping) para ver una introducción rápida de la asignación de red. En la adición de [leer](site-recovery-network-mapping.md) para obtener una explicación más en profundidad.
- Compruebe que máquinas virtuales en servidores VMM está conectados a una red de la máquina virtual.

Configurar asignación como sigue:

1. **Configuración de** > **Infraestructura del sitio de recuperación** > **Asignación de red** > **asignaciones de red** , haga clic en **+ asignación de red**.

    ![Asignación de red](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. En la ficha **Agregar asignación de red** , seleccione el origen y destino servidores VMM. Se recuperan las redes VM asociadas con los servidores VMM.
3. En la **red de origen**, seleccione la red que desea usar en la lista de redes VM asociado con el servidor VMM principal.
6. En la **red de destino** , seleccione la red que desea usar en el servidor VMM secundario. A continuación, haga clic en **Aceptar**.

    ![Asignación de red](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Aquí es lo que ocurre cuando se inicia la asignación de red:

- Cualquier réplica máquinas virtuales que corresponden a la red de la máquina virtual de origen se conectará a la red de la máquina virtual de destino.
- Nuevas máquinas virtuales que están conectados a la red de la máquina virtual de origen se conectará a la red de destino que se asignan después de la replicación.
- Si modifica una asignación de una nueva red existente, máquinas virtuales de réplica se conectará con la nueva configuración.
- Si la red de destino tiene varias subredes y una de esas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, a continuación, la máquina virtual de réplica se conectará a esa subred de destino después de la migración. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

### <a name="configure-storage-mapping"></a>Configurar la asignación de almacenamiento
De forma predeterminada cuando se duplica una máquina virtual en un servidor de host de Hyper-V de origen a un servidor de host de destino Hyper-V, los datos duplicados se almacenan en la ubicación predeterminada que se indica el Hyper-V para host de destino en el Administrador de Hyper-V. Para tener más control sobre dónde se almacenan los datos duplicados, puede configurar la asignación de almacenamiento<br/><br/> Para configurar la asignación de almacenamiento, debe configurar clasificaciones de almacenamiento en el origen y destino en servidores VMM antes de comenzar la implementación. Asignación de almacenamiento a través de nuevo portal Azure no es compatible actualmente. Sin embargo, se puede habilitar a través de Powershell. [Más información](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Paso 5: Planear la capacidad

Ahora que ya tiene su basic infraestructura configurada por el usuario puede pensar planeamiento de capacidad y averiguar si necesita recursos adicionales.

Recuperación de sitio proporciona un planificador de capacidad basados en Excel para ayudarle a asignar los recursos adecuados para su entorno de origen, los componentes de recuperación de sitio, redes y almacenamiento. Puede ejecutar el organizador en el modo rápido para realizar cálculos en función de un número medio de máquinas virtuales, discos y almacenamiento o en el modo detallado en la que deberá introduce las cifras en el nivel de carga de trabajo. Antes de empezar necesitará:

- Recopilar información acerca de su entorno de replicación, incluidos VM, discos por máquinas virtuales y almacenamiento por disco.
- Estimar la tasa cambio (renovación) diarias que tendrá datos replicada delta. Puede usar el [Organizador de la capacidad de réplica de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para ayudarle a hacerlo.

1.  Haga clic en **Descargar** para descargar la herramienta y ejecútelo. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2.  Cuando haya terminado seleccione **Sí** en **¿ha ejecutado el programador de capacidad**?

    ![Planificación de capacidad](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Ancho de banda de control

Una vez recopilada la información de replicación de delta en tiempo real con la réplica de Hyper-V de planificador de capacidad, la herramienta de planificador de capacidad basados en Excel le ayuda a calcular el ancho de banda que necesario para la replicación (inicial y delta). Para controlar la cantidad de ancho de banda utilizado para la replicación puede configurar la directiva de NetQos con la directiva de grupo o Windows PowerShell. Hay varias maneras que puede hacer lo siguiente:

**PowerShell** | **Detalles**
--- | ---
**New - NetQosPolicy "QoS a subred de destino"** | Limitar el tráfico de un host de Hyper-V que ejecutan Windows Server 2012 R2 a una subred secundaria. Usar si las subredes primaria y secundarias son diferentes.
**New - NetQosPolicy "QoS al puerto de destino"** | Limitar el tráfico de un host de Hyper-V que se ejecuta Windows Server 2012 R2 al puerto de destino.
**Nuevo - NetQosPolicy "Acelerador tráfico de VMM"** | Limitar el tráfico de vmms.exe. Esto limita el tráfico de migración de Live y Hyper-V. Hacer coincidir refinar los puertos y protocolos IP.

Puede usar la configuración de ancho de banda grosor o limitar el tráfico entre bits por secundario. Si está usando un clúster debe hacer esto en todos los nodos de clúster. Para obtener más información, vea:


- Blog de Thomas Maurer en el [Límite de tráfico de réplica de Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Información adicional sobre el [cmdlet NetQosPolicy de nuevo](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Paso 6: Habilitar la replicación

Habilitar ahora la replicación de la siguiente manera:

1. Haga clic en **paso 2: replicar aplicación** > **origen**. Después de que se ha habilitado la replicación por primera vez, hacer clic en **+ replicar** en el depósito al habilitar la replicación de equipos adicionales.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. En el módulo de **origen** > Seleccionar servidor VMM y la nube en el que se encuentran los hosts Hyper-V que desea duplicar. A continuación, haga clic en **Aceptar**.

    ![Habilitar la replicación](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. En el módulo de **destino** Compruebe el servidor VMM secundario y la nube.
4. En **máquinas virtuales de Windows** , seleccione las máquinas virtuales que desea proteger de la lista.

    ![Habilitar la protección de máquina virtual](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Puede seguir el progreso de la acción de **Habilitar protección** en Configuración > **trabajos** > **trabajos de recuperación de sitios**. Después de que se ejecuta el trabajo de **Protección de finalizar** la máquina virtual está lista para la migración tras error.


>[AZURE.NOTE] También puede habilitar la protección de máquinas virtuales de la consola VMM. Haga clic en **Habilitar protección** en la barra de herramientas en las propiedades de la máquina virtual > ficha **Recuperación de sitio de Azure** .

Después de que se ha habilitado la replicación puede ver las propiedades de la máquina virtual en la **configuración de** > **Elementos replicar** > nombre de la máquina virtual. En el panel de **Essentials** puede ver información sobre la directiva de replicación de la máquina virtual y su estado. Para obtener más detalles, haga clic en **Propiedades** .

### <a name="onboard-existing-virtual-machines"></a>Incorporado máquinas virtuales existentes

Si tiene máquinas virtuales de VMM que va a replicar con la réplica de Hyper-V puede integrado para la protección de recuperación de sitio de Azure como sigue:

1. Asegúrese de que el servidor de Hyper-V hospeda la máquina virtual existente se encuentra en la nube principal y que el servidor de Hyper-V la máquina virtual de réplica de hospedaje se encuentra en la nube secundaria.
2. Asegúrese de que se configura una directiva de replicación de la nube VMM principal.
2. Habilitar la replicación de la máquina virtual principal. Azure VMM y la recuperación de sitio se asegurarán de que se detecta el mismo host de réplica y máquina virtual y recuperación de sitio de Azure se reutilizar y restablecer la replicación usando los valores especificados.


## <a name="step-7-test-your-deployment"></a>Paso 7: Comprobar la implementación

Para probar la implementación puede ejecutar un error de prueba para una sola máquina virtual o crear un plan de recuperación que contiene una o más máquinas virtuales.

### <a name="prepare-for-failover"></a>Prepararse para la migración tras error

- Para probar la implementación necesita una infraestructura de la máquina duplicada para que funcione como se esperaba. Si desea probar DNS y Active Directory puede crear una máquina virtual como un controlador de dominio con el DNS y replicar esto en Azure mediante la recuperación de sitio de Azure. Lea más en [Consideraciones de migración tras error de prueba de Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Las instrucciones de este artículo describen cómo ejecutar un error de prueba con ninguna red. Esta opción comprueba que la máquina virtual se produce un error sobre pero no probar configuración de la red de la máquina virtual. [Obtenga más](site-recovery-failover.md#run-a-test-failover) información sobre otras opciones.
- Si desea ejecutar una migración tras error planificado en lugar de un error de prueba tenga en cuenta lo siguiente:

    - Si es posible debe apagar equipos principales antes de ejecutar una migración tras error no planeado. Esto garantiza que no tiene el origen y la réplica máquinas que se ejecutan al mismo tiempo.
    - Al ejecutar una migración tras error planificado detiene la replicación de datos de equipos principales para cualquier delta de datos no se transferirán después de que comience una migración tras error no planeado. Además si ejecuta una migración tras error planificado en un plan de recuperación se ejecutará hasta que haya terminado, incluso si se produce un error.




### <a name="run-a-test-failover"></a>Ejecutar un error de prueba

1. Conmutar una única VM en la **configuración de** > **Replicar elementos**, haga clic en la máquina virtual > **+ migración tras error de prueba**.

    ![Migración tras error de prueba](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Conmutar un plan de recuperación, en la **configuración de** > de**Planes de recuperación**, haga clic en el plan > **Migración tras error de prueba**. Para crear una recuperación plan [siga estas instrucciones](site-recovery-create-recovery-plans.md).
2. En la **Migración tras error de prueba**, seleccione **Ninguno**. Con esta opción es comprobar que la máquina virtual falla sobre según lo esperado, pero la VM replicada no estar conectada a una red.

    ![Migración tras error de prueba](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Haga clic en **Aceptar** para comenzar la migración tras error. Puede realizar el seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo de **Migración tras error de prueba** en la **configuración de** > **trabajos** > **trabajos de recuperación de sitios**.
4. Cuando el trabajo de migración tras error alcanza la fase de **pruebas completado** , haga lo siguiente:

    -  Ver la réplica VM en la nube VMM secundaria.
    -  Haga clic en **completado la prueba** para terminar la migración tras error de prueba.
    -  Haga clic en **notas** para grabar y guardar las observaciones asociadas con la migración tras error de prueba.

5. La máquina virtual de prueba se creará en el mismo host que el host en la que se encuentra la máquina virtual de réplica. Se agrega a la misma nube donde se encuentra la máquina virtual de réplica.
6. Después de comprobar que comiencen máquinas virtuales correctamente haga clic en **la prueba de migración tras error está completa**. En este momento, se eliminan los elementos creados automáticamente por sitio de recuperación durante la migración tras error de prueba.  

    > [AZURE.NOTE] Si un error de prueba continúa más de dos semanas, se completa en vigor.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Actualizar DNS con la dirección IP de VM de réplica

Después de la migración la réplica VM que no tenga la misma dirección IP como la máquina virtual principal.

- Máquinas virtuales se actualizará el servidor DNS que están usando una vez que empiezan.
- Puede actualizar manualmente DNS como sigue:

#### <a name="retrieve-the-ip-address"></a>Recuperar la dirección IP

Ejecute esta secuencia de comandos de ejemplo para recuperar la dirección IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Actualizar DNS

Ejecute esta secuencia de comandos de ejemplo para actualizar DNS, que especifica la dirección IP que ha recuperado mediante el script de ejemplo anterior.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Pasos siguientes

Después de la implementación está configurado y en ejecución, [Obtenga más](site-recovery-failover.md) información sobre los distintos tipos de migraciones tras error.
