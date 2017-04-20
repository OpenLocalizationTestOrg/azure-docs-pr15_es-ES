<properties
    pageTitle="Planear la infraestructura de copia de seguridad de VM en Azure | Microsoft Azure"
    description="Consideraciones al planear la copia máquinas virtuales de Azure"
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="cfreeman"
    editor=""
    keywords="máquinas virtuales de copia de seguridad, máquinas virtuales de copia de seguridad"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="trinadhk; jimpark; markgal;"/>

# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planear la infraestructura de copia de seguridad de VM en Azure
Este artículo proporciona rendimiento y sugerencias de recursos que le ayudarán a planear la infraestructura de copia de seguridad de la máquina virtual. También define los aspectos clave del servicio de copia de seguridad; estos aspectos pueden ser críticos en la determinación de la arquitectura, planear y programar la capacidad. Si ha [preparado el entorno](backup-azure-vms-prepare.md), esto es el siguiente paso antes de empezar [a máquinas virtuales de copia de seguridad](backup-azure-vms.md). Si necesita más información sobre máquinas virtuales de Windows Azure, consulte la [documentación de máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>¿Cómo Azure máquinas virtuales de copia de seguridad?
Cuando el servicio de copia de seguridad de Azure inicia un trabajo de copia de seguridad en el tiempo programado, activa la extensión de copia de seguridad para tomar una instantánea de punto en el tiempo. Esta instantánea se toma junto con el servicio de copia de sombra de volumen (VSS) para obtener una instantánea coherente de los discos de la máquina virtual sin tener que salir de ella.

Después de que se toma la instantánea, los datos se transfieren por el servicio de copia de seguridad de Azure en el depósito de copia de seguridad. Para realizar el proceso de copia de seguridad más eficaz, el servicio identifica y transfiere sólo los bloques de datos que han cambiado desde la última copia de seguridad.

![Arquitectura de copia de seguridad de Azure máquina virtual](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Una vez completada la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación.

### <a name="data-consistency"></a>Coherencia de los datos
Realizar copias de seguridad y restaurar datos críticos es complicados por el hecho de que deben hacer copia datos críticos del negocio mientras las aplicaciones que producen los datos de empresa se están ejecutando. Para solucionar esto, copia de seguridad de Azure proporciona copias de seguridad coherentes para la aplicación para las cargas de trabajo de Microsoft mediante VSS para garantizar que los datos se escriben correctamente al almacenamiento.

>[AZURE.NOTE] Para máquinas virtuales de Linux, solo las copias de seguridad coherentes para el archivo son posibles, como Linux no tiene una plataforma equivalente a VSS.

Copia de seguridad de Azure toma las copias de seguridad completas de VSS en máquinas virtuales de Windows (Obtenga más información acerca de la [copia de seguridad completa](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Para habilitar las copias de seguridad de VSS, la debajo del registro debe establecerse en la máquina virtual de clave.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


Esta tabla explica los tipos de coherencia y las condiciones que se producen en Azure VM durante la copia de seguridad y restauración procedimientos.

| Coherencia | Basadas en VSS | Explicación y detalles |
|-------------|-----------|---------|
| Coherencia de aplicación | Sí | Este es el tipo de coherencia ideal para cargas de trabajo de Microsoft como garantiza:<ol><li> La máquina virtual se *inicia*. <li>No hay *ningún daño*. <li>No hay *ninguna pérdida de datos*.<li> Los datos son coherentes con la aplicación que usa los datos por relacionadas con la aplicación en el momento de la copia de seguridad--mediante VSS.</ol> La mayoría de las cargas de trabajo de Microsoft tienen creadores VSS que realizar acciones específicas de carga de trabajo que están relacionadas con la coherencia de los datos. Por ejemplo, Microsoft SQL Server tiene un escritor VSS que se asegura de que la escritura en el archivo de registro de transacciones y la base de datos se realiza correctamente.<br><br> Para Azure VM copias de seguridad, obtener un punto de recuperación consistente con las aplicaciones significa que la extensión de copia de seguridad ha podido invocar el flujo de trabajo VSS y completar *correctamente* antes de que se realizó la instantánea de la máquina virtual. Por supuesto, esto significa que los autores VSS de todas las aplicaciones de la máquina virtual de Azure se ha llamado también.<br><br>Aprenda los [conceptos básicos de VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) y analice en profundidad los detalles de [cómo funciona](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Coherencia del sistema de archivos | Sí, para equipos basados en Windows | Hay dos escenarios donde el punto de recuperación puede ser *coherentes de sistema de archivos*:<ul><li>Copias de seguridad de máquinas virtuales de Linux en Azure, como Linux no tiene una plataforma equivalente a VSS.<li>Error VSS durante la copia de seguridad para máquinas virtuales de Windows en Azure.</li></ul> En ambos casos estos, lo mejor que puede hacer es asegurarse de que: <ol><li> La máquina virtual se *inicia*. <li>No hay *ningún daño*.<li>No hay *ninguna pérdida de datos*.</ol> Las aplicaciones deben implementar su propio mecanismo de "reparación" en los datos restaurados.|
| Coherencia de bloqueo | No | Esta situación es equivalente a una máquina virtual experimentando un "bloqueo" (a través de un restablecimiento suave o duro). Normalmente, esto sucede cuando se cierra la máquina virtual de Azure en el momento de la copia de seguridad. Copias de seguridad de Azure máquina virtual, recibe un medio de punto de recuperación consistente de bloqueo que copia de seguridad de Azure no proporciona ninguna garantía en torno a la coherencia de los datos en el medio de almacenamiento desde la perspectiva del sistema operativo o desde la perspectiva de la aplicación. Solo los datos que ya existe en el disco en el momento de la copia de seguridad están lo que obtiene genera y copia de seguridad. <br/> <br/> Aunque no hay ninguna garantía, en la mayoría de los casos, se iniciará el sistema operativo. Normalmente, esto es seguido un procedimiento de comprobación de disco, como chkdsk, para corregir los errores de daños. Se perderá los datos en memoria o escribe que no se han vaciado completamente en el disco. Normalmente, la aplicación sigue con su propio mecanismo de comprobación en caso de recuperación de datos debe hacerse. <br><br>Por ejemplo, si el registro de transacciones tiene entradas que no están presentes en la base de datos, a continuación, el software de base de datos no deshacer hasta que los datos son coherentes. Cuando se distribuyan datos en varios discos virtuales (por ejemplo, volúmenes distribuidos), un punto de recuperación consistente de bloqueo no proporciona ninguna garantía para la corrección de los datos.|


## <a name="performance-and-resource-utilization"></a>Rendimiento y utilización de recursos
Como software de copia de seguridad está implementado en local, debe planear la capacidad y el uso de recursos necesidades cuando la copia de seguridad de máquinas virtuales en Azure. Los [límites de almacenamiento de Azure](azure-subscription-service-limits.md#storage-limits) definir cómo estructurar las implementaciones de máquina virtual para obtener el máximo rendimiento con un mínimo impacto cargas de trabajo en ejecución.

Preste atención a los siguientes límites de almacenamiento de Azure al planear el rendimiento de copia de seguridad:

- Salida Max por cuenta de almacenamiento
- Tasa de solicitud total por cuenta de almacenamiento

### <a name="storage-account-limits"></a>Límites de almacenamiento de cuenta
Cuando se copian datos de copia de seguridad de una cuenta de almacenamiento, cuenta hacia las operaciones de entrada y salida por segundo (IOPS) y salida (o rendimiento) métricas de la cuenta de almacenamiento. Al mismo tiempo, los equipos virtuales se ejecuta y consumo IOPS y rendimiento. El objetivo es garantizar que el tráfico total - máquina virtual y copia de seguridad: no supere los límites de la cuenta de almacenamiento.

### <a name="number-of-disks"></a>Número de discos
El proceso de copia de seguridad intenta completar un trabajo de copia de seguridad lo antes posible. En este modo, consume recursos tantas como sea posible. No obstante, todas las operaciones de i/OS están limitadas por el *Rendimiento de destino para Blob único*, que tiene un límite de 60 MB por segundo. En un intento de maximizar la velocidad, el proceso de copia de seguridad intenta hacer copia de seguridad de cada uno de los discos *en paralelo de la máquina virtual*. Por lo tanto, si una máquina virtual tiene cuatro discos, copia de seguridad de Azure intenta hacer copia de seguridad de todos los discos en paralelo. Por este motivo, el factor más importante determinar el tráfico de copia de seguridad salir de una cuenta de almacenamiento de cliente el **número de discos** de copia de la cuenta de almacenamiento.

### <a name="backup-schedule"></a>Programación de copia de seguridad
Un factor adicional que afecta al rendimiento es la **programación de copia de seguridad**. Si configura las directivas de modo que se realizan copias de seguridad de todas las máquinas virtuales al mismo tiempo, ha programado un atasco de tráfico. El proceso de copia de seguridad intentará hacer copia de seguridad de todos los discos en paralelo. Es una forma de reducir el tráfico de copia de seguridad de una cuenta de almacenamiento, asegúrese de que se realizan copias de seguridad de las máquinas virtuales distintas a diferentes horas del día, con ninguna superposición.

## <a name="capacity-planning"></a>Planificación de capacidad
Reunir todos estos factores significa que el uso de la cuenta de almacenamiento debe planificar correctamente. Descargue la [hoja de cálculo de Excel planeación de VM capacidad de copia de seguridad](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver el impacto de su disco y opciones de programación de copia de seguridad.

### <a name="backup-throughput"></a>Rendimiento de copia de seguridad
Para cada disco de copia de seguridad, copia de seguridad de Azure lee los bloques en el disco y almacena sólo los datos modificados (copia de seguridad incremental). Esta tabla muestra los valores de rendimiento medio que puede esperar de copia de seguridad de Azure. Con esto, puede calcular la cantidad de tiempo que se tardará en realizar copias de seguridad de un disco de un determinado tamaño.

| Operación de copia de seguridad | Mejor rendimiento |
| ---------------- | ---------- |
| Copia de seguridad inicial | 160 Mbps |
| Copia de seguridad incremental (DR) | 640 Mbps <br><br> Este rendimiento puede anular significativamente si hay una gran cantidad de renovación dispersada en el disco que debe hacer copia de seguridad. |

## <a name="total-vm-backup-time"></a>Tiempo total de copia de seguridad de VM
Mientras que la mayoría de la hora de copia de seguridad se invierte en leer y copiar datos, hay otras operaciones que contribuyen a que el tiempo total necesario para realizar copias de seguridad una máquina virtual:

- Tiempo necesitado para [instalar o actualizar la extensión de copia de seguridad](backup-azure-vms.md#offline-vms).
- Tiempo de instantánea, que es el tiempo necesario para desencadenar una instantánea. Se activan instantáneas cerca de la hora de copia de seguridad.
- Tiempo de espera de cola. Dado que el servicio de copia de seguridad está procesando copias de seguridad de varios clientes, copiar los datos de copia de seguridad de instantánea a la copia de seguridad o depósito de servicios de recuperación posible que no se inicie inmediatamente. Tiempos de picos de carga, la espera puede estirar hasta 8 horas por el número de copias de seguridad que se está procesando. Sin embargo, el tiempo total de copia de seguridad de VM será menor de 24 horas para las directivas de copia de seguridad diarias.

## <a name="best-practices"></a>Prácticas recomendadas
Le recomendamos seguir estos procedimientos recomendados al configurar copias de seguridad de máquinas virtuales de Windows:

- No programar más de cuatro máquinas virtuales clásicas desde el mismo servicio de nube para hacer una copia de seguridad al mismo tiempo. Le recomendamos escalonamiento de horas de inicio de copia de seguridad de una hora si desea hacer copia de seguridad varias VM desde el mismo servicio de nube.
- No programar más de 40 máquinas virtuales implementado por el Administrador de recursos para realizar una copia de seguridad al mismo tiempo.
- Programar las copias de seguridad de la máquina virtual durante las horas para que el servicio de copia de seguridad usa IOPS para transferir datos de la cuenta de almacenamiento de información del cliente a la copia de seguridad o depósito de servicios de recuperación.
- Asegúrese de que una directiva de direcciones máquinas virtuales distribuidos en cuentas de almacenamiento diferente. Le recomendamos no más de 20 discos total de una cuenta de almacenamiento única estar protegido por una directiva. Si tiene mayor que 20 discos en una cuenta de almacenamiento, extendido esas máquinas virtuales por varias directivas para obtener las IOPS necesarias durante la fase de transferencia del proceso de copia de seguridad.
- No restaurar una máquina virtual que se ejecuta en almacenamiento Premium a la misma cuenta de almacenamiento. Si el proceso de la operación de restauración coincide con la operación de copia de seguridad, reduce el IOPS disponible para la copia de seguridad.
- Se recomienda que ejecute cada VM Premium en una cuenta de almacenamiento de distintas premium para garantizar el mejor rendimiento de copia de seguridad.

## <a name="data-encryption"></a>Cifrado de datos

Copia de seguridad de Azure no cifra los datos como parte del proceso de copia de seguridad. Sin embargo, puede cifrar datos dentro de la máquina virtual y copia de los datos protegidos sin problemas (Obtenga más información acerca de la [copia de seguridad de los datos cifrados](backup-azure-vms-encryption.md)).


## <a name="how-are-protected-instances-calculated"></a>¿Cómo se calculan las instancias protegidas?
Máquinas virtuales de Azure que se realizan copias de seguridad a través de la copia de seguridad de Azure están sujetos a [precios de copia de seguridad de Azure](https://azure.microsoft.com/pricing/details/backup/). El cálculo de instancias protegido se basa en el tamaño *real* de la máquina virtual, que es la suma de todos los datos de la máquina virtual, excepto el "disco del recurso".

Está facturado *no* basada en el tamaño máximo admitido para cada disco de datos adjunto a la máquina virtual, pero en los datos reales almacenados en el disco de datos. Del mismo modo, la lista de almacenamiento de copia de seguridad se basa en la cantidad de datos que se almacenan con copia de seguridad de Azure, que es la suma de los datos reales en cada punto de recuperación.

Por ejemplo, realizar una máquina virtual estándar de A2 tamaño que tiene dos discos de datos adicionales con un tamaño máximo de 1 TB. La tabla siguiente proporciona los datos reales almacenados en cada uno de estos discos:

|Tipo de disco|Tamaño máximo|Presentar los datos reales|
|---------|--------|------|
| Disco del sistema operativo | 1023 GB | 17 GB |
| Disco local o disco del recurso | 135 GB | 5 GB (no incluido para la copia de seguridad) |
| Disco de datos 1 | 1023 GB | 30 GB |
| Disco de datos 2 | 1023 GB | 0 GB |

En este caso, el tamaño *real* de la máquina virtual es 17 GB + 30 GB + 0 GB = 47 GB. Se convertirá en el tamaño de instancia protegido que se basa la factura mensual. A medida que crece la cantidad de datos en la máquina virtual, el tamaño de la instancia protegida de facturación también cambiará en consecuencia.

Facturación no se iniciará hasta que se complete la primera copia de seguridad correcta. En este momento, se iniciará la facturación de almacenamiento e instancias protegido. Facturación continúa siempre y cuando *los datos de copia de seguridad almacenados con copia de seguridad de Azure* en la máquina virtual. La operación de detener protección detenga la facturación si se conservan los datos de copia de seguridad.

La facturación de una máquina virtual especificada se se utilizan sólo si la protección está detenido *y* los datos de copia de seguridad se eliminarán. Cuando no hay ningún trabajo de copia de seguridad activa (cuando se ha detenido la protección), el tamaño de la máquina virtual en el momento de la última copia de seguridad correcta, se convierte en el tamaño de instancia protegido que se basa la factura mensual.

## <a name="questions"></a>¿Preguntas?
Si tiene preguntas, o si no hay ninguna característica que le gustaría ver incluyen, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Pasos siguientes

- [Realizar copias de seguridad de máquinas virtuales](backup-azure-vms.md)
- [Administrar la copia de seguridad de máquina virtual](backup-azure-manage-vms.md)
- [Restaurar máquinas virtuales de Windows](backup-azure-restore-vms.md)
- [Solucionar problemas de copia de seguridad de VM](backup-azure-vms-troubleshoot.md)
