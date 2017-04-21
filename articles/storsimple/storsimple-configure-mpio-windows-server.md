<properties 
   pageTitle="Configurar MPIO para su dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo configurar i/OS de múltiples rutas (MPIO) de su dispositivo StorSimple conectado a un host que ejecuta Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurar múltiples rutas i/OS para su dispositivo StorSimple

Microsoft integrado la compatibilidad con la característica de i/OS de múltiples rutas (MPIO) en Windows Server configuraciones de SAN altamente disponible, tolerancia generación de ayuda. MPIO utiliza componentes de ruta de acceso física redundantes: adaptadores, cables y modificadores: crear rutas lógicas entre el servidor y el dispositivo de almacenamiento. Si se produce un error de componente causando una ruta lógica errores, lógica de múltiples rutas utiliza una ruta alternativa para i/OS para que las aplicaciones pueden seguir teniendo acceso a sus datos. Además según su configuración, MPIO puede mejorar el rendimiento al volver a equilibrar la carga a través de estas rutas de acceso. Para obtener más información, vea [Introducción a MPIO](https://technet.microsoft.com/library/cc725907.aspx "Introducción MPIO y características").  

La disponibilidad de alto de la solución StorSimple, debe configurarse MPIO en el dispositivo StorSimple. Cuando MPIO está instalado en los servidores de host que ejecutan Windows Server 2012 R2, a continuación, pueden tolerar los servidores de un vínculo, la red o el error de la interfaz. 

MPIO es una característica opcional en Windows Server y no está instalado de forma predeterminada. Debe instalarse como característica mediante el administrador del servidor. Este tema describe los pasos que debe seguir para instalar y usar la característica MPIO en un host que ejecuta Windows Server 2012 R2 y conectado a un dispositivo físico de StorSimple.

>[AZURE.NOTE] **Este procedimiento es aplicable StorSimple 8000 sólo para la serie. MPIO no es compatible actualmente en un dispositivo virtual de StorSimple.**

Debe seguir estos pasos para configurar MPIO en el dispositivo StorSimple:

- Paso 1: Instalar MPIO en el host de Windows Server

- Paso 2: Configurar MPIO para volúmenes StorSimple

- Paso 3: Volúmenes de montaje StorSimple en el host

- Paso 4: Configurar MPIO para alta disponibilidad y equilibrio de carga

Cada uno de los pasos anteriores se describe en las secciones siguientes.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Paso 1: Instalar MPIO en el host de Windows Server

Para instalar esta característica en el host de Windows Server, complete el procedimiento siguiente.

#### <a name="to-install-mpio-on-the-host"></a>Para instalar MPIO en el host

1. Abra el administrador del servidor en el host de Windows Server. De forma predeterminada, el administrador del servidor inicia cuando un miembro del grupo Administradores inicia sesión en un equipo que ejecuta Windows Server 2012 R2 o Windows Server 2012. Si el administrador del servidor no está ya abierto, haga clic en **Inicio > Administrador del servidor**.
![Administrador del servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Haga clic en **Administrador del servidor > paneles > Agregar funciones y características**. Esto inicia al Asistente para **Agregar funciones y características** .
![Agregar funciones y características Asistente 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. En el Asistente para **Agregar funciones y características** , haga lo siguiente:

    - En la página **antes de empezar** , haga clic en **siguiente**.
    - En la página **Seleccionar el tipo de instalación** , acepte la configuración predeterminada de instalación **basado en roles o característica** . Haga clic en **siguiente**. ![Agregar funciones y características Asistente 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - En la página **Seleccionar el servidor de destino** , elija **Seleccionar un servidor del grupo de servidores**. Sería detectar automáticamente el servidor de host. Haga clic en **siguiente**.
    - En la página **Seleccionar funciones de servidor** , haga clic en **siguiente**.
    - En la página **Seleccionar características** , seleccione **I/OS de múltiples rutas**y haga clic en **siguiente**. ![Agregar funciones y características Asistente 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - En la página **Confirmar selecciones de instalación** , confirme la selección y, a continuación, seleccione **reiniciar el servidor de destino automáticamente si es necesario**, como se muestra a continuación. Haga clic en **instalar**. ![Agregar funciones y características Asistente 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Se le notificará cuando termine la instalación. Haga clic en **Cerrar** para cerrar al asistente. ![Agregar funciones y características Asistente 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Paso 2: Configurar MPIO para volúmenes StorSimple

MPIO debe estar configurado para identificar volúmenes de StorSimple. Para configurar MPIO reconozca volúmenes StorSimple, realice los pasos siguientes.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Configurar MPIO para volúmenes StorSimple

1. Abra la **configuración MPIO**. Haga clic en **Administrador del servidor > paneles > Herramientas > MPIO**.

2. En el cuadro de diálogo **Propiedades de MPIO** , seleccione la pestaña **Detectar múltiples rutas** .

3. Seleccione **Agregar compatibilidad con dispositivos iSCSI**y, a continuación, haga clic en **Agregar**.  
![Propiedades de MPIO descubrir entre varios trazados](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Reinicie el servidor cuando se le solicite.
5. En el cuadro de diálogo **Propiedades de MPIO** , haga clic en la pestaña **Dispositivos MPIO** . Haga clic en **Agregar**.
    </br>![MPIO propiedades MPIO dispositivos](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. En el cuadro de diálogo **Agregar la compatibilidad con MPIO** en **ID de Hardware del dispositivo**, escriba el número de serie de su dispositivo. Puede obtener el número de serie de dispositivo, tener acceso al servicio de administrador de StorSimple y vaya a **dispositivos > paneles**. El número de serie de dispositivo se muestra en el panel de **Resumen rápido** del panel del dispositivo de derecho.
    </br>![Agregar la compatibilidad con MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie el servidor cuando se le solicite.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Paso 3: Volúmenes de montaje StorSimple en el host

Después de configura MPIO en Windows Server, volúmenes creado en el dispositivo StorSimple pueden colocarse y, a continuación, pueden aprovechar las ventajas de MPIO para redundancia. Para un volumen de montaje, realice los pasos siguientes.

#### <a name="to-mount-volumes-on-the-host"></a>Montaje de volúmenes en el host

1. Abra la ventana de **Propiedades del iniciador iSCSI** en el host de Windows Server. Haga clic en **Administrador del servidor > paneles > Herramientas > iniciador iSCSI**.
2. En el cuadro de diálogo **Propiedades del iniciador iSCSI** , haga clic en la ficha Detección y, a continuación, haga clic en **Detectar el Portal de destino**.
3. En el cuadro de diálogo **Descubrir Portal de destino** , haga lo siguiente:
    
    - Escriba la dirección IP del puerto de datos de su dispositivo StorSimple (por ejemplo, escriba datos 0).
    - Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI** .

    >[AZURE.IMPORTANT] **Si está utilizando una red privada para conexiones, escriba la dirección IP del puerto de datos que está conectado a una red privada.**

4. Repita los pasos 2 y 3 para una segunda interfaz de red (por ejemplo, 1 de datos) en el dispositivo. Tenga en cuenta que estas interfaces deben habilitarse para iSCSI. Para obtener más información sobre esto, vea [modificar interfaces de red](storsimple-modify-device-config.md#modify-network-interfaces).
5. Seleccione la ficha **destinos** en el cuadro de diálogo **Propiedades del iniciador iSCSI** . Debería ver el destino del dispositivo StorSimple IQN en **Destinos detectados**.
 ![Ficha de destinos de propiedades del iniciador de iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Haga clic en **Conectar** para establecer una sesión iSCSI con el dispositivo de StorSimple. Aparecerá un cuadro de diálogo **Conectar con el destino** .

7. En el cuadro de diálogo **conectar al destino** , active la casilla de verificación **Habilitar múltiples rutas** . Haga clic en **Avanzadas**.

8. En el cuadro de diálogo **Configuración avanzada** , haga lo siguiente:                                       
    -    En la lista desplegable de **Adaptador Local** , seleccione el **iniciador iSCSI de Microsoft**.
    -    En la lista desplegable **IP del iniciador** , seleccione la dirección IP del host.
    -    En la lista desplegable IP de **Portal de destino** , seleccione la dirección IP de la interfaz del dispositivo.
    -    Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI** .

9. Haga clic en **Propiedades**. En el cuadro de diálogo **Propiedades** , haga clic en **Agregar sesión**.
10. En el cuadro de diálogo **conectar al destino** , active la casilla de verificación **Habilitar múltiples rutas** . Haga clic en **Avanzadas**.
11. En el cuadro de diálogo **Configuración avanzada** :                                        
    -  En la lista desplegable de **adaptador Local** , seleccione el iniciador iSCSI de Microsoft.
    -  En la lista desplegable **IP del iniciador** , seleccione la dirección IP correspondiente al host. En este caso, se conecta dos interfaces de red en el dispositivo a una única interfaz de red en el host. Por lo tanto, esta interfaz es el mismo que se proporcionó para la primera sesión.
    -  En la lista desplegable **IP del Portal de destino** , seleccione la dirección IP de la segunda interfaz datos habilitada en el dispositivo.
    -  Haga clic en **Aceptar** para volver al cuadro de diálogo de propiedades del iniciador de iSCSI. Ha agregado una segunda sesión al destino.

12. Abra **Administración de equipos** desplazándose hasta **Administrador del servidor > paneles > Administración de equipos**. En el panel izquierdo, haga clic en **almacenamiento > Administración de discos**. Los volúmenes creados en el dispositivo StorSimple que son visibles para este host aparecerán en **Administración de discos** como discos nuevo.

13. Inicialice el disco y cree un nuevo volumen. Durante el proceso de formato, seleccione un tamaño de bloque de 64 KB.
![Administración de disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. En **Administración de discos**, haga clic en el **disco** y seleccione **Propiedades**.
15. En el modelo de StorSimple ### cuadro de diálogo **Propiedades de dispositivo de disco de múltiples rutas** cuadro, haga clic en la pestaña **MPIO** .
![StorSimple 8100 disco de múltiples rutas DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. En la sección **Nombre DSM** , haga clic en **Detalles** y compruebe que los parámetros se configuran los parámetros de forma predeterminada. Los parámetros predeterminados son:

    - Ruta de acceso comprobar período = 30
    - Número de reintentos = 3
    - P quitar período = 20
    - Intervalo de reintento = 1
    - Compruebe la ruta de acceso habilitado = desactivado.


>[AZURE.NOTE] **No modifique los parámetros de forma predeterminada.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Paso 4: Configurar MPIO para alta disponibilidad y equilibrio de carga

Para Multi-path basados en alta disponibilidad y equilibrio de carga, deben agregarse manualmente varias sesiones declarar las distintas rutas disponibles. Por ejemplo, si el host tiene dos interfaces conectados a SAN y el dispositivo tiene dos interfaces conectados a SAN, necesita cuatro sesiones configuradas con permutaciones ruta adecuada (solo dos sesiones serán necesarias si cada interfaz de datos y la interfaz de host se están en una subred IP diferente y no enrutables).

>[AZURE.IMPORTANT] **Se recomienda no mezclar 1 GbE e interfaces de red de 10 GbE. Si usa dos interfaces de red, ambas interfaces deben ser del tipo idénticos.**

El siguiente procedimiento describe cómo agregar sesiones cuando un dispositivo StorSimple con dos interfaces de red está conectado a un host con dos interfaces de red.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Configurar MPIO para alta disponibilidad y equilibrio de carga

1. Realizar una detección de destino: en el cuadro de diálogo **Propiedades del iniciador iSCSI** , en la pestaña de **detección** , haga clic en el **Portal de descubrir**.
2. En el cuadro de diálogo **conectar al destino** , escriba la dirección IP de una de las interfaces de red del dispositivo.
3. Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI** .

4. En el cuadro de diálogo **Propiedades del iniciador iSCSI** , seleccione la ficha **destinos** , resalte el destino detectado y, a continuación, haga clic en **Conectar**. Aparece el cuadro de diálogo **Conectar con el destino** .

5. En el cuadro de diálogo **conectar al destino** :
    
    - Deje el destino seleccionado de forma predeterminada configuración para **Agregar esta conexión** a la lista de destinos favoritos. Esto hará que el dispositivo intenta reiniciar la conexión cada vez que se reinicia el equipo automáticamente.
    - Active la casilla de verificación **Habilitar múltiples rutas** .
    - Haga clic en **Avanzadas**.

6. En el cuadro de diálogo **Configuración avanzada** :
    - En la lista desplegable de **Adaptador Local** , seleccione el **iniciador iSCSI de Microsoft**.
    - En la lista desplegable **IP del iniciador** , seleccione la dirección IP del host.
    - En la lista desplegable **IP del Portal de destino** , seleccione la dirección IP de la interfaz de datos habilitada en el dispositivo.
    - Haga clic en **Aceptar** para volver al cuadro de diálogo de propiedades del iniciador de iSCSI.

7. Haga clic en **Propiedades**y, en el cuadro de diálogo **Propiedades** , haga clic en **Agregar sesión**.

8. En el cuadro de diálogo **conectar al destino** , active la casilla de verificación **Habilitar múltiples rutas** y, a continuación, haga clic en **Avanzadas**.

9. En el cuadro de diálogo **Configuración avanzada** :
    1. En la lista desplegable de **adaptador Local** , seleccione el **iniciador iSCSI de Microsoft**.
    2. En la lista desplegable **IP del iniciador** , seleccione la dirección IP correspondiente a la segunda interfaz en el host.
    3. En la lista desplegable **IP del Portal de destino** , seleccione la dirección IP de la segunda interfaz datos habilitada en el dispositivo.
    4. Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI** . Ya ha agregado una segunda sesión al destino.

10. Repita los pasos 8 a 10 para agregar sesiones adicionales (rutas) a la de destino. Con dos interfaces en el host y dos en el dispositivo, puede agregar un total de cuatro sesiones.

11. Después de agregar las sesiones deseadas (rutas), en el cuadro de diálogo **Propiedades del iniciador iSCSI** , seleccione el destino y haga clic en **Propiedades**. En la ficha sesiones del cuadro de diálogo **Propiedades** , tenga en cuenta la sesión de cuatro identificadores que corresponden a las permutaciones posibles de la ruta de acceso. Para cancelar una sesión, seleccione la casilla de verificación situada junto a un identificador de sesión y, a continuación, haga clic en **Desconectar**.

12. Para ver los dispositivos que se presentan en sesiones, seleccione la pestaña **dispositivos** . Para configurar la directiva MPIO de un dispositivo seleccionado, haga clic en **MPIO**. Aparecerá el cuadro de diálogo de **Detalles del dispositivo** . En la pestaña **MPIO** , puede seleccionar las configuraciones de **Directivas de equilibrio de carga** . También puede ver el tipo de ruta de acceso **activa** o **en espera** .

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [usar el servicio de administrador de StorSimple para modificar la configuración del dispositivo StorSimple](storsimple-modify-device-config.md).
 
