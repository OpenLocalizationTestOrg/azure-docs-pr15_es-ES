<properties
    pageTitle="Entrega continuo con Visual Studio Team Services en Azure | Microsoft Azure"
    description="Obtenga información sobre cómo configurar los proyectos de equipo de Visual Studio Team Services para automáticamente Compile e implemente la característica de aplicación Web de servicios de nube o servicio de la aplicación de Azure."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Entrega continua a Azure mediante Visual Studio Team Services

Puede configurar sus proyectos de equipo de Visual Studio Team Services para generar y distribuir aplicaciones web Azure o servicios de nube de automáticamente.  (Para obtener información sobre cómo configurar una compilación continua e implementar el sistema mediante un Team Foundation Server *local* , consulte [Entrega continua para los servicios de nube de Azure](cloud-services-dotnet-continuous-delivery.md)).

En este tutorial, se supone que tiene instalado el SDK de Azure y de 2013 de Visual Studio. Si todavía no tiene 2013 de Visual Studio, descárguelo seleccionando el vínculo **Introducción de forma gratuita** en [www.visualstudio.com](http://www.visualstudio.com). Instale el SDK de Azure desde [aquí](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Necesita una cuenta de servicios de equipo de Visual Studio para completar este tutorial: puede [Abrir una cuenta de servicios de equipo de Visual Studio de forma gratuita](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar un servicio de nube automáticamente compilar e implementar en Azure mediante Visual Studio Team Services, siga estos pasos.

## <a name="1-create-a-team-project"></a>1: crear un proyecto

Siga las instrucciones que aparecen [a continuación](http://go.microsoft.com/fwlink/?LinkId=512980) para crear el proyecto de equipo y vincularlo a Visual Studio. Este tutorial se supone que está utilizando el Control de versión de Team Foundation (TFVC) como la solución de control de origen. Si desea usar Git para control de versiones, vea [la versión Git de este tutorial](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2: Compruebe en un proyecto de control de código fuente

1. En Visual Studio, abra la solución que desea implementar o cree uno nuevo.
Puede implementar una aplicación web o un servicio de nube (aplicación de Azure) siguiendo los pasos de este tutorial.
Si desea crear una nueva solución, cree un nuevo proyecto de servicio de nube de Azure o un nuevo proyecto de ASP.NET MVC. Asegúrese de que el proyecto está destinado a .NET Framework 4 o 4.5 y si va a crear un proyecto de servicio de nube, agregar una función de web de ASP.NET MVC y una función de trabajo y elija la aplicación de Internet para el rol de web. Cuando se le pida, elija la **Aplicación de Internet**.
Si desea crear una aplicación web, elija la plantilla de proyecto de aplicación Web de ASP.NET y, a continuación, elija MVC. Consulte [crear una aplicación web de ASP.NET en la aplicación de servicio de Azure](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Visual Studio Team Services solo admite implementaciones de elemento de configuración de Visual Studio Web Applications en este momento. Proyectos de sitio Web están fuera del ámbito.

1. Abrir el menú contextual para la solución y elija **Agregar solución al Control de código fuente**.

    ![][5]

1. Aceptar o cambiar los valores predeterminados y elija el botón **Aceptar** . Una vez finalizado el proceso, aparecen iconos de control de código fuente en el **Explorador de soluciones**.

    ![][6]

1. Abrir el menú contextual para la solución y elija **Proteger**.

    ![][7]

1. En el área de **Cambios pendientes** de **Team Explorer**, escriba un comentario para la verificación en y elija el botón **Proteger** .

    ![][8]

    Observe las opciones para incluir o excluir cambios específicos cuando se protege. Si se excluyen los cambios que desee, elija el vínculo **Incluir todo** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3: conectar el proyecto a Azure

1. Ahora que ya tiene un proyecto de equipo VS Team Services con parte del código fuente en ella, ya está listo para conectarse a su proyecto de equipo de Azure.  En el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), seleccione la aplicación web o servicio de nube o cree uno nuevo eligiendo la **+** icono en la parte inferior izquierda y elegir **Servicio de nube** o **Aplicación Web** y, a continuación, **Crear rápido**. Elija el vínculo **Configurar publicación con Visual Studio Team Services** .

    ![][10]

1. En el asistente, escriba el nombre de su cuenta de Visual Studio Team Services en el cuadro de texto y haga clic en el vínculo **Autorizar ahora** . Le puede solicitar que iniciar sesión.

    ![][11]

1. En el diálogo emergente **Solicitud de conexión** , elija el botón **Aceptar** para autorizar Azure para configurar su proyecto de equipo en servicios de grupo de VS.

    ![][12]

1. Cuando se complete la operación de autorización, verá una lista desplegable que contiene una lista de los proyectos de equipo de Visual Studio Team Services. Elija el nombre del proyecto de equipo que creó en los pasos anteriores y, a continuación, elija el botón del asistente marca de verificación.

    ![][13]

1. Cuando se vincula un proyecto, verá algunas instrucciones para proteger cambios en el proyecto de equipo de Visual Studio Team Services.  En la siguiente comprobación de, Visual Studio Team Services se compile e implemente el proyecto en Azure.  Pruebe esto ahora haciendo clic en el vínculo **Comprobar en desde Visual Studio** y, a continuación, el vínculo de **Inicio de Visual Studio** (o el equivalente **Visual Studio** botón en la parte inferior de la pantalla portal).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: desencadenar un recompilar y volver a su proyecto

1. En Visual Studio **Team Explorer**, seleccione el vínculo del **Explorador de Control de código fuente** .

    ![][15]

1. Desplácese hasta el archivo de solución y abrirlo.

    ![][16]

1. En el **Explorador de soluciones**, abra un archivo y cambiarlo. Por ejemplo, cambiar el archivo `_Layout.cshtml` en las vistas\\carpeta compartida en un rol de web MVC.

    ![][17]

1. Modificar el logotipo del sitio y elija **CTRL+s** para guardar el archivo.

    ![][18]

1. En el **Explorador de grupo**, seleccione el vínculo **Cambios pendientes** .

    ![][19]

1. Escriba un comentario y, a continuación, elija el botón **Proteger** .

    ![][20]

1. Seleccione el botón **Inicio** para volver a la página de inicio de **Team Explorer** .

    ![][21]

1. Seleccione el vínculo **crea** para ver las versiones en curso.

    ![][22]

    **Team Explorer** muestra que se ha desencadenado una compilación para su verificación.

    ![][23]

1. Haga doble clic en el nombre de la compilación en curso para ver un registro detallado progresa de compilación.

    ![][24]

1. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vinculado TFS a Azure mediante el Asistente para.  Abrir el menú contextual para la definición de compilación y elija **Editar definición de compilación**.

    ![][25]

    En la ficha **desencadenador** , verá que la definición de compilación se establece en generarse cada casilla en predeterminada.

    ![][26]

    En la pestaña **proceso** , puede ver que el entorno de implementación se establece en el nombre de la aplicación web o servicio de nube. Si trabaja con aplicaciones web, las propiedades que aparecen será diferentes a las que se muestra aquí.

    ![][27]

1. Si desea que los valores distintos de los valores predeterminados, especifique los valores de las propiedades. Las propiedades de la publicación de Azure se encuentran en la sección de **implementación** .

    La siguiente tabla muestra las propiedades disponibles en la sección de **implementación** :

  	|(Propiedad)|Valor predeterminado|
  	|---|---|
  	|Permitir que los certificados de confianza|Si es false, deben haber iniciado sesión certificados SSL por una entidad de certificación raíz.|
  	|Permitir actualización|Permite la implementación actualizar una implementación existente en lugar de crear uno nuevo. Conserva la dirección IP.|
  	|No elimine|Si es true, no sobrescribe una implementación no relacionada existente (actualización se permite).|
  	|Ruta de acceso a la configuración de implementación|La ruta de acceso al archivo .pubxml para una aplicación web, en relación con la carpeta raíz de la repo. Si se omite para los servicios de nube.|
  	|Entorno de implementación de SharePoint|El mismo que el nombre de servicio.|
  	|Entorno de implementación de Azure|El nombre del servicio web app o en la nube.|

1. Si usa varias configuraciones de servicio (archivos .cscfg), puede especificar la configuración de servicios deseada en la configuración de **compilación avanzadas, MSBuild argumentos** . Por ejemplo, para usar ServiceConfiguration.Test.cscfg, establezca los argumentos de MSBuild opción línea `/p:TargetProfile=Test`.

    ![][38]

    En este momento, la compilación debe completarse correctamente.

    ![][28]

1. Si hace doble clic en el nombre de compilación, Visual Studio muestra un **Resumen de la compilación**, incluidos los resultados de pruebas de proyectos de prueba de unidad asociada.

    ![][29]

1. En el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), puede ver la implementación asociado en la pestaña **implementaciones** cuando se selecciona el entorno de ensayo.

    ![][30]

1.  Vaya a la dirección URL del sitio. Para una aplicación web, haga clic en el botón **Examinar** en la barra de comandos. Para un servicio de nube, elija la dirección URL en la sección de **Resumen rápido** de la página de **panel** que muestra el entorno de prueba de un servicio de nube. Implementaciones de integración continua para los servicios de nube se publican en el entorno de prueba de forma predeterminada. Puede cambiar esta opción estableciendo la propiedad de **Entorno de servicio de nube alternativo** a **producción**. Esta captura de pantalla se muestra la dirección URL del sitio se encuentra en la página de panel del servicio de nube.

    ![][31]

    Se abrirá una nueva pestaña del navegador para mostrar su sitio está ejecutando.

    ![][32]

    Para los servicios de nube, si realizar otros cambios en el proyecto, desencadenador más crea y acumulará varias implementaciones. La última como como activa.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: implementar una versión anterior

Este paso es opcional y se aplica a servicios en la nube. En el portal de clásico Azure, elija una implementación anterior y, a continuación, elija el botón **volver a implementar** para rebobinar el sitio de un verificación en versiones anterior.  Tenga en cuenta que esto desencadenar una nueva generación en TFS y crear una nueva entrada en el historial de implementación.

![][34]

## <a name="6-change-the-production-deployment"></a>6: cambiar la implementación de producción

Este paso solo se aplica a servicios en la nube, no las aplicaciones de web. Cuando esté listo, puede promover el entorno de ensayo en el entorno de producción seleccionando el botón **intercambiar** en el portal de clásico de Azure. El entorno de ensayo recién implementado se transfieren a producción y el entorno de producción anterior, si la hay, se convierte en un entorno de prueba. La implementación de Active pueden diferir de producción y entornos de ensayo, pero el historial de versiones recientes de implementación es la misma independientemente del entorno.

![][35]

## <a name="7-run-unit-tests"></a>7: ejecutar pruebas unitarias

Este paso solo se aplica a aplicaciones web, servicios de nube no. Para poner una puerta de calidad en la implementación, puede ejecutar pruebas unitarias y si se produce un error, puede dejar la implementación.

1.  En Visual Studio, agregue un proyecto de prueba de unidad.

    ![][39]

1.  Agregar referencias de proyecto para el proyecto que desee probar.

    ![][40]

1.  Agregar varias pruebas de unidades. Para empezar, pruebe una prueba ficticia que siempre pasará.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Editar la definición de compilación, elija la pestaña **proceso** y expanda el nodo de **prueba** .

1.  Establezca la **compilación de éxito en caso de error de prueba** en True. Esto significa que no se produce la implementación a menos que pasan las pruebas.

    ![][41]

1.  Cola de una nueva compilación.

    ![][42]

    ![][43]

1. Mientras está realizando la compilación, compruebe su progreso.

    ![][44]

    ![][45]

1. Cuando haya terminado la compilación, compruebe los resultados.

    ![][46]

    ![][47]

1.  Pruebe a crear una prueba que se producirá un error. Agregar una nueva prueba copiando primero, cambiarle el nombre y comente la línea de código que indica que una NotImplementedException es una excepción esperada.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Proteger el cambio a una nueva generación de cola.

    ![][48]

1. Ver los resultados de pruebas para ver los detalles del error.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de pruebas unitarias en Visual Studio Team Services, vea [Ejecutar pruebas de unidad en la compilación](http://go.microsoft.com/fwlink/p/?LinkId=510474). Si está usando Git, vea [compartir el código en Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [implementación continua al servicio de la aplicación de Azure](../app-service-web/app-service-continuous-deployment.md).  Para obtener más información acerca de los servicios de equipo de Visual Studio, vea [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
