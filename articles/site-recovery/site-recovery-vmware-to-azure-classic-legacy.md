<properties
    pageTitle="Replicar máquinas virtuales de VMware y servidores físicos en Azure con la recuperación de sitio de Azure (heredado) | Microsoft Azure" 
    description="Describe cómo replicar VM local y el uso de Windows/Linux servidores físicos en Azure recuperación de sitio de Azure en una implementación heredada en el portal de clásico." 
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
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Replicar máquinas virtuales de VMware y servidores físicos en Azure con la recuperación de sitio de Azure con el portal clásico (heredado)

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmware-to-azure.md)
- [Portal clásica](site-recovery-vmware-to-azure-classic.md)
- [Portal clásica (heredado)](site-recovery-vmware-to-azure-classic-legacy.md)


Bienvenido a Azure sitio recuperación! Este artículo describe una distribución heredada para replicar máquinas virtuales de VMware local o servidores físicos de Windows/Linux en Azure con la recuperación de sitios de Azure en el portal de clásico.

## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia BCDR que determina cómo mantenerse ejecución y está disponible durante el tiempo de inactividad planificado y aplicaciones, cargas de trabajo y datos y recuperación tan pronto como sea posible a las condiciones de trabajo normal. La estrategia BCDR debe mantener datos empresariales seguros y recuperables y garantizar que las cargas de trabajo permanecen continuamente disponibles cuando se produce desastre.

Recuperación de sitio es un servicio de Azure que contribuye a la estrategia BCDR por coordinar la replicación de servidores físicos de local y máquinas virtuales en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en su ubicación primaria, conmutar a la ubicación secundaria para mantener las aplicaciones y cargas de trabajo disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales. Más información en [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md)


>[AZURE.WARNING] Este artículo contiene **instrucciones heredadas**. No se utiliza para implementaciones nuevas. En su lugar, [siga estas instrucciones](site-recovery-vmware-to-azure.md) para implementar la recuperación de sitio en el portal de Azure o [siga estas instrucciones](site-recovery-vmware-to-azure-classic.md) para configurar la implementación mejorada en el portal de clásico. Si ya ha implementado con el método descrito en este artículo, se recomienda migrar a la implementación mejorada en el portal de clásico.


## <a name="migrate-to-the-enhanced-deployment"></a>Migrar a la implementación mejorada

Esta sección solo es relevante si ya ha implementado la recuperación de sitios con las instrucciones de este artículo.

Para migrar su implementación existente, que tendrá que:

1. Implementar los componentes de recuperación de un sitio nuevos en su sitio local.
2. Configurar las credenciales para la detección automática de máquinas virtuales de VMware en el servidor de configuración de nuevo.
3. Descubra los servidores de VMware con el servidor de configuración de nuevo.
3. Crear un nuevo grupo de protección con el servidor de configuración de nuevo.


Antes de empezar:

- Se recomienda que configure una ventana de mantenimiento de migración.
- La opción **Migrar máquinas** solo está disponible si tiene grupos de protección existentes que se crearon durante una implementación heredada.
- Una vez haya completado los pasos de la migración puede tardar 15 minutos o más para actualizar las credenciales y para ver y actualizar máquinas virtuales de modo que puede agregar a un grupo de protección. Puede actualizar manualmente en lugar de esperar. 

Migrar como sigue:

1. Obtenga información sobre [implementación mejorada en el portal de clásico](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Revise los [requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)y mejorada [arquitectura](site-recovery-vmware-to-azure-classic.md#scenario-architecture).
2. Desinstalar el servicio de movilidad en equipos que actualmente está replicar. Una nueva versión del servicio se instalará en los equipos cuando se agregan al nuevo grupo de protección.
3. Descargue una [clave de registro de cámara](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) y [ejecutar al Asistente para instalación unificado](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) para instalar el servidor de configuración, servidor de procesos y componentes de servidor de destino principal. Obtenga más información sobre la [planeación de capacidad](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Configure las credenciales de](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) recuperación del sitio puede usar para tener acceso a VMware server para detectar automáticamente las máquinas virtuales de VMware. Obtenga información sobre los [permisos necesarios](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Agregar [servidores de vCenter o vSphere anfitriones](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Puede tardar 15 minutos para obtener más información para los servidores que aparezcan en el portal de recuperación del sitio.
6. Crear un [nuevo grupo de protección](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Puede tardar hasta 15 minutos para el portal de la actualización de modo que los equipos virtuales se detectan y aparecen. Si no desea esperar puede resaltar el nombre del servidor de administración (no haga clic en él) > **Actualizar**.
7. En el nuevo grupo de protección, haga clic en **Migrar equipos**.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. En **Seleccione equipos** , seleccione el grupo de protección que desea migrar desde y los equipos que desea migrar.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. En **Configuración de destino** , especifique si desea usar la misma configuración para todos los equipos y seleccione el servidor de procesos y la cuenta de almacenamiento de Azure. Si no tiene un servidor de proceso separado será la la dirección IP del servidor de configuración de servidor.


    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] No se admite la [migración de cuentas de almacenamiento](../resource-group-move-resources.md) en grupos de recursos en la misma suscripción o en suscripciones para las cuentas de almacenamiento usadas para implementar el sitio de recuperación.

10. En **Especificar cuentas**, seleccione la cuenta que ha creado para que el servidor de procesos acceder al equipo para insertar la nueva versión del servicio de movilidad.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Recuperación de sitio migrará los datos duplicados en la cuenta de almacenamiento de Azure que ha proporcionado. Opcionalmente, puede volver a la cuenta de almacenamiento que usó en la implementación heredada.
12. Cuando termine el trabajo máquinas virtuales sincronizará automáticamente. Después de que finalice la sincronización puede eliminar las máquinas virtuales del grupo de protección heredados.
13. Después de que han migrado todos los equipos puede eliminar el grupo de protección heredados.
14. Recuerde que debe especificar las propiedades de migración tras error para equipos y la configuración de red Azure una vez completada la sincronización.
15. Si dispone de los planes de recuperación existentes, se puede migrar a la implementación mejorada con la opción **Migrar el Plan de recuperación** . Debe hacer esto solo después de que se han migrado todos los equipos protegidos. 

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Una vez que haya terminado la migración continuar con el [artículo mejorada](site-recovery-vmware-to-azure-classic.md). El resto de este artículo heredado ya no será relevante y que no necesita seguir más de los pasos descritos en TI **.




## <a name="what-do-i-need"></a>¿Qué necesito?

Este diagrama muestra los componentes de implementación.

![Nuevo depósito](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Aquí es lo que necesita:

**Componente** | **Implementación** | **Detalles**
--- | --- | ---
**Servidor de configuración** | Una Azure A3 máquina virtual estándar en la misma suscripción como sitio de recuperación. | El servidor de configuración coordenadas comunicación entre máquinas protegidos, el servidor de procesos y los servidores de destino principal en Azure. Establece la replicación y recuperación de las coordenadas en Azure cuando se produce error.
**Servidor de destino principal** | Una máquina virtual Azure: un servidor de Windows basado en una imagen de la Galería de Windows Server 2012 R2 (para proteger los equipos de Windows) o como un servidor Linux basado en una imagen de la Galería de OpenLogic CentOS 6.6 (para proteger equipos Linux).<br/><br/> Opciones de tamaño tres están disponibles – A4 estándar, D14 estándar y DS4 estándar.<br/><br/> El servidor está conectado a la misma red Azure que el servidor de configuración.<br/><br/> Configurar en el portal de recuperación de sitios | Recibe y conserva los datos duplicados de los equipos protegidos con VHD adjunto creado en el almacenamiento de blobs de Windows en su cuenta de almacenamiento de Azure.<br/><br/> Seleccione estándar DS4 específicamente para la configuración de la protección de cargas de trabajo que requieren coherente de alto rendimiento y baja latencia con la cuenta de almacenamiento Premium.
**Servidor de procesos** | Un servidor virtual o física local ejecuta Windows Server 2012 R2<br/><br/> Se recomienda se coloca en la misma red y el segmento de LAN como los equipos que desea proteger, pero se puede ejecutar en una red diferente como equipos protegidas tienen visibilidad de red nivel 3.<br/><br/> Configurar el y registrar en el servidor de configuración en el portal de recuperación del sitio. | Equipos protegidas envían datos de replicación al servidor local. Tiene un datos replicación de caché a caché basada en disco que recibe. Realiza una serie de acciones en esos datos.<br/><br/> Optimiza datos, almacenamiento en caché, compresión y cifrado antes de enviarlo al servidor de destino principal.<br/><br/> Controladores de instalación de inserción del servicio de movilidad.<br/><br/> Realiza la detección automática de máquinas virtuales de VMware.
**Equipos locales** | Máquinas virtuales de VMware local o físicos servidores ejecutando Windows o Linux. | Configurar la replicación que se aplican a uno o varios equipos. Puede fallar en un equipo individual o con más frecuencia, varios equipos que se recopilan juntos en un plan de recuperación. 
**Servicio de movilidad** | Instalado en cada máquina virtual o el servidor físico que desea proteger<br/><br/> Puede instalar manualmente o inserta e instala automáticamente el servidor de proceso cuando se habilita la replicación de un equipo. | El servicio de movilidad envía datos al servidor de procesos durante la replicación inicial (sincronizar). Después de que el equipo está en un estado protegido (cuando termine de sincronizar) el servicio de movilidad captura la escritura en disco en la memoria y los envía al servidor de procesos. Uniformidadde aplicaciones para servidores de Windows se logra utilizando VSS.
**Azure depósito de recuperación de sitios** | Crear un depósito de recuperación del sitio con una suscripción de Azure y registrar los servidores de la cámara. | El depósito coordenadas y organiza la replicación de datos, migración tras error y recuperación entre su sitio local y Azure.
**Mecanismo de replicación** | **En Internet**, datos de comunicados y en paralelo de servidores local protegida a Azure con seguro de SSL/TLS canal por internet. Esta es la opción predeterminada.<br/><br/> **VPN/ExpressRoute**: comunica y duplica datos entre servidores locales de Azure en una conexión VPN. Debe configurar una VPN de sitio a sitio o una conexión de ExpressRoute entre su sitio local y la red de Azure.<br/><br/> Seleccione cómo desea replicar durante la implementación de recuperación de sitios. No puede cambiar el mecanismo después de configurar sin afectar a la replicación de los equipos existentes. | Ninguna opción requiere que abra los puertos de red entrantes en equipos protegidos. Todas las comunicaciones de red se inician desde el sitio local. 

## <a name="capacity-planning"></a>Planificación de capacidad

Las áreas principales que debe tener en cuenta:

- **Entorno de origen**: VMware la infraestructura, configuración de la máquina de origen y los requisitos.
- **Servidores de componentes**: el servidor de procesos, el servidor de configuración y el servidor de destino principal 

### <a name="considerations-for-the-source-environment"></a>Consideraciones para el entorno de origen

- **Tamaño máximo del disco**: el tamaño máximo actual del disco que puede estar asociado a una máquina virtual es 1 TB. Por lo tanto el tamaño máximo de un disco de origen que se puede replicar también se limita a 1 TB.
- **Tamaño máximo por origen**, el tamaño máximo de una máquina de origen único es 31 TB (con 31 discos) y con una instancia de D14 aprovisionados para el servidor de destino principal. 
- **Número de orígenes por el servidor de destino principal**, se pueden proteger varios equipos de origen con un servidor de destino de maestro único. Sin embargo, una máquina de origen único no protegida entre varios servidores de destino principal, porque como discos replican, se crea un disco duro virtual que refleja el tamaño del disco en el almacenamiento de blobs de Windows Azure y adjunto como un disco de datos en el servidor de destino principal.  
- **Tasa por origen de cambio de máximo diariamente**, hay tres factores que deben tener en cuenta al considerar la tasa de cambio recomendado por origen. Para las consideraciones de destino según dos IOPS son necesarios en el disco de destino para cada operación en el origen. Esto es porque se producen una lectura de datos antiguos y escritura de los nuevos datos en el disco de destino. 
    - **Diaria cambiar tasa admitido por el servidor de procesos**: un equipo de origen no puede abarcar varios servidores de procesos. Un servidor de un solo proceso puede admitir hasta 1 TB de tasa de cambio diaria. Por lo tanto, 1 TB es que el máximos datos diarios cambiarán tasa compatible para una máquina de origen. 
    - **Rendimiento máximo compatible con el disco de destino**: renovación máximo por disco de origen no puede ser más de 144 GB por día (con tamaño de escritura de 8 KB). Vea la tabla en la sección de destino principal para el rendimiento y la IOPs del destino para distintos tamaños de escribir. Este número debe dividirse por dos porque cada origen IOP genera 2 IOPS en el disco de destino. Lea acerca de [los objetivos de rendimiento y escalabilidad Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) al configurar el destino para las cuentas de almacenamiento premium.
    - **Rendimiento máximo compatible con la cuenta de almacenamiento**, un origen no puede abarcar varias cuentas de almacenamiento. Dado que una cuenta de almacenamiento tiene un máximo de 20.000 solicitudes por segundo y que cada origen IOP genera 2 IOPS en el servidor objetivo principal, le recomendamos que mantenga el número de IOPS a través de la fuente a 10.000. Lea acerca de [los objetivos de rendimiento y escalabilidad Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) al configurar el origen de para cuentas de almacenamiento premium.

### <a name="considerations-for-component-servers"></a>Consideraciones para servidores de componentes

Tabla 1 resume los tamaños de máquina virtual para la configuración y los servidores de destino principal.

**Componente** | **Implementar instancias de Azure** | **Núcleos** | **Memoria** | **Discos Max** | **Tamaño del disco**
--- | --- | --- | --- | --- | ---
Servidor de configuración | A3 estándar | 4 | 7 GB | 8 | 1023 GB
Servidor de destino principal | A4 estándar | 8 | 14 GB | 16 | 1023 GB
 | D14 estándar | 16 | 112 GB | 32 | 1023 GB
 | DS4 estándar | 8 | 28 GB | 16 | 1023 GB

**Tabla 1**

#### <a name="process-server-considerations"></a>Consideraciones acerca del servidor de procesos

Generalmente el tamaño del servidor de proceso depende de la tasa de cambio diaria a través de todas las cargas de trabajo protegidas.


- Debe calcular suficiente para realizar tareas como compresión en línea y cifrado.
- El servidor de procesos utiliza la caché basada en disco. Asegúrese de que el espacio de caché recomendado y está disponible para facilitar los cambios de datos almacenados en caso de botella en la red o interrupciones del rendimiento del disco. 
- Asegúrese de que suficiente ancho de banda para que el servidor de proceso puede cargar los datos en el servidor de destino principal para proporcionar protección continua de datos. 

Tabla 2 proporciona un resumen de las directrices de servidor de proceso.

**Tasa de cambio de datos** | **CPU** | **Memoria** | **Tamaño de caché de disco**| **Rendimiento del disco de caché** | **Ancho de banda de entrada/salida**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPUs (2 sockets * 2 núcleos @ 2,5 GHz) | 4 GB | 600 GB | 7 a 10 MB por segundo | 30 Mbps 21 Mbps
300 a 600 GB | 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) | 6 GB | 600 GB | 11 a 15 MB por segundo | 60 Mbps Mbps/42
600 GB hasta 1 TB | 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 8 GB | 600 GB | 16 y 20 MB por segundo | 100 Mbps/70 Mbps
> 1 TB | Implementar otro servidor de proceso | | | | 

**Tabla 2**

Donde: 

- Entrada es el ancho de banda de descarga (intranet entre el servidor de origen y de proceso).
- Salida es el ancho de banda de carga (internet entre el patrón de destino y servidor de proceso). Números de salida suponen promedio compresión de servidor de proceso de 30%.
- Para la caché de disco un disco de sistema operativo independiente de 128 GB mínimo recomendado para todos los servidores de proceso.
- Para el rendimiento de caché de disco el almacenamiento siguiente se utilizó para pruebas: 8 unidades SA de 10 K RPM con configuración RAID 10.

#### <a name="configuration-server-considerations"></a>Consideraciones acerca del servidor de configuración

Cada servidor de configuración admite hasta 100 equipos de origen con volúmenes de 3 y 4. Si la implementación es más grande, se recomienda que implementar otro servidor de configuración. Consulte la tabla 1 para las propiedades de la máquina virtual predeterminado del servidor de configuración. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Consideraciones de cuenta de almacenamiento y servidores de destino principal

El almacenamiento de cada servidor de destino principal incluye un disco de sistema operativo, un volumen de retención y discos de datos. La unidad de retención mantiene el diario de cambios del disco para la duración de la ventana de retención definida en el portal de recuperación del sitio.  Consulte la tabla 1 para las propiedades de la máquina virtual del servidor de destino principal. Tabla 3 muestra cómo se usan los discos de A4.

**Instancia** | **Disco de sistema operativo** | **Retención** | **Discos de datos**
--- | --- | --- | ---
 | | **Retención** | **Discos de datos**
A4 estándar | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (1023 * 15 GB)
D14 estándar |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 31 discos (15 * 1023 GB)
DS4 estándar |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (1023 * 15 GB)

**Tabla 3**

Planificación la capacidad para el servidor de destino principal depende de:

- Limitaciones y el rendimiento de almacenamiento de azure
    - El número máximo de altamente utiliza discos VM nivel estándar, es de aproximadamente 40 (20.000/500 IOPS por disco) en una cuenta de almacenamiento única. Lea acerca de [los objetivos de escalabilidad para sccounts de almacenamiento estándar](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) y [premium almacenamiento sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Tasa de cambio diaria 
-   Almacenamiento de volumen de retención.

Tenga en cuenta que:

- Un origen no puede abarcar varias cuentas de almacenamiento. Esto se aplica en el disco de datos que vaya a las cuentas de almacenamiento seleccionadas al configurar la protección. El sistema operativo y los discos de retención normalmente vaya a la cuenta de almacenamiento implementada automáticamente.
- El volumen de almacenamiento de retención requerido depende de la tasa de cambio diaria y el número de días de retención. El almacenamiento de retención requerido por el servidor de destino patrón = renovación total de origen al día * número de días de retención. 
- Cada servidor de destino principal tiene un único volumen de retención. El volumen de retención se comparte entre los discos conectados al servidor de destino principal. Por ejemplo:
    - Si hay un equipo de origen con 5 discos y cada disco genera 120 IOPS (tamaño de 8 KB) en el origen, se traduce 240 IOPS por disco (2 operaciones en el disco de destino por origen IO). 240 IOPS está dentro del Azure por límite de 500 IOPS en disco.
    - En el volumen de retención, se convierte en 120 * 5 = 600 IOPS y esto pueden convertirse en un cuello de botella. En este escenario, una buena estrategia sería agregar más discos al volumen de retención y abarcar a través, como una configuración de bandas RAID. Esto mejorará el rendimiento porque la IOPS se distribuye entre varias unidades. El número de unidades que se agregarán al volumen de retención será como sigue:
        - Total de IOPS desde el entorno de origen / 500
        - Renovación total por día de entorno de origen (sin comprimir) / 287 GB. GB 287 es el rendimiento máximo compatible con un disco de destino al día. Esta métrica varían según el tamaño de escritura con un factor de 8K, porque en este caso, 8K es tres supone el tamaño de escritura. Por ejemplo, si el tamaño de escritura es 4K rendimiento será 287/2. Y si el tamaño de escritura es 16K rendimiento se 287 * 2.
- El número de cuentas de almacenamiento necesarias = origen total IOPs/10000.


## <a name="before-you-start"></a>Antes de empezar

**Componente** | **Requisitos** | **Detalles**
--- | --- | --- 
**Cuenta de Azure** | Tendrá una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Almacenamiento de Azure** | Necesitará una cuenta de almacenamiento de Azure para almacenar datos duplicados<br/><br/> La cuenta debe ser una [Cuenta de almacenamiento Geo redundantes estándar](../storage/storage-redundancy.md#geo-redundant-storage) o [Cuenta de almacenamiento Premium](../storage/storage-premium-storage.md).<br/><br/> Debe en la misma región como el servicio de recuperación de sitios de Azure y estar asociadas a la misma suscripción. No se admite el movimiento de las cuentas de almacenamiento creados con el [nuevo portal de Azure](../storage/storage-create-storage-account.md) a través de grupos de recursos.<br/><br/> Para obtener más información, lea [Introducción a Microsoft Azure almacenamiento](../storage/storage-introduction.md)
**Red virtual Azure** | Necesitará una red virtual Azure en la que se implementará el servidor de configuración y objetivo principal. Debe ser en la misma suscripción y región como depósito de recuperación de sitios de Azure. Si desea replicar datos a través de una conexión VPN o ExpressRoute la red virtual Azure debe estar conectada a su red local a través de una conexión de ExpressRoute o una VPN de sitio a otro.
**Recursos de Azure** | Asegúrese de que tiene suficiente recursos para implementar todos los componentes de Azure. Lea más en [Los límites de suscripción de Azure](../azure-subscription-service-limits.md).
**Máquinas virtuales de Windows Azure** | Máquinas virtuales de Windows que desea proteger deben cumplir con los [requisitos previos de Azure](site-recovery-best-practices.md).<br/><br/> **Recuento de disco**: un máximo de 31 discos pueden incluir en un único servidor protegido<br/><br/> **Tamaños de disco**: capacidad de disco Individual no debería ser más de 1023 GB<br/><br/> **Clúster**: no se admiten servidores agrupada<br/><br/> **Inicio**: Unified Extensible Firmware interfaz (UEFI) / inicio Extensible Interface(EFI) Firmware no es compatible<br/><br/> **Volúmenes**: Bitlocker volúmenes encriptados no compatibles<br/><br/> **Los nombres de servidor**, nombres deben contener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y termine con una letra o un número. Después de protege un equipo puede modificar el nombre de Azure.
**Servidor de configuración** | Estándar máquina virtual de A3 basada en una imagen de la Galería de Azure sitio recuperación Windows Server 2012 R2 se creará en la suscripción para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio de nube. Si selecciona Internet pública como el tipo de conectividad del servidor de configuración del servicio de nube se creará con una dirección IP pública reservada.<br/><br/> La ruta de instalación debe estar en inglés caracteres solo.
**Servidor de destino principal** | Azure máquina virtual estándar A4, D14 o DS4.<br/><br/> La ruta de instalación debe estar en inglés caracteres solo. Por ejemplo, la ruta de acceso debería **/usr/local/ASR** para un servidor de destino principal con Linux.
**Servidor de procesos** | Puede implementar el servidor de procesos en físico o máquina virtual que ejecuta Windows Server 2012 R2 con las últimas actualizaciones. Instalar en C: /.<br/><br/> Se recomienda que colocar el servidor en la misma red y subred que los equipos que desea proteger.<br/><br/> Instalar VMware vSphere CLI 5.5.0 en el servidor de procesos. El componente de VMware vSphere CLI es necesaria en el servidor para descubrir máquinas virtuales administradas por un servidor de vCenter o máquinas virtuales se ejecutan en un host ESXi.<br/><br/> La ruta de instalación debe estar en inglés caracteres solo.<br/><br/> No se admite el sistema de archivos de referencias cruzadas.
**VMware** | Servidor VMware vCenter administrar su hipervisores de VMware vSphere. Debe estar ejecutándose vCenter versión 5.1 o 5.5 con las últimas actualizaciones.<br/><br/> Uno o varios hipervisores vSphere que contienen máquinas virtuales VMware que desee proteger. Debe ejecutar el hipervisor ESX/ESXi versión 5.1 o 5.5 con las últimas actualizaciones.<br/><br/> Máquinas virtuales de VMware debe tener instalado y ejecuta las herramientas de VMware. 
**Equipos con Windows** | Protegido servidores físicos o máquinas virtuales de VMware con Windows tienen un número de requisitos.<br/><br/> Compatibles de un sistema operativo de 64 bits: **Windows Server 2012 R2**, **Windows Server 2012**, o **Windows Server 2008 R2 con al menos SP1**.<br/><br/> El nombre de host, puntos de montaje, nombres de dispositivo, ruta del sistema de Windows (por ejemplo: C:\Windows) debe ser sólo en inglés.<br/><br/> El sistema operativo debe instalarse en unidad C:\.<br/><br/> Sólo los discos básicos son compatibles. No se admiten discos dinámicos.<br/><br/> Las reglas de Firewall en equipos protegidos deben que puedan llegar a los servidores de destino de configuración y patrón en Azure.p ><p>Deberá proporcionar una cuenta de administrador (debe ser un administrador local en el equipo de Windows) para instalar el servicio de movilidad en servidores de Windows de inserción. Si la cuenta proporcionada es una cuenta de dominio no debe deshabilitar el control de acceso remoto al usuario en el equipo local. Para ello, agregue la entrada de registro LocalAccountTokenFilterPolicy DWORD con un valor de 1 en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para agregar la entrada del registro de una CLI abrir cmd o powershell y escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Obtenga más](https://msdn.microsoft.com/library/aa826699.aspx) información acerca de control de acceso.<br/><br/> Después de la migración, si desea conectarse a máquinas virtuales de Windows en Azure con Escritorio remoto Asegúrese de que Escritorio remoto está habilitado para el equipo local. Si no va a conectarse a través de VPN, las reglas de firewall deben permitir las conexiones de escritorio remoto por internet.
**Equipos Linux** | Un sistema operativo de 64 bits compatibles: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 ejecutando rojos sombrero compatible kernel o Unbreakable Enterprise Kernel versión 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Las reglas de Firewall en equipos protegidos deben que puedan llegar a los servidores de destino principal en Azure y configuración.<br/><br/> archivos / etc/hosts en equipos protegidos deben contener entradas que asignan el nombre de host local a direcciones IP asociadas con todas las NIC <br/><br/> Si desea conectarse a una máquina virtual Azure ejecutan Linux después de la migración con un Secure Shell cliente (ssh), asegúrese de que el servicio de Shell seguro en el equipo protegido está establecido para iniciar automáticamente en el inicio del sistema, y que las reglas de firewall permiten una ssh conexión a ella.<br/><br/> El nombre de host, puntos de montaje, nombres de dispositivo y rutas de acceso de sistema de Linux y nombres de archivo (por ejemplo, / etcetera /, / usr) solo deben estar en inglés.<br/><br/> Se puede habilitar protección para equipos locales con el almacenamiento siguiente:-<br>Sistema de archivos: EXT3, ETX4, ReiserFS, XFS<br>Dispositivo de software de múltiples rutas asignador (múltiples rutas)<br>Administrador de volumen: LVM2<br>Servidores físicos con almacenamiento del controlador HP CCISS no son compatibles.
**Aplicaciones de terceros** | Algunos componentes de implementación en este escenario dependen de software de terceros para que funcione correctamente. Para obtener una lista completa, vea [información y avisos de software de terceros](#third-party)


### <a name="network-connectivity"></a>Conectividad de red

Tiene dos opciones al configurar la conectividad de red entre el sitio local y la red virtual Azure en la que se implementan los componentes de infraestructura (servidor de configuración, servidores de destino principal). Debe decidir qué opción de conectividad de red para usar antes de implementar el servidor de configuración. Debe elegir esta opción en el momento de la implementación. No se puede cambiar más adelante.

**Internet:** Comunicaciones y replicación de datos entre los servidores locales (servidor de procesos, equipos protegidas) y los servidores de componentes de infraestructura de Azure (servidor de configuración, el servidor de destino principal) pasa mediante una conexión segura de SSL/TLS locales a los extremos del públicos en los servidores de destino de configuración y patrón. (La única excepción es la conexión entre el servidor y el servidor de destino principal en el puerto TCP 9080 que está cifrado. Solo la información del control relacionada con el protocolo de replicación de configuración de la replicación se intercambiado en esta conexión.)

![Diagrama de implementación de internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: pasa comunicación y replicación de datos entre los servidores locales (servidor de procesos, equipos protegidas) y los servidores de componentes de infraestructura de Azure (servidor de configuración, el servidor de destino principal) en una conexión VPN entre su red local y la red virtual Azure en la que se implementan los servidores de destino principal y el servidor de configuración. Asegúrese de que su red local está conectada a la red virtual Azure ExpressRoute conexión o una conexión VPN de sitio a otro.

![Diagrama de implementación VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Paso 1: Crear un depósito

1. Inicie sesión en el [Portal de administración](https://portal.azure.com).


2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Depósito de recuperación del sitio**.


3. Haga clic en **Crear nuevo** > **crear rápido**.

4. En **nombre**, escriba un nombre descriptivo para identificar la cámara.

5. En la **región**, seleccione la región geográfica de la cámara. Para comprobar regiones admitidas vea disponibilidad geográfica en [Detalles de precios de Azure sitio recuperación](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Haga clic en **Crear depósito**.

    ![Nuevo depósito](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Compruebe la barra de estado para confirmar que la cámara se creó correctamente. La cámara se mostrarán como **activo** en la página principal de **Servicios de recuperación** .

## <a name="step-2-deploy-a-configuration-server"></a>Paso 2: Implementar un servidor de configuración

### <a name="configure-server-settings"></a>Configuración del servidor

1. En la página **Servicios de recuperación** , haga clic en la cámara para abrir la página de inicio rápido. También se pueden abrir en cualquier momento mediante el icono de inicio rápido.

    ![Icono de inicio rápido](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. En la lista desplegable, seleccione **entre un sitio local con servidores VMware o físico y Azure**.
3. Haga clic en **Implementar el servidor de configuración**de **Preparar Target(Azure) recursos** .

    ![Implementación del servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. En la **Nueva detalles de configuración de servidor** , especifique:

    - Un nombre para el servidor de configuración y las credenciales para conectarse a él.
    - En el tipo de conectividad de red desplegable Seleccionar **VPN**o **Internet público** . Tenga en cuenta que no se puede modificar esta configuración después de que se aplica.
    - Seleccione la red de Azure en la que puede encontrar el servidor. Si usa hacer VPN sabe la red Azure está conectada a su red local según lo esperado. 
    - Especificar la dirección IP interna y subred que se asignarán al servidor. Tenga en cuenta que las primeras cuatro direcciones IP de cualquier subred están reservadas para uso interno de Azure. Usar cualquier dirección IP disponible.
    
    ![Implementación del servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Al hacer clic en **Aceptar** una máquina virtual de A3 estándar en función de una imagen de la Galería de Azure sitio recuperación Windows Server 2012 R2 se creará en la suscripción para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio de nube. Si ha seleccionado para conectarse a través de internet del servicio de nube se crea con una dirección IP pública reservada. Puede supervisar el progreso en la ficha **trabajos** .

    ![Supervisar el progreso](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Si se está conectando a través de internet, el servidor de configuración Nota implementado la dirección IP pública asignado en la página de **máquinas virtuales** en el portal de Azure. A continuación, en los **extremos** Nota el puerto HTTPS público de pestaña asignado al puerto 443 de forma privado. Tendrá más tarde esta información cuando se registran el destino principal y los servidores de proceso con el servidor de configuración. El servidor de configuración se implementa con estos extremos:

    - HTTPS: Un puerto público se usa para coordinar la comunicación entre servidores de componentes y Azure a través de internet. Privado puerto 443 se usa para coordinar la comunicación entre servidores de componentes y Azure sobre VPN.
    - Personalizado: Un puerto público se usa para la comunicación de la herramienta de recuperación a través de internet. Puerto privado 9443 se utiliza para la comunicación de la herramienta de recuperación sobre VPN.
    - PowerShell: Puerto privado 5986
    - Escritorio remoto: el puerto 3389 privado
    
    ![Extremos VM](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] No elimine o cambie el número de puerto público o privado de los extremos creados durante la implementación del servidor de configuración.

El servidor de configuración se implementa en un servicio de nube de Azure creado automáticamente con una dirección IP reservada. La dirección reservada es necesaria para asegurarse de que la dirección IP de servicio de nube de configuración servidor sigue siendo la misma a través de reinicio de la máquinas virtuales de Windows (incluido el servidor de configuración) en el servicio de nube. La dirección IP pública reservada se debe estar reservada manualmente cuando el servidor de configuración se retira o permanecerá reservado. Hay un límite predeterminado de 20 público las direcciones IP reservadas por suscripción. [Obtenga más](../virtual-network/virtual-networks-reserved-private-ip.md) información acerca de las direcciones IP reservadas. 

### <a name="register-the-configuration-server-in-the-vault"></a>Registrar el servidor de configuración de la cámara

1. En la página de **Inicio rápido** , haga clic en **Recursos de destino preparar** > **descargar una clave de registro**. El archivo de clave se genera automáticamente. Es válido durante 5 días después de que se genera. Copiar en el servidor de configuración.
2. En **máquinas virtuales** , seleccione el servidor de configuración de la lista de máquinas virtuales de Windows. Abra la pestaña **panel** y haga clic en **Conectar**. **Abrir** el archivo descargado de RDP a iniciar sesión en el servidor de configuración de escritorio remoto. Si usa VPN, use la dirección IP interna (es decir, la dirección especificada cuando se implementa el servidor de configuración) para una conexión a Escritorio remoto desde el sitio local. El Asistente de configuración del servidor de configuración de Azure sitio recuperación se ejecuta automáticamente cuando inicie sesión por primera vez.

    ![Registro](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. **Instalación de Software de terceros** haga clic en **** para descargar e instalar MySQL.

    ![Instalar MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. En **Detalles del servidor MySQL** crear credenciales para iniciar sesión en la instancia del servidor MySQL.

    ![Credenciales de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. En la **Configuración de Internet** , especifique cómo el servidor de configuración se conecte a internet. Tenga en cuenta que:

    - Si desea usar a un proxy personalizado debe configurarlo antes de instalar el proveedor de servicios.
    - Al hacer clic en **siguiente** se ejecuta una prueba para comprobar la conexión de proxy.
    - Si utiliza a un proxy personalizado o su proxy predeterminado requiere autenticación que deberá especificar los detalles de proxy, incluida la dirección, el puerto y credenciales.
    - Las siguientes direcciones URL deben ser accesible a través del proxy:
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Si tiene basada en la dirección IP reglas de firewall asegurarse de que las reglas para permitir la comunicación desde el servidor de configuración para las direcciones IP que se describe en [Intervalos de IP del centro de datos de Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) y HTTPS protocolo (443). Tendrá que los intervalos de IP de la lista de notas de la región de Azure que planea usar y de Estados Unidos oeste.

    ![Registro de proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. En **Configuración de localización de mensaje de Error de proveedor** especifique en el idioma que desee que aparezcan los mensajes de error.

    ![Registro de mensaje de error](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. En **El registro de recuperación del sitio de Azure** examinar y seleccione el archivo de clave que ha copiado en el servidor.

    ![Registro de archivo de clave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. En la página de finalización del Asistente para seleccionar estas opciones:

    - Seleccione el **Cuadro de diálogo de administración de cuenta de inicio** para especificar que debe abrir el cuadro de diálogo Administrar cuentas después de que finalice al asistente.
    - Seleccione **crear un icono de escritorio para Cspsconfigtool** para agregar un acceso directo de escritorio en el servidor de configuración para que pueda abrir el cuadro de diálogo **Administrar cuentas** en cualquier momento sin necesidad de volver a ejecutar al asistente.

    ![Completar el registro](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Haga clic en **Finalizar** para completar al asistente. Se genera una frase de contraseña. Cópiela en una ubicación segura. Tendrá que autenticar y registrar el proceso y los servidores de destino principal con el servidor de configuración. También se utiliza para garantizar la integridad de canal de comunicaciones del servidor de configuración. Puede volver a generar la frase de contraseña pero, a continuación, tendrá que volver a registrar el patrón de destino y servidores con la nueva contraseña de procesos.

    ![Frase de contraseña](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Después de registrar el servidor de configuración se mostrará en la página de **Los servidores de configuración** de la cámara.

### <a name="set-up-and-manage-accounts"></a>Configurar y administrar cuentas

Durante la implementación de recuperación de sitio solicita las credenciales para las siguientes acciones:

- Una cuenta para thatSite recuperación automáticamente pueda máquinas virtuales de detección en servidores de vCenter o vSphere hosts de VMware. 
- Al agregar equipos de protección, para que el sitio de recuperación puede instalar el servicio de movilidad en ellas.

Una vez que haya registrado el servidor de configuración puede abrir el cuadro de diálogo **Administrar cuentas** para agregar y administrar las cuentas que deben usarse para estas acciones. Hay un par de maneras de hacerlo:

- Abra el acceso directo que elegido creado para el cuadro de diálogo en la última página de la configuración del servidor de configuración (cspsconfigtool).
- Abrir el cuadro de diálogo de finalización de la configuración del servidor de configuración.

1. En **Administrar cuentas** , haga clic en **Agregar cuenta**. También puede modificar y eliminar las cuentas existentes.

    ![Administrar cuentas](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. En **Detalles de la cuenta** , especifique un nombre de cuenta para usar en Azure y credenciales (nombre de usuario del dominio). 

    ![Administrar cuentas](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Conectar con el servidor de configuración 

Hay dos formas de conectarse al servidor de configuración:

- Mediante una conexión de ExpressRoute o VPN de sitio a sitio
- En internet 

Tenga en cuenta que:

- Una conexión a internet utiliza los extremos de la máquina virtual junto con la dirección IP virtual pública del servidor.
- Una conexión VPN utiliza la dirección IP interna del servidor junto con los puertos privada de extremo.
- Es una sola vez decisión para decidir si desea conectar (control y la replicación de datos) de los servidores locales a los distintos servidores de componente (servidor de configuración, el servidor de destino principal) ejecuta en Azure a través de internet o de una conexión VPN. No puede cambiar esta configuración posteriormente. Si no hace deberá volver a implementar el escenario y proteger sus equipos.  


## <a name="step-3-deploy-the-master-target-server"></a>Paso 3: Implementar el servidor de destino principal

1. Haga clic en **Recursos de Target(Azure) preparar** > **Implementar servidor de patrón de destino**.
2. Especifique los detalles del patrón de destino del servidor y las credenciales. El servidor se implementará en la misma red Azure que el servidor de configuración. Al hacer clic en para completar una máquina virtual Azure se creará con una imagen de galería de Windows o Linux.

    ![Configuración del servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Tenga en cuenta que las primeras cuatro direcciones IP de cualquier subred están reservadas para uso interno de Azure. Especificar cualquier dirección IP disponible.

>[AZURE.NOTE] Seleccione DS4 estándar cuando la configuración de la protección de cargas de trabajo que requieren alto performance consistente de i/OS y latencia baja para hospedar i/OS intensivas cargas de trabajo con la [Cuenta de almacenamiento Premium](../storage/storage-premium-storage.md).


3. Windows maestra VM se crea con estos extremos de servidor de destino. Observe que se crean los extremos públicos solo si su conectarse a través de internet.

    - Personalizado: Puerto público usado el servidor de procesos para enviar datos de replicación a través de internet. Puerto privado 9443 se utiliza en el servidor de procesos para enviar datos de replicación al servidor de destino principal a VPN.
    - Custom1: Puerto público que utiliza el servidor de procesos para enviar metadatos a través de internet. Puerto privado 9080 se utiliza en el servidor de procesos para enviar metadatos al servidor de destino principal a través de la VPN.
    - PowerShell: Puerto privado 5986
    - Escritorio remoto: el puerto 3389 privado

4. Se crea un servidor de destino maestra Linux VM con estos extremos. Tenga en cuenta que se crean los extremos públicos solo si se está conectando a través de internet.

    - Personalizado: Puerto público usado servidor proceso para enviar datos de replicación a través de internet. Puerto privado 9443 se utiliza en el servidor de procesos para enviar datos de replicación al servidor de destino principal a VPN.
    - Custom1: Puerto público se utiliza en el servidor de procesos para enviar metadatos a través de internet. Puerto privado 9080 se utiliza en el servidor de procesos para enviar metadatos al servidor de destino principal a través de la VPN
    - SSH: Puerto privado 22

    >[AZURE.WARNING] No elimine o cambie el número de puerto público o privado de cualquiera de los extremos creados durante la implementación de servidor de destino principal.

5. En espera de **máquinas virtuales** de la máquina virtual iniciar.

    - Si se trata de una nota de Windows server abajo los detalles de escritorio remotos.
    - Si es un servidor Linux y se está conectando a través de VPN anote la dirección IP interna de la máquina virtual. Si se está conectando a través de internet, anote la dirección IP pública.

6.  Inicie sesión en el servidor para completar la instalación y registrar con el servidor de configuración. 
7.  Si está ejecutando Windows:

    1. Iniciar una conexión a Escritorio remoto en la máquina virtual. La primera vez que inicie sesión en una secuencia de comandos se ejecutará en una ventana de PowerShell. No ciérrelo. Cuando finaliza la herramienta de configuración de agente de Host se abre automáticamente para registrar el servidor.
    2. En la **Configuración de agente de Host** , especifique la dirección IP interna del servidor de configuración y el puerto 443. Puede usar la dirección interna y privado puerto 443, incluso si no se está conectando a través de VPN porque la máquina virtual está conectada a la misma red Azure que el servidor de configuración. Dejar de **Usar HTTPS** habilitado. Escriba la frase de contraseña para el servidor de configuración que anotó anteriormente. Haga clic en **Aceptar** para registrar el servidor. Puede pasar por alto las opciones de NAT. No se usan.
    3. Si el requisito de unidad de retención estimado es más de 1 TB puede configurar el volumen de retención (R:) utilizando un disco virtual y los [espacios de almacenamiento](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Servidor de destino principal de Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Si está ejecutando Linux:
    1. Asegúrese de que ha instalado los servicios de integración de Linux más recientes (vertical) antes de instalar al servidor de destino principal. Puede encontrar la última versión de vertical junto con instrucciones sobre cómo instalar [aquí](https://www.microsoft.com/download/details.aspx?id=46842). Reinicie el equipo después de instala la vertical.
    2. **Recursos de destino preparar (Azure)** haga clic en **descargar e instalar software adicional (solo para el servidor de destino del patrón de Linux)**. Copie el archivo descargado tar a la máquina virtual usando a un cliente de sftp. También puede iniciar sesión en el servidor de destino maestra linux implementada y utilice *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* para descargar el archivo.
    2. Inicie sesión en el servidor usando a un cliente de Secure Shell. Si está conectado a la red de Azure sobre VPN usar la dirección IP interna. En caso contrario, use la dirección IP externa y el extremo del público SSH.
    3. Extraer los archivos del instalador comprimido con gzip ejecutando: **alquitrán – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![servidor de destino maestra Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Asegúrese de que está en el directorio a la que extrae el contenido del archivo .tar.
    5. Copiar la frase de contraseña del servidor de configuración en un archivo local con el comando * *eco* `<passphrase>` * > passphrase.txt**
    6. Ejecute el comando "**sudo. / instalar -t ambos - a host -R MasterTarget -d /usr/local/ASR -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt**".

    ![Registrar el servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Espere unos minutos (10-15) y en la página, compruebe que el servidor de destino principal aparece como registrado en **servidores** > pestaña de **Detalles del servidor** de**Configuración de servidores** . Si está ejecutando Linux y no se registró ejecutar el host de herramienta de configuración del nuevo desde /usr/local/ASR/Vx/bin/hostconfigcli. Debe establecer permisos de acceso al ejecutar chmod como raíz.

    ![Compruebe el servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Puede tardar hasta 15 minutos después de completar para el servidor de destino principal que se mostrarán en el portal de registro. Para actualizar inmediatamente, haga clic en **Actualizar** en la página **Configuración de servidores** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Paso 4: Implementar el servidor de procesos en local

Antes de empezar, se recomienda configurar una dirección IP estática en el servidor para que lo ha garantiza persistente en reiniciar el equipo.

1. Haga clic en Inicio rápido > **instalar proceso servidor local** > **descargar e instalar el servidor de procesos**.

    ![Instalar el servidor de procesos](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copie el archivo zip descargado en el servidor en el que va a instalar al servidor de procesos. El archivo zip contiene dos archivos de instalación:

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Descomprima el archivo y copiar los archivos de instalación a una ubicación en el servidor.
4. Ejecutar la **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** instalación de archivos y siga las instrucciones. Esto instala necesarios para la implementación de componentes de terceros.
5. Vuelva a ejecutar **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. En la página de **Modo de servidor** , seleccione **Servidor de procesos**.
7. En la página de **Detalles del entorno** haga lo siguiente:


    - Si desea proteger VMware virtual de equipos, haga clic en **Sí**
    - Si solo desea proteger servidores físicos y, por tanto, no es necesario vCLI VMware instalado en el servidor de procesos. Haga clic en **No** y continuar.

8. Al instalar VMware vCLI, tenga en cuenta lo siguiente:

    - **Sólo VMware vSphere CLI 5.5.0 es compatible**. El servidor de procesos no funciona con otras versiones o actualizaciones de vSphere CLI.
    - Descargar vSphere CLI 5.5.0 desde [aquí.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Si instaló vSphere CLI justo antes de empezar a instalar al servidor de procesos y, a continuación, el programa de instalación no lo detecta, espere hasta cinco minutos antes de intentar el programa de instalación de nuevo. Esto asegura que todas las variables de entorno es necesario para la detección de CLI vSphere ha sido inicializadas correctamente.

9.  En **Selección de NIC para el servidor de procesos** , seleccione el adaptador de red que debe utilizar el servidor de procesos.

    ![Seleccione adaptador](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. En **Detalles de configuración del servidor**:

    - Para la dirección IP y puerto, si se está conectando a través de VPN, especifique la dirección IP interna del servidor de configuración y 443 para el puerto. En caso contrario, especifique la dirección IP virtual pública y asignada extremo HTTP público.
    - Escriba la contraseña de su servidor de configuración.
    - Desactive la **firma de software de servicio de movilidad de comprobar** si desea deshabilitar la comprobación cuando utiliza la inserción automática para instalar el servicio. Comprobación de la firma tiene conectividad a internet en el servidor de procesos.
    - Haga clic en **siguiente**.

    ![Servidor de configuración de registro](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. En **la unidad de instalación seleccione** seleccione una unidad de caché. El servidor de proceso necesita una unidad de caché con al menos 600 GB de espacio libre. A continuación, haga clic en **instalar**. 

    ![Servidor de configuración de registro](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Tenga en cuenta que necesite reiniciar el servidor para completar la instalación. En el **Servidor de configuración de** > **Detalles del servidor para** comprobar que el servidor de procesos aparece y está registrado correctamente en la cámara.

>[AZURE.NOTE]Puede tardar hasta 15 minutos después de completar para el servidor de proceso aparezca tal y como aparece en el servidor de configuración de registro. Para actualizar inmediatamente, actualizar al servidor de configuración haciendo clic en el botón Actualizar en la parte inferior de la página Configuración del servidor
 
![Validar servidor de proceso](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Si no ha deshabilitar la comprobación de firma para el servicio de movilidad cuando registra el servidor de proceso, puede hacerlo más adelante como sigue:

1. Inicie sesión en el servidor de procesos como administrador y abra el archivo C:\pushinstallsvc\pushinstaller.conf para su edición. En la sección **[PushInstaller.transport]** , agregue esta línea: **SignatureVerificationChecks = "0"**. Guarde y cierre el archivo.
2. Reinicie el servicio de InMage PushInstall.


## <a name="step-5-update-site-recovery-components"></a>Paso 5: Componentes de recuperación de sitios de actualización

Componentes de recuperación de sitio se actualizan de vez en cuando. Cuando haya nuevas actualizaciones disponibles debe instalarlos en el siguiente orden:

1. Servidor de configuración
2. Servidor de procesos
3. Servidor de destino principal
4. Herramienta de recuperación (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Obtener e instalar las actualizaciones


1. Puede obtener actualizaciones para la configuración, los procesos y los servidores de destino principal del **panel**de recuperación de sitio. Para la instalación Linux extraer los archivos del instalador comprimido con gzip y ejecute el comando "sudo. / instalar" para instalar la actualización.
2. [Descargar](http://go.microsoft.com/fwlink/?LinkID=533813) la última actualización de la tool(vContinuum) de recuperación.
3. Si está ejecutando máquinas virtuales o servidores físicos que ya tienen instalado el servicio de movilidad, puede obtener actualizaciones del servicio como sigue:

    - **Opción 1**: descargar actualizaciones:
        - [Windows Server (64 bits sólo)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [6.4,6.5,6.6 centOS (solo 64 bits)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (solo 64 bits)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (solo 64 bits)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Después de actualizar el servidor de procesos la versión actualizada del servicio de movilidad estarán disponible en la carpeta C:\pushinstallsvc\repository en el servidor.
    - **Opción 2**: si tiene un equipo con una versión anterior del servicio de movilidad instalado, puede actualizar automáticamente el servicio de movilidad en el equipo desde el portal de administración.

        1. Asegúrese de que se actualiza el servidor de procesos.
        2. Asegúrese de que el equipo protegido cumple con los [requisitos previos](#install-the-mobility-service-automatically) para insertar automáticamente el servicio de movilidad para que la actualización funciona según lo esperado.
        2. Seleccione el grupo de protección, resalte el equipo protegido y haga clic en **servicio de movilidad de actualización**. Este botón solo está disponible si hay una versión más reciente del servicio de movilidad. 

            ![Seleccione servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

En Seleccione las cuentas especifique la cuenta de administrador que se utilizará para actualizar el servicio de movilidad en el servidor protegido. Haga clic en Aceptar y espere a completar el trabajo desencadenado.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Paso 6: Agregar servidores de vCenter o vSphere hosts

1. Haga clic en **servidores** > **Servidores de configuración** > Configuración servidor >**Agregar vCenter Server** para agregar un host de servidor o vSphere vCenter.

    ![Seleccione servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Especificar los detalles para el servidor o host y seleccione el servidor de procesos que se usará para descubrirlo.

    - Si el servidor de vCenter no se ejecuta en el puerto 443 de forma predeterminada, especifique el número de puerto en el que se está ejecutando el servidor vCenter.
    - El servidor de procesos debe estar en la misma red que el host de servidor/vSphere vCenter y debería tener VMware vSphere CLI 5.5.0 instalado.

    ![configuración del servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Cuando termine de detección el servidor vCenter aparecerán en los detalles del servidor de configuración.

    ![configuración del servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Si usa una cuenta de administrador para agregar el servidor o host, asegúrese de que la cuenta tiene los privilegios siguientes:

    - cuentas de vCenter deben tener centro de datos, almacén de datos, carpeta, Host, red, recursos, almacenamiento vistas, Máquina Virtual y privilegios del conmutador de distribuido vSphere habilitados.
    - cuentas de host de vSphere deben tener el centro de datos, almacén de datos, carpeta, Host, red, recursos, Máquina Virtual y privilegios del conmutador de distribuido vSphere habilitados



## <a name="step-7-create-a-protection-group"></a>Paso 7: Crear un grupo de protección

1. Abrir **Elementos protegidos** > **Grupo protección** > **Crear grupo de protección**.

    ![Crear grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. En la página **Especificar la configuración de protección de grupo** , especifique un nombre para el grupo y seleccione el servidor de configuración en la que desea crear el grupo.

    ![Configuración de protección de grupo](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. En la página **Especificar la configuración de replicación de** configurar las opciones de replicación que se usará para todos los equipos en el grupo.

    ![Replicación de grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Configuración:
    - **Coherencia entre varios VM**: si activar esta función crea puntos de recuperación consistente con las aplicaciones compartidas en el grupo de protección los equipos. Esta opción es más relevante cuando todos los equipos en el grupo de protección están ejecutando la misma carga de trabajo. En el mismo punto de datos se recuperarán todos los equipos. Sólo está disponible para los servidores de Windows.
    - **Umbral RPO**: se generarán alertas cuando la replicación de protección de datos continua RPO supera el valor de umbral RPO configurado.
    - **Retención de punto de recuperación**: especifica la ventana de retención. Equipos protegidas se pueden recuperar a cualquier punto dentro de esta ventana.
    - **Frecuencia de instantáneas coherentes de aplicación**: especifica la frecuencia con la que se creará puntos de recuperación que contiene Dr.

Puede supervisar el grupo de protección como se crean en la página de **Elementos protegidos** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Paso 8: Configurar los equipos que desee proteger

Debe instalar el servicio de movilidad en máquinas virtuales y servidores físicos que desea proteger. Puede hacerlo de dos maneras:

- Push e instale automáticamente el servicio de cada equipo desde el servidor de procesos.
- Instalar manualmente el servicio. 

### <a name="install-the-mobility-service-automatically"></a>Instalar el servicio de movilidad automáticamente

Al agregar equipos a un grupo de protección es inserta automáticamente el servicio de movilidad y se instalado en cada equipo por el servidor de procesos. 

**Inserción instalar automáticamente el servicio de movilidad en servidores de Windows:** 

1. Instale las últimas actualizaciones para el servidor de proceso, como se describe en [paso 5: instalar las últimas actualizaciones de](#step-5-install-latest-updates)y asegúrese de que el servidor de procesos está disponible. 
2. Asegúrese de que hay conectividad de red entre el equipo de origen y el servidor de procesos y que el equipo de origen es accesible desde el servidor de procesos.  
3. Configurar el firewall de Windows para permitir el **uso compartido de impresoras y archivos** e **Instrumentación de administración de Windows**. En configuración de Firewall de Windows, seleccione la opción "Permitir una aplicación o una característica a través del Firewall" y seleccione las aplicaciones tal como se muestra en la siguiente imagen. Puede configurar la directiva de firewall con un GPO de equipos que pertenecen a un dominio.

    ![Configuración del Firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. La cuenta utilizada para realizar la instalación de inserción debe estar en el grupo Administradores en el equipo que desea proteger. Solo usa estas credenciales para la instalación de inserción de servicio de movilidad y deberá proporcionar al agregar un equipo a un grupo de protección.
5. Si la cuenta proporcionada no es una cuenta de dominio debe deshabilitar el control de acceso remoto al usuario en el equipo local. Para ello, agregue la entrada de registro LocalAccountTokenFilterPolicy DWORD con un valor de 1 en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para agregar la entrada del registro de una CLI abrir cmd o powershell y escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Inserción instalar automáticamente el servicio de movilidad en servidores Linux:**

1. Instale las últimas actualizaciones para el servidor de proceso, como se describe en [paso 5: instalar las últimas actualizaciones de](#step-5-install-latest-updates)y asegúrese de que el servidor de procesos está disponible.
2. Asegúrese de que hay conectividad de red entre el equipo de origen y el servidor de procesos y que el equipo de origen es accesible desde el servidor de procesos.  
3. Asegúrese de que la cuenta es un usuario de raíz del servidor de origen Linux.
4. Asegúrese de que el archivo/etc/hosts del servidor de origen Linux contiene entradas que asignan el nombre de host local a direcciones IP asociadas con todas las NIC.
5. Instalar la última openssh, servidor openssh, openssl paquetes en el equipo que desea proteger.
6. Asegúrese de que SSH está habilitado y en ejecución en el puerto 22. 
7. Habilitar la autenticación de SFTP subsistema y la contraseña del archivo sshd_config como sigue: 

    - un) sesión como raíz.
    - b) en el archivo de /etc/ssh/sshd_config archivo, busque la línea que comienza con **PasswordAuthentication**.
    - c), elimine la línea y cambie el valor de "no" a "Sí".

        ![Movilidad de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) busque la línea que comienza con subsistema y elimine la línea.
    
        ![Movilidad de inserción de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Asegúrese de que es compatible la variante de origen máquina Linux. 
 
### <a name="install-the-mobility-service-manually"></a>Instalar manualmente el servicio de movilidad

Los paquetes de software que se usó para instalar el servicio de movilidad se encuentran en el servidor de procesos en C:\pushinstallsvc\repository. Inicie sesión en el servidor de procesos y copie el paquete de instalación correspondiente en el equipo de origen basado en la tabla siguiente:-

| Sistema operativo de origen                               | Paquete de servicio de movilidad en el servidor de procesos                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (64 bits sólo)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (solo 64 bits)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Para instalar el servicio de movilidad manualmente en un servidor de Windows**, haga lo siguiente:

1. Copie el paquete de **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** de la ruta del directorio de servidor de proceso enumerada en la tabla anterior en el equipo de origen.
2. Instalar el servicio de movilidad, ejecute el archivo ejecutable en el equipo de origen.
3. Siga las instrucciones de instalación.
4. Seleccione el **servicio de movilidad** como la función y haga clic en **siguiente**.
    
    ![Instalar el servicio de movilidad](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Deje el directorio de instalación, como la ruta de instalación predeterminada y haga clic en **instalar**.
6. En la **Configuración de agente de Host** , especifique la dirección IP y el puerto HTTPS del servidor de configuración.

    - Si se está conectando a través de internet, especifique la dirección IP virtual pública y pública extremo HTTPS como el puerto.
    - Si se está conectando a través de VPN, especifique la dirección IP interna y 443 para el puerto. Dejar de **Usar HTTPS** activada.

    ![Instalar el servicio de movilidad](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Especifique la frase de contraseña del servidor de configuración y haga clic en **Aceptar** para registrar el servicio de movilidad con el servidor de configuración.

**Para ejecutar desde la línea de comandos:**

1. Copie la frase de contraseña de la CX el archivo "C:\connection.passphrase" en el servidor y ejecutar este comando. En nuestro ejemplo CX i 104.40.75.37 y el puerto HTTPS es 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Instalar el servicio de movilidad manualmente en un servidor Linux**:

1. Copie el archivo tar adecuado basado en la tabla anterior, desde el servidor de procesos en el equipo de origen.
2. Abra un programa de consola y extraer el archivo tar en zip a una ruta de acceso local al ejecutar`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Crear un archivo de passphrase.txt en el directorio local a la que extrae el contenido del archivo tar especificando *`echo <passphrase> >passphrase.txt`* de shell.
4. Instalar el servicio de movilidad especificando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique la dirección IP y puerto:

    - Si se está conectando en el servidor de configuración a través de internet especificar la configuración servidor virtual dirección IP pública y pública extremo HTTPS en `<IP address>` y `<port>`.
    - Si se está conectando a través de una conexión VPN, especifique la dirección IP interna y 443.

**Para ejecutar desde la línea de comandos**:

1. Copie la frase de contraseña de la CX el archivo "passphrase.txt" en el servidor y ejecutar este comando. En nuestro ejemplo CX i 104.40.75.37 y el puerto HTTPS es 62519:

Instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Instalar en el servidor de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Al agregar equipos a un grupo de protección que ya se están ejecutando una versión adecuada del servicio de movilidad, a continuación, en la instalación de inserción se omite.


## <a name="step-9-enable-protection"></a>Paso 9: Activar la protección

Para habilitar la protección agregar máquinas virtuales y servidores físicos a un grupo de protección. Antes de empezar, tenga en cuenta que:

- Máquinas virtuales se detectan cada 15 minutos y puede tardar hasta 15 minutos para que aparezcan en la recuperación de sitio de Azure tras la detección.
- Cambios en el entorno de la máquina virtual (como la instalación de herramientas de VMware) también pueden tardar hasta 15 minutos en actualizarse en el sitio de recuperación.
- Puede comprobar la última vez descubierta en el **último contacto** campo para el host de servidor/ESXi vCenter en la página **Configuración de servidores** .
- Si tiene un grupo de protección que ya ha creado y agregar un host de servidor o ESXi vCenter después de eso, le lleva a quince minutos para el portal de Azure sitio recuperación actualizar y máquinas virtuales que se mostrarán en el cuadro de diálogo **Agregar equipos a un grupo de protección** .
- Si desea continuar inmediatamente con agregar equipos al grupo de protección sin esperar la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar** .
- Al agregar máquinas virtuales o equipos físicos a un grupo de protección, el servidor de procesos inserta automáticamente e instala el servicio de movilidad en el servidor de origen si ya no está instalado el TI.
- Para la inserción automática mecanismo que trabaje Asegúrese de que ha configurado las equipos protegidas como se describe en el paso anterior.

Agregar máquinas como sigue:

1. Haga clic en **elementos protegidos** > **Grupo protección** > **máquinas** > **Agregar equipos**. Como práctica recomendada, se recomienda que los grupos de protección deberían reflejarse las cargas de trabajo para que agregue equipos que ejecutan una aplicación específica al mismo grupo.
2. En **Seleccionar máquinas virtuales** si está proteger servidores físicos, en el asistente **Agregar máquinas físicas** proporcione la dirección IP y un nombre descriptivo. A continuación, seleccione la familia de sistemas operativos.

    ![Agregar servidor del centro de V](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. En **Seleccionar máquinas virtuales** si está proteger máquinas virtuales VMware, seleccione un servidor de vCenter que está administrando su máquinas virtuales de Windows (o el host de EXSi en el que está ejecutando) y, a continuación, seleccione los equipos.

    ![Agregar servidor del centro de V](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. En **Especificar recursos de destino** , seleccione los servidores de destino principal y almacenamiento para usar para la replicación y seleccionar si se debe usar la configuración para todas las cargas de trabajo. Seleccione [Cuenta de almacenamiento Premium](../storage/storage-premium-storage.md) durante la configuración de la protección de cargas de trabajo que requieren coherente alto rendimiento IO y baja latencia para poder alojar IO cargas de trabajo intensivo. Si desea usar una cuenta de almacenamiento Premium para los discos de carga de trabajo, debe usar el patrón de destino de la serie DS. No puede utilizar discos de almacenamiento de Premium con un destino de patrón de-DS-series.

    >[AZURE.NOTE] No se admite el movimiento de las cuentas de almacenamiento creados con el [nuevo portal de Azure](../storage/storage-create-storage-account.md) a través de grupos de recursos.

    ![servidor de vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. En **Especificar cuentas** , seleccione la cuenta que desea usar para instalar el servicio de movilidad en equipos protegidas. Las credenciales de cuenta son necesarias para la instalación automática del servicio de movilidad. Si no puede seleccionar una cuenta, asegúrese configurar uno tal y como se describe en el paso 2. Tenga en cuenta que Azure no puede tener acceso a esta cuenta. Para Windows server, la cuenta debe tener privilegios de administrador en el servidor de origen. Para Linux, la cuenta debe ser raíz.

    ![Credenciales de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Haga clic en la marca de verificación para terminar de agregar equipos al grupo de protección y para comenzar la replicación inicial para cada equipo. Puede supervisar su estado en la página de **trabajos** .

    ![Agregar servidor del centro de V](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. También puede supervisar el estado de protección haciendo clic en los **Elementos protegidos** > nombre del grupo de protección > **máquinas virtuales de Windows** . Después de que se completa la replicación inicial y los equipos están sincronizando datos aparecerán estado **protegido** .

    ![Trabajos de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Propiedades de la máquina conjunto protegido

1. Después de un equipo no tiene un estado **protegido** puede configurar sus propiedades de migración tras error. En los detalles del grupo de protección seleccione el equipo y abra la ficha **Configurar** .
2. Puede modificar el nombre que se asignará a la máquina en Azure tras la migración tras error y el tamaño de Azure máquina virtual. También puede seleccionar la red Azure al que se conectará el equipo después de la migración.

    > [AZURE.NOTE] [Migración de redes](../resource-group-move-resources.md) entre los grupos de recursos en la misma suscripción o a través de suscripciones no es compatible con redes usadas para implementar el sitio de recuperación.

    ![Establecer las propiedades de la máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Tenga en cuenta que:

- El nombre de la máquina Azure debe cumplir los requisitos de Azure.
- De forma predeterminada replicadas máquinas virtuales en Azure no está conectados a una red de Azure. Si desea replicada máquinas virtuales para comunicar Asegúrese de establecer la misma red Azure para ellos.
- Si cambia el tamaño de un volumen en una máquina virtual VMware o servidor físico entra en un estado de tareas críticas. Si necesita modificar el tamaño, haga lo siguiente:

    - un) cambiar el tamaño.
    - b) en la pestaña **máquinas virtuales de Windows** , seleccione la máquina virtual y haga clic en **Quitar**.
    - c) en **Quitar máquina Virtual** , seleccione la opción **deshabilita la protección (se usa para recuperación de detalle y cambiar el tamaño de volumen)**. Esta opción deshabilita la protección, pero conserva los puntos de recuperación de Azure.

        ![Establecer las propiedades de la máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) volver a habilitar la protección de la máquina virtual. Al habilitar la protección de los datos para el volumen redimensionado se transferirán a Azure.

    

## <a name="step-10-run-a-failover"></a>Paso 10: Ejecutar una migración tras error

Actualmente sólo se puede ejecutar failovers planificados para máquinas virtuales de VMware protegidas y servidores físicos. Tenga en cuenta lo siguiente:



- Antes de iniciar un error, asegúrese de que los servidores de destino de la configuración y patrón se están ejecutando y correcto. En caso contrario, se producirá un error de migración tras error.
- Equipos de origen no se cerró como parte de una migración tras error no planeado. Realizar una migración tras error planificado detiene la replicación de datos para los servidores protegidos. Deberá eliminar los equipos del grupo de protección y agregar de nuevo para empezar a proteger máquinas nuevamente una vez completada la migración tras error no planeada.
- Si desea conmutar sin perder los datos, asegúrese de que están desactivadas las máquinas virtuales de sitio principal antes de iniciar la migración tras error.

1. En los **Planes de recuperación de** página y agregar un plan de recuperación. Especificar los detalles del plan y seleccione **Azure** como destino.

    ![Configurar el plan de recuperación](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. En **Seleccione una máquina Virtual** seleccione un grupo de protección y, a continuación, seleccione equipos en el grupo al que agregar el plan de recuperación. [Obtenga más información](site-recovery-create-recovery-plans.md) acerca de los planes de recuperación.

    ![Agregar máquinas virtuales de Windows](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Si es necesario puede personalizar el plan para crear grupos y secuencia el orden en qué equipos en la recuperación de plan se produce un error sobre. También puede agregar avisos para las secuencias de comandos y acciones manuales. Las secuencias de comandos al recuperar a Azure pueden agregarse usando [Runbooks de automatización de Azure](site-recovery-runbook-automation.md).

4. En la página de **Planes de recuperación** seleccione el plan y haga clic en **No planeado migración tras error**.
5. En **Confirme de migración tras error** , compruebe la dirección de migración tras error (a Azure) y seleccione el punto de recuperación conmutar a.
6. Espere a que el trabajo de migración tras error completar y, a continuación, compruebe que ha realizado la migración tras error como se esperaba y que los equipos virtuales replicados se inicie correctamente en Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Paso 11: Éxito volver error sobre los equipos de Azure

[Más información](site-recovery-failback-azure-to-vmware-classic-legacy.md) sobre cómo conectar el error sobre los equipos que se ejecuta en Azure atrás en su entorno local.


## <a name="manage-your-process-servers"></a>Administrar los servidores de procesos

El servidor envía los datos de replicación en el servidor de destino principal en Azure y descubre nuevas máquinas virtuales de VMware agregadas a un servidor de vCenter. En las siguientes circunstancias que podría querer cambiar el servidor de procesos en la implementación:

- Si el servidor actual de proceso se interrumpe
- Si su objetivo de punto de recuperación (RPO) aumenta a un nivel aceptable de su organización.

Si es necesario puede mover la replicación de algunos o todos los servidores físicos y máquinas virtuales de VMware local a un servidor de proceso diferente. Por ejemplo:

- **Error**: si se produce un error de un servidor de procesos o no está disponible puede mover replicación de equipo protegido a otro servidor de proceso. Metadatos de la máquina de origen y el equipo de réplica se transferirán al nuevo servidor y volver a sincronizar datos. El nuevo servidor de proceso se conectará automáticamente al servidor vCenter para realizar la detección automática. Puede supervisar el estado de los servidores de procesos en el panel de recuperación del sitio.
- **Para ajustar RPO de equilibrio de carga**, puede seleccionar un servidor de proceso diferente en el portal de recuperación de sitios equilibrio de carga de mejoradas y realice la replicación de uno o varios equipos equilibrio de carga manual. En este caso metadatos de los equipos de origen y réplica seleccionados se mueven al nuevo servidor. El servidor de proceso original permanece conectado al servidor vCenter. 

### <a name="monitor-the-process-server"></a>Supervisar el servidor de procesos

Si es un servidor de procesos en un estado de tareas críticas se mostrará una advertencia de estado en el panel de recuperación del sitio. Puede hacer clic en el estado para abrir la pestaña eventos y profundizar en tareas específicas en la ficha trabajos. 

### <a name="modify-the-process-server-used-for-replication"></a>Modificar el servidor de procesos que se utilizan para la replicación

1. Abra **servidores** > **Servidores de configuración** > Configuración servidor > **Detalles del servidor**.
2. Haga clic en **Servidores de proceso** > **Cambiar el servidor de proceso** junto al servidor que desea modificar.

    ![Cambiar el servidor de proceso 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. En el **Servidor de procesos de cambio** > **Servidor del proceso de destino** seleccione el nuevo servidor que desea usar y, a continuación, seleccione las máquinas virtuales que desea duplicar en el servidor nuevo. Haga clic en el icono de información junto al nombre del servidor para obtener información detallada de espacio libre y memoria utilizada. El espacio medio que deberán replicar cada máquina virtual seleccionada en el servidor de proceso nueva se muestra para ayudarle a tomar decisiones de carga.

    ![Cambiar el servidor de procesos 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Haga clic en la marca de verificación para comenzar a replicar en el servidor de proceso de nuevo. Tenga en cuenta que si quita todas las máquinas virtuales de un servidor de procesos que era crítico debe mostrar una advertencia crítica ya no está en el panel.


## <a name="third-party-software-notices-and-information"></a>Avisos de software de terceros e información

No traducir texto o localizar

El software y el firmware que se ejecuta en el producto de Microsoft o un servicio basado en o incorpora material de los proyectos que se enumeran a continuación (colectivamente, "código de terceros").  Microsoft es el autor del código de terceros no original.  El aviso de copyright original y la licencia, que Microsoft ha recibido este código de terceros y se establecen presentado más adelante.

La información en la sección A está relacionado con componentes de terceros de código de los proyectos que se muestran a continuación. Tales licencias y la información se proporcionan solo con fines informativos.  Este código de terceros que se está relicensed a usted Microsoft en términos de licencia de software de Microsoft para el producto de Microsoft o un servicio.  

La información en la sección B está relacionado con los componentes de código de terceros que se están disponibles para Microsoft en términos de la licencia originales.

Puede encontrar el archivo completo en el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserva todos los derechos que no se conceden expresamente, ya sea de forma implícita, desestimación o de otro tipo.
