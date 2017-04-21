<properties
    pageTitle="Alertas en tiempo real de Azure CDN | Microsoft Azure"
    description="Alertas en tiempo real en Microsoft Azure CDN. Alertas en tiempo real proporcionan notificaciones sobre el rendimiento de los extremos del perfil CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="casoper"/>

# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas en tiempo real en Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]


## <a name="overview"></a>Información general

Este documento explica las alertas en tiempo real de Microsoft Azure CDN. Esta funcionalidad proporciona notificaciones en tiempo real sobre el rendimiento de los extremos del perfil CDN.  Puede configurar el correo electrónico o alertas HTTP basadas en:

* Ancho de banda
* Códigos de estado
* Estados de caché
* Conexiones

## <a name="creating-a-real-time-alert"></a>Crear una alerta en tiempo real

1. En el [Portal de Azure](https://portal.azure.com), vaya a su perfil CDN.

    ![Módulo de perfil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)

2. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-real-time-alerts/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

3. Desplace el puntero sobre la pestaña **análisis** , a continuación, desplace el puntero sobre el flotante **Estadísticas en tiempo real** .  Haga clic en **alertas en tiempo real**.

    ![Portal de administración de CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)

    Se muestra la lista de las configuraciones de alerta existentes (si existe).

4. Haga clic en el botón **Agregar alerta** .

    ![Agregar botón alerta](./media/cdn-real-time-alerts/cdn-add-alert.png)

    Se muestra un formulario para crear una nueva alerta.

    ![Nuevo formulario de alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)

5. Si desea que esta alerta esté activa cuando haga clic en **Guardar**, active la casilla de verificación **Habilitado de alerta** .

6. En el campo **nombre** , escriba un nombre descriptivo para la alerta.

7. En la lista desplegable **Tipo de medio** , seleccione **Objeto grande de HTTP**.

    ![Tipo de medio con objeto grande HTTP seleccionado](./media/cdn-real-time-alerts/cdn-http-large.png)

    > [AZURE.IMPORTANT] Debe seleccionar **Objeto grande de HTTP** como el **Tipo de medio**.  **CDN de Azure de Verizon**no se usan las otras opciones.  Error al seleccionar **Objeto grande de HTTP** hará que la alerta nunca se activa.

8. Crear una **expresión** para supervisar seleccionando una **métrica**, **operador**y **valor desencadenador**.

    - Para **métrica**, seleccione el tipo de condición que desee supervisar.  **Ancho de banda Mbps** es la cantidad de uso de ancho de banda en megabits por segundo.  **Total de conexiones** es el número de conexiones HTTP simultáneas a nuestras servidores perimetrales.  Las definiciones de los diversos estados de caché y códigos de estado, consulte [Códigos de estado de caché de CDN de Azure](https://msdn.microsoft.com/library/mt759237.aspx) y [Azure CDN HTTP estado](https://msdn.microsoft.com/library/mt759238.aspx)
    - **Operador** es el operador matemático que establece la relación entre la métrica y el valor de desencadenador.
    - **Valor desencadenador** es el valor de umbral que debe cumplirse antes de que se enviará una notificación.

    En el ejemplo siguiente, la expresión que he creado indica que deseo recibir una notificación cuando el número de 404 códigos de estado es mayor de 25.

    ![Expresión de ejemplo de alerta en tiempo real](./media/cdn-real-time-alerts/cdn-expression.png)

9. **Intervalo**, escriba la frecuencia con la que le gustaría la expresión evaluada.

10. En la **notificación en** la lista desplegable, seleccione cuándo desea recibir una notificación cuando la expresión es true.
    
    - **Iniciar la condición** indica que se enviará una notificación cuando se detecta primero la condición especificada.
    - **Final de la condición** indica que se enviará una notificación cuando no se ha detectado la condición especificada. Esta notificación solo puede activarse después de nuestra sistema para supervisar la red detecta que se ha producido la condición especificada.
    - **Continua** indica que se puede enviar una notificación cada vez que el sistema de supervisión de redes detecta la condición especificada. Tenga en cuenta que el sistema de supervisión de redes solo Compruebe una vez por intervalo de la condición especificada.
    - **Condición de inicio y finalización** indica que una notificación se enviará la primera vez que se detecta la condición especificada y una vez más cuando se detecta ya no la condición.

11. Si desea recibir notificaciones por correo electrónico, active la casilla de verificación **notificar por correo electrónico** .  

    ![Notificar por formulario de correo electrónico](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    En el campo **para** , escriba la dirección de correo electrónico donde desea que las notificaciones se envía. **Asunto** y el **cuerpo**, puede dejar el valor predeterminado o puede personalizar el mensaje con la lista de **palabras clave disponibles** para insertar dinámicamente los datos de alerta cuando se envía el mensaje.

    > [AZURE.NOTE] Puede probar la notificación de correo electrónico haciendo clic en el botón **Probar notificación** , pero solo una vez guardada la configuración de alertas.

12. Si desea que las notificaciones que se publicará en un servidor web, active la casilla de verificación **notificar por correo HTTP** .

    ![Notificar por formulario de HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)

    En el campo **dirección Url** , escriba la dirección URL donde desea que el mensaje HTTP publicado. En el cuadro de texto de **los encabezados** , escriba los encabezados HTTP se envíen en la convocatoria.  En el **cuerpo de** puede personalizar el mensaje con la lista de **palabras clave disponibles** para insertar dinámicamente los datos de alerta cuando se envía el mensaje.  **Encabezados** y el **cuerpo** predeterminado para una carga XML similar al ejemplo siguiente.

    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```

    > [AZURE.NOTE] Puede probar la notificación de HTTP Post haciendo clic en el botón **Probar notificación** , pero solo una vez guardada la configuración de alertas.

13. Haga clic en el botón **Guardar** para guardar la configuración de alertas.  Si activa la **Alerta habilitado** en el paso 5, la alerta está activa.

## <a name="next-steps"></a>Pasos siguientes

- Analizar [estadísticas en tiempo real en CDN de Azure](cdn-real-time-stats.md)
- Profundizar con [informes HTTP avanzados](cdn-advanced-http-reports.md)
- Analizar [patrones de uso](cdn-analyze-usage-patterns.md)

