<properties
  pageTitle="Conjunto de aplicaciones de Azure IoT preguntas más frecuentes | Microsoft Azure"
  description="Preguntas más frecuentes sobre IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Preguntas más frecuentes sobre IoT Suite

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>¿Cuál es la diferencia entre la eliminación de un grupo de recursos en el portal de Azure y haciendo clic en Eliminar en una solución preconfigurada en azureiotsuite.com?

- Si elimina la solución preconfigurada en [azureiotsuite.com][lnk-azureiotsuite], eliminar todos los recursos que se prepararon cuando creó la solución preconfigurada; Si agrega recursos adicionales para el grupo de recursos, estos también se eliminan. 

- Si elimina el grupo de recursos en el [portal de Azure][lnk-azure-portal], sólo eliminar los recursos de ese grupo de recursos También debe eliminar la aplicación de Azure Active Directory asociada con la solución preconfigurada en el [portal de clásica Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de concentrador IoT ¿aprovisionar en una suscripción? 

Diez. Puede crear una [incidencia de soporte técnico de Azure] [ link-azuresupportticket] elevar este límite, pero de forma predeterminada, solo se pueden aprovisionar diez Hubs IoT por suscripción, tal como se describe en los [límites de suscripción Azure][link-azuresublimits]. Como resultado, ya que cada solución preconfigurado aprovisiona un concentrador IoT nuevo, solo puede aprovisionar hasta diez soluciones preconfiguradas en una suscripción determinada. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>¿Cuántas instancias de DocumentDB ¿aprovisionar en una suscripción?

Cincuenta. Puede crear una [incidencia de soporte técnico de Azure] [ link-azuresupportticket] elevar este límite, pero de forma predeterminada, solo se pueden aprovisionar cincuenta instancias de DocumentDB por suscripción. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>¿API de mapas de Bing gratuita cuántos puedo aprovisionar en una suscripción?

Dos. Puede crear sólo dos interno transacciones nivel 1 mapas de Bing para planes de empresa en una suscripción de Azure. La solución de supervisión se aprovisiona con el plan 1 de nivel de transacciones interno de forma predeterminada. Como resultado, solo puede aprovisionar hasta dos remotas soluciones de supervisión en una suscripción sin modificaciones.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tengo una implementación de soluciones de supervisión remota con un mapa estático, ¿cómo puedo agregar un mapa de Bing interactivo? 
1. Obtener la API de Bing Maps de Enterprise QueryKey desde el [portal de Azure][lnk-azure-portal]: 
 1. Vaya hasta el grupo de recursos, ¿dónde está la API de Bing Maps para la empresa en el [portal de Azure][lnk-azure-portal].
 2. Haga clic en todos los ajustes de administración de claves después. 
 3. Verá dos claves: claves maestras y QueryKey. Copie el valor de QueryKey.

     > [AZURE.NOTE] ¿No tiene un API de Bing Maps de cuenta de la empresa? Crear una en el [portal de Azure] [ lnk-azure-portal] por haciendo clic en + nuevo, buscando API de mapas de Bing para Enterprise y siga las indicaciones para crear.

2. Extraer el código más reciente de la [Supervisión una remota de IoT Azure][lnk-remote-monitoring-github].

3. Ejecute a una local o siguiendo las instrucciones de implementación de línea de comandos en la carpeta /docs/ en el repositorio de implementación de nube. 

4. Después de que ha ejecutado local o en la nube implementación, mire en la carpeta raíz de la *. archivo user.config creado durante la implementación. Abrir este archivo en un editor de texto. 

5. Cambie la línea siguiente para incluir el valor que ha copiado de la QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>¿Puedo crear una solución preconfigurada si tengo Microsoft Azure para DreamSpark?
En este momento, no puede crear una solución preconfigurada con un [Microsoft Azure para DreamSpark] [ lnk-dreamspark] cuenta. Sin embargo, puede crear una [cuenta de prueba gratuita de Azure] [ lnk-30daytrial] de solo un par de minutos que le permitirán crea una solución preconfigurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>¿Cómo elimino un inquilino AAD?

Consulte blog de Eric Golpe [tutorial acerca de la eliminación de un inquilino de Azure AD][lnk-delete-aad-tennant].

## <a name="next-steps"></a>Pasos siguientes

También puede explorar algunas de las otras características y las capacidades de las soluciones de Suite IoT preconfiguradas:

- [Información general de solución de mantenimiento predictiva preconfigurado][lnk-predictive-overview]
- [Seguridad IoT conceptos básicos][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
