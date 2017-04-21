<properties 
   pageTitle="¿Qué es el Administrador de instantáneas StorSimple? | Microsoft Azure"
   description="Describe el Administrador de instantáneas StorSimple, su arquitectura y sus características."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="what-is-storsimple-snapshot-manager"></a>¿Qué es el Administrador de instantáneas StorSimple?

## <a name="overview"></a>Información general

Administrador de instantáneas StorSimple es un complemento de consola de administración de Microsoft (MMC) que simplifica la protección de datos y administración de copia de seguridad en un entorno de Microsoft Azure StorSimple. Con el Administrador de instantáneas StorSimple, puede administrar los datos en el centro de datos y en la nube de Microsoft Azure StorSimple como una solución de almacenamiento de información integrada, por tanto, simplificar los procesos de copia de seguridad y reducir los costos.

Esta introducción presenta al administrador de instantáneas StorSimple, describe sus características y explica su función en Microsoft Azure StorSimple. 

Para obtener información general de todo el sistema de Microsoft Azure StorSimple, incluido el dispositivo StorSimple, servicio Administrador StorSimple, Administrador de instantáneas de StorSimple y StorSimple adaptador de SharePoint, vea [serie 8000 StorSimple: una solución de almacenamiento de nube híbrida](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- No puede usar el Administrador de instantáneas StorSimple para administrar Microsoft Azure StorSimple Virtual matrices (también conocido como StorSimple local dispositivos virtuales).
>
>- Si planea instalar StorSimple 2 en el dispositivo StorSimple, asegúrese de descargar la versión más reciente del Administrador de instantánea StorSimple e instálelo **antes de instalar la actualización StorSimple 2**. La versión más reciente del Administrador de instantánea StorSimple es compatible con versiones anteriores y funciona con todas las versiones publicadas de Microsoft Azure StorSimple. Si está utilizando la versión anterior del Administrador de instantánea StorSimple, deberá actualizarlo (no es necesario desinstalar la versión anterior antes de instalar la versión nueva).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Arquitectura y finalidad del Administrador de instantáneas StorSimple

Administrador de instantáneas StorSimple proporciona una consola de administración central que puede usar para crear copias de seguridad en el momento de local y datos de la nube. Por ejemplo, puede usar la consola de:

- Configurar, realizar copias de seguridad y eliminar volúmenes.
- Configurar grupos de volumen para asegurarse de que la copia de seguridad de datos es coherente para la aplicación.
- Administrar directivas de copia de seguridad para que se realizan copias de seguridad de datos en una programación predeterminada.
- Crear local y la nube instantáneas, que pueden almacenadas en la nube y utilizarlo para recuperación.

El Administrador de instantáneas StorSimple búsquedas en la lista de aplicaciones registradas con el proveedor VSS en el host. A continuación, para crear copias de seguridad coherentes para la aplicación, comprueba los volúmenes utilizados por una aplicación y sugiere grupos de volumen para configurar. Administrador de instantáneas StorSimple usa estos grupos de volumen para generar copias de seguridad son coherentes para la aplicación. (Coherencia de aplicación existe cuando los archivos correspondientes y bases de datos se sincronizan y representan el verdadero estado de la aplicación en un punto específico en el tiempo). 

Copias de seguridad de administrador de instantáneas StorSimple tomar la forma de instantáneas incrementales que capturar sólo los cambios desde la última copia. Como resultado, las copias de seguridad requieren menos almacenamiento y pueden crear y restaurar rápidamente. Administrador de instantáneas StorSimple usa el servicio de copia de sombra volumen (VSS) de Windows para asegurarse de que las instantáneas capturan datos coherentes para la aplicación. (Para obtener más información, vaya a la integración con la sección servicio de Windows volumen instantáneas). Con el Administrador de instantáneas StorSimple, puede crear programaciones de copia de seguridad o realizar copias de seguridad inmediatos según sea necesario. Si necesita restaurar los datos de una copia de seguridad, Administrador de instantáneas de StorSimple permite seleccionar desde un catálogo de local o instantáneas de la nube. StorSimple Azure restaura solo los datos que se necesitan según sea necesario, que evita retrasos en la disponibilidad de los datos durante las operaciones de restauración).

![Arquitectura de administrador de instantáneas StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitectura de administrador de instantáneas StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Compatibilidad con varios tipos de volumen

Puede usar el Administrador de instantáneas StorSimple para configurar y realizar copias de seguridad de los siguientes tipos de volúmenes: 

- **Volúmenes básicos** : un volumen básico es una sola partición en un disco básico. 

- **Volúmenes simples** : un volumen simple es un volumen dinámico que contiene el espacio de un único disco dinámico. Un volumen simple consta de una sola región en un disco o múltiples regiones vinculadas entre sí en el mismo disco. (Puede crear volúmenes simples en discos dinámicos). Volúmenes simples no son tolerancia.

- **Volúmenes dinámicos** : un volumen dinámico es un volumen creado en un disco dinámico. Los discos dinámicos utilizan una base de datos para realizar un seguimiento de información acerca de los volúmenes que se encuentran en discos dinámicos en un equipo. 

- **Volúmenes dinámicos con reflejo** : volúmenes dinámicos con reflejo se basan en la arquitectura de RAID 1. Con RAID 1, datos idénticos se escriben en el disco de dos o más, para producir un conjunto reflejado. Una solicitud de lectura, a continuación, puede corregirse por cualquier disco que contiene los datos solicitados.

- **Volúmenes compartidos de clúster** : con volúmenes compartidos de clúster (CSVs), varios nodos en un clúster simultáneamente pueden leer o escribir en el mismo disco. Migración tras error de un nodo a otro nodo puede ocurrir rápidamente, sin necesidad de un cambio en la propiedad de la unidad o montaje, desmontaje y quitar un volumen. 

>[AZURE.IMPORTANT] No mezcle CSVs y no CSVs en la misma instantánea. No se admite la mezcla de CSVs y no CSVs en una instantánea. 
 
Puede usar el Administrador de instantáneas StorSimple para restaurar los grupos de todo el volumen o clonar volúmenes individuales y recuperar archivos individuales.

- [Volúmenes y grupos de volumen](#volumes-and-volume-groups) 
- [Tipos de copia de seguridad y directivas de copia de seguridad](#backup-types-and-backup-policies) 

Para obtener más información sobre las características de administrador de instantáneas StorSimple y cómo usarlas, vea la [interfaz de usuario de administrador de instantáneas StorSimple](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volúmenes y grupos de volumen

Con el Administrador de StorSimple instantáneas, crear volúmenes y configúrelo en grupos de volumen. 

Administrador de instantáneas StorSimple usa grupos de volumen para crear copias de seguridad son coherentes para la aplicación. Coherencia de aplicación existe cuando los archivos correspondientes y bases de datos se sincronizan y representan el verdadero estado de una aplicación en un punto específico en el tiempo. Grupos de volumen (que también se conoce como *grupos de coherencia*) constituyen la base de una copia de seguridad o restauración el trabajo.

Grupos de volumen no son los mismos como contenedores de volumen. Un contenedor de volumen contiene uno o más volúmenes que tienen una cuenta de almacenamiento de la nube y otros atributos, como el consumo de ancho de banda y cifrado. Un contenedor de volumen solo puede contener hasta 256 volúmenes StorSimple thin aprovisionados. Para obtener más información acerca de los contenedores de volumen, vaya a [administrar los contenedores de volumen](storsimple-manage-volume-containers.md). Grupos de volumen son colecciones de volúmenes que configurar para facilitar las operaciones de copia de seguridad. Si selecciona dos volúmenes que pertenecen a los contenedores de volumen diferente, colocan en un grupo de volumen único y, a continuación, crear una directiva de copia de seguridad para dicho grupo de volumen, cada volumen se copiarán en el contenedor de volumen correspondiente, con la cuenta de almacenamiento apropiado.

>[AZURE.NOTE] Todos los volúmenes en un grupo de volumen deben proceder de un proveedor de servicios de nube solo.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integración con el servicio de instantáneas de volumen Windows

Administrador de instantáneas StorSimple usa el servicio de copia de sombra volumen (VSS) de Windows para capturar datos coherentes para la aplicación. VSS facilita la coherencia de la aplicación mediante la comunicación con aplicaciones compatibles con VSS para coordinar la creación de instantáneas incrementales. VSS garantiza que las aplicaciones estén temporalmente inactivo o inactiva, cuando se toman instantáneas. 

La implementación del Administrador de instantánea StorSimple de VSS funciona con SQL Server y volúmenes NTFS genéricos. El proceso es como sigue: 

1. Un solicitante, que normalmente es una administración de datos y solución de protección (por ejemplo, StorSimple instantánea administrador) o una aplicación de copia de seguridad, invoca VSS y se le pide que recopile información de software de escritura en la aplicación de destino.

2. VSS el componente escritor para recuperar una descripción de los datos de contactos. El escritor devuelve la descripción de los datos de copia de seguridad. 

3. VSS indica el escritor para preparar la aplicación de copia de seguridad. El escritor prepara los datos de copia de seguridad Complete las transacciones abiertas, actualizar registros de transacciones y así sucesivamente y, a continuación, le notifica VSS.

4. VSS indica que el escritor temporalmente detener almacena datos de la aplicación y asegúrese de que no se escriben datos en el volumen mientras se crea la instantánea. Este paso asegura la coherencia de los datos y toma de no más de 60 segundos.

5. VSS indica que el proveedor de crear la instantánea. Proveedores, que pueden ser según de software o hardware, administran los volúmenes que se están ejecutando actualmente y crear instantáneas de ellos a petición. El proveedor crea la instantánea y notifica VSS cuando se complete.

6. VSS el escritor para notificar a la aplicación que puede reanudar i/OS y también para confirmar que la i/OS se detuvo correctamente durante la creación de instantáneas de contactos. 

7. Si la copia se realizó correctamente, VSS devuelve la ubicación de la copia al solicitante. 

8. Si se escriben datos mientras se creó la instantánea, la copia de seguridad será incoherente. VSS elimina la instantánea y notifique al solicitante. El solicitante puede repetir el proceso de copia de seguridad automáticamente o notificar al administrador que vuelva a intentar más tarde.

Vea la siguiente ilustración.

![Proceso de VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proceso de servicio de Windows volumen instantáneas** 

## <a name="backup-types-and-backup-policies"></a>Tipos de copia de seguridad y directivas de copia de seguridad

Con el Administrador de instantáneas StorSimple, puede realizar copias de seguridad de datos y guardarlo localmente y en la nube. Puede usar el Administrador de instantáneas StorSimple para copiar los datos inmediatamente, o puede utilizar una directiva de copia de seguridad para crear una programación para realizar copias de seguridad automáticamente. Directivas de copia de seguridad también le permiten especificar cuántas copias instantáneas se conservarán. 

### <a name="backup-types"></a>Tipos de copia de seguridad

Puede usar el Administrador de instantáneas StorSimple para crear los siguientes tipos de copias de seguridad:

- **Instantáneas locales** : instantáneas locales son copias instantáneas de los datos de volumen que se almacenan en el dispositivo StorSimple. Normalmente, este tipo de copia de seguridad se puede crear y restaurar rápidamente. Puede utilizar una instantánea local como lo haría con una copia local.

- **Instantáneas de nube** – nube instantáneas son copias instantáneas de los datos de volumen que se almacenan en la nube. Una instantánea de la nube es equivalente a una instantánea replicada en un sistema de almacenamiento diferente, fuera del sitio. Instantáneas de nube son particularmente útiles en escenarios de recuperación de desastres.

### <a name="on-demand-and-scheduled-backups"></a>Copias de seguridad a petición y programadas

Con el Administrador de instantáneas StorSimple, puede iniciar una única copia de seguridad que se creen inmediatamente o puede utilizar una directiva de copia de seguridad para programar operaciones de copia de seguridad periódicas.

Una directiva de copia de seguridad es un conjunto de reglas automatizados que puede usar para programar copias de seguridad periódicas. Una directiva de copia de seguridad le permite definir los parámetros de tomar instantáneas de un grupo específico de volumen y frecuencia. Puede utilizar las directivas para especificar fechas de inicio y vencimiento, horas, frecuencias y requisitos de retención para ambos local y la nube instantáneas. Una directiva se aplica inmediatamente después de definirla. 

Puede usar el Administrador de instantáneas StorSimple configurar o volver a configurar directivas de copia de seguridad cuando sea necesario. 

Configurar la siguiente información para cada directiva de copia de seguridad que cree:

- **Nombre** : el nombre único de la directiva de copia de seguridad seleccionada.

- **Tipo** : el tipo de directiva de copia de seguridad; instantánea local o en la instantánea de la nube.

- **Grupo de volumen** : el grupo de volumen a la que está asignada la directiva de copia de seguridad seleccionada.

- **Retención** , el número de copias de seguridad para conservar. Si activa la casilla **todo** , todas las copias de seguridad se conservan hasta que se alcance el número máximo de copias de seguridad por volumen, momento en que la directiva se producirá un error y generar un mensaje de error. Como alternativa, puede especificar un número de copias de seguridad para conservar (entre 1 y 64).

- **Fecha** : la fecha de creación de la directiva de copia de seguridad.

Para obtener información sobre la configuración de directivas de copia de seguridad, vaya a [Usar el Administrador de instantánea StorSimple para crear y administrar las directivas de copia de seguridad](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Administración y supervisión de trabajo de copia de seguridad

Puede usar el Administrador de instantáneas StorSimple para supervisar y administrar trabajos de copia de seguridad próximos, programados y completados. Además, Administrador de instantáneas StorSimple proporciona un catálogo de copias de seguridad completadas hasta 64. Puede usar el catálogo para encontrar y restaurar archivos individuales o volúmenes. 

Para obtener información acerca de cómo supervisar trabajos de copia de seguridad, vaya a [Usar el Administrador de instantánea StorSimple para ver y administrar trabajos de copia de seguridad](storsimple-snapshot-manager-manage-backup-jobs.md).


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [uso de administrador de instantáneas StorSimple para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).

- Descargar el [Administrador de instantáneas StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).
