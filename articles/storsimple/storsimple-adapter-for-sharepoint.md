<properties 
   pageTitle="Adaptador StorSimple para SharePoint | Microsoft Azure"
   description="Describe cómo instalar y configurar o quitar el adaptador StorSimple de SharePoint en una granja de servidores de SharePoint."
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
   ms.date="07/11/2016"
   ms.author="v-sharos" />

# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalar y configurar el adaptador StorSimple para SharePoint

## <a name="overview"></a>Información general

El adaptador StorSimple para SharePoint es un componente que le permite proporcionar almacenamiento de Microsoft Azure StorSimple flexible y protección de datos en granjas de servidores de SharePoint. Puede usar el adaptador para mover el contenido de un objeto grande binario (BLOB) de las bases de datos de contenido de SQL Server para el dispositivo de almacenamiento de nube de Microsoft Azure StorSimple híbrido.

El adaptador StorSimple para SharePoint funciona como un proveedor de servicios de almacenamiento de blobs remoto (EDR) y usa la característica de almacenamiento remoto de blobs de SQL Server para almacenar el contenido de SharePoint no estructurado (en forma de BLOB) en un servidor de archivos que se copia mediante un dispositivo de StorSimple.

>[AZURE.NOTE] El adaptador StorSimple para SharePoint admite el almacenamiento de blobs de remoto (EDR) de SharePoint Server 2010. No admite el almacenamiento de blobs de externo (EBS) de SharePoint Server 2010.

- Para descargar el adaptador StorSimple de SharePoint, vaya a [StorSimple adaptador para SharePoint] [ 1] en Microsoft Download Center.

- Para obtener información sobre la planeación de EDR y EDR limitaciones, vaya a [decidir usar EDR en SharePoint 2013] [ 2] o [Planear EDR (SharePoint Server 2010)][3].

El resto de esta introducción describe brevemente el rol del adaptador StorSimple para SharePoint y los límites de capacidad y el rendimiento de SharePoint que debe tener en cuenta antes de instalar y configurar el adaptador. Después de revisar esta información, vaya a [StorSimple adaptador de instalación de SharePoint](#storsimple-adapter-for-sharepoint-installation) para empezar a configurar el adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adaptador StorSimple para los beneficios de SharePoint

En un sitio de SharePoint, el contenido se almacena como datos BLOB no estructurados en una o más bases de datos de contenido. De forma predeterminada, estas bases de datos se hospedan en equipos que ejecutan SQL Server y se encuentran en la granja de servidores de SharePoint. BLOB puede aumentar rápidamente de tamaño, consumir grandes cantidades de almacenamiento local. Por este motivo, podría desea buscar solución de otro almacenamiento menos costosas. SQL Server proporciona una tecnología denominada remoto Blob almacenamiento (EDR) que le permite almacenar el contenido BLOB en el sistema de archivos, fuera de la base de datos de SQL Server. Con EDR, BLOB puede residen en el sistema de archivos en el equipo que ejecuta SQL Server o pueden almacenarse en el sistema de archivos en otro equipo servidor.

EDR requiere el uso de un proveedor de EDR, como el adaptador StorSimple para SharePoint, para habilitar EDR en SharePoint. El adaptador StorSimple para SharePoint funciona con EDR, lo que le permite mover BLOB a un servidor de copia de seguridad por el sistema de Microsoft Azure StorSimple. Microsoft Azure StorSimple, a continuación, almacena los datos BLOB localmente o en la nube, basado en uso. BLOB que están muy activa (suele denominarse nivel 1 o datos activos) reside localmente. Datos menos activos y archivado se encuentran en la nube. Después de habilitar EDR en una base de datos de contenido, cualquier contenido BLOB nuevo creado en SharePoint se almacena en el dispositivo StorSimple y no en la base de datos de contenido.

La implementación de Microsoft Azure StorSimple de EDR proporciona las siguientes ventajas:

- Al mover el contenido BLOB en un servidor independiente, puede reducir la carga de consultas en SQL Server, que puede mejorar la capacidad de respuesta de SQL Server. 

- StorSimple Azure usa compresión y deduplicación para reducir el tamaño de los datos.

- StorSimple Azure proporciona protección de datos en el formulario de local y la nube instantáneas. Además, si coloca la base de datos en el dispositivo StorSimple, puede revertir la base de datos de contenido y BLOB juntos de forma coherente bloqueo. (Solo se admite el movimiento de la base de datos de contenido en el dispositivo para el dispositivo de la serie 8000 StorSimple. Esta característica no es compatible con la serie 5000 o 7000.)

- Azure StorSimple incluye características de recuperación de desastres incluidos migración tras error, recuperación de archivos y volumen (incluida la recuperación de prueba) y restauración rápida de datos.

- Puede usar el software de recuperación de datos, como Kroll Ontrack PowerControls, con instantáneas de StorSimple de datos BLOB para realizar la recuperación de nivel de elemento del contenido de SharePoint. (Este software de recuperación de datos es una compra independiente).

- El adaptador StorSimple de SharePoint se conecta al portal de Administración Central de SharePoint, que le permite administrar la solución de SharePoint completa desde una ubicación central.

Mover el contenido BLOB al sistema de archivos puede proporcionar otros ahorros y beneficios. Por ejemplo, usando EDR puede reducir la necesidad de costosa almacenamiento de nivel 1 y, porque reduce el tamaño de la base de datos de contenido, EDR puede reducir el número de bases de datos necesarios en la granja de servidores de SharePoint. Sin embargo, otros factores, como los límites de tamaño de base de datos y la cantidad de contenido no EDR, también puede afectar a los requisitos de almacenamiento. Para obtener más información acerca de los costes y las ventajas de usar EDR, vea [Planear EDR (SharePoint Foundation 2010)] [ 4] y [decidir usar EDR en SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Límites de capacidad y rendimiento

Antes de usar EDR en la solución de SharePoint, debe tener en cuenta de los límites de capacidad de SharePoint Server 2010 y SharePoint Server 2013 y cómo se relacionan estos límites rendimiento aceptable y rendimiento probado. Para obtener más información, consulte [límites de Software y los límites de SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Revise lo siguiente antes de configurar EDR:

- Asegúrese de que el tamaño total del contenido (el tamaño de una base de datos de contenido) más el tamaño de cualquier BLOBs asociados externos no supere el límite de tamaño de EDR compatible con SharePoint. Este límite es 200 GB. 

    **Base de datos de contenido de medida y tamaño BLOB**

     1. Ejecutar esta consulta en la WFE Administración Central. Inicie el Shell de administración de SharePoint y, a continuación, escriba el siguiente comando de Windows PowerShell para obtener el tamaño de las bases de datos de contenido:

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         En este paso, se obtiene el tamaño de la base de datos de contenido en el disco.

     2. Ejecute una de las siguientes consultas SQL en SQL Management Studio en el cuadro SQL server en cada base de datos de contenido y agregar el resultado con el paso obtenido en número 1.

        En SharePoint 2013 bases de datos de contenido, escriba:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        En bases de datos de contenido SharePoint 2010, escriba:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        Este paso, obtiene el tamaño de los objetos binarios que han sido externalizar.

- Se recomienda almacenar todo el contenido de base de datos y BLOB localmente en el dispositivo StorSimple. El dispositivo de StorSimple es un clúster de dos nodos para alta disponibilidad. Colocación de las bases de datos de contenido y BLOB en el dispositivo StorSimple proporciona alta disponibilidad.

    Usar tradicionales procedimientos recomendados de migración de SQL Server para mover la base de datos de contenido en el dispositivo StorSimple. Mover la base de datos solo después de que todo el contenido BLOB de la base de datos se ha movido a archivos compartidos mediante EDR. Si decide mover la base de datos de contenido en el dispositivo StorSimple, le recomendamos que configure el almacenamiento de contenido de la base de datos en el dispositivo como un volumen principal.

- En Microsoft Azure StorSimple si usa volúmenes en niveles, no hay ninguna manera garantiza que almacenan localmente en el dispositivo no se en niveles al almacenamiento de nube de Microsoft Azure de StorSimple el contenido. Por lo tanto, se recomienda utilizar volúmenes StorSimple anclado localmente junto con SharePoint RBS. Se asegurará de que todo el contenido BLOB permanece localmente en el dispositivo de StorSimple y no se mueve a Microsoft Azure.

- Si no se almacenan las bases de datos de contenido en el dispositivo StorSimple, utilice tradicional SQL Server alta disponibilidad recomendaciones que admiten EDR. El clúster de SQL Server admite EDR, mientras SQL Server que coinciden con las opciones no. 

>[AZURE.WARNING] Si no ha habilitado EDR, no es recomendable mover la base de datos de contenido en el dispositivo StorSimple. Se trata de una configuración sin probar.
 
## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adaptador StorSimple de instalación de SharePoint

Antes de poder instalar al adaptador StorSimple de SharePoint, debe configurar el dispositivo de StorSimple y asegúrese de que la granja de servidores de SharePoint y la creación de instancia de SQL Server cumplen todos los requisitos previos. Este tutorial describe los requisitos de configuración, así como procedimientos para instalar y actualizar el adaptador StorSimple para SharePoint. 

## <a name="configure-prerequisites"></a>Configurar los requisitos previos

Antes de poder instalar al adaptador StorSimple de SharePoint, asegúrese de que el dispositivo de StorSimple, la granja de servidores de SharePoint y la creación de instancia de SQL Server cumplen los siguientes requisitos previos.

### <a name="system-requirements"></a>Requisitos del sistema

El adaptador StorSimple para SharePoint funciona con el hardware y software siguiente:

- Sistema operativo: Windows Server 2008 R2 SP1, Windows Server 2012 o Windows Server 2012 R2 

- Versiones de SharePoint compatibles: SharePoint Server 2010 o SharePoint Server 2013

- Versiones de SQL Server compatibles: SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition o SQL Server 2012 Enterprise Edition

- Dispositivos de StorSimple – compatibles serie 8000 StorSimple, serie StorSimple 7000 o serie 5000 StorSimple.

### <a name="storsimple-device-configuration-prerequisites"></a>Requisitos previos de configuración de dispositivo StorSimple

El dispositivo de StorSimple es un dispositivo de bloque y así requiere un servidor de archivos en el que se pueden alojar los datos. Se recomienda usar un servidor independiente en lugar de un servidor existente desde el conjunto de servidores de SharePoint. Este servidor de archivos debe estar en la misma red de área local (LAN) que el equipo de SQL Server que aloja las bases de datos de contenido. 

>[AZURE.TIP] 
>
>- Si configura su granja de SharePoint para alta disponibilidad, debe implementar también el servidor de archivos de alta disponibilidad.
>
>- Si no se almacenan la base de datos de contenido en el dispositivo StorSimple, use tradicional alta disponibilidad los procedimientos recomendados que admiten EDR. El clúster de SQL Server admite EDR, mientras SQL Server que coinciden con las opciones no. 

Asegúrese de que el dispositivo StorSimple está configurado correctamente y que están configurados volúmenes adecuados para la implementación de SharePoint y accesible desde el equipo de SQL Server. Vaya a [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough.md) si aún no ha implementado y ha configurado su dispositivo StorSimple. Anote la dirección IP del dispositivo StorSimple; se necesita durante StorSimple adaptador de instalación de SharePoint. 

Además, asegúrese de que el volumen que se usará para externalización BLOB cumple los requisitos siguientes:

- El volumen debe tener el formato con un tamaño de unidad de asignación de 64 KB.

- Los servidores de aplicaciones y web front-end (WFE) puedan tener acceso al volumen a través de una ruta de acceso de la convención de nomenclatura Universal (UNC). 

- El conjunto de servidores de SharePoint debe estar configurado para escribir en el volumen.

>[AZURE.NOTE] Después de instalar y configurar el adaptador, todos los externalización BLOB debe ir a través del dispositivo de StorSimple (el dispositivo presentar los volúmenes a SQL Server y administrar los niveles de almacenamiento). No puede usar cualquier otro destino para externalización BLOB.
 
Si planea usar el Administrador de instantáneas StorSimple tomar instantáneas de los datos BLOB y base de datos, asegúrese de instalar a StorSimple instantánea administrador en el servidor de base de datos para que pueda usar el servicio del escritor SQL para implementar el servicio de copia de sombra volumen (VSS) de Windows. 

>[AZURE.IMPORTANT] Administrador de instantáneas StorSimple no admite el escritor de VSS de SharePoint y no puede tomar instantáneas coherentes para la aplicación de datos de SharePoint. En un escenario de SharePoint, Administrador de instantáneas StorSimple proporciona sólo bloqueo coherente copias de seguridad. 
 
## <a name="sharepoint-farm-configuration-prerequisites"></a>Requisitos previos de configuración del conjunto de servidores de SharePoint

Asegúrese de que su granja de servidores de SharePoint está configurado correctamente, como sigue:

- Compruebe que su granja de servidores de SharePoint está en buen estado y compruebe lo siguiente: 

- Todos los WFE SharePoint y registrados en la granja de servidores de aplicaciones se están ejecutando y se pueden hacer ping desde el servidor en el que va a instalar al adaptador StorSimple para SharePoint.

- Se ejecuta el servicio de temporizador de SharePoint (SPTimerV3 o SPTimerV4) en cada servidor WFE y el servidor de aplicaciones.

- El servicio de temporizador de SharePoint y el grupo de aplicaciones de IIS en el que se está ejecutando el sitio de Administración Central de SharePoint tienen privilegios de administrador. 

- Asegúrese de que el contexto de seguridad mejorada de Internet Explorer (IE ESC) está deshabilitado. Siga estos pasos para deshabilitar ESC de Internet Explorer:

    1. Cierre todas las instancias de Internet Explorer.

    2. Inicie el administrador del servidor.

    3. En el panel izquierdo, haga clic en el **Servidor Local**.

    4. En el panel derecho, junto a la **Configuración de seguridad mejorada de Internet Explorer**, haga clic **en**.

    5. **Los administradores**, haga clic en **desactivar**.

    6. Haga clic en **Aceptar**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Requisitos previos de almacenamiento de blobs de trabajo (EDT) remoto

Asegúrese de que está usando una versión compatible de SQL Server. Solo las versiones siguientes son compatibles y pueden usar EDR:

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

Es posible externalizar BLOB en solo volúmenes de dispositivo de StorSimple se presenta a SQL Server. Ningún otro destino para externalización blobs es compatibles.

Cuando haya completado todos los pasos de configuración de requisitos previos, vaya a [instalar el adaptador StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalar al adaptador StorSimple para SharePoint

Realice los pasos siguientes para instalar al adaptador StorSimple para SharePoint. Si se vuelve a instalar el software, consulte [actualizar o volver a instalar el adaptador StorSimple para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). El tiempo necesario para la instalación depende del número total de bases de datos de SharePoint en su granja de servidores de SharePoint.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## <a name="configure-rbs"></a>Configurar EDR

Después de instalar al adaptador StorSimple para SharePoint, configure EDR como se describe en el procedimiento siguiente.

>[AZURE.TIP] El adaptador StorSimple de SharePoint se conecta a la página de Administración Central de SharePoint, lo EDR para habilitar o deshabilitar en cada base de datos de contenido del conjunto de servidores de SharePoint. Sin embargo, habilitar o deshabilitar EDR en la base de datos de contenido provoca un restablecer IIS, que momentáneamente puede afectar a la disponibilidad de SharePoint web front-end (WFE) según la configuración de la granja. (Factores como el uso de un equilibrador de carga de front-end, la carga de trabajo actual y así sucesivamente, pueden limitar o eliminar esta interrupción). Para proteger a los usuarios de una interrupción, le recomendamos que habilitar o deshabilitar EDR solo durante una ventana de mantenimiento planeado.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## <a name="configure-garbage-collection"></a>Configurar recolección

Cuando se eliminan objetos de un sitio de SharePoint, no se eliminan automáticamente desde el volumen de almacenamiento de EDR. En su lugar, un asincrónica, programa de mantenimiento de fondo elimina huérfano BLOB desde el almacén de archivos. Los administradores del sistema pueden programar este proceso para ejecutarse periódicamente o puede iniciar siempre que sea necesario.

Este programa de mantenimiento (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) se instala automáticamente en todos los servidores de SharePoint WFE y aplicación al habilitar EDR. El programa está instalado en la siguiente ubicación: *unidad de inicio*: \Program Files\Microsoft almacenamiento de blobs de SQL remoto 10.50\Maintainer\

Para obtener información acerca de la configuración y el uso del programa de mantenimiento, vea [Mantener EDR en SharePoint Server 2013][8].

>[AZURE.IMPORTANT] El programa de encargado de EDR es recursos. Debe programar que se ejecute sólo durante los períodos de actividad de luz en el conjunto de servidores de SharePoint.

### <a name="delete-orphaned-blobs-immediately"></a>Eliminar huérfano BLOB inmediatamente

Si necesita eliminar BLOB huérfano inmediatamente, puede usar las siguientes instrucciones. Tenga en cuenta que estas instrucciones son un ejemplo de cómo se puede hacer esto en un entorno de SharePoint 2013 con los siguientes componentes:

- El nombre de la base de datos de contenido es WSS_Content.
- El nombre de SQL Server es SHRPT13 SQL12\SHRPT13.
- El nombre de la aplicación web es SharePoint-80.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Actualizar o volver a instalar el adaptador StorSimple para SharePoint

Utilice el procedimiento siguiente para actualizar el servidor de SharePoint y, a continuación, vuelva a instalar StorSimple adaptador de SharePoint o para simplemente actualizar o volver a instalar el adaptador en una granja de servidores de SharePoint existente. 

>[AZURE.IMPORTANT] Revise la información siguiente antes de intentar actualizar el software de SharePoint o una actualización o reinstalar el adaptador StorSimple para SharePoint:
>
>- Todos los archivos que anteriormente se han movido a almacenamiento externo mediante EDR no estará disponibles hasta que haya finalizado la reinstalación y está habilitada la característica de EDR nuevamente. Para limitar el impacto en el usuario, realice cualquier actualización o reinstalación durante una ventana de mantenimiento planeado.
>
>- El tiempo necesario para la actualización o reinstalación puede variar, dependiendo del número total de bases de datos de SharePoint en la granja de servidores de SharePoint.
>
>- Una vez completada la actualización o reinstalación, debe habilitar EDR para las bases de datos de contenido. Para obtener más información, vea [Configurar EDR](#configure-rbs) .
>
>- Si está configurando EDR para una granja de SharePoint que tiene un gran número de bases de datos (más de 200), la página de **Administración Central de SharePoint** es posible que el tiempo de espera. En este caso, actualice la página. No afecta el proceso de configuración.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adaptador StorSimple para la eliminación de SharePoint

Los procedimientos siguientes describen cómo volver a mover el BLOB a las bases de datos de contenido de SQL Server y, a continuación, desinstalar el adaptador StorSimple para SharePoint. 

>[AZURE.IMPORTANT] Tendrá que volver a mover el BLOB a las bases de datos de contenido antes de desinstalar el software del adaptador. 

### <a name="before-you-begin"></a>Antes de empezar 

Recopilar la información siguiente antes de volver a las bases de datos de contenido de SQL Server y comenzar el proceso de eliminación de adaptador:

- Los nombres de las bases de datos para el que está habilitado EDR
- La ruta de acceso UNC de la tienda de blobs configurada

### <a name="move-the-blobs-back-to-the-content-databases"></a>Volver a mover el BLOB a las bases de datos de contenido

Antes de desinstalar el adaptador StorSimple de software de SharePoint, debe migrar todos los BLOB que se han externalizar volver a las bases de datos de contenido de SQL Server. Si intenta desinstalar el adaptador StorSimple para SharePoint antes de mover todos los BLOB volver a las bases de datos de contenido, verá el siguiente mensaje de advertencia.

![Mensaje de advertencia](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####<a name="to-move-the-blobs-back-to-the-content-databases"></a>Para volver a mover el BLOB a las bases de datos de contenido 

1. Descargar todos los objetos externos.

2. Abra la página de **Administración Central de SharePoint** y vaya a **Configuración del sistema**. 

3. **StorSimple de Azure**, haga clic en **Configurar adaptador de StorSimple**.

4. En la página **Configurar StorSimple adaptador** , haga clic en el botón **Deshabilitar** debajo de cada una de las bases de datos de contenido que desea quitar de almacenamiento de blobs externo. 

5. Eliminar los objetos de SharePoint y, después, cargarlos.

Como alternativa, puede usar Microsoft` RBS Migrate()` cmdlet de PowerShell que se incluyen con SharePoint. Para obtener más información, vea [migrar contenido dentro o fuera de EDR](https://technet.microsoft.com/library/ff628255.aspx).

Después de mover los BLOB volver a la base de datos de contenido, vaya al paso siguiente: [desinstalar el adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar el adaptador

Después de mover los BLOB a las bases de datos de contenido de SQL Server, utilice una de las siguientes opciones para desinstalar el adaptador StorSimple de SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Usar el programa de instalación para desinstalar el adaptador 

1. Usar una cuenta con privilegios de administrador para iniciar sesión en el servidor de web front-end (WFE).
2. Haga doble clic en el adaptador StorSimple para el instalador de SharePoint. Se inicia el Asistente de configuración.

    ![Asistente de configuración](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. Haga clic en **siguiente**. Aparece la página siguiente.

    ![Configuración de la página del Asistente para quitar](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. Haga clic en **Quitar** para seleccionar el proceso de eliminación. Aparece la página siguiente.

    ![Página de confirmación del Asistente de configuración](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. Haga clic en **Quitar** para confirmar la eliminación. Aparece la siguiente página de progreso.

    ![Página de progreso del Asistente de configuración](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. Cuando se complete la eliminación, aparece la página Finalizar. Haga clic en **Finalizar** para cerrar al Asistente de configuración.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Usar el Panel de Control para desinstalar el adaptador 

1. Abra el Panel de Control y, a continuación, haga clic en **programas y características**.

2. Seleccione **Adaptador StorSimple de SharePoint**y, a continuación, haga clic en **desinstalar**. 

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre la StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx
