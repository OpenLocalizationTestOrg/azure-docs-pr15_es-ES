<properties 
    pageTitle="Depurar un servicio de nube Azure o una máquina virtual en Visual Studio | Microsoft Azure"
    description="Depurar un servicio de nube o máquina Virtual en Visual Studio"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />
<tags 
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/15/2016"
    ms.author="tarcher" />

# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Depurar un servicio de nube Azure o una máquina virtual en Visual Studio

Visual Studio ofrece diferentes opciones para depurar servicios de nube de Azure y máquinas virtuales de Windows.



## <a name="debug-your-cloud-service-on-your-local-computer"></a>Depurar el servicio de nube en el equipo local

Puede ahorrar tiempo y dinero utilizando el Azure calcular emulador depurar su servicio de nube en un equipo local. Al depurar un servicio localmente antes de implementarlo, puede mejorar la confiabilidad y el rendimiento sin pagar por el tiempo de cálculo. Sin embargo, pueden producirse algunos errores solo al ejecutar un servicio de nube en Azure propiamente dicho. Puede depurar estos errores si habilita la depuración remota al publicar su servicio y, a continuación, adjuntar al depurador a una instancia de funciones.

El emulador simula el servicio de Azure calcular y se ejecuta en su entorno local para que puedan probar y depurar el servicio de nube antes de implementarlo. El emulador administra el ciclo de vida de las instancias de la función y proporciona acceso a los recursos simulados, como almacenamiento local. Al depurar o ejecutar el servicio de Visual Studio, el emulador se inicia automáticamente como una aplicación de fondo y, a continuación, implementa el servicio en el emulador. Puede usar el emulador para ver su servicio cuando se ejecuta en el entorno local. Puede ejecutar la versión completa o la versión express del emulador. (A partir de Azure 2.3, la versión express del emulador es el valor predeterminado). Consulte [usar emulador Express para ejecutar y depurar un servicio de nube localmente](https://msdn.microsoft.com/library/dn339018.aspx).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Depurar el servicio de nube en el equipo local

1. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar el proyecto de servicio de nube de Azure. Como alternativa, puede presionar F5. Verá un mensaje que se está iniciando el emulador calcular. Cuando se inicia el emulador, el icono de la bandeja del sistema lo confirma.

    ![Emulador de Azure en la bandeja del sistema](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

1. Mostrar la interfaz de usuario para el emulador cálculo abriendo el menú contextual para el icono de Azure en el área de notificación y, a continuación, seleccione **Mostrar IU de emulador calcular**.

    El panel izquierdo de la interfaz de usuario muestra los servicios que están instalados actualmente en el emulador de cálculo y las instancias de la función que se ejecuta cada servicio. Puede elegir el servicio o funciones para mostrar el ciclo de vida, el registro y la información de diagnóstico en el panel derecho. Si coloca el foco en el margen superior de una ventana incluye, se expande para rellenar el panel derecho.

1. Paso a través de la aplicación, seleccione comandos en el menú **Depurar** y establecer puntos de interrupción en el código. Como paso a través de la aplicación en el depurador, se actualizan los paneles con el estado actual de la aplicación. Al dejar de depuración, se elimina la implementación de aplicaciones. Si su aplicación incluye una función web y ha establecido la propiedad acción de inicio para iniciar el explorador web, Visual Studio inicia la aplicación web en el explorador. Si cambia el número de instancias de una función en la configuración del servicio, debe detener el servicio de nube y reiniciar la depuración de modo que puede depurar estas nuevas instancias de la función.

    **Nota:** Al dejar de ejecutar o depurar el servicio, el emulador de cálculo local y emulador de almacenamiento no se detienen. Debe detener ellos explícitamente desde el área de notificación.


## <a name="debug-a-cloud-service-in-azure"></a>Depurar un servicio de nube de Azure

Para depurar un servicio de nube desde un equipo remoto, debe habilitar explícitamente que la funcionalidad cuando se implementa el servicio de nube de modo que requiera servicios (por ejemplo, msvsmon.exe) están instalados en los equipos virtuales que se ejecutan las instancias de la función. Si no habilita la depuración remota cuando se publica el servicio, debe volver a publicar el servicio con depuración remota habilitada.

Si habilita la depuración remota de un servicio de nube, no provocar rendimiento degradado o se incurre en gastos adicionales. No debe utilizar depuración remota en un servicio de producción, ya que los clientes que usan el servicio pueden verse afectados negativo.

>[AZURE.NOTE] Cuando se publica un servicio de nube de Visual Studio, puede habilitar **IntelliTrace** para las funciones de ese servicio de destino .NET Framework 4 o .NET Framework 4.5. Si usa **IntelliTrace**, puede examinar los eventos que ocurrieron en una instancia de funciones en el pasado y reproducir el contexto de ese momento. Vea [Depurar un servicio de nube publicados con IntelliTrace y Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) y [Usar IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Para habilitar la depuración remota de un servicio de nube

1. Abrir el menú contextual para el proyecto de Azure y, a continuación, seleccione **Publicar**.

1. Seleccione el entorno de **ensayo** y la configuración de **depuración** .

    Esto es sólo de referencia. Puede elegir para ejecutar los entornos de prueba en un entorno de producción. Sin embargo, se pueden afectar los usuarios si habilita la depuración remota en el entorno de producción. Puede elegir la configuración de lanzamiento, pero la configuración de depuración simplifica la depuración.

    ![Elija la configuración de depuración](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

1. Siga los pasos habituales, pero Active la casilla **Habilitar depurador remoto para todos los roles** en la pestaña **Configuración avanzada** .

    ![Configuración de depuración](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Para adjuntar al depurador a un servicio de nube de Azure

1. En el Explorador de servidores, expanda el nodo de su servicio de nube.

1. Abrir el menú contextual de la función o instancia de rol al que desea adjuntar y, a continuación, seleccione **Adjuntar depurador**.

    Si depurar una función, el depurador de Visual Studio se asocia a cada instancia de esa función. La interrumpirá en un punto de interrupción de la primera instancia de la función que se ejecuta la línea de código y cumple las condiciones de ese punto de interrupción. Si depurar una instancia, las conexiones de depurador solo para esa instancia y saltos en un punto de interrupción solo cuando ejecuta la línea de código de esa instancia específica y cumpla las condiciones del punto de interrupción.

    ![Adjuntar depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

1. Después de que el depurador se asocia a una instancia, depurar como de costumbre. El depurador se asocia automáticamente al proceso de host adecuado para su rol. Dependiendo de qué es la función, el depurador se asocia a w3wp.exe, WaWorkerHost.exe o WaIISHost.exe. Para comprobar el proceso al que se asocia el depurador, expanda el nodo de la instancia del explorador de servidores. Para obtener más información acerca de los procesos de Azure, consulte [Arquitectura de la función de Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

    ![Seleccione el cuadro de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Para identificar los procesos a los que está asociado el depurador, abra el cuadro de diálogo procesos, en la barra de menús, elija depuración, Windows, procesos. (Teclado: Ctrl + Alt + Z) Desasociar un proceso específico, abra el menú contextual y, a continuación, seleccione **Desasociar proceso**. O bien, busque el nodo de la instancia del explorador de servidores, encuentra el proceso, abrir el menú contextual y, a continuación, seleccione **Desasociar proceso**.

    ![Depurar procesos](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

>[AZURE.WARNING] Evitar larga se detiene en los puntos de interrupción cuando remoto de depuración. Azure trata un proceso que se detiene durante más de unos minutos como no responde y deja de enviar tráfico a esa instancia. Si detiene durante mucho tiempo, msvsmon.exe se separa del proceso.

Para desconectar al depurador de todos los procesos en su instancia o rol, abrir el menú contextual de la función o la instancia que se va a depurar y, a continuación, seleccione **Depurador desasociar**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Limitaciones de depuración remota en Azure

De Azure SDK 2.3, depuración remota tiene las limitaciones siguientes.

- Con la depuración remota habilitada, no puede publicar un servicio de nube en el que cualquier rol tiene más de 25 instancias.

- El depurador utiliza puertos 30400 a 30424, 31400 a 31424 y 32400 a 32424. Si intenta utilizar cualquiera de estos puertos, no podrá publicar su servicio y uno de los siguientes mensajes de error aparecerán en el registro de actividad de Azure: 

    - Error al validar el archivo .cscfg con el archivo .csdef. 
    El rango de puerto reservado 'rango' extremo Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector de función 'función' se superpone con un puerto ya definido o un rango.
    - Error de asignación. Vuelva a intentarlo más tarde, intente reducir el tamaño de la máquina virtual o el número de instancias de la función o intente implementar a una región distinta.


## <a name="debugging-azure-virtual-machines"></a>Depuración máquinas virtuales de Windows Azure

Puede depurar programas que se ejecutan en máquinas virtuales de Windows Azure mediante el Explorador de servidor en Visual Studio. Al habilitar depuración remota en una máquina virtual Azure, Azure instala la extensión de depuración remota en la máquina virtual. A continuación, puede adjuntar a procesos en la máquina virtual y depurar como lo haría normalmente.

>[AZURE.NOTE] Máquinas virtuales creadas por la pila de administrador de recursos de Azure se pueden depurar remotamente mediante el Explorador de nube en Visual Studio de 2015. Para obtener más información, vea [Administración de recursos de Azure con el Explorador de la nube](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Depurar una máquina virtual de Azure

1. En el Explorador de servidores, expanda el nodo de máquinas virtuales de Windows y seleccione el nodo de la máquina virtual que desea depurar.

1. Abrir el menú contextual y seleccione **Habilitar depuración**. Cuando se le pregunte si está seguro de si desea habilitar la depuración en la máquina virtual, seleccione **Sí**.

    Azure instala la extensión de depuración remota en la máquina virtual para habilitar la depuración.

    ![Máquina virtual Habilitar depuración de comando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registro de actividad de Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Cuando la extensión de depuración remoto finalice la instalación, abra el menú contextual de la máquina virtual y seleccione **Adjuntar depurador**

    Azure Obtiene una lista de los procesos en la máquina virtual como muestra en el cuadro de diálogo de proceso asociar al.

    ![Comando de depurador adjuntar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. En el cuadro de diálogo **asociar al proceso** , seleccione **Seleccionar** para limitar la lista de resultados para mostrar solo los tipos de código que desee depurar. Puede depurar código de 32 bits o 64 bits administrado, código nativo o ambos.

    ![Seleccione el cuadro de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Seleccione los procesos que desea depurar en la máquina virtual y, a continuación, seleccione **asociar**. Por ejemplo, puede elegir el proceso w3wp.exe si desea depurar una aplicación web en la máquina virtual. Para obtener más información, vea [Depurar uno o más procesos en Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) y [Arquitectura de la función de Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Crear un proyecto web y una máquina virtual para depuración

Antes de publicar el proyecto de Azure, puede resultarle útil para probar en un entorno contenido que admite depurar y probar escenarios y donde puede instalar pruebas y programas de supervisión. Una forma de hacerlo es depurar su aplicación en una máquina virtual de forma remota.

Proyectos de Visual Studio ASP.NET ofrecen una opción para crear una máquina virtual útil que puede usar para probar la aplicación. La máquina virtual incluye normalmente necesitan extremos como PowerShell, escritorio remoto y WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Para crear un proyecto web y una máquina virtual para depuración

1. En Visual Studio, cree una nueva aplicación Web de ASP.NET.

1. En el cuadro de diálogo nuevo proyecto, en la sección de Azure, elija **Máquina Virtual** en el cuadro de lista desplegable. Deje activada la casilla de verificación **crear remotos recursos** . Seleccione **Aceptar** para continuar.

    Aparece el cuadro de diálogo de la **máquina virtual de crear en Azure** .


    ![Crear el cuadro de diálogo de proyecto web ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Nota:** Le pedirá que inicie sesión en su cuenta de Azure si aún no ha iniciado sesión.

1. Seleccione las diferentes configuraciones para la máquina virtual y, a continuación, seleccione **Aceptar**. Para obtener más información, vea [máquinas virtuales de Windows]( http://go.microsoft.com/fwlink/?LinkId=623033) .

    El nombre que escribe el nombre de DNS será el nombre de la máquina virtual. 

    ![Crear la máquina virtual en el cuadro de diálogo de Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure crea la máquina virtual y, a continuación, disposiciones y configura los extremos, como escritorio remoto e implementar de Web



1. Después de la máquina virtual está configurada completamente, seleccione el nodo de la máquina virtual en el Explorador de servidores.

1. Abrir el menú contextual y seleccione **Habilitar depuración**. Cuando se le pregunte si está seguro de si desea habilitar la depuración en la máquina virtual, seleccione **Sí**. 

    Azure instala la extensión de depuración remota a la máquina virtual para habilitar la depuración.

    ![Máquina virtual Habilitar depuración de comando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registro de actividad de Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Publicar el proyecto como se describe en [Cómo: implementar un proyecto usando uno y haga clic en publicación Web en Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Puesto que desea depurar en la máquina virtual, en la página **configuración** del Asistente de **Publicación Web** , seleccione **Depurar** como la configuración. Esto se asegura de que los símbolos de código están disponibles al depurar.

    ![Configuración de publicación](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

1. En las **Opciones de publicación de archivo**, seleccione **Quitar archivos adicionales en el destino** si el proyecto ya se ha implementado en un momento anterior.

1. Una vez que publique el proyecto, en el menú contextual de la máquina virtual en el Explorador de servidor, seleccione **Adjuntar depurador**

    Azure Obtiene una lista de los procesos en la máquina virtual como muestra en el cuadro de diálogo de proceso asociar al.

    ![Comando de depurador adjuntar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. En el cuadro de diálogo **asociar al proceso** , seleccione **Seleccionar** para limitar la lista de resultados para mostrar solo los tipos de código que desee depurar. Puede depurar código de 32 bits o 64 bits administrado, código nativo o ambos.

    ![Seleccione el cuadro de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Seleccione los procesos que desea depurar en la máquina virtual y, a continuación, seleccione **asociar**. Por ejemplo, puede elegir el proceso w3wp.exe si desea depurar una aplicación web en la máquina virtual. Para obtener más información, vea [Depurar uno o más procesos en Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) .

## <a name="next-steps"></a>Pasos siguientes

- Usar **Intellitrace** para recopilar un registro de llamadas y eventos de un servidor de lanzamiento. Vea [Depurar un servicio de nube publicados con IntelliTrace y Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
- Use **Diagnósticos de Azure** para registrar información detallada de código que se ejecuta dentro de funciones, si se están ejecutando los roles en el entorno de desarrollo o en Azure. Ver [registro de recopilación de datos mediante el uso de diagnósticos de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).
