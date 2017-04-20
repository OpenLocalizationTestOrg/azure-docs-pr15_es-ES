<properties 
    pageTitle="Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse" 
    description="En este tutorial se muestra cómo usar el Kit de herramientas de Azure para Eclipse para crear un saludo World Online para Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse

Este tutorial muestra cómo crear e implementar una aplicación básica de Hola a todos en Azure como una aplicación Web mediante el [Kit de herramientas de Azure para Eclipse]. Se muestra un ejemplo JSP básico para simplificar, pero es muy similares pasos sería adecuados para un servlet Java, lo que se refiere a implementación de Azure.

Cuando haya completado este tutorial, la aplicación tendrá un aspecto similar a la siguiente ilustración cuando se ve en un explorador web:

![][01]
 
## <a name="prerequisites"></a>Requisitos previos

* Un Java Developer Kit (JDK), v 1.7 o posterior.
* Eclipse IDE para los desarrolladores de Java EE, Indigo o posterior. Esto puede descargarse desde <http://www.eclipse.org/downloads/>.
* Una distribución de un servidor web basado en Java o el servidor de aplicaciones, como Apache Tomcat o embarcadero.
* Una suscripción de Azure, que se puede adquirir de <https://azure.microsoft.com/en-us/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El Kit de herramientas de Azure para Eclipse. Para obtener más información, vea [instalar el Kit de herramientas de Azure para Eclipse].

## <a name="to-create-a-hello-world-application"></a>Para crear una aplicación del mundo Hola a todos

En primer lugar, empezaremos con la creación de un proyecto de Java.

1. Iniciar Eclipse, en el menú, haga clic en **archivo**, haga clic en **nuevo**y, a continuación, haga clic en **Proyecto de Web dinámica**. (Si no ve el **Proyecto de Web dinámica** aparece como un proyecto disponibles tras hacer clic en **archivo** y **nuevo**, haga lo siguiente: haga clic en **archivo**, haga clic en **nuevo**, haga clic en **proyecto...**, expanda **Web**, haga clic en **Proyecto de Web dinámica**y haga clic en **siguiente**.)

1. Para el propósito de este tutorial, el nombre del proyecto **MyHelloWorld**. La pantalla tendrá un aspecto similar al siguiente:

    ![][02]

1. Haga clic en **Finalizar**.

1. En la vista de explorador de proyectos de Eclipse, expanda **MyHelloWorld**. Haga clic con el botón **ContenidoWeb**, haga clic en **nuevo**y, a continuación, haga clic en **Archivo JSP**.

1. En el cuadro de diálogo **Nuevo archivo JSP** , asigne un nombre del archivo **index.jsp**. Mantener la carpeta principal como **MyHelloWorld/ContenidoWeb**.

1. En el cuadro de diálogo **Seleccionar plantilla de JSP** , para el propósito de este tutorial, seleccione **Nuevo archivo JSP (html)**y, a continuación, haga clic en **Finalizar**.

1. Cuando se abre el archivo index.jsp en Eclipse, agregar texto para mostrar dinámicamente **Hola a todos!** dentro de la existente `<body>` elemento. La actualización `<body>` contenido debe ser similar en el siguiente ejemplo:

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Guardar index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Para implementar la aplicación a un contenedor de la aplicación Web de Azure

Hay varias maneras de implementar una aplicación web de Java en Azure. Este tutorial describe uno de los más sencillo: la aplicación se implementará en un contenedor de aplicación Web de Azure - ningún tipo de proyecto especial ni herramientas adicionales son necesarias. El JDK y el software de contenedor web proporcionará para usted Azure, por lo que no es necesario para cargar sus propios; todo lo que necesita es la aplicación Web de Java. Como resultado, el proceso de publicación para su aplicación tardará a segundos, no en minutos.

1. En el Explorador de proyectos de Eclipse, haga clic con el botón **MyHelloWorld**.

1. En el menú contextual, seleccione **Azure**y luego haga clic en **Publicar como Azure Web App...**

    ![][03]
   
    Como alternativa, mientras selecciona el proyecto de aplicación web en el Explorador de proyectos, puede haga clic en el botón de lista desplegable de **Publicar** en la barra de herramientas y seleccione **Publicar como aplicación Web de Azure** desde allí:
   
    ![][14]
   
1. Si no ha iniciado en Azure de Eclipse, se le pedirá que inicie sesión en su cuenta de Azure:

    ![][04]
   
    Nota: Si tiene varias cuentas de Azure, algunas de las indicaciones durante el proceso de inicio de sesión pueden mostrarlo más de una vez, incluso si parecen ser los mismos. Cuando ocurre esto, siga las instrucciones de inicio de sesión.
1. Después de que ha iniciado sesión en su cuenta de Azure, el cuadro de diálogo **Administrar suscripciones** mostrará una lista de suscripciones que están asociados con sus credenciales. Si hay varias suscripciones mencionados y desea trabajar con solo un subconjunto específico de ellos, se pueden desactivar la opción los que quieras utilizar. Cuando haya seleccionado las suscripciones, haga clic en **Cerrar**.

    ![][05]
   
1. Cuando aparezca el cuadro de diálogo **implementar al contenedor de aplicación Web de Azure** , aparecerán los contenedores de aplicación Web que ha creado previamente; Si no ha creado ningún contenedores, la lista estará vacía.

    ![][06]
   
1. Si no ha creado un contenedor de aplicación Web de Azure antes, o si desea publicar la aplicación a un nuevo contenedor, realice los pasos siguientes. En caso contrario, seleccione un contenedor de aplicación Web existente y vaya al paso 7 debajo.

    1. Haga clic en **nuevo...**

    1. Se mostrará el cuadro de diálogo **Nuevo contenedor de aplicación Web** :

        ![][07]

    1. Escriba una **etiqueta DNS** para el contenedor de la aplicación Web; Esto formar la etiqueta DNS de la hoja de la dirección URL del host de la aplicación web de Azure. Nota: El nombre debe estar disponible y se ajustan a los requisitos de los nombres de Azure Web App.

    1. En el menú desplegable de **Contenedor de Web** , seleccione el software adecuado para su aplicación.

        Actualmente, puede elegir entre 8 Tomcat, Tomcat 7 o embarcadero 9. Azure proporciona una distribución reciente del software seleccionado y, a continuación, se ejecuta en una distribución de JDK 8 creada por Oracle y proporcionado por Azure reciente.

    1. En el menú desplegable de **suscripción** , seleccione la suscripción que desea utilizar para esta implementación.

    1. En el menú desplegable del **Grupo de recursos** , seleccione el grupo de recursos con la que desea asociar la aplicación Web.

        Nota: Los grupos de recursos Azure permiten agrupar recursos relacionados para que, por ejemplo, puede eliminarse juntos.

        Puede seleccionar un grupo de recursos existente (si tiene alguna) y saltar al paso siguiente g o utilice la siguiente estos pasos para crear un nuevo grupo de recursos:

        * Haga clic en **nuevo...**

        * Se mostrará el cuadro de diálogo **Nuevo grupo de recursos** :

            ![][08]

        * En el cuadro de texto de **nombre** , especifique un nombre para el nuevo grupo de recursos.

        * En el menú de lista desplegable **región** , seleccione los datos de Azure adecuados centrar la ubicación del grupo de recursos.

        * Haga clic en **Aceptar**.

    1. El menú desplegable de **Plan de servicio de aplicación** enumera los planes de servicio de aplicación que están asociados con el grupo de recursos que haya seleccionado.

        Nota: Un Plan de servicio de aplicación especifica información como la ubicación de la aplicación Web, el nivel de precios y el tamaño de la instancia de cálculo. Un Plan de servicio de aplicación única puede usarse para varias aplicaciones Web, que es el motivo por el se mantiene por separado de una implementación específica de la aplicación Web.

        Puede seleccionar una existente aplicación Plan servicio (si tiene alguna) y saltar al paso siguiente de h o utilice la siguiente estos pasos para crear un nuevo Plan de servicio de aplicación:

        * Haga clic en **nuevo...**

        * Se mostrará el cuadro de diálogo **Nuevo Plan de servicio de aplicación** :

            ![][09]

        * En la el cuadro de texto **nombre** , especifique un nombre para el nuevo Plan de servicio de aplicación.

        * En el menú de lista desplegable **ubicación** , seleccione los datos de Azure adecuados centrar ubicación para el plan.

        * En la el menú desplegable de **Nivel de precios** , seleccione el precio adecuado para el plan. Puede elegir **libre**con fines de pruebas.

        * En la el **Tamaño de la instancia** menú desplegable, seleccione Cambiar el tamaño de la instancia adecuada para el plan. Puede elegir **pequeña**con fines de pruebas.

    1. Una vez haya completado todos los pasos anteriores, el cuadro de diálogo nuevo contenedor de aplicación Web debe ser similar a la siguiente ilustración:

        ![][10]

    1. Haga clic en **Aceptar** para completar la creación de su nuevo contenedor de Web App.

        Espere unos segundos para que se actualice la lista de los contenedores de Web App y, a continuación, el contenedor de la aplicación web recién creado ahora debe estar seleccionado en la lista.

1. Ya está listo para completar la implementación inicial de la aplicación Web de Azure:

    ![][11]

    Haga clic en **Aceptar** para implementar la aplicación de Java en el contenedor seleccionado de la aplicación Web.

    Nota: de forma predeterminada, la aplicación se implementará como un subdirectorio del servidor de aplicaciones. Si desea que su implementación como la aplicación raíz, marque la casilla de **implementar a raíz** antes de hacer clic en **Aceptar**.

1. A continuación, debería ver la vista del **Registro de actividad de Azure** , que indica el estado de implementación de la aplicación Web.

    ![][12]

    El proceso de implementación de la aplicación Web en Azure debe tomar solo unos pocos segundos. Cuando su lista de aplicaciones, verá un vínculo denominado **publicado** en la columna **estado** . Al hacer clic en el vínculo, se tardará en la página de inicio de la aplicación Web implementada.

## <a name="updating-your-web-app"></a>Actualizar la aplicación Web

Actualizar una existente que se ejecuta la aplicación Web de Azure es un proceso rápido y sencillo y tiene dos opciones para actualizar:

* Puede actualizar la implementación de una aplicación Web de Java existente.
* Puede publicar una aplicación de Java adicional para el mismo contenedor de la aplicación Web.

En cualquier caso, el proceso es idéntico y tarda unos pocos segundos:

1. En el Explorador de proyectos Eclipse, haga clic en la aplicación de Java que desea actualizar o agregar a un contenedor de aplicación Web existente.

2. Cuando aparezca el menú contextual, seleccione **Azure** y, a continuación, en **Publicar como Azure Web App...**

3. Puesto que ya ha iniciado previamente, verá una lista de los contenedores de la aplicación Web existentes. Seleccione el que desea publicar o volver a publicar la aplicación Java y haga clic en **Aceptar**.

Unos segundos más tarde, la vista del **Registro de actividad de Azure** mostrará la implementación actualizada como **publicado** y podrá comprobar su aplicación actualizado en un explorador web.

## <a name="stopping-an-existing-web-app"></a>Detener una aplicación Web existente

Para detener un aplicación Web de Azure contenedor existente, (incluidos todas las aplicaciones de Java implementadas en él), puede usar la vista del **Explorador de Azure** .

Si la vista del **Explorador de Azure** no está ya abierta, puede abrir, haga clic en menú de la **ventana** de Eclipse, a continuación, haga clic en **Mostrar vista**, **otro...**, a continuación, **Azure**y, a continuación, haga clic en **Explorador de Azure**. Si no ha iniciado previamente, le pedirá que lo haga.

Cuando aparezca la vista del **Explorador de Azure** , use siga estos pasos para detener la aplicación Web: 

1. Expanda el nodo de **Azure** .

1. Expanda el nodo de **Aplicaciones Web** . 

1. Haga clic en la aplicación Web que desee.

1. Cuando aparezca el menú contextual, haga clic en **Detener**.

    ![][13]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes vínculos:

* [Centro para desarrolladores de Java](/develop/java/).
* [Introducción a aplicaciones Web](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[14]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png