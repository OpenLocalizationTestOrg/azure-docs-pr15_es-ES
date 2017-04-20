<properties
    pageTitle="Implementación flighting (de pruebas beta) en la aplicación de servicio de Azure"
    description="Aprenda a nuevas características de la aplicación de vuelo o beta prueba las actualizaciones en este tutorial to-end. Reúne las características de servicio de aplicación como publicación continua, ranuras, enrutamiento de tráfico e integración de información de la aplicación."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cephalin"/>
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Implementación flighting (de pruebas beta) en la aplicación de servicio de Azure

En este tutorial se muestra cómo realizar *implementaciones flighting* mediante la integración de las distintas capacidades de [Servicio de la aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y [Perspectivas de aplicación de Azure](/services/application-insights/). 

*Flighting* es un proceso de implementación que valida una nueva característica o cambiar con un número limitado de los clientes reales y está probando un importante en caso de producción. Es similar a pruebas beta y a veces se conoce como "prueba controlado vuelo". Muchas grandes empresas con una presencia web utilizan este enfoque para obtener validación anticipado en sus actualizaciones de la aplicación en su práctica de [desarrollo ágil](https://en.wikipedia.org/wiki/Agile_software_development). Azure de aplicación de servicio permite integrar prueba en producción con publicación continua y perspectivas de aplicación para implementar el mismo escenario DevOps. Ventajas de este enfoque incluyen:

- **Ganancia se publican actualizaciones de _antes_ de comentarios real a producción** - algo mejor que tengan comentarios tan pronto como soltar es obtener comentarios antes de soltar. Puede probar actualizaciones con tráfico de usuarios real y comportamientos tan pronto como se desee en el ciclo de vida del producto.
- **Mejorar [continuo desarrollo basado en pruebas (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development) ** - mediante la integración de prueba en producción con integración continua e instrumentación con recomendaciones de aplicación, validación de usuario ocurre temprano y automáticamente en su ciclo de vida del producto. Esto ayuda a reducir las inversiones de tiempo de ejecución de prueba manual.
- **Optimizar probar el flujo de trabajo** - mediante la automatización de prueba de producción con instrumentación supervisión continua, puede potencialmente lograr los objetivos de los distintos tipos de pruebas en un único proceso, como la [integración](https://en.wikipedia.org/wiki/Integration_testing), [regresión](https://en.wikipedia.org/wiki/Regression_testing), [uso](https://en.wikipedia.org/wiki/Usability_testing), accesibilidad, localización, [rendimiento](https://en.wikipedia.org/wiki/Software_performance_testing), [seguridad](https://en.wikipedia.org/wiki/Security_testing)y [aceptación](https://en.wikipedia.org/wiki/Acceptance_testing).

Una implementación flighting prácticamente no enruta el tráfico directo. En una implementación que desea conocer lo antes posible, sean un error inesperado, degradación del rendimiento, problemas de la experiencia de usuario. Recuerde que está trabajando con los clientes reales. Así que hacerlo derecha, debe asegurarse de que ha configurado su implementación flighting para recopilar todos los datos que necesita para tomar una decisión informada para el siguiente paso. Este tutorial muestra cómo recopilar datos con la información de la aplicación, pero puede usar nueva Relic u otras tecnologías que se adapte a su caso. 

## <a name="what-you-will-do"></a>Lo que debe hacer

En este tutorial, aprenderá a poner las situaciones siguientes para probar la aplicación de servicio de aplicaciones de producción:

- [Enrutar el tráfico de producción](app-service-web-test-in-production-get-start.md) a su aplicación de beta
- [Instrumento su aplicación](../application-insights/app-insights-web-track-usage.md) para obtener la métrica útil
- Implemente la aplicación beta continuamente y seguimiento de las mediciones de la aplicación activa
- Comparar métricas entre la aplicación de producción y la aplicación de beta para ver cómo se convierten los cambios de código para los resultados

## <a name="what-you-will-need"></a>Lo que necesita

-   Una cuenta de Azure
-   Una cuenta de [GitHub](https://github.com/)
- 2015 de Visual Studio - puede descargar la [Edición de la Comunidad](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).
-   GIT Shell (instalado con [GitHub para Windows](https://windows.github.com/)) - Esto le permite ejecutar comandos Git y de PowerShell en la misma sesión
-   Bits de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) más recientes.
-   Fundamentos de las siguientes acciones:
    -   Implementación de la plantilla de [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) (vea [implementar una aplicación compleja predecible en Azure](app-service-deploy-complex-application-predictably.md))
    -   [GIT](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial:
> + Puede [Abrir una cuenta de Azure gratis](/pricing/free-trial/) : obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se usan puede mantener la cuenta y usar libre Azure servicios, como aplicaciones Web.
> + Puede [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/) : Visual Studio su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.
>
> Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="set-up-your-production-web-app"></a>Configurar la aplicación web de producción

>[AZURE.NOTE] La secuencia de comandos usado en este tutorial configurará automáticamente continua publicación desde el repositorio de GitHub. Esto requiere que sus credenciales de GitHub ya se almacenan en Azure, en caso contrario, la implementación de secuencias de comandos se producirá un error al intentar establecer la configuración de control de origen de las aplicaciones web.
>
>Para almacenar sus credenciales de GitHub en Azure, cree una aplicación web en el [Portal de Azure](https://portal.azure.com/) y [Configurar la implementación de GitHub](app-service-continuous-deployment.md#Step7). Solo debe hacer esto una vez.

En un escenario de DevOps típico tiene una aplicación que se está ejecutando live en Azure y que desea realizar cambios en él a la publicación continua. En este escenario, se implementará en producción una plantilla que ha desarrollado y probado.

1.  Crear su propio horquilla del repositorio de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Para obtener información sobre la creación de la horquilla, vea [horquilla un Repo](https://help.github.com/articles/fork-a-repo/). Una vez creada la horquilla, puede ver en el explorador.

    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Abra una sesión de Git Shell. Si aún no tiene Git Shell, instalar ahora [GitHub para Windows](https://windows.github.com/) .
3.  Crear una copia local de la horquilla ejecutando el siguiente comando:

        git clone https://github.com/<your_fork>/ToDoApp.git

4.  Una vez que tenga su clonar local, vaya a * &lt;repository_root >*\ARMTemplates y ejecutar la deploy.ps1 secuencia de comandos con un sufijo único, como se muestra a continuación:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.  Cuando se le pida, escriba en el nombre de usuario que desee y la contraseña para el acceso de la base de datos. Recuerde las credenciales de la base de datos porque debe especificarlos cuando se actualiza el grupo de recursos.

    Debería ver el progreso de aprovisionamiento de diversos recursos de Azure. Cuando se complete la implementación, la secuencia de comandos se inicie la aplicación en el explorador y le ofrecen un pitido descriptivo.
    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.  Atrás en la sesión de Git Shell, ejecute:

        .\swap –Name ToDoApp<your_suffix>

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.  Cuando finalice el script, volver atrás para ir a la dirección del cliente (http://ToDoApp*&lt;your_suffix >*.azurewebsites.net/) para ver la aplicación que se ejecuta en producción.
5.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y eche un vistazo a lo que se crea.

    Debería poder ver dos aplicaciones web en el mismo grupo de recursos, una con el `Api` sufijo en el nombre. Si mira la vista del grupo de recursos, también verá la base de datos de SQL y servidor, el plan de servicio de aplicaciones y las posiciones de ensayo para las aplicaciones web. Navegar por los distintos recursos y compárelas con * &lt;repository_root >*\ARMTemplates\ProdAndStage.json para ver cómo se configuran en la plantilla.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Ha configurado la aplicación de producción.  Ahora, imaginemos que recibe comentarios que uso es una mala para la aplicación. Por lo que decide investigar. Va instrumentación de la aplicación para enviar sus comentarios.

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>Investigar: Instrumentación la aplicación cliente de supervisión y métricas

5. Abrir * &lt;repository_root >*\src\MultiChannelToDo.sln en Visual Studio.
6. Restaurar todos los paquetes de Nuget pulsando solución > **Administrar paquetes de NuGet para solución** > **Restaurar**.
6. Haga clic con el botón **MultiChannelToDo.Web** > **Agregar aplicación perspectivas telemetría** > **Configurar opciones** > grupo de recursos de cambio de ToDoApp*&lt;your_suffix >* > **Agregar perspectivas de aplicación al proyecto**.
7. En el Portal de Azure, abra el módulo para el recurso de información de la aplicación **MultiChannelToDo.Web** . En el elemento de **estado de la aplicación** , haga clic en **obtener información sobre cómo recopilar datos de carga de página de explorador** > Copiar código.
7. Agregar el código de instrumentación JS copiado a * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml inmediatamente antes de cerrar `<heading>` etiqueta. Debe contener la tecla instrumentación únicos de los recursos de una perspectiva de la aplicación.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Enviar eventos personalizados a la información de la aplicación para mouse clics agregando el código siguiente a la parte inferior del cuerpo:

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Este fragmento de código de JavaScript envía un evento personalizado a perspectivas de aplicación cada vez que un usuario hace clic en cualquier parte de la aplicación web.

12. En el Git Shell, confirme y aplicar los cambios a la horquilla en GitHub. A continuación, espere a que los clientes actualizar el explorador.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.  Intercambiar los cambios de la aplicación implementada en producción:

        .\swap –Name ToDoApp<your_suffix>

13. Busque el recurso de información de la aplicación que ha configurado. Haga clic en eventos personalizados.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Si no ve métricas para los eventos personalizados, espere unos minutos y haga clic en **Actualizar**.

Supongamos que verá un gráfico como las siguientes:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

Y la cuadrícula de evento debajo del mismo:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

Según el código de la aplicación ToDoApp, el evento de **botón** se corresponde con el botón Enviar y, a continuación, el evento de **entrada** se corresponde con el cuadro de texto. Hasta ahora, las cosas que tenga sentido. Sin embargo, parece que hay una buena cantidad de clics y muy pocos clics en los elementos de tareas pendientes (los eventos **LI** ).

En función de esto, el formulario la hipótesis que algunos usuarios confundir qué parte de la interfaz de usuario es interactivo y es porque el cursor estilo de selección de texto cuando mantiene el mouse sobre los elementos de lista y sus iconos.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Esto podría ser un ingenioso ejemplo. Sin embargo, se va a realizar una mejora a su aplicación y, a continuación, realice una implementación flighting para obtener comentarios de facilidad de uso de los clientes.

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>Instrumentación de la aplicación de servidor de supervisión y métricas
Esta es una tangente desde el escenario que se muestra en este tutorial se trata solo con la aplicación de cliente. Sin embargo, para conclusión se configure la aplicación de servidor.

6. Haga clic con el botón **MultiChannelToDo** > **Agregar aplicación perspectivas telemetría** > **Configurar opciones** > grupo de recursos de cambio de ToDoApp*&lt;your_suffix >* > **Agregar perspectivas de aplicación al proyecto**.
12. En el Git Shell, confirme y aplicar los cambios a la horquilla en GitHub. A continuación, espere a que los clientes actualizar el explorador.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.  Intercambiar los cambios de la aplicación implementada en producción:

        .\swap –Name ToDoApp<your_suffix>

¡Eso es todo!

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>Investigar: Agregar etiquetas específicas de franja a las métricas de aplicación de cliente

En esta sección, configurará las ranuras de implementación diferentes para enviar telemetría franja específicas para el mismo recurso de información de la aplicación. De este modo, puede comparar los datos de telemetría entre el tráfico de diferentes slots (entornos de implementación) para ver fácilmente el efecto de los cambios de la aplicación. Al mismo tiempo, puede separar el tráfico de producción del resto de modo que puede seguir supervisar la aplicación de producción según sea necesario.

Dado que va a recopilar datos de comportamiento de cliente, deberá [Agregar a un inicializador de telemetría para el código de JavaScript](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) en index.cshtml. Si desea probar el rendimiento del servidor, por ejemplo, también puede hacer del mismo modo en el código del servidor (consulte la [Aplicación perspectivas API para métricas y eventos personalizados](../application-insights/app-insights-api-custom-events-metrics.md).

1. En primer lugar, agregue la bewteen código los dos `//` comentarios debajo de JavaScript que se agregan a bloquear la `<heading>` etiqueta de una versión anterior.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Este código inicializador hace que la `appInsights` objeto para agregar el una propiedad personalizada denominada `Environment` cada fragmento de telemetría envía.

2. A continuación, agregue esta propiedad personalizada como una [opción de franja](web-sites-staged-publishing.md#AboutConfiguration) para la aplicación web de Azure. Para ello, ejecute los comandos siguientes en la sesión de Git Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    Web.config en su proyecto ya define el `environment` configuración de aplicación. Con esta configuración, al probar la aplicación localmente, las métricas se etiquetarán con `VS Debugger`. Sin embargo, cuando presiona los cambios a Azure, Azure encontrará y usar la `environment` aplicación configuración en configuración de la aplicación web y sus mediciones se etiquetarán con `Production`.

3. Confirmar los cambios de código de inserción a la horquilla en GitHub y espere a que los usuarios usar la nueva aplicación (es necesario actualizar el explorador). Tarda aproximadamente 15 minutos para la nueva propiedad aparezca en sus ideas de aplicación `MultiChannelToDo.Web` recursos.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. Ahora, vaya a módulo de **eventos personalizados** de nuevo y filtrar las mediciones en `Environment=Production`. Ahora debería poder ver todos los eventos personalizados nuevos en la franja de producción con este filtro.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Haga clic en el botón **Favoritos** para guardar la configuración actual del explorador de métricas en algo parecido a **eventos personalizados: producción**. Puede cambiar fácilmente entre esta vista y una vista de la franja de implementación más adelante.

    > [AZURE.TIP] Para análisis aún más eficaz, considere la posibilidad de [integración de los recursos de aplicación impresiones con Power BI](../application-insights/app-insights-export-power-bi.md).

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>Agregar etiquetas de franja específica a la métrica de aplicación de servidor
De nuevo, para conclusión va a configurar la aplicación de servidor. A diferencia de la aplicación cliente que está instrumentada en JavaScript, etiquetas de franja específicas para la aplicación de servidor cuenta con código .NET.

1. Abrir * &lt;repository_root >*\src\MultiChannelToDo\Global.asax.cs. Agregue el bloque de código siguiente, inmediatamente antes de cerrar llave de espacio de nombres.

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }

2. Corregir los errores de resolución de nombre agregando el `using` instrucciones debajo de al principio del archivo:

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;

3. Agregue el código siguiente al principio de la `Application_Start()` método:

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Confirmar los cambios de código de inserción a la horquilla en GitHub y espere a que los usuarios usar la nueva aplicación (es necesario actualizar el explorador). Tarda aproximadamente 15 minutos para la nueva propiedad aparezca en sus ideas de aplicación `MultiChannelToDo` recursos.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>Actualización: Configurar la rama beta

2. Abrir * &lt;repository_root >*\ARMTemplates\ProdAndStagetest.json y buscar el `appsettings` recursos (buscar `"name": "appsettings"`). Hay 4 de ellos, uno para cada franja. 

2. Para cada `appsettings` recursos, agregar un `"environment": "[parameters('slotName')]"` configuración de aplicación al final de la `properties` matriz. No olvide al final de la línea anterior con un punto y coma.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Que acaba de agregar el `environment` configuración de aplicación a las todas las casillas en la plantilla.
    
2. En el mismo archivo, busque la `slotconfignames` recursos (buscar `"name": "slotconfignames"`). Hay 2 de ellos, uno para cada aplicación.

2. Para cada `slotconfignames` recursos, agregar `"environment"` hasta el final de la `appSettingNames` matriz. No olvide al final de la línea anterior con un punto y coma.

    Que acaba de crear el `environment` aplicación establecer palo en su franja de implementación correspondientes de ambas aplicaciones.  

3. En la sesión de Git Shell, ejecute los comandos siguientes con el mismo sufijo de grupo de recursos que ha usado antes.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Cuando se le solicite, especifique las mismas credenciales de base de datos SQL que antes. A continuación, cuando se le pida que actualice el grupo de recursos, escriba `Y`, a continuación, `ENTER`.

    Una vez que finalice la secuencia de comandos, todos los recursos en el grupo de recursos original se conservan, pero un espacio nuevo llamado "beta" se crea en él con la misma configuración que la franja de "Ensayo" que se creó en el principio.

    >[AZURE.NOTE] Este método de creación de diferentes entornos de implementación es diferente del método de [desarrollo de software ágil con el servicio de aplicación de Azure](app-service-agile-software-development.md). Aquí puede crear entornos de implementación con ranuras de implementación, donde como crear entornos de implementación con los grupos de recursos. Administrar los entornos de implementación con grupos de recursos le permite conservar el entorno de producción off-limits a los desarrolladores, pero no es fácil realizar pruebas de producción, que puede llevar a cabo fácilmente con ranuras.

Si lo desea, también puede crear una aplicación alfa ejecutando

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Para este tutorial, que se acaba mantener usando la aplicación de beta.

## <a name="update-push-your-updates-to-the-beta-app"></a>Actualización: Las actualizaciones de inserción la aplicación de beta

Volver a la aplicación que desea mejorar.

1. Asegúrese de que está ahora en la sucursal beta

        git checkout beta

2. En * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, busque la `<li>` etiquetar y agregar la `style="cursor:pointer"` de atributo, como se muestra a continuación.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. ejecutar y push en Azure.

4. Compruebe que el cambio se refleja en la franja de beta, vaya a http://todoapp*&lt;your_suffix >*-beta.azurewebsites.net/. Si no ve el cambio todavía, actualice el explorador para obtener el nuevo código de javascript.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Ahora que tiene el cambio que se ejecuta en la franja de beta, ya está listo para realizar una implementación flighting.

## <a name="validate-route-traffic-to-the-beta-app"></a>Validar: Enrutar el tráfico a la aplicación de beta

En esta sección, se enrutar el tráfico a la aplicación de beta. Por motivos de claridad de demostración, va a distribuir una parte significativa del tráfico de usuario a él. En realidad, la cantidad de tráfico que desee distribuir dependerá de su situación específica. Por ejemplo, si su sitio está en la escala de microsoft.com, debe menor que uno por ciento del tráfico total para obtener datos útiles.

1. En la sesión de Git Shell, ejecute los comandos siguientes para enrutar la mitad del tráfico de producción a la franja de beta:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  La `ReroutePercentage=50` propiedad especifica que 50%, el tráfico de producción redirigirá a la dirección URL de la aplicación beta (especificado por el `ActionHostName` propiedad).

2. Ahora, explore http://ToDoApp*&lt;your_suffix >*. azurewebsites.net. 50% del tráfico ahora debe redirigirá a la franja de beta.

3. En el recurso de aplicación perspectivas, filtrar las mediciones por entorno = "beta".

    > [AZURE.NOTE] Si guarda esta vista filtrada como favorito otra, puede voltear fácilmente las vistas del explorador métrica entre las vistas de beta y producción.

Supongamos que en la información de la aplicación verá algo similar al siguiente:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

No solo se muestra que hay muchos más chasquidos en el `<li>` etiquetas, pero parece haber un aumento de clics en `<li>` etiquetas. A continuación, puede concluir que las personas han descubierto el nuevo `<li>` etiquetas son interactivo y ahora está borrando todas sus tareas completadas previamente en la aplicación.

En función de los datos de la implementación flighting, decide que la nueva interfaz de usuario está listo para producción.

## <a name="go-live-move-your-new-code-into-production"></a>Vaya directo: traslade el nuevo código de producción

Ahora ya está listo para mover la actualización de producción. Lo ideal es que ahora que sabe que la actualización ya ha sido validada _antes de que_ se inserta en producción. Ahora puede con confianza implementarlo. Dado que realiza una actualización a la aplicación de cliente AngularJS, sólo se valida el código de cliente. Si desea realizar cambios en la aplicación Web API de back-end, pudo validar los cambios de forma similar y fácilmente.

1. En el Git Shell, quitar la regla de enrutamiento de tráfico, ejecute el siguiente comando:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Ejecute los comandos Git:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Espere unos minutos para el nuevo código de implementará en la franja de ensayo y luego iniciar http://ToDoApp*&lt;your_suffix >*-staging.azurewebsites.net para comprobar que la nueva actualización esté caliente en la franja de ensayo. Recuerde que la rama de patrón de la horquilla está vinculado a la franja provisional de la aplicación.

3. Ahora, intercambiar la franja provisional en producción

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>Resumen ##

Servicio de aplicaciones de Azure facilita para pequeñas y medianas empresas probar sus aplicaciones para clientes en producción, algo que se ha hecho tradicionalmente en grandes empresas. Afortunadamente, este tutorial le ha el conocimiento que necesita reunir aplicación de servicio y perspectivas de aplicación hacer posible implementación flighting e incluso otras situaciones de prueba de producción, en el mundo DevOps. 

## <a name="more-resources"></a>Más recursos ##

-   [Desarrollo de software ágil con el servicio de aplicación de Azure](app-service-agile-software-development.md)
-   [Configurar entornos de web apps en el servicio de aplicación de Azure de ensayo](web-sites-staged-publishing.md)
-   [Implementar una aplicación compleja predecible en Azure](app-service-deploy-complex-application-predictably.md)
-   [Creación de plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md)
-   [JSONLint - el control de validación JSON](http://jsonlint.com/)
-   [Bifurcación GIT – básico de bifurcación y combinación](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Azure PowerShell](../powershell-install-configure.md)
-   [Proyecto Kudu Wiki](https://github.com/projectkudu/kudu/wiki)
