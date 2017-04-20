<properties
    pageTitle="Introducción a prueba de producción para las aplicaciones Web"
    description="Obtenga información sobre la prueba en función de producción (sugerencia) en las aplicaciones de Azure aplicación de servicio Web."
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
    ms.date="01/13/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-test-in-production-for-web-apps"></a>Introducción a prueba de producción para las aplicaciones Web

Pruebas de producción o live pruebas de la aplicación web de tráfico de cliente directo, es una estrategia de prueba que los desarrolladores de la aplicación se integran cada vez más en su metodología de [desarrollo ágil](https://en.wikipedia.org/wiki/Agile_software_development) . Le permite probar la calidad de sus aplicaciones con el tráfico de usuario activo en su entorno de producción, en lugar de síntesis datos en un entorno de prueba. Al exponer la nueva aplicación a usuarios reales, puede ser informados en los problemas reales que puede enfrentarse a su aplicación una vez que se ha implementado. Puede comprobar la funcionalidad, el rendimiento y el valor de las actualizaciones de la aplicación contra el volumen y la velocidad de la variedad de tráfico de usuario real, que no puede aproximar nunca en un entorno de prueba.

## <a name="traffic-routing-in-app-service-web-apps"></a>El tráfico de enrutamiento en las aplicaciones de servicio de aplicación Web

Con la característica enrutamiento de tráfico del [Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714), puede dirigir una parte del tráfico de usuario directo a una o más [ranuras de implementación](web-sites-staged-publishing.md)y, a continuación, analice la aplicación con [Información de la aplicación de Azure](/services/application-insights/) [HDInsight de Azure](/services/hdinsight/)o una herramienta de terceros como [Nuevo Relic](/marketplace/partners/newrelic/newrelic/) para validar el cambio. Por ejemplo, puede implementar los siguientes escenarios con la aplicación de servicio:

- Descubrir errores funcionales o botella de rendimiento en las actualizaciones antes de la implementación de todo el sitio de pinpoint
- Realizar "prueba controlado vuelos" de los cambios mediante la medición métricas usibility en la aplicación de beta
- Rampa gradualmente hasta una nueva actualización y correctamente hacia abajo a la versión actual si se produce un error 
- Optimizar los resultados de la aplicación empresarial ejecutando [A y B las pruebas](https://en.wikipedia.org/wiki/A/B_testing) o [pruebas multivariate](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) en varias casillas de implementación

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Requisitos para usar el enrutamiento del tráfico en aplicaciones Web

- La aplicación web debe ejecutar en el nivel **estándar** o **Premium** , ya que es necesario para varias ranuras de implementación.
- Para que funcione correctamente, el enrutamiento de tráfico requiere cookies estén habilitadas en el Explorador de los usuarios. Enrutamiento de tráfico utiliza cookies para anclar el Explorador de un cliente a un espacio de implementación de la vida de la sesión de cliente.
- Enrutamiento de tráfico admite escenarios avanzados de sugerencia a través de los cmdlets de PowerShell de Azure.

## <a name="route-traffic-segment-to-a-deployment-slot"></a>Segmento de tráfico de ruta para un espacio de implementación

En el nivel básico en cada escenario de sugerencia, se redirige un porcentaje predefinido del tráfico directo a un espacio de no producción implementación. Para ello, siga los pasos siguientes:

>[AZURE.NOTE] Los siguientes pasos se supone que ya tiene una [franja de implementación no es de producción](web-sites-staged-publishing.md) y que el contenido de la aplicación web deseado ya está [implementado](web-sites-deploy.md) .

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Módulo de la aplicación web, haga clic en **configuración** > **Enrutamiento de tráfico**.
  ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Seleccione la casilla que desee redirigir el tráfico a y el porcentaje del total de tráfico que desee y luego haga clic en **Guardar**.

    ![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Vaya a la hoja de la zona de implementación. Ahora debe ver tráfico real ha enrutado a él.

    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Una vez que se configura el enrutamiento de tráfico, el porcentaje de clientes especificado redirigirá aleatoriamente a la franja de producción no. Sin embargo, es importante que tenga en cuenta que una vez que un cliente se enruta automáticamente a una franja específica, se "fija" a los que se incluyen durante la duración de esa sesión de cliente. Esto lo hace mediante una cookie para anclar la sesión de usuario. Si inspeccionar las solicitudes HTTP, encontrará una `TipMix` cookie en todas las solicitudes subsiguientes.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>Forzar las solicitudes de cliente en una franja específica

Además de enrutar el tráfico automático, es posible enrutar las solicitudes a una franja específica aplicación de servicio. Esto es útil cuando desea que los usuarios que pueden participar en o rechazar de la aplicación de beta. Para ello, se utiliza el `x-ms-routing-name` parámetro de consulta.

Para redirigir a los usuarios a una franja específica mediante `x-ms-routing-name`, asegúrese de que la casilla se ha agregado a la lista de distribución de tráfico. Puesto que desea enrutar a un espacio explícitamente, el porcentaje de enrutamiento real que se establece no es relevante. Si lo desea, puede crear un "vínculo beta" que los usuarios pueden hacer clic para tener acceso a la aplicación de beta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>Cambiar de opinión de los usuarios de la aplicación de beta

Para permitir que los usuarios no participar en la aplicación de beta, por ejemplo, puede incluir este vínculo en su página web:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

La cadena `x-ms-routing-name=self` especifica la franja de producción. Una vez que el explorador del cliente tener acceso al vínculo, no solo se redirige a la franja de producción, sino que todas las solicitudes subsiguientes contendrá el `x-ms-routing-name=self` cookie que fija la sesión de la franja de producción.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>Cambiar de opinión de los usuarios de la aplicación de beta

Para permitir que los usuarios a participar en la aplicación de beta, establecer el mismo parámetro de consulta en el nombre de la casilla no es de producción, por ejemplo:

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>Más recursos ##

-   [Configurar entornos de web apps en el servicio de aplicación de Azure de ensayo](web-sites-staged-publishing.md)
-   [Implementar una aplicación compleja predecible en Azure](app-service-deploy-complex-application-predictably.md)
-   [Desarrollo de software ágil con el servicio de aplicación de Azure](app-service-agile-software-development.md)
-   [Utilizar DevOps entornos de forma eficaz para las aplicaciones web](app-service-web-staged-publishing-realworld-scenarios.md)