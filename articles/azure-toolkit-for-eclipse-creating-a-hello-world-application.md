<properties
    pageTitle="Crear un servicio de nube de Hola mundo de Azure en Eclipse"
    description="Obtenga información sobre cómo crear una aplicación Hola a todos sencilla con el Kit de herramientas de Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Crear un servicio de nube de Hola mundo de Azure en Eclipse #

Los pasos siguientes muestran cómo crear e implementar una aplicación de JSP básica en Azure con el Kit de herramientas de Azure para Eclipse. Se muestra un ejemplo JSP para simplificar, pero es muy similares pasos sería adecuados para un servlet Java, lo que se refiere a implementación de Azure.

La aplicación tendrá un aspecto similar al siguiente:

![][ic600360]

## <a name="prerequisites"></a>Requisitos previos ##

* Un Java Developer Kit (JDK), v 1.7 o posterior.
* Eclipse IDE para los desarrolladores de Java EE, Indigo o posterior. Esto puede descargarse desde <http://www.eclipse.org/downloads/>.
* Una distribución de un servidor web basado en Java o el servidor de aplicaciones, como Apache Tomcat, GlassFish, servidor de aplicaciones JBoss, embarcadero o IBM® WebSphere® aplicación Server libertad Core.
* Una suscripción de Azure, que se puede adquirir de <http://azure.microsoft.com/pricing/purchase-options/>.
* El Kit de herramientas de Azure para Eclipse. Para obtener más información, vea [instalar el Kit de herramientas de Azure para Eclipse][].

## <a name="to-create-a-hello-world-application"></a>Para crear una aplicación Hola a todos ##

En primer lugar, empezaremos con la creación de un proyecto de Java.

*  Iniciar Eclipse, en el menú, haga clic en **archivo**, haga clic en **nuevo**y, a continuación, haga clic en **Proyecto de Web dinámica**. (Si no ve el **Proyecto de Web dinámica** enumerados como proyecto disponible tras hacer clic en **archivo**, **nuevo**, a continuación, haga lo siguiente: haga clic en **archivo**, haga clic en **nuevo**, haga clic en **proyecto...**, expanda **Web**, haga clic en **Proyecto de Web dinámica**y haga clic en **siguiente**.)
*  Para el propósito de este tutorial, el nombre del proyecto **MyHelloWorld**. (Asegúrese de usar este nombre, pasos posteriores de este tutorial esperan que el archivo guerra denominarse MyHelloWorld). La pantalla tendrá un aspecto similar al siguiente: ![][ic589576]
* Haga clic en **Finalizar**.
* En la vista de explorador de proyectos de Eclipse, expanda **MyHelloWorld**. Haga clic con el botón **ContenidoWeb**, haga clic en **nuevo**y, a continuación, haga clic en **Archivo JSP**.
* En el cuadro de diálogo **Nuevo archivo JSP** , el nombre del archivo **index.jsp**. Mantener la carpeta principal como **MyHelloWorld/ContenidoWeb**, como se muestra en la siguiente:  ![][ic659262]
* En el cuadro de diálogo **Seleccionar plantilla de JSP** , para el propósito de este tutorial, seleccione **Nuevo archivo JSP (html)** y haga clic en **Finalizar**.
* Cuando se abre el archivo index.jsp en Eclipse, agregar texto para mostrar dinámicamente **Hola a todos!** dentro de la existente `<body>` elemento. La actualización `<body>` debe aparecer el contenido como lo siguiente:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Guardar index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Para implementar la aplicación en Azure, de forma rápida y sencilla ##

Tan pronto como tiene una aplicación web de Java lista para probarlo, puede usar el siguiente método abreviado para probarlo directamente en la nube de Azure.

1. En el Explorador de proyectos de Eclipse, haga clic en **MyHelloWorld**.
1. En la barra de herramientas Eclipse, haga clic en el botón desplegable **Publicar** y, a continuación, haga clic en **Publicar como servicio de nube de Azure**
    ![][publishDropdownButton]
1. Si está publicando esta aplicación para Azure por primera vez y no ha creado un proyecto de implementación de Azure para esta aplicación antes, un proyecto de implementación de Azure se crea automáticamente. Verá el mensaje siguiente, que también muestra el paquete JDK y el servidor de aplicación que se implementará automáticamente para ejecutar la aplicación.
    ![][ic789598]

    Este enfoque de método abreviado permite una manera rápida y fácil de probar la aplicación en Azure sin tener que configurar un servidor específico o JDK diferente de los valores predeterminados. Si está satisfecho con las opciones predeterminadas, hacer clic en **Aceptar** para continuar con los pasos siguientes.
    Sin embargo, si desea cambiar el JDK o servidor de aplicaciones para la aplicación, puede hacerlo más adelante mediante la edición del proyecto de implementación de Azure que se creó automáticamente para usted, o puede ahora haga clic en **Cancelar** y lea la **sección de proyectos de implementación de Azure acerca** de este tutorial.
1. En el cuadro de diálogo **publicar en Azure** :
    1. Si no hay ninguna suscripción para seleccionar en la lista de **suscripciones** aún, siga estos pasos para importar la información de la suscripción:
        1. Haga clic en **Importar desde un archivo de configuración de publicación**.
        1. En el cuadro de diálogo **Información de la suscripción de importación** , haga clic en **Descargar el archivo de configuración de publicación**. Si todavía no ha iniciado sesión en su cuenta de Azure, le pedirá que inicie sesión en. A continuación, le pedirá que guarde un Azure publicar el archivo de configuración. Para guardarlo en el equipo local.
        1. Aún en el cuadro de diálogo **Información de la suscripción de importación** , haga clic en el botón **Examinar** , seleccione el archivo de configuración de publicación que haya guardado localmente en el paso anterior y, a continuación, haga clic en **Abrir**. La pantalla debe ser similar al siguiente: ![][ic644267]
        1. Haga clic en **Aceptar**.
    1. Para la **suscripción**, seleccione la suscripción que desee usar para su implementación.
    1. Para la **cuenta de almacenamiento**, seleccione la cuenta de almacenamiento que desee usar o haga clic en **nuevo** para crear una nueva cuenta de almacenamiento.
    1. **Nombre de servicio**, seleccione el servicio de nube que desee usar o haga clic en **nuevo** para crear un nuevo servicio de nube.
    1. **Sistema operativo de destino**, seleccione la versión del sistema operativo que desea usar para su implementación.
    1. **Entorno de destino**, para el propósito de este tutorial, seleccione **ensayo**. (Cuando esté listo para implementar el sitio de producción, deberá cambiarlo a **producción**).
    1. Opcional: Asegúrese de que está activada la **Sobrescribir implementación anterior** si desea que la nueva implementación de la implementación anterior se sobrescribe automáticamente. Al habilitar esta opción, podrá no problemas de experiencia "409 Conflicto" cuando se publica en la misma ubicación.
        Observe que el cuadro de diálogo **publicar en Azure** contiene una sección para el **Acceso remoto**. De forma predeterminada, acceso remoto no está habilitado y no se le permitirá en este ejemplo. Para habilitar el acceso remoto, debe escribir un nombre de usuario y contraseña que utiliza al iniciar sesión de forma remota. Para obtener más información acerca del acceso remoto, vea [Habilitar el acceso remoto para implementaciones de Azure en Eclipse][].
        El cuadro de diálogo **publicar en Azure** tendrá un aspecto similar al siguiente: ![][ic719488]
1. Haga clic en **Publicar** para publicar en el entorno de prueba.
    Cuando se le solicite para realizar una generación completa, haga clic en **Sí**. Esto puede tardar varios minutos para que la primera compilación.
    Un **Registro de actividad de Azure** se mostrará en la sección de vistas Eclipse con pestañas.
    ![][ic719489]
   Puede usar este registro, así como la vista de la **consola** , para ver el progreso de la implementación. Inicie sesión en el [Portal de administración de Azure][]y use la sección de **Servicios en la nube** para supervisar el estado es una alternativa.
1. Si se ha implementado correctamente la implementación, el **Registro de actividad de Azure** mostrará un estado **publicado**. Haga clic en **publicado**, tal como se muestra en la siguiente imagen, y el explorador abrirá una instancia de la implementación.
    ![][ic719490]

Porque se tratase de una implementación en un entorno de ensayo, será el nombre DNS de la forma http://&lt;*guid*&gt;. cloudapp.net y se eliminará mediante la dirección URL contienen el nombre DNS más un sufijo para la aplicación. Por ejemplo, http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (La parte **MyHelloWorld** distingue mayúsculas de minúsculas). También puede ver el nombre DNS si hace clic en el nombre de la implementación en el Portal de administración de la plataforma de Azure (en la parte de los servicios de nube del portal de administración).

Aunque este tutorial era para una implementación al entorno de ensayo, una implementación de producción sigue los mismos pasos, excepto en el cuadro de diálogo **publicar en Azure** , seleccione **producción** en lugar de **ensayo** para el **entorno de destino**. Una implementación de producción el resultado de una dirección URL basada en el nombre DNS de su elección, en lugar de un GUID ya usa para el ensayo.

>[AZURE.WARNING] En este momento ha implementado la aplicación de Azure en la nube. Sin embargo, antes de continuar, tenga en cuenta que una aplicación implementada, incluso si no se está ejecutando, seguirá acumular tiempo facturable para la suscripción. Por lo tanto, es muy importante que eliminar implementaciones no deseadas de la suscripción de Azure.

## <a name="about-azure-deployment-projects"></a>Acerca de los proyectos de implementación de Azure ##

Para implementar una o más aplicaciones de Java a Azure, se necesita un proyecto de implementación de Azure. Se reproduce el rol del "paquete" que necesitan las aplicaciones que se ajustarán en para publicar en Azure.

Además de la información sobre las aplicaciones, un proyecto de implementación de Azure también contiene información sobre otros componentes clave de la implementación, lo más importante: para ejecutar la aplicación web en el contenedor del servidor de aplicación y el tiempo de ejecución de Java para que se ejecute. Azure es compatible con una gran selección de tiempo de ejecución de Java y puede elegir entre los servidores de aplicaciones Java.

Aunque se ha simplificado en gran medida el ejemplo utilizado aquí con fines ilustrativos, un proyecto de implementación de Azure también puede contener otra información de configuración importantes que permite crear servicios de nube casi arbitrario complejas, scalable, altamente disponible, varios niveles con las aplicaciones. Puede habilitar la **afinidad de la sesión ("sesiones rápidas")**, **rápida de almacenamiento en caché**, **depuración remota**, **descarga SSL**, **firewall/puerto enrutamiento**, **acceso remoto**y un número de otras funciones eficaces.

Si ha finalizado la sección anterior de este tutorial ("para instalar la aplicación para Azure, de forma rápida y sencilla"), ahora verá un nuevo proyecto de implementación de Azure en el Explorador de proyectos que se generan automáticamente y denominada "**MyHelloWorld_onAzure**".

Se ha también iniciado este tutorial creando primero un proyecto de implementación de Azure en blanco y, a continuación, agregar las aplicaciones a él. Tiene un más proceso, pero que le da un mayor control sobre la configuración inicial desde el principio.

Para crear un nuevo proyecto de implementación de Azure desde cero, haga clic en el botón **Nuevo proyecto de implementación de Azure** ![][ic710876].

Independientemente de si se trabaja con un proyecto de implementación de Azure ya existente o crear uno desde cero, es posible cambiar sus opciones de configuración y componentes, como el JDK o el servidor de aplicaciones, igualmente fácilmente en cualquier momento.

Para cambiar el JDK, o el servidor de aplicaciones o la lista de aplicaciones en un proyecto de implementación de Azure existente:

1. Expanda el nodo de proyecto (por ejemplo, **MyHelloWorld_onAzure**) en el Explorador de proyectos
2. Haga clic con el botón **WorkerRole1**
3. Expandir el submenú de **Azure** en el menú contextual
4. Haga clic en **Configuración del servidor**

Independientemente de si ha empezado estos pasos de configuración del servidor mediante la edición de un proyecto de implementación de Azure tal como se muestra encima o crear uno nuevo desde cero, verá el mismo tipo de cuadros de diálogo que permite configurar su JDK, componentes de servidor y la aplicación. Para obtener más cómo cambiar la configuración de los cuadros de diálogo, por ejemplo, para cambiar el JDK, el servidor de aplicaciones y agregar o quitar aplicaciones en una implementación, vea el artículo de [Propiedades de configuración de servidor][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Sólo Windows: implementar la aplicación en el emulador de cálculo ##

>[AZURE.NOTE] El emulador Azure solo está disponible en Windows. Omitir esta sección si está utilizando un sistema operativo que no sea de Windows.

Si ha creado un nuevo proyecto de implementación de Azure siguiendo los pasos descritos anteriormente, es decir, implícitamente, mediante la aplicación de Azure, el JDK y la aplicación de publicación servidores se han configurado para la nube, pero no para emulación local. Para preparar el proyecto para realizar pruebas en el emulador local, siga estos pasos:

1. En el Explorador de proyectos de Eclipse, haga clic en **MyHelloWorld_onAzure**.
1. Haga clic en **WorkerRole1**.
1. Expanda el submenú de **Azure** en el menú contextual.
1. Haga clic en **Configuración del servidor**.
1. En la pestaña **JDK** , compruebe si el Kit de herramientas previamente ha configurado un valor predeterminado JDK local para usted. Si no, o si desea cambiar los valores predeterminados supuestos, asegúrese de que esté activada la casilla de verificación **usar el JDK desde esta ruta de acceso para probar localmente** y se especifica la ubicación de instalación de JDK que desea usar. Si desea cambiarlo, haga clic en el botón **Examinar** y con el control de exploración, seleccione la ubicación del directorio de la JDK usar.
1. Haga clic en la ficha **servidor** .
1. En el cuadro de texto de **ruta de acceso de servidor Local** en la parte inferior del cuadro de diálogo, escriba la ruta de acceso de un servidor instalado localmente que coincida con el tipo y el número de versión principal del servidor seleccionado en la parte superior del cuadro de diálogo, en la casilla de verificación **implementar un servidor de este tipo** . Si desea usar un tipo diferente o una versión principal del servidor de aplicaciones, cambie la selección en esa casilla de verificación en primer lugar.
1. Haga clic en **Aceptar**.
1. En la barra de herramientas Eclipse, haga clic en el botón **ejecutar en Azure emulador** , ![][ic710879]. Si no está habilitado el botón **ejecutar en Azure emulador** , asegúrese de que **MyHelloWorld_onAzure** está seleccionado en el Explorador de proyectos de Eclipse y Explorador de proyectos de Eclipse que tiene el foco como la ventana actual. Esto primero iniciar una compilación completa del proyecto y, a continuación, inicie la aplicación web de Java en el emulador de cálculo. (Observe que, dependiendo de las características de rendimiento de su equipo, la primera generación puede tardar entre unos segundos en unos minutos, pero crea las siguientes se proporciona más rápido.) Una vez finalizado el primer paso de compilación, se le pedirá por Windows Control (cuentas de usuario) para permitir que este comando realizar cambios en el equipo. Haga clic en **Sí**.

>[AZURE.IMPORTANT] Si no ve la UAC, active la barra de tareas de Windows para el icono UAC y haga clic en primer lugar. A veces el UAC símbolo del sistema no se muestra como una ventana de nivel superior, pero está visible sólo como un icono de la barra de tareas.

1. Examine el resultado del cálculo emulador interfaz de usuario para determinar si existen problemas relacionados con el proyecto. Según el contenido de la implementación, puede tardar un par de minutos para la aplicación para que se inicie completamente el emulador de cálculo.
1. Inicie el explorador y usar la dirección URL `http://localhost:8080/MyHelloWorld` como la dirección (la `MyHelloWorld` parte de la dirección URL distingue mayúsculas de minúsculas). Verá la aplicación MyHelloWorld (la salida de index.jsp), similar a la siguiente imagen: ![][ic589579]

Cuando esté listo para detener la aplicación que se ejecute en el emulador de cálculo, en la barra de herramientas Eclipse, haga clic en el botón **Restablecer emulador de Azure** , ![][ic710880].

## <a name="to-delete-your-deployment"></a>Para eliminar la implementación ##

Para eliminar la implementación dentro del Kit de herramientas de Azure para Eclipse, asegurarse de que **MyHelloWorld_onAzure** está seleccionado en el Explorador de proyectos de Eclipse, asegurarse de que el Explorador de proyectos Eclipse tiene la ventana actual centrar y, a continuación, haga clic en el botón **Quitar** , ![][ic710883], en la barra de herramientas Eclipse. (Puede hacer la misma operación haciendo clic en **MyHelloWorld_onAzure** en Explorador de proyectos de Eclipse, haciendo clic en **Azure** y haga clic en **anular la implementación de nube de Azure**). Esta opción mostrará el cuadro de diálogo **Anular la publicación de proyecto de Azure** .

![][ic719491]

Seleccione el servicio de suscripción y la nube que contiene la implementación, seleccione la implementación que desea eliminar y, a continuación, haga clic en **Quitar**.

(Una alternativa a usar el Kit de herramientas para eliminar la implementación es usar la sección **Servicios de nube** de Portal de administración de Azure: vaya a la implementación, selecciónelo y, a continuación, haga clic en el botón **Eliminar** . Esto se detenga y, a continuación, eliminar, la implementación. Si solo desea detener la implementación y no eliminarlo, haga clic en el botón **Detener** en lugar del botón **Eliminar** , pero como se mencionó anteriormente, si elimina la implementación, cargos facturables seguirá acumular para su implementación, incluso si se ha detenido).

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

[Novedades en el Kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Habilitar el acceso remoto para las implementaciones de Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propiedades de configuración de servidor]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Novedades en el Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
