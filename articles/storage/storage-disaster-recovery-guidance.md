<properties
    pageTitle="Qué hacer en caso de una interrupción de almacenamiento de Azure | Microsoft Azure"
    description="Qué hacer en caso de una interrupción de almacenamiento de Azure"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Qué hacer si se produce una interrupción de almacenamiento de Azure

En Microsoft, nos trabajar duro para asegurarse de que nuestros servicios siempre están disponibles. A veces, fuerza más allá de nuestro impacto control nos de maneras que producen interrupciones de servicio planificadas en una o varias áreas. Para ayudarle a controlar estas repeticiones raras, le proporcionamos las siguientes directrices de alto nivel para los servicios de almacenamiento de Azure.

## <a name="how-to-prepare"></a>Para preparar 

Es fundamental para todos los clientes preparar su propio plan de recuperación. El esfuerzo recuperarse de una interrupción de almacenamiento normalmente implica personal de operaciones y procedimientos automatizados para reactivar las aplicaciones en un estado de funcionamiento. Consulte la documentación de Azure siguiente para crear su propio plan de recuperación:

-   [Recuperación y alta disponibilidad para las aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Orientación técnica de la resistencia de Azure](../resiliency/resiliency-technical-guidance.md)

-   [Servicio de recuperación de sitio de Azure](https://azure.microsoft.com/services/site-recovery/)

-   [Replicación de almacenamiento de Azure](storage-redundancy.md)

-   [Servicio de copia de seguridad de Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Cómo detectar 

La manera recomendada para determinar el estado del servicio de Azure es suscribirse a [Panel de estado del servicio de Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Qué hacer si se produce un corte de almacenamiento

Si uno o más servicios de almacenamiento disponibles temporalmente en una o más regiones, hay dos opciones para tener en cuenta. Si desea tener acceso inmediato a los datos, considere la opción 2.

### <a name="option-1-wait-for-recovery"></a>Opción 1: Esperar recuperación

En este caso, se requiere ninguna acción por su parte. Estamos trabajando diligentemente para restaurar la disponibilidad de los servicios de Azure. Puede supervisar el estado del servicio en el [Panel de estado del servicio de Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opción 2: Copie datos de secundaria

Si ha elegido el [almacenamiento de acceso de lectura geo redundantes (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado) para las cuentas de almacenamiento, tendrá acceso de lectura a los datos de la región secundaria. Puede usar herramientas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)y la [biblioteca de movimiento de datos de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar datos de la región secundaria a otra cuenta de almacenamiento en una región unimpacted y, a continuación, seleccione las aplicaciones a esa cuenta de almacenamiento para ambos leer y escribir disponibilidad.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Qué puede esperar si se produce un error de almacenamiento

Si eligió [almacenamiento Geo redundantes (GRS)](storage-redundancy.md#geo-redundant-storage) o [almacenamiento de acceso de lectura geo redundantes (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado), el almacenamiento de Azure conserva los datos resistentes en dos regiones (primario y secundario). En ambas regiones, almacenamiento Azure constantemente mantiene varias réplicas de los datos.

Cuando un desastre regional afecta a su región principal, primero se intentará restaurar el servicio de esa región. Depende de la naturaleza de desastre y sus efectos, en algunas ocasiones que no podamos restaurar el área principal. En ese momento, llevará a cabo un error de geo. La replicación de datos entre región es un proceso asincrónico que puede implicar un retraso, por lo que es posible que se pierdan cambios que aún no se ha duplicado a la región secundaria. Puede consultar la ["hora de última sincronización" de la cuenta de almacenamiento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) para obtener detalles sobre el estado de replicación.

Un par de puntos importantes sobre la experiencia de migración tras error de geo de almacenamiento:

-   Almacenamiento geo failover solo se activará por el equipo de almacenamiento de Azure: no se requiere ninguna acción del usuario.

-   Sus extremos existente del servicio de almacenamiento de blobs, tablas, colas y archivos permanecerán igual después de la migración; la entrada DNS deberá actualizarse para cambiar de la región principal a la región secundaria.

-   Antes y durante la migración geo-tras error, no tendrá acceso de escritura a su cuenta de almacenamiento por el impacto del desastre pero aún puede leer desde el secundario si se ha configurado su cuenta de almacenamiento como RA GRS.

-   Cuando se ha completado la recuperación geo-ante fallos y propagarán los cambios DNS, puede reanudar la lectura y el acceso de escritura a su cuenta de almacenamiento. Puede crear una consulta ["Geo migración tras error hora del último" de la cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/ee460802.aspx) para obtener más detalles.

-   Después del error, su cuenta de almacenamiento totalmente funcionará, pero en un estado de "Degradado", tal y como se hospeda realmente en una región independiente con no es posible la replicación de geo. Para reducir el riesgo, revisaremos restaurar la región principal original y, a continuación, realice una recuperación geo para restaurar el estado original. Si la región principal original es no recuperable, se asignará a otra región secundaria.
Para obtener más detalles sobre la infraestructura de replicación de almacenamiento de Azure geo, consulte el artículo sobre el blog del equipo de almacenamiento de información sobre las [Opciones de redundancia y RA GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##<a name="best-practices-for-protecting-your-data"></a>Prácticas recomendadas para proteger los datos

Hay algunos enfoques recomendados para realizar copias de seguridad de sus datos de almacenamiento de forma regular.

-   Discos de VM: Use el [servicio de copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) para realizar copias de seguridad de los discos VM utilizados por sus máquinas virtuales Azure.

-   Bloque blobs: crear una [instantánea](https://msdn.microsoft.com/library/azure/hh488361.aspx) de cada blob bloque o copiar el BLOB al almacenamiento de otra cuenta en otra región con [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)o la [biblioteca de movimiento de datos de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tablas: use [AzCopy](storage-use-azcopy.md) para exportar los datos de la tabla a otra cuenta de almacenamiento en otra región.

-   Archivos: usar [AzCopy](storage-use-azcopy.md) o [Azure PowerShell](storage-powershell-guide-full.md) para copiar los archivos a otra cuenta de almacenamiento en otra región.
