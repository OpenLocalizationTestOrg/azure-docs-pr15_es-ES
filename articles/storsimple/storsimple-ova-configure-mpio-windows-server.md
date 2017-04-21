<properties 
   pageTitle="Configurar MPIO en el host de la matriz virtual StorSimple | Microsoft Azure"
   description="Describe cómo configurar i/OS de múltiples rutas (MPIO) para su StorSimple matriz virtual conectado a un host que ejecuta Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurar múltiples rutas i/OS en el host de Windows Server para la matriz Virtual StorSimple

## <a name="overview"></a>Información general

En este artículo se describe cómo instalar la característica de i/OS de múltiples rutas (MPIO) en el host de Windows Server, aplicar configuración específica para volúmenes solo StorSimple y compruebe MPIO para volúmenes de StorSimple. El procedimiento supone que la matriz Virtual StorSimple 1200 con dos interfaces de red está conectada a un host de Windows Server con dos interfaces de red. La información contenida en este artículo se aplica solo a la matriz virtual. Para obtener información sobre los dispositivos de la serie 8000 StorSimple, vaya a [Configurar MPIO para StorSimple host](storsimple-configure-mpio-windows-server.md). 

La característica MPIO en configuraciones de almacenamiento altamente disponible, tolerancia a errores de Windows Server le ayuda a generar. MPIO utiliza componentes de ruta de acceso física redundantes: adaptadores, cables y modificadores: crear rutas lógicas entre el servidor y el dispositivo de almacenamiento. Si se produce un error de componente causando una ruta lógica errores, lógica de múltiples rutas utiliza una ruta alternativa para i/OS para que las aplicaciones pueden seguir teniendo acceso a sus datos. Además según su configuración, MPIO puede mejorar el rendimiento al volver a equilibrar la carga a través de estas rutas de acceso. Para obtener más información, vea [Introducción a MPIO](https://technet.microsoft.com/library/cc725907.aspx "Introducción MPIO y características").  

La disponibilidad de alto de la solución StorSimple, configure MPIO en el host de Windows Server conectados a su matriz Virtual de 1200 StorSimple (también conocido como el dispositivo virtual de local). Los servidores de host, a continuación, pueden tolerar un vínculo, la red o el error de la interfaz. 

Debe seguir estos pasos para configurar MPIO: 

- Requisitos previos de configuración

- Paso 1: Instalar MPIO en el host de Windows Server

- Paso 2: Configurar MPIO para volúmenes StorSimple

- Paso 3: Volúmenes de montaje StorSimple en el host

Cada uno de los pasos anteriores se describe en las secciones siguientes.


## <a name="prerequisites"></a>Requisitos previos

Esta sección describe los requisitos previos de configuración para el host del servidor de Windows y la matriz virtual.

### <a name="on-windows-server-host"></a>En el host de Windows Server

-  Asegúrese de que el host de Windows Server tiene 2 interfaces de red habilitado.


### <a name="on-storsimple-virtual-array"></a>En StorSimple virtual matriz

- La matriz virtual debe configurarse como un servidor de iSCSI. Para obtener más información, vea [Configurar matriz virtual como un servidor de iSCSI](storsimple-ova-deploy3-iscsi-setup.md). Una o más interfaces de red deben estar habilitados en la matriz.   

- Las interfaces de red en la matriz virtual deben ser accesibles desde el host de Windows Server.

- Uno o más volúmenes deben crearse en la matriz Virtual de StorSimple. Para obtener más información, vea [Agregar un volumen](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) en la matriz virtual de 1200 StorSimple. En este procedimiento, creamos 3 volúmenes (2 localmente anclada y 1 en niveles volumen tal como se muestra a continuación) en la matriz virtual.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuración de hardware para StorSimple virtual matriz

La figura siguiente muestra la configuración de hardware de alta disponibilidad y múltiples rutas de equilibrio de carga de su host de Windows Server y matriz virtual StorSimple utilizados en este procedimiento.  

![configuración de hardware de MPIO](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Como se muestra en la figura anterior:

- La matriz virtual StorSimple aprovisionada en Hyper-V es un dispositivo activo único nodo está configurado como un servidor iSCSI.

- Dos interfaces de red virtual están habilitadas en la matriz. En la interfaz de usuario de la matriz virtual de 1200 de web local, compruebe que las dos interfaces de red están habilitadas, vaya a **Configuración de la red** , tal como se muestra a continuación:

    ![Interfaces de red habilitadas en 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Tenga en cuenta las direcciones IPv4 de las interfaces de red habilitado (Ethernet, Ethernet 2 de forma predeterminada) y guardar para su uso posterior en el host.

- Dos interfaces de red están habilitados en el host de Windows Server. Si las interfaces conectadas para host y matriz están en la misma subred, a continuación, habrá 4 rutas disponibles. Esto era el caso de este procedimiento. Sin embargo, si cada interfaz de red en la interfaz de matriz y host está en una subred IP diferente (y no enrutables), a continuación, 2 solo rutas estarán disponibles.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Paso 1: Instalar MPIO en el host de Windows Server

MPIO es una característica opcional en Windows Server y no está instalado de forma predeterminada. Debe instalarse como característica mediante el administrador del servidor. Para instalar esta característica en el host de Windows Server, complete el procedimiento siguiente.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Paso 2: Configurar MPIO para volúmenes StorSimple

MPIO debe estar configurado para identificar volúmenes de StorSimple. Para configurar MPIO reconozca volúmenes StorSimple, realice los pasos siguientes.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Paso 3: Volúmenes de montaje StorSimple en el host

Después de configura MPIO en Windows Server, volúmenes creados en la matriz StorSimple pueden colocarse y, a continuación, pueden aprovechar las ventajas de MPIO para redundancia. Para un volumen de montaje, realice los pasos siguientes.

#### <a name="to-mount-volumes-on-the-host"></a>Montaje de volúmenes en el host

1. Abra la ventana de **Propiedades del iniciador iSCSI** en el host de Windows Server. Haga clic en **Administrador del servidor > paneles > Herramientas > iniciador iSCSI**.
2. En el cuadro de diálogo **Propiedades del iniciador iSCSI** , haga clic en la ficha Detección y, a continuación, haga clic en **Detectar el Portal de destino**.
3. En el cuadro de diálogo **Descubrir Portal de destino** , haga lo siguiente:
    
    - Escriba la dirección IP de la interfaz de red habilitado primera en la matriz virtual de StorSimple. De forma predeterminada, esta sería **Ethernet**. 
    - Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI** .

    >[AZURE.IMPORTANT] **Si está utilizando una red privada para conexiones, escriba la dirección IP del puerto de datos que está conectado a una red privada.**

4. Repita los pasos 2 y 3 para una segunda interfaz de red (por ejemplo, Ethernet 2) en la matriz. 

5. Seleccione la ficha **destinos** en el cuadro de diálogo **Propiedades del iniciador iSCSI** . Para la matriz virtual, debería ver cada superficie de volumen como destino en **Destinos detectados**. En este caso, se descubren tres destinos (correspondiente a tres volúmenes).

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Haga clic en **Conectar** para establecer una sesión iSCSI con la matriz StorSimple. Aparecerá un cuadro de diálogo **Conectar con el destino** . Active la casilla de verificación **Habilitar múltiples rutas** . Haga clic en **Avanzadas**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. En el cuadro de diálogo **Configuración avanzada** , haga lo siguiente:                                       
    -    En la lista desplegable de **Adaptador Local** , seleccione el **iniciador iSCSI de Microsoft**.
    -    En la lista desplegable **IP del iniciador** , seleccione la dirección IP del host.
    -    En la lista desplegable IP de **Portal de destino** , seleccione la dirección IP de interfaz de matriz.
    -    Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Haga clic en **Propiedades**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. En el cuadro de diálogo **Propiedades** , haga clic en **Agregar sesión**.

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. En el cuadro de diálogo **conectar al destino** , active la casilla de verificación **Habilitar múltiples rutas** . Haga clic en **Avanzadas**.
11. En el cuadro de diálogo **Configuración avanzada** :                                        
    -  En la lista desplegable de **adaptador Local** , seleccione el iniciador iSCSI de Microsoft.
    -  En la lista desplegable **IP del iniciador** , seleccione la dirección IP correspondiente al host. En este caso, se conecta dos interfaces de red en la matriz a una única interfaz de red en el host. Por lo tanto, esta interfaz es el mismo que se proporcionó para la primera sesión.
    -  En la lista desplegable **IP del Portal de destino** , seleccione la dirección IP de la segunda interfaz datos habilitada en la matriz.
    -  Haga clic en **Aceptar** para volver al cuadro de diálogo de propiedades del iniciador de iSCSI. Ha agregado una segunda sesión al destino.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Después de agregar las sesiones deseadas (rutas), en el cuadro de diálogo **Propiedades del iniciador iSCSI** , seleccione el destino y haga clic en **Propiedades**. En la ficha sesiones del cuadro de diálogo **Propiedades** , tenga en cuenta la sesión de cuatro identificadores que corresponden a las permutaciones posibles de la ruta de acceso. Para cancelar una sesión, seleccione la casilla de verificación situada junto a un identificador de sesión y, a continuación, haga clic en **Desconectar**.
 
    - Para ver los dispositivos que se presentan en sesiones, seleccione la pestaña **dispositivos** . Para configurar la directiva MPIO de un dispositivo seleccionado, haga clic en **MPIO**. La **
    -  Detalles** aparecerá el cuadro de diálogo. En la **MPIO** ficha, puede seleccionar el adecuado **directiva de equilibrio de carga** configuración. También puede ver la **activa** o **tipo de ruta de acceso en espera **.

10. Repita los pasos 8 a 11 para agregar sesiones adicionales (rutas) a la de destino. Con dos interfaces en el host y dos en la matriz virtual, puede agregar un total de cuatro sesiones de cada destino. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Debe Repita estos pasos para cada volumen (expone como destino).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Abra **Administración de equipos** desplazándose hasta **Administrador del servidor > paneles > Administración de equipos**. En el panel izquierdo, haga clic en **almacenamiento > Administración de discos**. Los volúmenes creados en la matriz virtual StorSimple que son visibles para este host aparecerán en **Administración de discos** como discos nuevo.

13. Inicialice el disco y cree un nuevo volumen. Durante el proceso de formato, seleccione un tamaño de unidad de asignación (Australia) de 64 KB. Repita el proceso para todos los volúmenes disponibles.

    ![Administración de disco](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. En **Administración de discos**, haga clic en el **disco** y seleccione **Propiedades**.

15. En el cuadro de diálogo **Propiedades de dispositivos de disco de ruta múltiple** , haga clic en la pestaña **MPIO** .

    ![Propiedades del disco](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. En la sección **Nombre DSM** , haga clic en **Detalles** y compruebe que los parámetros se configuran los parámetros de forma predeterminada. Los parámetros predeterminados son:

    - Ruta de acceso comprobar período = 30
    - Número de reintentos = 3
    - P quitar período = 20
    - Intervalo de reintento = 1
    - Compruebe la ruta de acceso habilitado = desactivado.

    >[AZURE.NOTE] **No modifique los parámetros de forma predeterminada.**


## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar la matriz Virtual de StorSimple](storsimple-ova-manager-service-administration.md).
 
