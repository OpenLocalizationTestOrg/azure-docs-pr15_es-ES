<properties 
   pageTitle="Procedimientos recomendados para matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe los procedimientos recomendados para implementar y administrar la matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Prácticas recomendadas de matriz Virtual de StorSimple

## <a name="overview"></a>Información general

Matriz Virtual de Microsoft Azure StorSimple es una solución de almacenamiento de información integrado que administra las tareas de almacenamiento entre un dispositivo virtual local ejecuta en un hipervisor y el almacenamiento de nube de Microsoft Azure. Matriz Virtual de StorSimple es una alternativa eficiente y rentable a la matriz de 8000 serie física. La matriz virtual puede ejecutar en la infraestructura de hipervisor, admite el iSCSI y los protocolos SMB y es ideal para escenarios de office remotas y sucursales. Para obtener más información acerca de las soluciones StorSimple, vaya a [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artículo describen los procedimientos recomendados que se implementan durante la instalación inicial, implementación y administración de la matriz Virtual StorSimple. Estas recomendaciones proporcionan instrucciones validadas para la configuración y administración de la matriz virtual. En este artículo está dirigido a los administradores de TI que implementación y administración las matrices virtuales en sus centros de datos.

Se recomienda una revisión periódica de los procedimientos recomendados para ayudar a asegurar que el dispositivo aún está en cumplimiento cuando se realizan cambios en el flujo de operación o el programa de instalación. Si encuentra problemas durante la implementación de estos procedimientos recomendados en la matriz virtual, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda.

## <a name="configuration-best-practices"></a>Prácticas recomendadas de configuración 

Estas recomendaciones cubren las directrices que deben seguir durante la instalación inicial y la implementación de las matrices virtuales. Estas recomendaciones incluyen aquellos relacionados con el aprovisionamiento de la máquina virtual, configuración de directiva de grupo, tamaño, configuración de la red, configurar cuentas de almacenamiento y creación de volúmenes y recursos compartidos de la matriz virtual. 

### <a name="provisioning"></a>Aprovisionamiento 

Matriz Virtual de StorSimple es una máquina virtual (VM) se aprovisione en el hipervisor (Hyper-V o sus) de su servidor de host. Al hacer el aprovisionamiento de la máquina virtual, asegúrese que el host puedo que se dedicará suficientes recursos. Para obtener más información, vaya a [requisitos de recursos mínimos](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) proporcionando una matriz. 

Implementar las siguientes prácticas recomendadas al hacer el aprovisionamiento de la matriz virtual:


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Tipo de máquina virtual**   | **Generación 2** Máquina virtual para su uso con Windows Server 2012 o posterior y una imagen de *.vhdx* . <br></br> **Generación 1** Máquina virtual para su uso con Windows Server 2008 o posterior y una imagen *.vhd* .                                                                                                              | Use máquina virtual versión 8-11 al usar imagen *.vmdk* .                                                                      |
| **Tipo de memoria**            | Configurar como **memoria estática**. <br></br> No use la opción de **memoria dinámica** .            |                                                    |
| **Tipo de datos de disco**         | Aprovisionar como **expansión dinámica**.<br></br> **Tamaño fijo** tarda mucho tiempo. <br></br> No use la opción **comparar** .                                                                                                                   | Use la opción de **aprovisionamiento fina** .                                                                                      |
| **Modificación del disco de datos** | No se permite la ampliación o reducción. Resultados de un intento de hacerlo en la pérdida de todos los datos locales en el dispositivo.                       | No se permite la ampliación o reducción. Resultados de un intento de hacerlo en la pérdida de todos los datos locales en el dispositivo. |

### <a name="sizing"></a>Tamaño

Cuando se cambia el tamaño de la matriz Virtual de StorSimple, tenga en cuenta los siguientes factores:

- Reserva local para volúmenes o recursos compartidos. Aproximadamente 12% del espacio está reservado en el nivel local para cada volumen en niveles aprovisiona o compartir. Aproximadamente 10% de espacio también está reservado para un volumen localmente anclado para su sistema de archivos.
- Instantánea de sobrecarga. Aproximadamente 15% de espacio en el nivel local está reservado para instantáneas.
- Necesidad de restaura. Si esta restaurar como una nueva operación, tamaño debe cuenta el espacio necesario para restaurar. Restaurar está listo para un recurso compartido o el volumen del mismo tamaño o mayor.
- Algunos búfer debe asignarse para cualquier crecimiento inesperado.

En función de los factores anteriores, los requisitos de tamaño se pueden representar mediante la siguiente ecuación:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Los siguientes ejemplos muestran cómo puede ajustar el tamaño de una matriz virtual según sus necesidades.

#### <a name="example-1"></a>Ejemplo 1:
En la matriz virtual, que desea que pueda 

- aprovisionar un 2 TB en niveles volumen o recurso compartido.
- Aprovisionamiento de 1 TB en niveles volumen o recurso compartido.
- aprovisionar un 300 GB de volumen localmente anclada o compartir.


La anterior volúmenes o recursos compartidos, deje que nosotros calcule los requisitos de espacio en el nivel local. 

En primer lugar, para cada volumen en niveles y compartir, reserva local sería igual a 12% del tamaño de volumen o compartido. Para el volumen o compartir localmente anclado, reserva local es 10% del tamaño de volumen localmente anclada o compartido (además del tamaño de preparación). En este ejemplo, necesita

- Reserva local de 240 GB (para un 2 TB en niveles de volumen o compartido)
- Reserva local de 120 GB (de 1 TB en niveles de volumen o compartido)
- 330 GB por volumen localmente anclada o compartir (agregar un 10% de reserva local hasta el tamaño de 300 GB aprovisionado)

Es el espacio total necesario hasta el momento en el nivel local: 240 GB + 120 GB + 330 GB = 690 GB.

En segundo término, se necesita al menos espacio en el nivel local como la mayor reserva único. Este importe adicional se utiliza en caso de que necesite restaurar desde una instantánea de la nube. En este ejemplo, la mayor reserva local es 330 GB (incluida la reserva de sistema de archivos), por lo que puede agregar a 690 GB: 690 GB + 330 GB = 1020 GB.
Si hemos realizado subsiguientes restaura adicional, nos podemos siempre liberar el espacio de la operación de restauración anterior.

Por último, debemos 15% del total espacio local hasta ese momento para almacenar instantáneas locales, para que esté disponible solo 85% de la misma. En este ejemplo, que sería de aproximadamente 1020 GB = 0,85&ast;datos aprovisionados disco TB. Por lo tanto, sería el disco de preparación de datos (1020&ast;(1/0,85)) = 1200 GB = 1,20 TB ~ 1,25 TB (redondeo que cuartil más cercano)

Factores de crecimiento inesperado y restaura nuevo, debe aprovisionar un disco local de alrededor de 1,25-1,5 TB.

> [AZURE.NOTE] También se recomienda que thin se aprovisiona el disco local. Esta recomendación es porque el espacio para restaurar solo es necesario cuando se desea restaurar los datos más de cinco días. Recuperación de nivel de elemento le permite restaurar los datos de los últimos cinco días sin necesidad del espacio adicional para restaurar.

#### <a name="example-2"></a>Ejemplo 2: 
En la matriz virtual, que desea que pueda 

- aprovisionar un 2 TB en niveles de volumen
- aprovisionar un volumen de 300 GB anclado localmente

En función de 12% de reserva de espacio local para en niveles volúmenes o recursos compartidos y 10% para localmente anclados volúmenes o recursos compartidos, debemos

- Reserva local de 240 GB (de 2 TB en niveles de volumen o compartido)
- 330 GB por volumen localmente anclada o compartir (agregar un 10% de reserva local hasta el espacio de 300 GB aprovisionado)

Espacio total necesario en el nivel local es: 240 GB + 330 GB = 570 GB

El espacio local mínimo necesario para restaurar es 330 GB. 

15% del total en el disco se usa para almacenar instantáneas de modo que 0,85 solo está disponible. Por lo tanto, es el tamaño del disco (900&ast;(1/0,85)) = TB 1,06 ~ 1,25 TB (redondeo que cuartil más cercano) 

Dividir en cualquier crecimiento inesperado, puede aprovisionar un disco local de 1,25-1,5 TB.


### <a name="group-policy"></a>Directiva de grupo

Directiva de grupo es una infraestructura que permite implementar configuraciones específicas para usuarios y equipos. Configuración de la directiva de grupo está contenidos en objetos de directiva de grupo (GPO), que están vinculados a los siguientes contenedores de servicios de dominio de Active Directory (AD DS): sitios, dominios o unidades organizativas (OU). 

Si la matriz virtual está unido al dominio, se pueden aplicar GPO. Estos GPO pueden instalar aplicaciones como un software antivirus que pueden afectar el funcionamiento de la matriz Virtual StorSimple.

Por lo tanto, se recomienda:

-   Asegúrese de que la matriz virtual se encuentra en su propia unidad organizativa (OU) de Active Directory. 

-   Asegúrese de que no hay objetos de directiva de grupo (GPO) se aplican a la matriz virtual. Puede bloquear la herencia para asegurarse de que la matriz virtual (nodo secundario) no hereda automáticamente todos los GPO del elemento primario. Para obtener más información, vaya a [Bloquear la herencia](https://technet.microsoft.com/library/cc731076.aspx).


### <a name="networking"></a>Redes

La configuración de red de la matriz virtual se realiza a través de la interfaz de usuario de web local. Una interfaz de red virtual se habilita mediante el hipervisor en el que se aprovisiona la matriz virtual. Utilice la página [Configuración de red](storsimple-ova-deploy3-fs-setup.md) para configurar la dirección IP de la interfaz de red virtual, la subred y la puerta de enlace.  También puede configurar el servidor DNS principal y secundario, la configuración de hora y la configuración de proxy opcional de su dispositivo. La mayoría de la configuración de red es una instalación de una sola vez. Revise los [requisitos de red StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar la matriz virtual.

Al implementar la matriz virtual, le recomendamos que siga estos procedimientos recomendados:

-   Asegúrese de que la red en la que la matriz virtual se implementa siempre tiene la capacidad que se dedicará 5 ancho de banda de Mbps Internet (o más). 

    -   Necesidad de ancho de banda de Internet varía según las características de carga de trabajo y la velocidad de cambio de datos.

    -   El cambio de datos que se puede controlar es directamente proporcional a su ancho de banda de Internet. Por ejemplo al realizar una copia de seguridad, un ancho de banda de 5 Mbps puede aceptar un cambio de datos de alrededor de 18 GB en 8 horas. Con cuatro veces más ancho de banda (20 Mbps), puede controlar el cambio de datos cuatro veces más (72 GB). 

-   Asegúrese de que la conectividad a Internet siempre está disponible. Conexiones a Internet se o poco confiables a los dispositivos podrían ocasionar una pérdida de acceso a datos en la nube y podrían causar una configuración incompatible.

-   Si va a implementar el dispositivo como servidor iSCSI: 
    -   Le recomendamos que desactive la opción **dirección IP obtener automáticamente** (DHCP). 
    -   Configurar direcciones IP estáticas. Configure un principal y un servidor DNS secundario.

    -   Si define varias interfaces de red en su virtual de matriz, la primera interfaz de red (de forma predeterminada, esta interfaz es **Ethernet**) puede llegar a la nube. Para controlar el tipo de tráfico, puede crear varias interfaces de red virtual en la matriz virtual (configurada como un servidor de iSCSI) y conectar dichas interfaces a subredes diferentes.

-   Para limitar el ancho de banda de nube solo (utiliza la matriz virtual), configurar el límite en el enrutador o el firewall. Si define el límite de su hipervisor, se del Acelerador todos los protocolos incluidos iSCSI y SMB en lugar de solo el ancho de banda de nube. 

-   Asegúrese de que la sincronización de hora hipervisores está habilitada. Si usa Hyper-V, seleccione la matriz virtual en el Administrador de Hyper-V, vaya a **configuración &gt; Integration Services**y asegúrese de que está activada la **sincronización de hora** .

### <a name="storage-accounts"></a>Cuentas de almacenamiento

Matriz Virtual de StorSimple se pueden asociar con una cuenta de almacenamiento única. Esta cuenta de almacenamiento podría ser una cuenta de almacenamiento generada automáticamente, una cuenta en la misma suscripción como el servicio, o una cuenta de almacenamiento relacionados con otra suscripción. Para obtener más información, consulte cómo [Administrar cuentas de almacenamiento de la matriz virtual](storsimple-ova-manage-storage-accounts.md).

Utilice las siguientes recomendaciones para cuentas de almacenamiento asociadas con la matriz virtual.

-   Al vincular varios matrices virtuales con una cuenta de almacenamiento única, factor de la capacidad máxima (64 TB) de una matriz virtual y el tamaño máximo (500 TB) de una cuenta de almacenamiento. Esto limita el número de matrices virtuales completa que se pueden asociar con esa cuenta de almacenamiento a aproximadamente 7.

-   Al crear una nueva cuenta de almacenamiento
    -   Le recomendamos que crearlo en la región más cercana a la oficina remota o sucursal donde se implementa la matriz Virtual de StorSimple para minimizar latencia.

    -   Tenga en cuenta que no se puede mover una cuenta de almacenamiento a través de diferentes regiones. También se puede mover un servicio a través de suscripciones.

    -   Usar una cuenta de almacenamiento que implementa redundancia entre los centros de datos. Almacenamiento de geo redundantes (GRS), almacenamiento redundantes de zona (ZRS) y almacenamiento redundantes localmente (LRS) son compatibles para su uso con la matriz virtual. Para obtener más información sobre los distintos tipos de cuentas de almacenamiento, vaya a la [replicación de almacenamiento de Azure](../storage/storage-redundancy.md).


### <a name="shares-and-volumes"></a>Volúmenes y recursos compartidos

Para la matriz Virtual StorSimple, pueden aprovisionar recursos compartidos cuando está configurado como un servidor de archivos y volúmenes cuando está configurado como un servidor de iSCSI. Los procedimientos recomendados para la creación de volúmenes y recursos compartidos están relacionadas con el tamaño y el tipo de configurado.

#### <a name="volumeshare-size"></a>Tamaño de volumen o compartido

En la matriz virtual, pueden aprovisionar recursos compartidos cuando está configurado como un servidor de archivos y volúmenes cuando está configurado como un servidor de iSCSI. Los procedimientos recomendados para la creación de volúmenes y recursos compartidos se relacionan con el tamaño y el tipo de configurado. 

Tenga en cuenta las siguientes prácticas recomendadas al hacer el aprovisionamiento de recursos compartidos o volúmenes en el dispositivo virtual.

-   Los tamaños de archivo en relación con el tamaño de un recurso compartido en niveles aprovisiona pueden afectar al rendimiento niveles. Trabajar con archivos de gran tamaño podría provocar un nivel lento fuera. Cuando trabaje con archivos grandes, se recomienda que el archivo de mayor tamaño es menor que 3% del tamaño de compartir.

-   Un máximo de 16 volúmenes o recursos compartidos puede crearse en la matriz virtual. Si ancla localmente, puede estar volúmenes o recursos compartidos entre 50 GB a 2 TB. Si en niveles, las cuotas de volúmenes debe estar entre 500 GB a 20 TB. 

-   Al crear un volumen, factor en el consumo de datos esperado, así como aumento futuro. Mientras que el volumen no se puede expandir más adelante, siempre puede restaurar a un volumen más grande.

-   Una vez creado el volumen, no se puede reducir el tamaño del volumen en StorSimple.
   
-   Al escribir en un volumen en niveles en StorSimple, cuando los datos de volumen alcanza un determinado umbral (en relación con el espacio local reservado para el volumen), se reduce el IO. Continuar escribiendo en este volumen reduce el IO significativamente. Aunque puede escribir en un volumen en niveles más allá de su capacidad de preparación (que no conocidas detenga el usuario de escritura más allá de la capacidad de preparación), verá una notificación de alerta para el efecto que dispone de una suscripción excesiva. Una vez que ve la alerta, es fundamental que tomar medidas correctivas como eliminar los datos del volumen o restaurar el volumen a un volumen más grande (expansión de volumen no es compatible actualmente).

-   Para los casos de uso de recuperación de desastres, como el número de recursos compartidos o volúmenes permitidos es 16 y el número máximo de recursos compartidos o volúmenes que se pueden procesar en paralelo también es 16, el número de recursos compartidos o volúmenes no tiene una incidencia en sus RPOs y RTOs. 

#### <a name="volumeshare-type"></a>Tipo de volumen o compartido

StorSimple es compatible con dos tipos de volumen o compartir basados en el uso: localmente anclada y niveles. Mientras que la volúmenes o recursos compartidos en niveles thin aprovisionados grosor aprovisionados localmente anclados volúmenes o recursos compartidos. 

Se recomienda implementar las siguientes prácticas recomendadas al configurar StorSimple volúmenes o recursos compartidos:

-   Identificar el tipo de volumen en función de las cargas de trabajo que desee distribuir antes de crear un volumen. Utilice volúmenes localmente anclados para cargas de trabajo que requieren garantías locales de datos (incluso durante una interrupción de la nube) y que latencia de nube baja. Una vez creado un volumen en la matriz virtual, no puede cambiar el tipo de volumen de localmente anclados a niveles o *viceversa*. Por ejemplo, crear volúmenes localmente anclados al implementar cargas de trabajo SQL o cargas de trabajo que aloja máquinas virtuales (VM); usar volúmenes en niveles para las cargas de trabajo del recurso compartido de archivos.

-   Active la opción para menos archivados datos utilizados con frecuencia cuando se trabaja con tamaños de archivo grandes. Cuando esta opción está activada para acelerar la transferencia de datos en la nube, se utiliza un tamaño de fragmento desduplicación de 512 KB.

#### <a name="volume-format"></a>Formato de volumen

Después de crear volúmenes de StorSimple en su servidor iSCSI, debe inicializar, montaje y dar formato a los volúmenes. Esta operación se realiza en el host conectado a su dispositivo StorSimple. Se recomiendan la siguientes prácticas recomendadas al montaje y dar formato a volúmenes en el host StorSimple.

-   Realizar un formato rápido en todos los volúmenes de StorSimple.

-   Al dar formato a un volumen StorSimple, utilice un tamaño de unidad de asignación (Australia) de 64 KB (el valor predeterminado es 4 KB). Los 64 KB Australia está basado en las pruebas realizado internamente para cargas de trabajo de StorSimple comunes y otras cargas de trabajo.

-   Cuando se usa la matriz Virtual StorSimple está configurado como un servidor iSCSI, no use volúmenes distribuidos o discos dinámicos como estos volúmenes o discos no son compatibles con StorSimple.

#### <a name="share-access"></a>Compartir el acceso

Al crear recursos compartidos en el servidor de archivos de la matriz virtual, siga estas instrucciones:

-   Al crear un recurso compartido, asigne a un grupo de usuario como administrador de compartir en lugar de un solo usuario.

-   Puede administrar los permisos de NTFS después de crea el recurso compartido mediante la edición de los recursos compartidos a través del explorador de Windows.

#### <a name="volume-access"></a>Acceso de volumen

Cuando configure los volúmenes iSCSI de la matriz Virtual de StorSimple, es importante controlar el acceso siempre que sea necesario. Para determinar qué servidores de host pueden tener acceso a volúmenes, crear y asociar los registros de control de acceso (ACRs) StorSimple volúmenes.

Use las siguientes prácticas recomendadas al configurar ACRs para volúmenes de StorSimple:

-   Asociar siempre al menos una ACR con un volumen.

-   Definir varios ACRs solo en un entorno agrupado.

-   Al asignar ACR más de uno a un volumen, asegúrese de que el volumen no se expone de forma donde se puede acceder al mismo tiempo por más de un host no agrupado. Si ha asignado varias ACRs a un volumen, un mensaje de advertencia aparece para que revisar la configuración.

### <a name="data-security-and-encryption"></a>Cifrado y seguridad de datos

La matriz Virtual de StorSimple tiene características de seguridad y el cifrado de datos que garantizan la confidencialidad y la integridad de los datos. Al usar estas características, se recomienda que siga estos procedimientos recomendados: 

-   Definir una clave de cifrado de almacenamiento de nube para generar cifrado AES 256 antes de envían los datos de la matriz virtual en la nube. Esta clave no es necesaria si se cifran para empezar con los datos. La clave puede generar y conservarse segura con un sistema de administración de claves como [Azure depósito clave](../key-vault/key-vault-whatis.md).

-   Al configurar la cuenta de almacenamiento a través del servicio de administrador de StorSimple, asegúrese de que habilitar el modo de SSL para crear un canal seguro para la comunicación de red entre el dispositivo de StorSimple y la nube.

-   Vuelva a generar las claves para las cuentas de almacenamiento (obtener acceso al servicio de almacenamiento de Azure) periódicamente a cuenta para los cambios para tener acceso a basado en la lista de administradores modificada.

-   Datos de la matriz virtual se comprimen y desduplicados antes de enviarlo a Azure. No se recomienda usar el servicio de rol de desduplicación de datos en el host de Windows Server.


## <a name="operational-best-practices"></a>Prácticas recomendadas de funcionamiento

Las mejores prácticas operativas son las instrucciones que deben seguirse durante la operación de la matriz virtual o administración diaria. Estas prácticas tratan las tareas de administración específicas como realizar copias de seguridad, restaurar a partir de un conjunto de copia de seguridad, realizar una migración tras error, desactivar y eliminar la matriz, supervisión de estado y el uso de sistema y ejecutando virus explora en la matriz virtual.

### <a name="backups"></a>Realizar copias de seguridad

Los datos de la matriz virtual se copien en la nube de dos maneras, predeterminado automatizado de copia de seguridad diaria del dispositivo completo comenzando en 22:30 o a través de una copia de seguridad manual a petición. De forma predeterminada, el dispositivo crea automáticamente diarias instantáneas de nube de todos los datos que residen en él. Para obtener más información, vaya a [realizar copias de seguridad de la matriz Virtual de StorSimple](storsimple-ova-backup.md).

No se puede cambiar la frecuencia y retención asociados con las copias de seguridad de forma predeterminada, pero puede configurar la hora en que las copias de seguridad diarias se inician cada día. Al configurar la hora de inicio para las copias de seguridad automatizadas, le recomendamos:

-   Programar las copias de seguridad para las horas. Hora de inicio de copia de seguridad no debe coincidir con el host numerosos IO.

-   Iniciar una copia de seguridad manual a petición al planear realizar una migración tras error del dispositivo o antes de la ventana de mantenimiento, para proteger los datos de la matriz virtual.

### <a name="restore"></a>Restaurar

Puede restaurar desde una copia de seguridad de dos maneras: restaurar a otro volumen o compartir o realizar una recuperación de nivel de elemento (disponible sólo en una matriz virtual está configurada como un servidor de archivos). Recuperación de nivel de elemento le permite realizar una recuperación granular de archivos y carpetas desde una copia de seguridad de la nube de todas las acciones en el dispositivo StorSimple. Para obtener más información, vaya a [Restaurar a partir de una copia de seguridad](storsimple-ova-restore.md).

Al realizar una restauración, tenga en cuenta las directrices siguientes:

-   La matriz Virtual de StorSimple no admite la restauración de en contexto. Sin embargo puede ser fácilmente por un proceso de dos pasos: agregar espacio a la virtual de matriz y, a continuación, restaurar a otro volumen o recurso compartido.

-   Cuando restaurar a partir de un volumen local, tenga en cuenta la restauración será una operación larga. Aunque el volumen rápidamente puede poner en línea, los datos continúan se hidratado en segundo plano.

-   El tipo de volumen cambiará durante el proceso de restauración. Un volumen en niveles se restaura a otro volumen en niveles y un volumen localmente anclado a otro localmente anclados volumen.

-   Cuando intenta restaurar un volumen o un recurso compartido de un conjunto de copia de seguridad, si se produce un error en el trabajo de restauración, un volumen de destino o compartir aún pueden crearse en el portal. Es importante que eliminar este volumen de destino sin usar o compartir en el portal de minimizar futuros problemas que surjan de este elemento.

### <a name="failover-and-disaster-recovery"></a>Migración tras error y recuperación de desastres

Dispositivo permite migrar los datos desde un dispositivo de *origen* en el centro de datos a otro dispositivo de *destino* que se encuentra en la misma u otra ubicación geográfica. La migración tras error del dispositivo es para el dispositivo completo. Durante la migración tras error, los datos de la nube para el dispositivo de origen cambian la propiedad a la que el dispositivo de destino.

Para la matriz Virtual StorSimple, en sólo puede conmutar a otra matriz virtual administrado por el servicio de administrador de StorSimple mismo. No se permite una migración tras error a un dispositivo 8000 serie o una matriz administrado por un servicio de administrador de StorSimple diferente (a la que el dispositivo de origen). Para obtener más información sobre consideraciones de migración tras error, vaya a [los requisitos previos para la migración tras error del dispositivo](storsimple-ova-failover-dr.md).

Cuando se realiza un error sobre de la matriz virtual, tenga en cuenta lo siguiente:

-   Para una migración tras error planeada, es recomendable tomar todos los volúmenes y recursos compartidos sin conexión antes de iniciar la migración tras error. Siga las instrucciones de sistema operativo específico para realizar la volúmenes o recursos compartidos sin conexión en el host de primera y las tome sin conexión en el dispositivo virtual.

-   Archivo server recuperación de desastres (DR), se recomienda que se une el dispositivo de destino en el mismo dominio como origen de para que los permisos se resuelven automáticamente. Solo la migración de tras de error en un dispositivo de destino en el mismo dominio es compatible con esta versión.

-   Una vez que el DR se completó correctamente, se elimina automáticamente el dispositivo de origen. Aunque el dispositivo ya no está disponible, la máquina virtual que haya proporcionado en el sistema host aún consume recursos. Se recomienda que elimine esta máquina virtual de su sistema de host para evitar que los cargos se acumulan.

-   Tenga en cuenta que incluso si la migración tras error se realiza correctamente, **los datos están seguros siempre en la nube**. Tenga en cuenta los siguientes tres escenarios en los que la migración tras error no se completó correctamente:

    -   Se ha producido un error en las fases iniciales de la migración tras error como cuando se realizan las comprobaciones de DR anterior. En este caso, es puede seguir usando el dispositivo de destino. Puede volver a intentar la migración tras error en el mismo dispositivo de destino.

    -   Se ha producido un error durante el proceso de failover real. En este caso, el dispositivo de destino está marcado inutilizable. Debe aprovisionar y configurar otra matriz virtual de destino y usarlo para migración tras error.

    -   La migración tras error se completa después de que se ha eliminado el dispositivo de origen, pero el dispositivo de destino tiene problemas y no puede acceder a los datos. Los datos es seguros aún en la nube y se pueden recuperar fácilmente creando otra matriz virtual y usarlo como un dispositivo de destino para el DR.

### <a name="deactivate"></a>Desactivar

Al desactivar una matriz Virtual de StorSimple, servidor de la conexión entre el dispositivo y el servicio de administrador de StorSimple correspondiente. Desactivación es una operación **permanente** y no se puede deshacer. Un dispositivo desactivado no se puede registrar con el servicio Administrador de StorSimple nuevamente. Para obtener más información, vaya a [desactivar y eliminar la matriz Virtual de StorSimple](storsimple-deactivate-and-delete-device.md).

Mantener las siguientes prácticas recomendadas en cuenta al desactivar la matriz virtual:

-   Tomar una instantánea de la nube de todos los datos antes de la desactivación de un dispositivo virtual. Al desactivar una matriz virtual, se pierden todos los datos del dispositivo local. Tomar una instantánea de la nube, podrá recuperar datos posteriormente.

-   Antes de desactivar una matriz Virtual de StorSimple, asegúrese de detener o eliminar clientes y hosts que dependen de ese dispositivo.

-   Eliminar un dispositivo desactivado si ya no se usa para que no acumular los cargos.

### <a name="monitoring"></a>Supervisión

Para asegurarse de que la matriz Virtual de StorSimple se encuentra en buen estado continuo, debe supervisar la matriz y asegurarse de que recibe información del sistema, incluidas las alertas. Para supervisar el estado general de la matriz virtual, implementar las siguientes prácticas recomendadas:

- Configurar la supervisión para realizar un seguimiento de su disco de datos de la matriz virtual, así como el disco de sistema operativo el uso de disco. Si ejecuta Hyper-V, puede usar una combinación de System Center Virtual Machine Manager (SCVMM) y System Center Operations Manager (SCOM) para supervisar los hosts de virtualización.   

- Configurar notificaciones de correo electrónico en su matriz virtual para enviar alertas a determinados niveles de uso.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicaciones de análisis de virus y búsqueda de índice

Una matriz Virtual de StorSimple puede nivel automáticamente datos de la capa local en la nube de Microsoft Azure. Cuando se utiliza una aplicación como una búsqueda de índice o una detección de virus para analizar los datos almacenados en StorSimple, debe tener cuidado que los datos de nube no obtener acceso y suben al nivel local.

Se recomienda implementar las siguientes prácticas recomendadas al configurar el análisis de índice de búsqueda o virus en su matriz virtual:

-   Deshabilitar las operaciones de análisis completo configurado automáticamente.

-   Para volúmenes en niveles, configure la aplicación de exploración de virus o de búsqueda de índice para realizar un recorrido incremental. Esto podría analizar solo la nueva datos probable que reside en el nivel local. No se tiene acceso a los datos que niveles en la nube durante una operación de incremental.

-   Asegúrese de que los filtros de búsqueda correcta y la configuración se han configurado para que obtengan digitalizados solo previstos tipos de archivos. Por ejemplo, archivos (JPEG, GIF y TIFF) de imágenes y dibujos de ingeniería no se deben analizar durante la regeneración de índice incremental o completa.

Si usa Windows proceso de indización, siga estas instrucciones:

-   No utilice el indizador de Windows para volúmenes en niveles como recuerda grandes cantidades de datos (TB) de la nube si el índice debe generarse con frecuencia. Volver a generar el índice recuperar todos los tipos de archivo para ajustar su contenido.

-   Utilizar las ventanas de indización proceso para volúmenes localmente anclados como esto solo se obtiene acceso a datos en los niveles locales para crear el índice (nube no se pueden acceder a los datos).

### <a name="byte-range-locking"></a>Bloqueo de intervalo de bytes

Aplicaciones pueden bloquear un intervalo de bytes especificado dentro de los archivos. Si se habilita el bloqueo de intervalo de bytes en las aplicaciones que están escribiendo en el StorSimple, a continuación, interconexión no funciona en la matriz virtual. Para la interconexión para trabajar, se deben desbloqueadas todas las áreas de los archivos que se tiene acceso a. Bloqueo de intervalo de bytes no es compatible con volúmenes en niveles en la matriz virtual.

Recomendado medidas para mitigar el bloqueo del intervalo de bytes incluyen:

-   Desactivar el bloqueo en la lógica de la aplicación de intervalo de bytes.

-   Uso localmente anclados volúmenes (en lugar de niveles) para los datos asociados con esta aplicación. Volúmenes localmente anclados no nivel en la nube.

-   Cuando usa localmente ancla volúmenes con bloqueo del intervalo de bytes habilitado, el volumen puede proceder en línea antes de que finalice la restauración. En estos casos, debe esperar a que la restauración completo.

## <a name="multiple-arrays"></a>Varias matrices

Varias matrices virtuales que necesite implementará en cuenta para un conjunto de trabajo creciente de datos que podrían supresión en la nube, por tanto, afectar al rendimiento del dispositivo. En estos casos, es mejor dispositivos escala a medida que crece el conjunto de trabajo. Es necesario que uno o varios dispositivos para agregarse en el centro de datos local. Al agregar los dispositivos, puede:

-   Dividir el conjunto de datos actual.
-   Implementar nuevas cargas de trabajo en nuevos dispositivos.
-   Si implementar varias matrices virtuales, le recomendamos que Equilibrio de carga de perspectiva, distribuir la matriz en hipervisor distintos hosts.

-  Pueden implementar varias matrices virtuales (cuando está configurado como un servidor de archivos o en un servidor de iSCSI) en un sistema de archivos distribuido, Namespace. Para conocer los pasos detallados, vaya al [Archivo sistema Namespace solución distribuida con la Guía de implementación de almacenamiento de nube híbrida](https://www.microsoft.com/download/details.aspx?id=45507). Replicación de sistema de archivos distribuido actualmente no se recomienda para su uso con la matriz virtual. 


## <a name="see-also"></a>Vea también
Obtenga información sobre cómo [Administrar la matriz Virtual de StorSimple](storsimple-ova-manager-service-administration.md) a través del servicio de administrador de StorSimple.
