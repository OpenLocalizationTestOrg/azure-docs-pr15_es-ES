<properties
    pageTitle="Escalar una aplicación en Azure | Microsoft Azure"
    description="Aprenda a escalar en una aplicación de servicio de la aplicación de Azure agregar capacidad y características."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="cephalin"/>

# <a name="scale-up-an-app-in-azure"></a>Escalar una aplicación en Azure #

En este artículo se muestra cómo ajustar el tamaño de la aplicación de servicio de aplicaciones de Azure. Existen dos flujos de trabajo de escala de escala, hacia arriba y escalado y a continuación, en este artículo se explica la escala de flujo de trabajo.

- [Escalar](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtener más CPU, memoria, espacio en disco y características adicionales como dedicada máquinas virtuales de Windows (VM), dominios personalizados y certificados, provisional ranuras, ajuste automático y mucho más. Escalar cambiando el nivel de precios del plan de servicio de la aplicación que pertenece la aplicación.
- [Escalar](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumente el número de instancias VM que ejecute la aplicación.
Se puede escalar a instancias como 20, dependiendo de su nivel de precios. [Entornos de aplicación de servicios](../app-service/app-service-app-service-environments-readme.md) de nivel **Premium** aumentará aún más el número de escalado a 50 instancias. Para obtener más información sobre el escalado, vea [ajustar el recuento de instancias manual o automáticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Encontrará información sobre cómo usar ajuste automático, que es el recuento de la instancia automáticamente en función de programaciones y reglas predefinidas de escalado.

La configuración de escala de tomar sólo segundos para aplicar y afecta a todas las aplicaciones en su [plan de servicios de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
No requieren que cambie el código o volver a instalar la aplicación.

Para obtener información sobre las características de los planes de servicio de aplicaciones individuales y precios, consulte [Detalles de precios de servicio de aplicación](/pricing/details/web-sites/).  

> [AZURE.NOTE] Antes de cambiar un plan de servicio de la aplicación desde el nivel de **libre** , primero debe quitar los [límites de gastos](/pricing/spending-limits/) en lugar de su suscripción de Azure. Para ver o cambiar las opciones para su suscripción de servicio de aplicaciones de Microsoft Azure, consulte [Microsoft Azure suscripciones][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Ajustar el nivel de precios

1. En el explorador, abra el [portal de Azure][portal].

2. En el módulo de la aplicación, haga clic en **toda la configuración**y, a continuación, haga clic en **Escala y**.

    ![Desplácese para cambiar la escala de la aplicación de Azure.][ChooseWHP]

4. Elija el nivel y, a continuación, haga clic en **Seleccionar**.

    La ficha **notificaciones** parpadea verde **éxito** cuando se complete la operación.

<a name="ScalingSQLServer"></a>
## <a name="scale-related-resources"></a>Escalar recursos relacionados
Si su aplicación depende de otros servicios, como base de datos de SQL Azure o el almacenamiento de Azure, también puede escalar los recursos según sus necesidades. Estos recursos no se pueden escalar con el plan de servicios de aplicación y se deben escalar por separado.

1. En **Essentials**, haga clic en el vínculo del **grupo de recursos** .

    ![Recursos relacionados con la escala de la aplicación de Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. En el elemento de **Resumen** del módulo de **grupo de recursos** , haga clic en un recurso que desea ajustar. La siguiente captura de pantalla muestra un recurso de base de datos de SQL y un recurso de almacenamiento de Azure.

    ![Vaya a módulo de grupo de recursos para ajustar la escala de la aplicación de Azure](./media/web-sites-scale/ResourceGroup.png)

3. Para un recurso de base de datos de SQL, haga clic en **configuración** > **nivel de precios** para ajustar los precios de nivel.

    ![Ajustar tamaño de la base de datos SQL de back-end para la aplicación de Azure](./media/web-sites-scale/ScaleDatabase.png)

    También puede activar [geo replicación](../sql-database/sql-database-geo-replication-overview.md) de la instancia de base de datos de SQL.

    Para un recurso de almacenamiento de Azure, haga clic en **configuración de** > **configuración** escalar en las opciones de almacenamiento.

    ![Cambiar la escala de la cuenta de almacenamiento de Azure utilizada por la aplicación de Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>
## <a name="learn-about-developer-features"></a>Obtenga más información sobre características de desarrollador
Según el nivel de precio, están disponibles las siguientes características orientadas al desarrollador:

### <a name="bitness"></a>Valor de bits ###

- Los niveles **básico**, **estándar**y **Premium** admiten aplicaciones de 64 bits y 32 bits.
- Los niveles de plan de **Shared** y **Free** admiten sólo aplicaciones de 32 bits.

### <a name="debugger-support"></a>Depuración ###

- Soporte de depurador está disponible para los modos **libre**, **Shared**y **básicas** en una conexión por plan de servicios de aplicación.
- Soporte de depurador está disponible para los modos **estándar** y **Premium** en cinco conexiones simultáneas por plan de servicios de aplicación.

<a name="OtherFeatures"></a>
## <a name="learn-about-other-features"></a>Obtenga más información sobre otras características

- Para obtener información detallada acerca de todas las características de los planes de servicio de aplicaciones, incluidos los precios y características de interés para todos los usuarios (incluidos los desarrolladores) restante, consulte [Detalles de precios de servicio de aplicación](/pricing/details/web-sites/).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse a una cuenta de Azure, vaya a [Probar el servicio de aplicación](http://go.microsoft.com/fwlink/?LinkId=523751) donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Tarjetas de crédito no son necesarias y no hay ningún compromisos.

<a name="Next Steps"></a>
## <a name="next-steps"></a>Pasos siguientes

- Para empezar con Azure, consulte la [Versión de prueba gratuita de Microsoft Azure](/pricing/free-trial/).
- Para obtener información sobre precios, soporte técnico y SLA, visite los siguientes vínculos.

    [Los detalles de precios de transferencias de datos](/pricing/details/data-transfers/)

    [Planes de Azure de soporte técnico de Microsoft](/support/plans/)

    [Contratos de nivel de servicio](/support/legal/sla/)

    [Detalles de precios de base de datos SQL](/pricing/details/sql-database/)

    [Máquina virtual y los tamaños de servicio de nube de Microsoft Azure][vmsizes]

    [Detalles de precios de servicio de aplicaciones](/pricing/details/app-service/)

    [Servicio de aplicación precios detalles - conexiones SSL](/pricing/details/web-sites/#ssl-connections)

- Para obtener información sobre la aplicación de servicio de Azure mejores prácticas, incluida la creación de una arquitectura scalable y flexible, consulte [mejores prácticas: Azure aplicación de servicio Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Para ver vídeos sobre la escala de aplicaciones de servicio de aplicaciones, consulte los siguientes recursos:

    - [Cuándo escalar sitios Web Azure - con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
    - [Autoescala de sitios Web de Azure, CPU o programada - con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
    - [¿Cómo Azure escala de sitios Web: con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)


<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
