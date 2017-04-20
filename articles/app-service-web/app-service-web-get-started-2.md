<properties
    pageTitle="Agregar funcionalidad a la aplicación web de primera"
    description="Agregar características interesantes a la aplicación web de primera en unos minutos."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/12/2016"
    ms.author="cephalin"
/>

# <a name="add-functionality-to-your-first-web-app"></a>Agregar funcionalidad a la aplicación web de primera

En [implementar la aplicación web de primera a Azure en cinco minutos](app-service-web-get-started.md), se implementa una aplicación web de ejemplo en [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md). En este artículo, deberá agregar rápidamente algunas funcionalidades excelentes a la aplicación web implementada. En unos minutos, podrá:

- exigir la autenticación para los usuarios
- Ajustar automáticamente el tamaño de la aplicación
- recibir alertas sobre el rendimiento de la aplicación

Independientemente de qué aplicación de ejemplo implementado en el artículo anterior, puede seguir a lo largo del tutorial.

Las tres actividades en este tutorial son solo algunos ejemplos de las muchas funciones útiles que se obtiene al poner la aplicación web en la aplicación de servicio. Muchas de las características están disponibles en el nivel de **Free** (que es lo que se ejecuta la aplicación web de primera en), y puede usar su créditos de prueba para probar características que requieren niveles de precios superior. Estar seguro de que la aplicación web permanece en el nivel de **Free** a menos que cambia explícitamente a un nivel de precio diferente.

>[AZURE.NOTE] La aplicación web que creó con Azure CLI se ejecuta en el nivel de **libre** , que solo permite una instancia VM compartida con las cuotas de recursos. Para obtener más información sobre lo que se obtiene con el nivel de **libre** , vea [los límites de la aplicación de servicio](../azure-subscription-service-limits.md#app-service-limits).

## <a name="authenticate-your-users"></a>Autenticar a sus usuarios

Ahora, veamos lo fácil que es agregar autenticación a su aplicación (lecturas adicionales en la [Aplicación de servicio de autenticación o autorización](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)).

1. En el módulo de portal de la aplicación, que se acaba de abrir, haga clic en **configuración de** > **autenticación / autorización**.  
    ![Autenticar - módulo de configuración](./media/app-service-web-get-started/aad-login-settings.png)

2. Haga clic **en** activar la autenticación.  

4. **Proveedores de autenticación**, haga clic en **Azure Active Directory**.  
    ![Autenticar: seleccione Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. En el módulo de **Configuración de Azure Active Directory** , haga clic en **Express**, a continuación, haga clic en **Aceptar**. La configuración predeterminada de crea una nueva aplicación de Azure AD en su directorio de forma predeterminada.  
 ![Autenticar - express configuración](./media/app-service-web-get-started/aad-login-express.png)

6. Haga clic en **Guardar**.  
    ![Autenticar - Guardar configuración](./media/app-service-web-get-started/aad-login-save.png)

    Una vez que el cambio es correcto, verá la campana notificación activar verde, junto con un mensaje descriptivo.

7. En el módulo de portal de la aplicación, haga clic en el vínculo de **dirección URL** (o **busque** en la barra de menús). El vínculo es una dirección HTTP.  
    ![Autenticar: vaya a la dirección URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Pero, una vez que la aplicación se abre en una nueva pestaña, la dirección URL del cuadro redireccionamientos varias veces y finaliza en la aplicación con una dirección HTTPS. Lo que está viendo es que ya está conectado a su suscripción de Azure y está autenticado automáticamente en la aplicación.  
    ![Autenticar - conectado](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Si abre una sesión no autenticada ahora en un explorador diferente, aparecerá una pantalla de inicio de sesión cuando vaya a la misma dirección URL.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
   Si nunca ha hecho nada con Azure Active Directory, el directorio predeterminado que no tenga todos los usuarios de Azure AD. En ese caso, probablemente la única cuenta allí es la cuenta de Microsoft con su suscripción de Azure. Por eso se iniciará sesión automáticamente en la aplicación en una versión anterior del mismo explorador.
   Puede usar esa misma cuenta de Microsoft para iniciar sesión en esta página de inicio de sesión.

Enhorabuena, autentica todo el tráfico a su aplicación web.

Puede que haya observado en la **autenticación / autorización** módulo que puede hacer muchas más, como por ejemplo:

- Habilitar inicio de sesión social
- Habilitar varias opciones de inicio de sesión
- Cambiar el comportamiento predeterminado cuando personas en primer lugar vaya a su aplicación

Aplicación de servicio proporciona que una solución llave para algunos de la autenticación comunes necesita por lo que no es necesario proporcionar la lógica de autenticación usted mismo.
Para obtener más información, vea [Aplicación de servicio de autenticación o autorización](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

## <a name="scale-your-app-automatically-based-on-demand"></a>Ajustar el tamaño de la aplicación automáticamente en función de petición

A continuación, vamos a ajustar automáticamente su aplicación para que ajustará automáticamente la capacidad para responder a petición del usuario (lecturas adicionales en la [escala de la aplicación en Azure](web-sites-scale.md) y [escalar recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md)).

En resumen, ajustar la aplicación web de dos formas:

- [Escalar](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtener más CPU, memoria, espacio en disco y características adicionales como máquinas virtuales dedicadas, dominios personalizados y certificados, provisional ranuras, ajuste automático y mucho más. Escalar cambiando el nivel de precios del plan de servicio de aplicación al que pertenece la aplicación.
- [Escalar](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumenta el número de VM instancias que ejecute la aplicación.
Se puede escalar a como máximo 50 instancias, dependiendo de su nivel de precios.

Sin más, vamos a configurar el ajuste automático.

1. En primer lugar, vamos a escalar para habilitar el ajuste automático. Módulo de portal de la aplicación, haga clic en **configuración** > **Escala hacia arriba (Plan de servicio de aplicación)**.  
    ![Escalar - módulo de configuración](./media/app-service-web-get-started/scale-up-settings.png)

2. Desplácese y seleccione el nivel de **S1 estándar** , el nivel más bajo que admita el ajuste automático (en un círculo en captura de pantalla), a continuación, haga clic en **Seleccionar**.  
    ![Escalar: elegir nivel](./media/app-service-web-get-started/scale-up-select.png)

    Termine escalado hacia arriba.

    >[AZURE.IMPORTANT] Este nivel precisa utilizar su créditos de prueba gratuitas. Si tiene una cuenta de pago por uso, incurre cargos a su cuenta.

3. A continuación, vamos a configurar ajusta automáticamente. Módulo de portal de la aplicación, haga clic en **configuración** > **Escala fuera (Plan de servicio de aplicación)**.  
    ![Escalado - módulo de configuración](./media/app-service-web-get-started/scale-out-settings.png)

4. Cambiar la **escala por** porcentaje de **CPU**. Actualizan los controles deslizantes debajo de la lista desplegable. A continuación, defina un intervalo de **instancias** entre **1** y **2** y un **intervalo de destino** entre **40** y **80**. Hacerlo escribiendo en los cuadros o moviendo los controles deslizantes.  
 ![Escalar: configurar ajuste automático](./media/app-service-web-get-started/scale-out-configure.png)

    En función de esta configuración, la aplicación automáticamente escala al uso de CPU es superior al 80% y escala en cuando la CPU está por debajo de 40%.

5. En la barra de menús, haga clic en **Guardar** .

Enhorabuena, la aplicación de ajuste automático.

Puede que haya observado en el módulo de **Configuración de escala** que puede hacer muchas más, como por ejemplo:

- Escalar a un número específico de instancias de forma manual
- Ajustar a otras métricas de rendimiento, como cola de porcentaje o un disco de memoria
- Personalice el comportamiento de la escala cuando se activa una regla de rendimiento
- Autoescala en una programación
- Establecer el comportamiento de ajuste automático de un evento futuro

Para obtener más información sobre el escalado de la aplicación, vea [cambiar la escala de la aplicación en Azure](../app-service-web/web-sites-scale.md). Para obtener más información sobre el escalado, vea [escalar recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md).

## <a name="receive-alerts-for-your-app"></a>Recibir alertas de la aplicación

Ahora que la aplicación se ajusta automáticamente, ¿qué sucede cuando llega el recuento de instancia máximo (2) y la CPU está por encima de utilización deseada (80%)?
Puede configurar una alerta (lecturas adicionales en [notificaciones de alerta de recepción](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)) para informarle de esta situación de manera que pueda escalar más arriba o hacia fuera de la aplicación, por ejemplo. Vamos a configurar rápidamente una alerta para este escenario.

1. En el módulo de portal de la aplicación, haga clic en **Herramientas de** > **alertas**.  
    ![Alertas: módulo de configuración](./media/app-service-web-get-started/alert-settings.png)

2. Haga clic en **Agregar aviso**. A continuación, en el cuadro **recursos** , seleccione el recurso que termina con **(serverfarms)**. Es su plan de servicios de aplicación.  
    ![Alertas - Agregar alerta para el plan de servicios de aplicación](./media/app-service-web-get-started/alert-add.png)

3. Especifique el **nombre** como `CPU Maxed`, **métrica** como **Porcentaje de CPU**y **umbral** como `90`, a continuación, seleccione **los lectores, colaboradores y los propietarios de correo electrónico**y, a continuación, haga clic en **Aceptar**.   
 ![Alertas - Configurar alerta](./media/app-service-web-get-started/alert-configure.png)

    Cuando Azure termine de crear la alerta, lo verá en el módulo de **alertas** .  
    ![Alertas - terminada vista](./media/app-service-web-get-started/alert-done.png)

Enhorabuena, ahora está recibiendo alertas.

Esta configuración de alerta comprueba la CPU cada cinco minutos. Si ese número supera el 90%, recibirá una alerta de correo electrónico, junto con cualquier persona que está autorizado. Para ver todos los usuarios que está autorizado para recibir las alertas, vuelva a la hoja de portal de la aplicación y haga clic en el botón de **acceso** .  
![Ver quién obtiene alertas](./media/app-service-web-get-started/alert-rbac.png)

Verá que **los administradores de suscripción** ya están el **propietario** de la aplicación. Este grupo incluiría si es el Administrador de la cuenta de su suscripción de Azure (por ejemplo, su suscripción de prueba). Para obtener más información sobre el control de acceso basado en roles de Azure, consulte [Control de acceso de Azure Role-Based](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] Reglas de alerta es una característica de Azure. Para obtener más información, vea [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="next-steps"></a>Pasos siguientes

En la forma de configurar la alerta, puede que haya observado un amplio conjunto de herramientas de la hoja de **Herramientas** . Aquí, puede solucionar problemas, supervisar el rendimiento, probar vulnerabilidad, administrar recursos, interactuar con la consola de VM y agregar extensiones útiles. Nos invitar a hacer clic en cada una de estas herramientas para descubrir las herramientas sencillas pero efectiva a su alcance.

Obtenga información sobre cómo hacer más con la aplicación implementada. Aquí es sólo una lista parcial:

- [Comprar y configurar un nombre de dominio personalizado](custom-dns-web-site-buydomains-web-app.md) - comprar un dominio atractivo para la aplicación web en lugar de la *. dominio azurewebsites.net. O bien, use un dominio que ya tiene.
- [Configurar los entornos de ensayo](web-sites-staged-publishing.md) - implemente la aplicación a una dirección URL provisional antes de ponerlos en producción. Actualizar la aplicación web directo con confianza. Configurar una solución DevOps complejas con múltiples ranuras de implementación.
- [Configurar la implementación continua](app-service-continuous-deployment.md) - integrar implementación de la aplicación en el sistema de control de código fuente. Implementar en Azure con cada confirmación.
- [Acceso a los recursos locales](web-sites-hybrid-connection-get-started.md) - acceso existente locales de base de datos o sistema CRM.
- [Realizar copias de seguridad de la aplicación](web-sites-backup.md) - Configurar copia de seguridad y restauración de la aplicación web. Prepararse para errores inesperados y resolverlos.
- [Habilitar los registros de diagnóstico](web-sites-enable-diagnostic-log.md) - leer los registros de IIS seguimientos de Azure o de la aplicación. Leer en una secuencia, descargar o puerto en [Perspectivas de aplicación](../application-insights/app-insights-overview.md) para el análisis de llave.
- [Analizar la aplicación para vulnerabilidad](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
digitalizar la aplicación web de amenazas moderna mediante el servicio de [Seguridad de estaño](https://www.tinfoilsecurity.com/).
- [Ejecutar trabajos en segundo plano](../azure-functions/functions-overview.md) - ejecutar trabajos para el procesamiento de datos, informes, etcetera.
- [Obtenga información sobre cómo funciona el servicio de aplicación](../app-service/app-service-how-works-readme.md)
