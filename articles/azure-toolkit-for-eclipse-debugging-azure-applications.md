<properties
    pageTitle="Depuración de aplicaciones de Azure en Eclipse"
    description="Obtenga información sobre las aplicaciones de Azure depuración con el Kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Depuración de aplicaciones de Azure en Eclipse #

Usar el Kit de herramientas de Azure para Eclipse, puede depurar las aplicaciones si ejecuta en Azure o en el emulador de cálculo si usa el sistema operativo Windows. La imagen siguiente muestra las propiedades de **depuración** utiliza el cuadro de diálogo para habilitar la depuración remota:

![][ic719504]

En este tutorial se supone que ya tiene una aplicación que se ha creado correctamente y está familiarizado con el emulador de cálculo e implementación a Azure.

Usaremos la aplicación desde el tutorial de [uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP][] como punto de partida para este tema. Antes de continuar, cree esa aplicación si aún no lo ha hecho.

## <a name="to-debug-your-application-while-running-in-azure"></a>Depurar la aplicación mientras se ejecuta en Azure ##

>[AZURE.WARNING] Soporte de corriente del Kit de herramientas de depuración remota de Java está pensada principalmente para implementaciones que se ejecuta en el emulador cálculo Azure. Como la conexión de depuración no está protegida, no debe habilitar depuración remota en implementaciones de producción. Si necesita depurar una aplicación en la nube de Azure, use una implementación de ensayo pero observa que el acceso no autorizado a la sesión de depuración posible si alguien conoce la dirección IP de la implementación de la nube, incluso si se trata de una implementación de ensayo.

1. Crear un proyecto para realizar pruebas en el emulador: Explorador de Eclipse que en el de proyectos, haga clic con el botón **MyAzureProject**, haga clic en **Propiedades**, haga clic en **Azure**y establezca **crear para** implementación **en la nube**.
1. Volver a crear el proyecto: en el menú Eclipse, haga clic en **proyecto**, haga clic en **Crear todas**.
1. Implemente la aplicación de *ensayo* en Azure.
    >[AZURE.IMPORTANT] Como se mencionó anteriormente, se recomienda que depurar en el emulador de cálculo en la mayoría de los casos, a continuación, depurar en el entorno de ensayo solo si depuración adicionales es necesario. Se recomienda no depurar en el entorno de producción.
1. Una vez que la implementación está lista en Azure, obtener el nombre DNS para la implementación desde el [Portal de administración de Azure][]. Una implementación de ensayo tiene un nombre DNS en el formulario de http://*&lt;guid&gt;*. cloudapp.net, donde * &lt;guid&gt; * es un valor GUID asignado por Azure.
1. En el Explorador de proyectos de Eclipse, secundario **WorkerRole1**, haga clic en **Azure**y, a continuación, haga clic en **depuración**.
1. En el cuadro de diálogo **Propiedades de depuración de WorkerRole1** :
    1. Comprobar **Habilitar depuración remota para este rol.**
    1. **Extremo de entrada para usar**, use **depuración (público: 8090, privado: 8090)**.
    1. Asegúrese de **Que iniciar JVM en modo suspendido, esperando una conexión depurador** está desactivada.
        >[AZURE.IMPORTANT] La opción **Iniciar JVM en modo suspendido, esperando una conexión depurador** está destinada a depuración escenarios en el emulador de cálculo solo avanzadas (no disponible para las implementaciones de nube). Si se usa la opción **Iniciar JVM en modo suspendido, esperando una conexión de depurador** , anulará el proceso del servidor inicio hasta que el depurador Eclipse está conectado a su JVM. Aunque podría usar esta opción para una sesión de depuración uso del cálculo, no use para una sesión de depuración en una implementación de la nube. Inicialización del servidor lleve a cabo en una tarea de inicio de Azure y la nube de Azure no hacer públicos extremos disponibles hasta que se complete la tarea de inicio. Por lo tanto, un proceso de inicio no se completará correctamente si esta opción está habilitada en una implementación de la nube, ya no podrá recibir una conexión de un cliente de Eclipse externo.
1. Haga clic en **crear configuraciones de depuración**.
1. En el cuadro de diálogo **Configuración de depuración de Azure** :
    1. Para **proyectos de Java depurar**, seleccione el proyecto de **MyHelloWorld** .
    1. Para **Configurar depuración para**, compruebe la **nube Azure (provisional)**.
    1. Asegúrese de **que Azure calcular emulador** está desactivada.
    1. Para **Host**, escriba el nombre DNS de su implementación preconfigurada, pero sin el anterior **http://**. Por ejemplo (use su GUID en lugar de la GUID se muestra aquí): **4e616d65-6f6e-6 d 65-6973-526f62657274.cloudapp.net**
1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Configuración de depuración de Azure** .
1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Propiedades de depuración WorkerRole1** .
1. Si no tiene un punto de interrupción ya está establecido en index.jsp, configurarlo:
    1. En el Explorador de proyectos de Eclipse, expanda **MyHelloWorld**, expanda **ContenidoWeb**y haga doble clic en **index.jsp**.
    1. Dentro de index.jsp, secundario en la barra azul a la izquierda del código Java y haga clic en **Los puntos de interrupción de alternancia**, como se muestra en la siguiente: ![][ic551537]
1. En el menú de Eclipse, haga clic en **Ejecutar** y, a continuación, haga clic en **Depurar configuraciones**.
1. En el cuadro de diálogo **Configuraciones depurar** , expanda **La aplicación remota de Java** en el panel izquierdo, seleccione **Azure nube (WorkerRole1)**y haga clic en **Depurar**.
1. En el explorador, ejecute la aplicación preconfigurada, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, sustituir el GUID desde el nombre de DNS para * &lt;guid&gt;*. Si se le solicita por un cuadro de diálogo **Confirmar cambio de perspectiva** , haga clic en **Sí**. Ahora debe ejecutar la sesión de depuración a la línea de código donde se estableció el punto de interrupción.

>[AZURE.NOTE] Si está intentando iniciar un equipo remoto conexión a una implementación que tiene varias instancias de rol ejecutando, actualmente no se puede controlar qué instancia de depuración el depurador inicialmente conectará, como el equilibrador de carga de Azure seleccionará al azar una instancia. Sin embargo, una vez que esté conectado a esa instancia, le seguirá la misma instancia de depuración. Nota También, si hay un periodo de inactividad de más de 4 minutos (por ejemplo, cuando está detenido en un punto de interrupción durante mucho tiempo), Azure cerrar la conexión.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Depuración de una instancia de rol específico en una implementación de instancias múltiples ##

Cuando se ejecuta la implementación en la nube, es muy probable que ejecutará él en varios cálculo, o rol, instancias. Esto le permite aprovechar las ventajas de garantía de disponibilidad de Azure 99,95% y escalado de la aplicación.

En estos casos, debe depurar remotamente la aplicación de Java en una instancia de la función específica. Sin embargo, si habilita un extremo de entrada normal para depurar, el equilibrador de carga de Azure le será casi imposible conectar el depurador a una instancia de la función específica. En su lugar le conectará a una instancia de la función que selecciona al azar.

Éste es el tipo de escenario donde aprovechar extremos de entrada de instancia facilitará para depurar una instancia de la función específica.

Supongamos que desea ejecutar un máximo de 5 instancias de función de la implementación. Mediante la página de propiedades de **extremos** en el cuadro de diálogo de propiedades de rol, crear un extremo de entrada de la instancia y asigne un intervalo de puertos públicos, en lugar de un número de puerto único. Por ejemplo, en el cuadro de entrada de **Puerto público** , especifique **81 85**.

Después de implementar la aplicación con este extremo de instancia, Azure asignará a un número de puerto único de este rango a cada una de las instancias de la función. A continuación, para averiguar qué instancia tiene asignada el número de puerto, puede usar la variable de entorno *InstanceEndpointName***_PUBLICPORT** (donde *InstanceEndpointName* es el nombre asignado al crear el extremo de instancia) configurado automáticamente por el Kit de herramientas de la implementación (por ejemplo, mediante devuelve el valor del pie de página de una página Web, por lo que puede leer cuando vaya a él).

Una vez que sepa qué número de puerto público se ha asignado a esa instancia, puede hacer referencia en la configuración de depuración en Eclipse, colocación en el nombre de host de su servicio. Esto le permitirá el depurador Eclipse para conectarse a esa instancia específica y no cualquiera de las demás instancias.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Sólo Windows: depurar su aplicación mientras se ejecuta en el emulador de cálculo ##

>[AZURE.NOTE] El emulador Azure solo está disponible en Windows. Omitir esta sección si está utilizando un sistema operativo que no sea de Windows. 

1. Crear un proyecto para realizar pruebas en el emulador: Explorador de Eclipse que en el de proyectos, haga clic con el botón **MyAzureProject**, haga clic en **Propiedades**, haga clic en **Azure**y establecidos **Generar para** **probar en emulador**.
1. Volver a crear el proyecto: en el menú Eclipse, haga clic en **proyecto**, haga clic en **Crear todas**.
1. En el Explorador de proyectos de Eclipse, secundario **WorkerRole1**, haga clic en **Azure**y, a continuación, haga clic en **depuración**.
1. En el cuadro de diálogo **Propiedades de depuración de WorkerRole1** :
    1. Comprobar **Habilitar depuración remota para este rol.**
    1. **Extremo de entrada para usar**, use el extremo predeterminado que se genera automáticamente el Kit de herramientas, aparece como **depuración (público: 8090, privado: 8090)**.
    1. Asegúrese de que la opción **Iniciar JVM en modo suspendido, esperando una conexión depurador** esté desactivada.
        >[AZURE.IMPORTANT] La opción **Iniciar JVM en modo suspendido, esperando una conexión depurador** está destinada a depuración escenarios en el emulador de cálculo solo avanzadas (no disponible para las implementaciones de nube). Si se usa la opción **Iniciar JVM en modo suspendido, esperando una conexión de depurador** , anulará el proceso del servidor inicio hasta que el depurador Eclipse está conectado a su JVM. Aunque podría usar esta opción para una sesión de depuración uso del cálculo, no use para una sesión de depuración en una implementación de la nube. Inicialización del servidor lleve a cabo en una tarea de inicio de Azure y la nube de Azure no hacer públicos extremos disponibles hasta que se complete la tarea de inicio. Por lo tanto, un proceso de inicio no se completará correctamente si esta opción está habilitada en una implementación de la nube, ya no podrá recibir una conexión de un cliente de Eclipse externo.
1. Haga clic en **crear configuraciones de depuración**.
1. En el cuadro de diálogo **Configuración de depuración de Azure** :
    1. Para **proyectos de Java depurar**, seleccione el proyecto de **MyHelloWorld** .
    1. Para **Configurar depuración para**, consulte **Azure calcular emulador**.
1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Configuración de depuración de Azure** .
1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Propiedades de depuración WorkerRole1** .
1. Establecer un punto de interrupción en index.jsp:
    1. En el Explorador de proyectos de Eclipse, expanda **MyHelloWorld**, expanda **ContenidoWeb**y haga doble clic en **index.jsp**.
    1. Dentro de index.jsp, secundario en la barra azul a la izquierda del código Java y haga clic en **Los puntos de interrupción de alternancia**, como se muestra en la siguiente: ![][ic551537]

       Si ve el icono de punto de interrupción dentro de la barra azul a la izquierda del código Java, se establece un punto de interrupción.
1. Inicie la aplicación en el emulador de cálculo haciendo clic en el botón **ejecutar en emulador de Azure** en la barra de herramientas de Azure.
1. En el menú de Eclipse, haga clic en **Ejecutar** y, a continuación, haga clic en **Depurar configuraciones**.
1. En el cuadro de diálogo **Configuraciones de depurar** , expanda **La aplicación remota de Java** en el panel izquierdo, seleccione **Emulador de Azure (WorkerRole1)**y haga clic en **Depurar**.
1. Después de que el emulador cálculo indica que la aplicación se ejecuta en el explorador, ejecute **Http://localhost: 8080/MyHelloWorld**.
    Si se le solicita por un cuadro de diálogo **Confirmar cambio de perspectiva** , haga clic en **Sí**.
    Ahora debe ejecutar la sesión de depuración a la línea de código donde se estableció el punto de interrupción.

Esto le mostramos cómo depurar en el emulador de cálculo; en la sección siguiente se muestra cómo depurar una aplicación implementada en Azure.

## <a name="debugging-notes"></a>Notas de depuración ##

* Después de depurar, puede cambiar la perspectiva de **Depurar** con **Java** a través de hacer clic en el menú de Eclipse, haciendo clic en **ventana**, **Abrir perspectiva**y seleccionar la perspectiva que desea usar.
* Para habilitar la depuración remota en GlassFish, no use la característica de configuración de depuración remota del Kit de herramientas de Azure para Eclipse. En su lugar configurar GlassFish manualmente. Debido a la forma en que GlassFish trata las opciones de Java predefinidas en variables de entorno, característica de configuración de depuración remota del Kit de herramientas no funciona correctamente con GlassFish. Si está habilitada la característica de configuración de depuración remota del Kit de herramientas, puede impedir que GlassFish inicie.

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Uso de la biblioteca de tiempo de ejecución de servicio Azure en JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
