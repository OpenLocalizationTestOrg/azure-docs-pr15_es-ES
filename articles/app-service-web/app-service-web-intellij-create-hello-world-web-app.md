<properties 
    pageTitle="Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ | Microsoft Azure" 
    description="En este tutorial se muestra cómo usar el Kit de herramientas de Azure para IntelliJ para crear un saludo World Online para Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-intellij"></a>Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ

Este tutorial muestra cómo crear e implementar una aplicación básica de Hola a todos en Azure como una aplicación Web mediante el [Kit de herramientas de Azure para IntelliJ]. Se muestra un ejemplo JSP básico para simplificar, pero es muy similares pasos sería adecuados para un servlet Java, lo que se refiere a implementación de Azure.

Cuando haya completado este tutorial, la aplicación tendrá un aspecto similar a la siguiente ilustración cuando se ve en un explorador web:

![][01]
 
## <a name="prerequisites"></a>Requisitos previos

* Un Java Developer Kit (JDK), v 1,8 o posterior.
* IntelliJ IDEA Ultimate Edition. Esto puede descargarse desde <https://www.jetbrains.com/idea/download/index.html>.
* Una distribución de un servidor web basado en Java o el servidor de aplicaciones, como Apache Tomcat o embarcadero.
* Una suscripción de Azure, que se puede adquirir de <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El Kit de herramientas de Azure para IntelliJ. Para obtener más información, vea [instalar el Kit de herramientas de Azure para IntelliJ].

## <a name="to-create-a-hello-world-application"></a>Para crear una aplicación del mundo Hola a todos

En primer lugar, empezaremos con la creación de un proyecto de Java.

1. Iniciar IntelliJ, en el menú, haga clic en **archivo**y, a continuación, **nuevo**y, a continuación, haga clic en **proyecto**.

   ![][02]

1. En el cuadro de diálogo nuevo proyecto, seleccione **Java**, a continuación, **Aplicación Web**y, a continuación, haga clic en **siguiente**.

   ![][03a]

   Si se le pide que continúe con ningún SDK asignado, haga clic en **Sí**.

   ![][03b]

1. Para el propósito de este tutorial, el nombre del proyecto **Java aplicación Web en Azure**y, a continuación, haga clic en **Finalizar**.

   ![][04]

1. Dentro de la vista de explorador de proyectos de IntelliJ expandir **Java aplicación Web en Azure**, a continuación, expanda **web**y, a continuación, haga doble clic en **index.jsp**.

   ![][05]

1. Cuando se abre el archivo index.jsp en IntelliJ, agregar texto para mostrar dinámicamente **Hola a todos!** dentro de la existente `<body>` elemento. La actualización `<body>` contenido debe ser similar en el siguiente ejemplo:

   `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Guardar index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Para implementar la aplicación a un contenedor de la aplicación Web de Azure

Hay varias maneras de implementar una aplicación web de Java en Azure. Este tutorial describe uno de los más sencillo: la aplicación se implementará en un contenedor de aplicación Web de Azure - ningún tipo de proyecto especial ni herramientas adicionales son necesarias. El JDK y el software de contenedor web proporcionará para usted Azure, por lo que no es necesario para cargar sus propios; todo lo que necesita es la aplicación Web de Java. Como resultado, el proceso de publicación para su aplicación tardará a segundos, no en minutos.

1. En el Explorador de proyectos de IntelliJ, haga clic en el proyecto **Java aplicación Web en Azure** . Cuando aparezca el menú contextual, seleccione **Azure**y, a continuación, haga clic en **Publicar como Azure Web App...**

   ![][06]

1. Si no ha iniciado en Azure desde IntelliJ, se le pedirá que inicie sesión en su cuenta de Azure:

   ![][07]

   Nota: Si tiene varias cuentas de Azure, algunas de las indicaciones durante el proceso de inicio de sesión pueden mostrarlo más de una vez, incluso si parecen ser los mismos. Cuando ocurre esto, siga las instrucciones de inicio de sesión.

1. Después de que ha iniciado sesión en su cuenta de Azure, el cuadro de diálogo **Administrar suscripciones** mostrará una lista de suscripciones que están asociados con sus credenciales. Si hay varias suscripciones mencionados y desea trabajar con solo un subconjunto específico de ellos, se pueden desactivar la opción los que quieras utilizar. Cuando haya seleccionado las suscripciones, haga clic en **Cerrar**.

   ![][08]

1. Cuando aparezca el cuadro de diálogo **implementar al contenedor de aplicación Web de Azure** , aparecerán los contenedores de aplicación Web que ha creado previamente; Si no ha creado ningún contenedores, la lista estará vacía.   

   ![][09]

1. Si no ha creado un contenedor de aplicación Web de Azure antes, o si desea publicar la aplicación a un nuevo contenedor, realice los pasos siguientes. En caso contrario, seleccione un contenedor de aplicación Web existente y vaya al paso 6 más abajo.

  1. Haga clic en**+**

        ![][10]

  1. Aparecerá el cuadro de diálogo **Nuevo contenedor de aplicación Web** que se utilizará para los pasos siguientes.

        ![][11]

  1. Escriba una **etiqueta DNS** para el contenedor de la aplicación Web; Esto formar la etiqueta DNS de la hoja de la dirección URL del host de la aplicación web de Azure. Nota: El nombre debe estar disponible y se ajustan a los requisitos de los nombres de Azure Web App.

  1. En el menú desplegable de **Contenedor de Web** , seleccione el software adecuado para su aplicación.

        Actualmente, puede elegir entre 8 Tomcat, Tomcat 7 o embarcadero 9. Azure proporciona una distribución reciente del software seleccionado y, a continuación, se ejecuta en una distribución de JDK 8 creada por Oracle y proporcionado por Azure reciente.

  1. En el menú desplegable de **suscripción** , seleccione la suscripción que desea utilizar para esta implementación.

  1. En el menú desplegable del **Grupo de recursos** , seleccione el grupo de recursos con la que desea asociar la aplicación Web.

        Nota: Los grupos de recursos Azure permiten agrupar recursos relacionados para que, por ejemplo, puede eliminarse juntos.

        Puede seleccionar un grupo de recursos existente (si tiene alguna) y saltar al paso siguiente g o utilice la siguiente estos pasos para crear un nuevo grupo de recursos:

      * Haga clic en **nuevo...**

      * Se mostrará el cuadro de diálogo **Nuevo grupo de recursos** :

            ![][12]

      * En el cuadro de texto de **nombre** , especifique un nombre para el nuevo grupo de recursos.

      * En el menú de lista desplegable **región** , seleccione los datos de Azure adecuados centrar la ubicación del grupo de recursos.

      * Haga clic en **Aceptar**.

  1. El menú desplegable de **Plan de servicio de aplicación** enumera los planes de servicio de aplicación que están asociados con el grupo de recursos que haya seleccionado.

        Nota: Un Plan de servicio de aplicación especifica información como la ubicación de la aplicación Web, el nivel de precios y el tamaño de la instancia de cálculo. Un Plan de servicio de aplicación única puede usarse para varias aplicaciones Web, que es el motivo por el se mantiene por separado de una implementación específica de la aplicación Web.

        Puede seleccionar una existente aplicación Plan servicio (si tiene alguna) y saltar al paso siguiente de h o utilice la siguiente estos pasos para crear un nuevo Plan de servicio de aplicación:

      * Haga clic en **nuevo...**

      * Se mostrará el cuadro de diálogo **Nuevo Plan de servicio de aplicación** :

            ![][13]

      * En la el cuadro de texto **nombre** , especifique un nombre para el nuevo Plan de servicio de aplicación.

      * En el menú de lista desplegable **ubicación** , seleccione los datos de Azure adecuados centrar ubicación para el plan.

      * En la el menú desplegable de **Nivel de precios** , seleccione el precio adecuado para el plan. Puede elegir **libre**con fines de pruebas.

      * En la el **Tamaño de la instancia** menú desplegable, seleccione Cambiar el tamaño de la instancia adecuada para el plan. Puede elegir **pequeña**con fines de pruebas.

  1. Una vez haya completado todos los pasos anteriores, el cuadro de diálogo nuevo contenedor de aplicación Web debe ser similar a la siguiente ilustración:

        ![][14]

  1. Haga clic en **Aceptar** para completar la creación de su nuevo contenedor de Web App.

        Espere unos segundos para que se actualice la lista de los contenedores de Web App y, a continuación, el contenedor de la aplicación web recién creado ahora debe estar seleccionado en la lista.

1. Ya está listo para completar la implementación inicial de la aplicación Web de Azure; Haga clic en **Aceptar** para implementar la aplicación de Java en el contenedor seleccionado de la aplicación Web.

    ![][15]

    Nota: de forma predeterminada, la aplicación se implementará como un subdirectorio del servidor de aplicaciones. Si desea que su implementación como la aplicación raíz, marque la casilla de **implementar a raíz** antes de hacer clic en **Aceptar**.

1. A continuación, debería ver la vista del **Registro de actividad de Azure** , que indica el estado de implementación de la aplicación Web.

    ![][16]

    El proceso de implementación de la aplicación Web en Azure debe tomar solo unos pocos segundos. Cuando su lista de aplicaciones, verá un vínculo denominado **publicado** en la columna **estado** . Al hacer clic en el vínculo, se tardará en la página de inicio de la aplicación Web implementada, o puede usar los pasos de la sección siguiente para ir a la aplicación web.

## <a name="browsing-to-your-web-app-on-azure"></a>Exploración de la aplicación Web de Azure

Para brows a la aplicación Web de Azure, puede usar la vista del **Explorador de Azure** .

Si la vista del **Explorador de Azure** no está ya abierta, puede abrir, haga clic en menú de **vista** en IntelliJ, a continuación, haga clic en **Herramienta de Windows**y, a continuación, haga clic en **Explorador de servicio**. Si no ha iniciado previamente, le pedirá que lo haga.

Cuando aparezca la vista del **Explorador de Azure** , use siga estos pasos para detener la aplicación Web: 

1. Expanda el nodo de **Azure** .

1. Expanda el nodo de **Aplicaciones Web** . 

1. Haga clic en la aplicación Web que desee.

1. Cuando aparezca el menú contextual, haga clic en **Abrir en el explorador**.

    ![][17]

## <a name="updating-your-web-app"></a>Actualizar la aplicación Web

Actualizar una existente que se ejecuta la aplicación Web de Azure es un proceso rápido y sencillo y tiene dos opciones para actualizar:

* Puede actualizar la implementación de una aplicación Web de Java existente.
* Puede publicar una aplicación de Java adicional para el mismo contenedor de la aplicación Web.

En cualquier caso, el proceso es idéntico y tarda unos pocos segundos:

1. En el explorador del proyecto IntelliJ, haga clic en la aplicación de Java que desea actualizar o agregar a un contenedor de aplicación Web existente.

1. Cuando aparezca el menú contextual, seleccione **Azure** y, a continuación, en **Publicar como Azure Web App...**

1. Puesto que ya ha iniciado previamente, verá una lista de los contenedores de la aplicación Web existentes. Seleccione el que desea publicar o volver a publicar la aplicación Java y haga clic en **Aceptar**.

Unos segundos más tarde, la vista del **Registro de actividad de Azure** mostrará la implementación actualizada como **publicado** y podrá comprobar su aplicación actualizado en un explorador web.

## <a name="starting-or-stopping-an-existing-web-app"></a>Iniciar o detener una aplicación Web existente

Para iniciar o detener un contenedor de aplicación Web de Azure existente, (incluidos todas las aplicaciones de Java implementadas en él), puede usar la vista del **Explorador de Azure** .

Si la vista del **Explorador de Azure** no está ya abierta, puede abrir, haga clic en menú de **vista** en IntelliJ, a continuación, haga clic en **Herramienta de Windows**y, a continuación, haga clic en **Explorador de servicio**. Si no ha iniciado previamente, le pedirá que lo haga.

Cuando aparezca la vista del **Explorador de Azure** , use siga estos pasos para iniciar o detener la aplicación Web: 

1. Expanda el nodo de **Azure** .

1. Expanda el nodo de **Aplicaciones Web** . 

1. Haga clic en la aplicación Web que desee.

1. Cuando aparezca el menú contextual, haga clic en **Iniciar** o **Detener**. Tenga en cuenta que las opciones de menú reconocen el contexto, para que solo puedan dejar una aplicación web ejecución o iniciar una aplicación web que no se está ejecutando.

    ![][18]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los Toolkits de Azure para Java IDE, consulte los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - [Instalar el Kit de herramientas de Azure Eclipse]
  - [Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]
  - [Novedades en el Kit de herramientas de Azure para Eclipse]
- [Kit de herramientas de Azure para IntelliJ]
  - [Instalar el Kit de herramientas de Azure IntelliJ]
  - *Crear una aplicación Web de llamadas internacionales de saludo para Azure en IntelliJ (en este artículo)*
  - [Novedades en el Kit de herramientas de Azure para IntelliJ]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure].

Para obtener información adicional sobre la creación de aplicaciones Web de Azure, vea [Introducción a aplicaciones Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Crear una aplicación Web de llamadas internacionales de saludo para Azure en Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Instalar el Kit de herramientas de Azure Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Instalar el Kit de herramientas de Azure IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novedades en el Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novedades en el Kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a aplicaciones Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png
