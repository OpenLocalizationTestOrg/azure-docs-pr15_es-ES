<properties
    pageTitle="Entrega continuo con Git y Visual Studio Team Services en Azure | Microsoft Azure"
    description="Obtenga información sobre cómo configurar los proyectos de equipo de Visual Studio Team Services para usar Git automáticamente compilar e implementar la característica de Web App en servicios de aplicación de servicio de Azure o en la nube."
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Entrega continua a Azure con Visual Studio Team Services y Git

Puede utilizar proyectos de Visual Studio Team Services equipo para hospedar un repositorio Git para el código fuente y automáticamente generar e implementar en aplicaciones web de Azure o servicios de nube siempre push una confirmación al repositorio.

Necesitará 2013 de Visual Studio e instalado el SDK de Azure. Si todavía no tiene 2013 de Visual Studio, descárguelo seleccionando el vínculo **Introducción de forma gratuita** en [www.visualstudio.com](http://www.visualstudio.com). Instale el SDK de Azure desde [aquí](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Necesita una cuenta de servicios de equipo de Visual Studio para completar este tutorial: puede [Abrir una cuenta de servicios de equipo de Visual Studio de forma gratuita](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar un servicio de nube automáticamente compilar e implementar en Azure mediante Visual Studio Team Services, siga estos pasos.

## <a name="1-create-a-git-repository"></a>1: crear un repositorio Git

1. Si todavía no tiene una cuenta de Visual Studio Team Services, puede obtener uno [aquí](http://go.microsoft.com/fwlink/?LinkId=397665). Cuando se crea el proyecto de equipo, elija Git como el sistema de control de código fuente. Siga las instrucciones para conectar Visual Studio al proyecto de equipo.

2. En el **Explorador de grupo**, seleccione el vínculo **clonar este repositorio** .

    ![][3]

3. Especifique la ubicación de la copia local y, a continuación, elija el botón **Duplicar** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: crear un proyecto y confirmar al repositorio

1. En el **Explorador de equipo**, en la sección de **soluciones** , seleccione el vínculo de **nuevo** para crear un nuevo proyecto en el repositorio local.

    ![][4]

2. Puede implementar una aplicación web o un servicio de nube (aplicación de Azure) siguiendo los pasos de este tutorial. Crear un nuevo proyecto de servicio de nube de Azure o un nuevo proyecto de ASP.NET MVC. Asegúrese de que el proyecto está destinado a .NET Framework 4 o posterior. Si va a crear un proyecto de servicio de nube, agregue un rol web de ASP.NET MVC y una función de trabajo.
Si desea crear una aplicación web, elija la plantilla de proyecto de **Aplicación Web de ASP.NET** y, a continuación, elija **MVC**. Para obtener más información, vea [crear una aplicación web de ASP.NET en la aplicación de servicio de Azure](../app-service-web/web-sites-dotnet-get-started.md) .

3. Abrir el menú contextual para la solución y elija **Guardar**.

    ![][7]

4. Si es la primera vez que se ha usado Git en Visual Studio Team Services, deberá proporcionar información para identificar a usted mismo en Git. En el área de **Cambios pendientes** de **Team Explorer**, escriba su dirección de correo electrónico y nombre de usuario. Escriba un comentario para la confirmación y, a continuación, elija el botón de **confirmación** .

    ![][8]

5. Observe las opciones para incluir o excluir cambios específicos cuando se protege. Si se excluyen los cambios que desee, seleccione **Incluir todo**.

6. Ha ejecutado ahora los cambios en la copia local del repositorio. A continuación, sincronizar esos cambios con el servidor, seleccione el vínculo de **sincronización** .

## <a name="3-connect-the-project-to-azure"></a>3: conectar el proyecto a Azure

1. Ahora que ya tiene un repositorio Git en Visual Studio Team Services con parte del código fuente en ella, ya está listo para conectar su repositorio git con Azure.  En el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), seleccione la aplicación web o servicio de nube o crear una nueva seleccionando el icono + en la parte inferior izquierda y elegir **Servicio de nube** o **Aplicación Web** y, a continuación, **Crear rápido**.

    ![][9]

3. Para los servicios de nube, elija el vínculo **Configurar publicación con Visual Studio Team Services** . Para las aplicaciones web, seleccione el vínculo **Configurar la implementación de control de código fuente** .

    ![][10]

2. En el asistente, escriba el nombre de su cuenta de Visual Studio Team Services en el cuadro de texto y elija el vínculo **Autorizar ahora** . Le puede solicitar que iniciar sesión.

    ![][11]

3. En el diálogo emergente **Solicitud de conexión** , elija **Aceptar** para autorizar Azure para configurar su proyecto de equipo en Visual Studio Team Services.

    ![][12]

4. Después de autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos de equipo de Visual Studio Team Services.  Seleccione el nombre del proyecto de equipo que creó en los pasos anteriores y elija el botón del asistente marca de verificación.

    ![][13]

    La próxima vez que una confirmación de inserción al repositorio, Visual Studio Team Services se compile e implemente el proyecto en Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: desencadenar un recompilar y volver a su proyecto

1. En Visual Studio, abra un archivo y cambiarlo. Por ejemplo, cambiar el archivo `_Layout.cshtml` en las vistas\\carpeta compartida en un rol de web MVC.

    ![][17]

2. Editar el texto de pie de página para el sitio y guarde el archivo.

    ![][18]

3. En el **Explorador de soluciones**, abra el menú contextual para el nodo de la solución, el nodo de proyecto o el archivo que ha cambiado y, a continuación, elija **Confirmar**.

4. Escriba un comentario y elija **Confirmar**.

    ![][20]

5. Elija el vínculo de **sincronización** .

    ![][38]

6. Elija el vínculo de **inserción** para insertar la confirmación en el repositorio de Visual Studio Team Services. (También puede usar el botón de **sincronización** para copiar su confirmaciones en el repositorio. La diferencia es que **sincronizar** también extrae los cambios más recientes desde el repositorio.)

    ![][39]

7. Seleccione el botón **Inicio** para volver a la página de inicio de **Team Explorer** .

    ![][21]

8. Elija **crea** para ver las versiones en curso.

    ![][22]

    **Team Explorer** muestra que se ha desencadenado una compilación para su verificación.

    ![][23]

9. Para ver un registro detallado la compilación progresa, haga doble clic en el nombre de la compilación en curso.

10. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó cuando se utiliza el Asistente para vincular a Azure.  Abrir el menú contextual para la definición de compilación y elija **Editar definición de compilación**.

    ![][25]

11. En la ficha **desencadenador** , verá que la definición de compilación se establece en generarse cada de protección, de forma predeterminada. (Para un servicio de nube Visual Studio Team Services genera e implementa la rama principal en el entorno de ensayo automáticamente. Debe realizar un paso manual para implementar el sitio en vivo. Para una aplicación web que no tiene el entorno de ensayo, implementa la rama principal directamente al sitio activo.

    ![][26]

1. En la pestaña **proceso** , puede ver que el entorno de implementación se establece en el nombre de la aplicación web o servicio de nube.

    ![][27]

1. Si desea que los valores distintos de los valores predeterminados, especifique los valores de las propiedades. Las propiedades de la publicación de Azure están en la sección de **implementación** y también debe establecer parámetros de MSBuild. Por ejemplo, en una nube proyecto de servicio, para especificar una configuración de servicio que no sea la "Nube", establezca los parámetros de MSbuild en `/p:TargetProfile=[YourProfile]` donde *[YourProfile]* coincide con un archivo de configuración de servicio con un nombre como ServiceConfiguration. *YourProfile*.cscfg.

    La siguiente tabla muestra las propiedades disponibles en la sección de **implementación** :

  	|(Propiedad)|Valor predeterminado|
  	|---|---|
  	|Permitir que los certificados de confianza|Si es false, deben haber iniciado sesión certificados SSL por una entidad de certificación raíz.|
  	|Permitir actualización|Permite la implementación actualizar una implementación existente en lugar de crear uno nuevo. Conserva la dirección IP.|
  	|No elimine|Si es true, no sobrescribe una implementación no relacionada existente (actualización se permite).|
  	|Ruta de acceso a la configuración de implementación|La ruta de acceso al archivo .pubxml para una aplicación web, en relación con la carpeta raíz de la repo. Si se omite para los servicios de nube.|
  	|Entorno de implementación de SharePoint|El mismo que el nombre de servicio.|
  	|Entorno de implementación de Azure|El nombre del servicio web app o en la nube.|

1. En este momento, la compilación debe completarse correctamente.

    ![][28]

1. Si hace doble clic en el nombre de compilación, Visual Studio muestra un **Resumen de la compilación**, incluidos los resultados de pruebas de proyectos de prueba de unidad asociada.

    ![][29]

1. En el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), puede ver la implementación asociado en la pestaña **implementaciones** cuando se selecciona el entorno de ensayo.

    ![][30]

1.  Vaya a la dirección URL del sitio. Para una aplicación web, simplemente elija el botón **Examinar** en el portal. Para un servicio de nube, elija la dirección URL en la sección de **Resumen rápido** de la página de **panel** que muestra el entorno de prueba.

    Implementaciones de integración continua para los servicios de nube se publican en el entorno de prueba de forma predeterminada. Puede cambiar esta opción estableciendo la propiedad de **Entorno de servicio de nube alternativo** a **producción**. Aquí es donde es la dirección URL del sitio en la página de panel del servicio de nube.

    ![][31]

    Se abrirá una nueva pestaña del navegador para mostrar su sitio está ejecutando.

    ![][32]

1.  Si realizar otros cambios en el proyecto, desencadenador más crea y acumulará varias implementaciones. El elemento más reciente se marca como activo.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: implementar una versión anterior

Este paso es opcional. En el portal de clásico Azure, elija una implementación anterior y elija **implementar** rebobinar su sitio para un verificación en versiones anterior. Tenga en cuenta que esto desencadenar una nueva generación en TFS y crear una nueva entrada en el historial de implementación.

![][34]

## <a name="6-change-the-production-deployment"></a>6: cambiar la implementación de producción

Cuando esté listo, puede promover el entorno de ensayo en el entorno de producción eligiendo **intercambiar** en el portal de clásico de Azure. El entorno de ensayo recién implementado se transfieren a producción y el entorno de producción anterior, si la hay, se convierte en un entorno de prueba. La implementación de Active pueden diferir de producción y entornos de ensayo, pero el historial de versiones recientes de implementación es la misma independientemente del entorno.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: implementar desde una rama de trabajo.

Cuando utiliza Git, normalmente realizar cambios en una rama de trabajo e integrar en la rama principal cuando el desarrollo alcanza un Estado terminado. Durante la fase de desarrollo de un proyecto, desea Compile e implemente la rama trabajo Azure.

1. En el **Explorador de grupo**, elija el botón **Inicio** y, a continuación, elija el botón de **ramas** .

    ![][40]

2. Elija el vínculo de la **Nueva bifurcación** .

    ![][41]

3. Escriba el nombre de la rama, por ejemplo, "trabajo" y elija **Crear rama**. Esto crea una nueva bifurcación local.

    ![][42]

4. Publicar la rama. Elija el nombre de la rama en **ramas no publicados**y elija **Publicar**.

    ![][44]

6. De forma predeterminada, solo los cambios a la rama principal desencadenan una compilación continua. Para configurar la compilación continua para una rama de trabajo, elija la página **se basa** en el **Explorador de grupo**y elija **Editar definición de compilación**.

7. Abra la pestaña **Configuración de origen** . En la **supervisadas bifurca para la integración continua y crear**, elija **haga clic aquí para agregar una nueva fila**.

    ![][47]

8. Especifique la rama creado, como jefes de referencias cruzadas o trabajar.

    ![][48]

9. Realice un cambio en el código, abra el menú contextual para el archivo modificado y, a continuación, elija **Confirmar**.

    ![][43]

10. Seleccione el vínculo de **Confirmaciones no están sincronizados** y elija el botón **sincronizar** o en el vínculo **de inserción** para copiar los cambios en la copia de la rama de trabajo en Visual Studio Team Services.

    ![][45]

11. Vaya a la vista **, crea** y busque la compilación que tiene activada solo para la rama de trabajo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más sugerencias sobre cómo usar Git con Visual Studio Team Services, consulte [desarrollar y compartir su código en Git mediante Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) y para obtener información sobre el uso de un repositorio de Git no administrado por Visual Studio Team Services para publicar en Azure, vea [Implementación continua al servicio de la aplicación de Azure](../app-service-web/app-service-continuous-deployment.md). Para obtener más información sobre Visual Studio Team Services, vea [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
