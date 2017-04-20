<properties 
    pageTitle="Cómo ajustar el tamaño de una aplicación en un entorno de servicio de aplicaciones" 
    description="Escalar una aplicación en un entorno de servicio de aplicaciones" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Escala de aplicaciones en un entorno de servicio de aplicaciones #

En el servicio de aplicación de Azure hay puede escalar normalmente tres acciones:

- plan de precios
- tamaño de trabajo 
- número de instancias.

En un ASE no es necesario para seleccionar o cambiar el plan de precio.  En términos de capacidades aún está en un nivel de capacidad de precios de Premium.  

Con respecto a los tamaños de trabajo, el Administrador de ASE puede asignar el tamaño de los recursos de cálculo que se usará para cada grupo de trabajo.  Eso significa puede tener 1 de grupo de trabajo con recursos de cálculo P4 y 2 de grupo de trabajo con P1 calcular recursos, si lo desea.  No tienen que estar en orden de tamaño.  Para obtener información detallada alrededor de los tamaños y su precio vea aquí el documento [Tarifas de servicio de la aplicación de Azure][AppServicePricing].  Esto deja las opciones de escala de aplicaciones web y planes de servicio de aplicación en un entorno de servicio de la aplicación sea:

- selección de grupo de trabajo
- número de instancias de

Cambiar un elemento se realiza a través de la interfaz de usuario correspondiente que se muestran para su ASE había hospedado planes de servicio de aplicación.  

![][1]

No puede escalar más allá de la cantidad de recursos de cálculo disponibles en el grupo de trabajo que ASP está en la página ASP.  Si necesita calcular recursos de ese grupo de trabajo debe obtener el Administrador de ASE para agregarlos.  Para obtener información alrededor de volver a configurar su ASE lea la información aquí: [cómo configurar un entorno de aplicación de servicio][HowtoConfigureASE].  También puede aprovechar las ventajas de las características de Autoescala ASE agregar capacidad según programación o métricas.  Para obtener más detalles sobre cómo configurar Autoescala para la ASE propio entorno consulte [cómo configurar Autoescala para un entorno de servicio de la aplicación][ASEAutoscale].

Puede crear varios aplicación plan de servicio con recursos de cálculo de grupos de trabajo diferentes, o puede utilizar el mismo grupo de trabajo.  Por ejemplo, si tiene (10) de los recursos de cálculo disponibles en 1 de grupo de trabajo, puede crear un plan de servicio de la aplicación con los recursos de cálculo (6) y planear un segundo servicio de aplicación que utiliza (4) para calcular los recursos.

### <a name="scaling-the-number-of-instances"></a>El número de instancias de ajuste de escala ###

Al crear la aplicación web en un entorno de servicio de la aplicación se inicia con 1 instancia.  A continuación, se puede escalar a instancias adicionales para proporcionar recursos de cálculo adicionales para la aplicación.   

Si su ASE tiene suficiente capacidad esto es muy sencillo.  Vaya a su Plan de servicio de aplicación que contiene los sitios que desee escalar y seleccione escala.  Se abrirá la interfaz de usuario, donde puede establecer la escala de la página ASP o configurar reglas Autoescala para ASP manualmente.  En una escala de forma manual la aplicación simplemente defina la ***escala por*** a ***un recuento de la instancia que escribo manualmente***.  Desde aquí arrastre el control deslizante a la cantidad que desee o escriba en el cuadro junto al control deslizante.  

![][2] 

Las reglas de Autoescala de ASP en un ASE funcionan de la misma manera que normalmente.  Puede seleccionar el ***Porcentaje de CPU*** en ***escala por*** y crear reglas de Autoescala de ASP basada en el porcentaje de CPU o puede crear reglas más complejas con ***reglas de programación y rendimiento***.  Para ver información más detallada sobre cómo configurar Autoescala usar la guía aquí [escalar una aplicación de servicio de la aplicación de Azure][AppScale]. 


### <a name="worker-pool-selection"></a>Selección de grupo de trabajo ###

Como se mencionó anteriormente, la selección de grupo de trabajo se accede desde la interfaz de usuario de ASP.  Abra el módulo de ASP que desea cambiar la escala y seleccione el grupo de trabajo.  Verá todos los grupos de trabajo que ha configurado en su entorno de servicio de aplicación.  Si dispone de grupo de solo trabajo solo verá el grupo de una lista.  Para cambiar el grupo de trabajo ASP está en, simplemente seleccione el grupo de trabajo que desee a su Plan de servicio de aplicación para ir a.  

![][3]

Antes de mover el ASP grupo de trabajo de uno a otro es importante para asegurarse de que tendrá la capacidad adecuada para su ASP.  En la lista de grupos de trabajo, no solo se muestra el nombre del grupo de trabajo, pero también puede ver cuántas trabajadores están disponibles en ese grupo de trabajo.  Asegúrese de que hay instancias suficientes contener su Plan de servicio de aplicación.  Si necesita calcular más recursos en el grupo de trabajo que desea mover a, a continuación, obtenga el administrador ASE agregarlos.  

> [AZURE.NOTE] Mover que un ASP grupo de uno trabajo provocará fría inicia de las aplicaciones en que ASP.  Esto puede provocar solicitudes para ejecutar lentamente mientras la aplicación esté fría iniciado en los nuevos recursos de cálculo.  El inicio y se puede evitar mediante el uso de la [aplicación caliente capacidad] [ AppWarmup] en la aplicación de servicio de Azure.  El módulo de inicialización de la aplicación que se describe en el artículo también funciona para el inicio frío porque el proceso de inicialización también se invoca cuando aplicaciones estén frías iniciado en nuevos recursos de cálculo. 

## <a name="getting-started"></a>Introducción

Para empezar con la aplicación de servicio entornos, consulte [Cómo crear una aplicación de servicio entorno a][HowtoCreateASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
