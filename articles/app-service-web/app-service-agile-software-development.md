<properties
    pageTitle="Desarrollo de software ágil con el servicio de aplicación de Azure"
    description="Aprenda a crear aplicaciones complejas de alto nivel con la aplicación de servicio de Azure de manera que admite el desarrollo de software ágil."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>


# <a name="agile-software-development-with-azure-app-service"></a>Desarrollo de software ágil con el servicio de aplicación de Azure #

En este tutorial, aprenderá a crear aplicaciones complejas de alto nivel con la [Aplicación de servicio de Azure](/services/app-service/) de manera que admite el [desarrollo de software ágil](https://en.wikipedia.org/wiki/Agile_software_development). Se supone que ya sabe cómo [implementar aplicaciones complejas predecible en Azure](app-service-deploy-complex-application-predictably.md).

Limitaciones de procesos técnicos a menudo pueden tienen implementación correcta metodologías ágiles. Servicio de aplicaciones de Azure con características como [publicación continua](app-service-continuous-deployment.md), [entornos de ensayo](web-sites-staged-publishing.md) (ranuras) y [supervisión](web-sites-monitor.md), cuando se une sabiamente la orquestación y la administración de la instalación en el [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md), puede ser parte de una solución ideal para los desarrolladores que adoptan el desarrollo de software ágil.

En la tabla siguiente es una lista breve de requisitos asociados con el desarrollo ágil y cómo Azure services permite de ellos.

| Requisito | ¿Cómo permite Azure |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Generar con cada confirmación<br>-Generar automáticamente y rápido | Configurado con implementación continuo, servicio de aplicación de Azure puede funcionar como versiones de ejecución live basados en una rama de desarrollo. Cada vez que se inserta el código de la rama, es generada automáticamente y marcha live en Azure.|
| -Realizar pruebas automática de versiones | Cargar pruebas, pruebas web, etc., se puede implementar con la plantilla de administrador de recursos de Azure.|
| -Realizar pruebas en un duplicado del entorno de producción | Plantillas de administrador de recursos de Azure pueden utilizarse para crear copias del entorno de producción Azure (incluidos la configuración de la aplicación, plantillas de cadena de conexión, escala, etc.) para realizar pruebas rápidamente y predecible.|
| : Ver los resultados de la última generación fácilmente | Implementación continua a Azure desde un repositorio significa que puede probar el nuevo código en una aplicación activa inmediatamente después de confirmar los cambios. |
| -Confirmar en la rama principal todos los días<br>-Automatizar la implementación | La integración continua de una aplicación de producción con bifurcación principal del repositorio implementa automáticamente cada combinación de confirmación a la rama principal para producción. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Lo que debe hacer ##

Le guiará a través de un flujo de trabajo de desarrollo de prueba región de producción típico para publicar los cambios nuevos en la aplicación de ejemplo [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , que consta de dos [aplicaciones web](/services/app-service/web/), uno es un front-end (FE) y el otro Web API back-end (BE) y una [base de datos SQL](/services/sql-database/). Va a trabajar con la arquitectura de implementación que se muestra a continuación:

![](./media/app-service-agile-software-development/what-1-architecture.png)

Para colocar la imagen en palabras:

-   La arquitectura de implementación se divide en tres distintos entornos (o [grupos de recursos](../azure-resource-manager/resource-group-overview.md) en Azure), cada una con su propio [plan de servicios de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), la configuración de [escala](web-sites-scale.md) y la base de datos SQL. 
-   Cada entorno se puede administrar por separado. Incluso pueden existir en diferentes suscripciones.
-   Ensayo y producción se implementan como dos casillas de la misma aplicación de servicio de aplicación. La rama principal está configurado para la integración continua con la franja de ensayo.
-   Cuando se comprueba una confirmación de la rama principal en la franja de ensayo (con datos de producción), la aplicación de ensayo comprobada es intercambiar en la producción franja [sin tiempo de inactividad](web-sites-staged-publishing.md).

El entorno de producción y ensayo definido por la plantilla en [ * &lt;repository_root >*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Los entornos de desarrollo y prueba están definidos por la plantilla en [ * &lt;repository_root >*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

También usará la estrategia de bifurcación típica, con el código de cambio de la rama de desarrollo hasta la rama de prueba, a continuación, a la rama principal (mover hacia arriba en calidad, por decirlo).

![](./media/app-service-agile-software-development/what-2-branches.png) 

## <a name="what-you-will-need"></a>Lo que necesita ##

-   Una cuenta de Azure
-   Una cuenta de [GitHub](https://github.com/)
-   GIT Shell (instalado con [GitHub para Windows](https://windows.github.com/)) - Esto le permite ejecutar comandos Git y de PowerShell en la misma sesión 
-   Bits de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi) más recientes.
-   Fundamentos de las siguientes acciones:
    -   Implementación de la plantilla de [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) (también vea [implementar una aplicación compleja predecible en Azure](app-service-deploy-complex-application-predictably.md))
    -   [GIT](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial:
> + Puede [Abrir una cuenta de Azure gratis](/pricing/free-trial/) : obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se usan puede mantener la cuenta y usar libre Azure servicios, como aplicaciones Web.
> + Puede [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/) : Visual Studio su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.
>
> Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="set-up-your-production-environment"></a>Configurar el entorno de producción ##

>[AZURE.NOTE] La secuencia de comandos usado en este tutorial configurará automáticamente continua publicación desde el repositorio de GitHub. Esto requiere que sus credenciales de GitHub ya se almacenan en Azure, en caso contrario, la implementación de secuencias de comandos se producirá un error al intentar establecer la configuración de control de origen de las aplicaciones web. 
>
>Para almacenar sus credenciales de GitHub en Azure, cree una aplicación web en el [Portal de Azure](https://portal.azure.com/) y [Configurar la implementación de GitHub](app-service-continuous-deployment.md). Solo debe hacer esto una vez. 

En un escenario de DevOps típico tiene una aplicación que se está ejecutando live en Azure y que desea realizar cambios en él a la publicación continua. En este escenario, tiene una plantilla que desarrollado, probado y utilizado para implementar el entorno de producción. Se configurarlo en esta sección.

1.  Crear su propio horquilla del repositorio de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Para obtener información sobre la creación de la horquilla, vea [horquilla un Repo](https://help.github.com/articles/fork-a-repo/). Una vez creada la horquilla, puede ver en el explorador.
 
    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Abra una sesión de Git Shell. Si aún no tiene Git Shell, instalar ahora [GitHub para Windows](https://windows.github.com/) .

3.  Crear una copia local de la horquilla ejecutando el siguiente comando:

        git clone https://github.com/<your_fork>/ToDoApp.git 

4.  Una vez que tenga su clonar local, vaya a * &lt;repository_root >*\ARMTemplates y ejecutar la deploy.ps1 secuencia de comandos como sigue:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.  Cuando se le pida, escriba en el nombre de usuario que desee y la contraseña para el acceso de la base de datos.

    Debería ver el progreso de aprovisionamiento de diversos recursos de Azure. Cuando se complete la implementación, la secuencia de comandos se inicie la aplicación en el explorador y le ofrecen un pitido descriptivo.

    ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
    >[AZURE.TIP] Eche un vistazo a * &lt;repository_root >*\ARMTemplates\Deploy.ps1, para ver cómo genera recursos con identificadores únicos. Puede usar el mismo método para crear copias de la misma implementación sin tener que preocuparse por los nombres de los recursos en conflicto.
 
6.  Atrás en la sesión de Git Shell, ejecute:

        .\swap –Name ToDoApp<unique_string>master

    ![](./media/app-service-agile-software-development/production-4-swap.png)

7.  Cuando finalice el script, volver atrás para ir a la dirección del cliente (http://ToDoApp*&lt;unique_string >*master.azurewebsites.net/) para ver la aplicación que se ejecuta en producción.
 
5.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y eche un vistazo a lo que se crea.

    Debería poder ver dos aplicaciones web en el mismo grupo de recursos, una con el `Api` sufijo en el nombre. Si mira la vista del grupo de recursos, también verá la base de datos de SQL y servidor, el plan de servicio de aplicaciones y las posiciones de ensayo para las aplicaciones web. Navegar por los distintos recursos y compárelas con * &lt;repository_root >*\ARMTemplates\ProdAndStage.json para ver cómo se configuran en la plantilla.

    ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Ahora ha configurado el entorno de producción. A continuación, se lanzará una nueva actualización de la aplicación.

## <a name="create-dev-and-test-branches"></a>Crear desarrollo y probar ramas ##

Ahora que ya tiene una aplicación compleja en producción en Azure, se realiza una actualización en la aplicación de acuerdo con la metodología ágil. En esta sección, creará el desarrollo y probar ramas que debe realizar las actualizaciones necesarias.

1.  Crear el entorno de prueba. En la sesión de Git Shell, ejecute los comandos siguientes para crear el entorno para una nueva bifurcación denominado **NewUpdate**. 

        git checkout -b NewUpdate
        git push origin NewUpdate 
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.  Cuando se le pida, escriba en el nombre de usuario que desee y la contraseña para el acceso de la base de datos. 

    Cuando se complete la implementación, la secuencia de comandos se inicie la aplicación en el explorador y le ofrecen un pitido descriptivo. Y solo tiene que tiene ahora una nueva rama con su propio entorno de prueba. Dedique un momento a revisar consideraciones sobre este entorno de prueba:

    -   Puede crearla en cualquier suscripción Azure. Esto significa que el entorno de producción puede administrarse desde el entorno de prueba.
    -   Entorno de prueba se está ejecutando live en Azure.
    -   Entorno de prueba es idéntico al entorno de producción, salvo las ranuras provisionales y la configuración de escala. Se puede saber porque son las únicas diferencias entre ProdandStage.json y Dev.json.
    -   Puede administrar su entorno de prueba a su propio plan de servicios de aplicación, con un nivel de precio diferente (como **libre**).
    -   Eliminar este entorno de prueba será tan sencillo como eliminar el grupo de recursos. Encontrará información sobre cómo hacer este [más adelante](#delete).

2.  Continúe con para crear una rama de desarrollo, ejecute los comandos siguientes:

        git checkout -b Dev
        git push origin Dev
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.  Cuando se le pida, escriba en el nombre de usuario que desee y la contraseña para el acceso de la base de datos. 

    Dedique un momento a revisar consideraciones sobre este entorno de desarrollo: 

    -   Su entorno de desarrollo tiene una configuración idéntica al entorno de prueba porque se implementa con la misma plantilla.
    -   Cada entorno de desarrollo puede crearse en la suscripción de Azure del desarrollador, dejar el entorno de prueba que se administra por separado.
    -   Su entorno de desarrollo está ejecutando live en Azure.
    -   Eliminar el entorno de desarrollo es tan sencillo como eliminar el grupo de recursos. Encontrará información sobre cómo hacer este [más adelante](#delete).

>[AZURE.NOTE] Cuando haya varios desarrolladores trabajando en la nueva actualización, cada uno de ellos fácilmente puede crear un entorno de desarrollo dedicado y rama haciendo lo siguiente:
>
>1. Crear su propias horquilla del repositorio de GitHub (consulte [horquilla un Repo](https://help.github.com/articles/fork-a-repo/)).
>2. Clonar la horquilla en su equipo local
>3. Ejecute los comandos para crear sus propios rama de desarrollo y un entorno mismos.

Cuando haya terminado, la horquilla GitHub debería tener tres ramas:

![](./media/app-service-agile-software-development/test-1-github-view.png)

Mientras que las aplicaciones web de seis (tres series de dos) en tres grupos de recursos independiente:

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE] Observe que ProdandStage.json especifica el entorno de producción para utilizar el **estándar** precios nivel, lo que es adecuado para escalabilidad de la aplicación de producción.

## <a name="build-and-test-every-commit"></a>Crear y probar cada confirmación ##

Los archivos de plantilla ProdAndStage.json y Dev.json ya especifican los parámetros de control de origen, que de forma predeterminada configura continuo de publicación para la aplicación web. Por lo tanto, cada confirmación a la rama GitHub desencadenadores una implementación automática en Azure en esa rama. Veamos cómo funciona ahora la configuración.

1.  Asegúrese de que está en la sucursal de desarrollo del repositorio local. Para ello, ejecute el siguiente comando en el Shell de Git:

        git checkout Dev

2.  Realice un cambio simple en capas de interfaz de usuario de la aplicación cambiando el código para usar listas de [Inicio](http://getbootstrap.com/components/) . Abrir * &lt;repository_root >*\src\MultiChannelToDo.Web\index.cshtml y realizar el cambio resaltado a continuación:

    ![](./media/app-service-agile-software-development/commit-1-changes.png)

    >[AZURE.NOTE] Si no puede leer la imagen anterior: 
    >
    >- En la línea 18, cambiar `check-list` a `list-group`.
    >- En línea 19, cambie `class="check-list-item"` a `class="list-group-item"`.

3.  Guardar el cambio. Nuevo en el Git Shell, ejecute los comandos siguientes:

        cd <repository_root>
        git add .
        git commit -m "changed to bootstrap style"
        git push origin Dev
 
    Estos comandos git son similares a "proteger su código" en otro sistema de control de código fuente como TFS. Cuando se ejecuta `git push`, la confirmación nueva desencadenadores una inserción automática de código a Azure, que vuelve a crear la aplicación para reflejar el cambio en el entorno de desarrollo.

4.  Para comprobar que se ha producido esta inserción de código en el entorno de desarrollo, vaya a módulo de aplicación web de su entorno de desarrollo y busque en la parte de la **implementación** . Debería poder ver el último mensaje de confirmación allí.

    ![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.  Desde allí, haga clic en **Examinar** para ver los cambios nuevo en la aplicación activa en Azure.

    ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

    Se trata de un cambio bastante secundario a la aplicación. Sin embargo, muchas veces cambios nuevos para una aplicación web complejo tiene efectos de lado no deseados y no deseados. Probar fácilmente cada confirmación en versiones directo le permite abordar estos problemas antes de que sus clientes ven.

Por ahora, debería sentirse cómodo con la realización que, como desarrollador en el proyecto **NewUpdate** , podrá crear un entorno de desarrollo para usted mismo, a continuación, crear cada ejecutar y probar cada compilación fácilmente.

## <a name="merge-code-into-test-environment"></a>Combinar código en el entorno de prueba ##

Cuando esté listo para insertar el código de bifurcación de desarrollo hasta NewUpdate rama, es el proceso de git estándar:

1.  Combinar las nuevas se compromete a NewUpdate en la bifurcación de desarrollo en GitHub, como confirmaciones creados por otros desarrolladores. Cualquier nueva confirmación en GitHub se desencadenar una inserción de código y generar en el entorno de desarrollo. A continuación, puede asegurarse de que el código de bifurcación de desarrollo funciona con los bits de bifurcación NewUpdate más recientes.

2.  Combinar todas sus confirmaciones nuevos de bifurcación de desarrollo en rama NewUpdate en GitHub. Esta acción activa la inserción de código y de compilación en el entorno de prueba. 

Nuevamente, tenga en cuenta que porque implementación continua ya está configurado con estas bifurcaciones git, no es necesario que realice ninguna acción que ejecute la integración crea. Necesitará realizar prácticas de control de código fuente estándar usando git y Azure realizará todos los procesos de compilación para usted.

Ahora, vamos a su código de inserción **NewUpdate** rama. En el Git Shell, ejecute los comandos siguientes:

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

¡Eso es todo! 

Vaya a la hoja de aplicación web en su entorno de prueba ver su nueva confirmación (combinar NewUpdate rama) ahora se inserta en el entorno de prueba. A continuación, haga clic en **Examinar** para ver que el cambio de estilo se está ejecutando live en Azure.

## <a name="deploy-update-to-production"></a>Implemente la actualización de producción ##

Insertar código en el entorno de producción y ensayo debería sentirse no difiere mucho de lo que ya ha realizado cuando insertar código en el entorno de prueba. Es muy sencillo. 

En el Git Shell, ejecute los comandos siguientes:

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

Recuerde que en función del modo en que el entorno de producción y ensayo está configurado en ProdandStage.json, el nuevo código se inserta en la franja **de ensayo** y se está ejecutando allí. Por lo que si se desplaza a la dirección URL de la zona de ensayo, verá el nuevo código ejecutando allí. Para ello, ejecute el `Show-AzureWebsite` cmdlet del Git Shell.

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
Y ahora, después de haber comprobado la actualización en la franja de ensayo, lo único que queda por hacer es cambiar en producción. En el Git Shell, simplemente ejecute los comandos siguientes:

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

¡Felicidades! Ha publicado correctamente una actualización de nueva a la aplicación web de producción. Es más que simplemente ha hecho creando fácilmente desarrollo y probar entornos y crear y probar cada confirmación. Estos son los bloques de creación fundamentales para el desarrollo de software ágil.

<a name="delete"></a>
## <a name="delete-dev-and-test-enviroments"></a>Eliminar el desarrollo y probar entornos ##

Dado que deliberadamente han diseñado los entornos de desarrollo y prueba para grupos de recursos independientes, es muy fácil de eliminarlos. Para eliminar las que ha creado en este tutorial, la GitHub ramas y los efectos Azure simplemente ejecute los comandos siguientes en el Shell de Git:

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## <a name="summary"></a>Resumen ##

Desarrollo de software ágil es un elemento necesario para muchas empresas que desean adoptar Azure como su plataforma de aplicación. En este tutorial, ha aprendido a crear y eliminar réplicas exactas o cerca de réplicas del entorno de producción con facilidad, incluso para aplicaciones complejas. También ha aprendido a aprovechar esta capacidad para crear un proceso de desarrollo que puede crear y probar cada confirmación solo en Azure. Este tutorial Afortunadamente ha mostrado cómo puede mejor usar servicio de aplicación de Azure y Azure Resource Manager conjuntamente para crear una solución de DevOps se encarga de metodologías ágiles. A continuación, se pueden generar en este escenario realizando técnicas avanzadas de DevOps como [pruebas en producción](app-service-web-test-in-production-get-start.md). Para un escenario de producción pruebas comunes, vea [implementación de Flighting (de pruebas beta) en la aplicación de servicio de Azure](app-service-web-test-in-production-controlled-test-flight.md).

## <a name="more-resources"></a>Más recursos ##

-   [Implementar una aplicación compleja predecible en Azure](app-service-deploy-complex-application-predictably.md)
-   [Desarrollo ágil en práctica: sugerencias y trucos para el ciclo de desarrollo modernas](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-   [Estrategias avanzadas de implementación de aplicaciones Web de Azure con plantillas de administrador de recursos](http://channel9.msdn.com/Events/Build/2015/2-620)
-   [Creación de plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md)
-   [JSONLint - el control de validación JSON](http://jsonlint.com/)
-   [ARMClient: Configurar publicación GitHub al sitio](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-   [Bifurcación GIT – básico de bifurcación y combinación](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Blog de David Ebbo](http://blog.davidebbo.com/)
-   [Azure PowerShell](../powershell-install-configure.md)
-   [Herramientas de línea de comandos de Azure entre plataformas](../xplat-cli-install.md)
-   [Crear o editar usuarios en Azure AD](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-   [Proyecto Kudu Wiki](https://github.com/projectkudu/kudu/wiki)
