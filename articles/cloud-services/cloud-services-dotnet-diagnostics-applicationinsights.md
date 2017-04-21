<properties
   pageTitle="Solucionar problemas con la información de la aplicación de servicios de nube | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar problemas de servicio de nube mediante perspectivas de aplicación a los datos de proceso de diagnósticos de Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Solucionar problemas con la información de la aplicación de servicios de nube

Con la extensión de diagnósticos de [2,8 SDK de Azure](https://azure.microsoft.com/downloads/) y Azure 1,5 puede ahora enviar los datos de Azure diagnósticos de su servicio de nube directamente a la información de la aplicación. Registros de los distintos tipos de registros recopilados por diagnósticos de Azure, incluidos los registros de aplicación, registros de eventos de windows ETW y contadores de rendimiento ahora se envía a la información de la aplicación y visualizarse en el portal de aplicación perspectivas interfaz de usuario. Cuando se usa junto con el SDK de perspectivas aplicación ahora puede obtener recomendaciones sobre métricas y los registros procedentes de la aplicación, así como los datos de nivel sistema y la infraestructura procedentes de diagnósticos de Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurar diagnósticos de Azure para enviar datos a la aplicación perspectivas

Siga estos pasos para configurar el proyecto de servicio de nube para enviar datos de Azure diagnósticos de impresiones de aplicación.

1) En el Explorador de soluciones de Visual Studio, haga clic en una función y seleccione **Propiedades** para abrir el Diseñador de función

![Propiedades de la función de solución Explorer][1]

2) En el Diseñador de la función en la sección diagnóstico, active la casilla de verificación para **enviar datos de diagnósticos de impresiones de aplicación**

![Diseñador de rol enviar datos de diagnósticos de impresiones de aplicación][2]

3) En el cuadro de diálogo que aparece, seleccione el recurso de perspectivas de aplicación que desea enviar los datos de diagnóstico de Azure. El cuadro de diálogo le permite seleccionar un recurso de información de la aplicación existente de la suscripción o especificar manualmente una clave de instrumentación para un recurso de información de la aplicación. Si no tiene un recurso de información de la aplicación existente puede crear en haciendo clic en el vínculo de **crear un nuevo recurso** que se abrirá una ventana del explorador en el portal de clásica Azure donde puede crear un recurso de información de aplicación. Para obtener más información sobre la creación de un recurso de información de la aplicación, vea [crear un nuevo recurso de aplicación perspectivas](../application-insights/app-insights-create-new-resource.md)

![Seleccione el recurso de información de la aplicación][3]

4) Una vez que haya agregado el recurso de información de la aplicación, se almacena la clave de instrumentación para ese recurso como una opción de configuración de servicio con el nombre **APPINSIGHTS_INSTRUMENTATIONKEY**. Puede cambiar esta configuración para cada entorno o la configuración del servicio, seleccione una configuración diferente de la lista de configuración de servicio hacia abajo y especificar una nueva clave de instrumentación para esa configuración.

![Seleccione configuración del servicio][4]

La opción de configuración **APPINSIGHTS_INSTRUMENTATIONKEY** se usa Visual Studio para configurar la extensión de diagnósticos con la información de recursos de aplicación perspectivas adecuada durante la publicación. La configuración es una forma conveniente de definir claves instrumentación diferentes configuraciones de servicio diferente. Visual Studio se traducir dicha configuración e insertarlo en la configuración de diagnósticos de extensión público al publicar. Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, el paquete de salida de Visual Studio también contiene la configuración del pública XML con la instrumentación perspectivas de aplicación correspondiente clave incluida. Los archivos de configuración público se crean en la carpeta Extensiones y sigan el patrón PaaSDiagnostics. <RoleName>. PubConfig.xml. Las implementaciones de PowerShell según pueden usar este patrón para asignar cada configuración a un rol.

5) Habilitar la **enviar datos de diagnósticos de aplicación impresiones** configurará automáticamente diagnóstico de Azure para enviar todos los contadores de rendimiento y registros de nivel de error que se recopilan por el agente de diagnóstico de Azure de impresiones de aplicación. Si desea configurar qué datos se envían a la aplicación perspectivas, a continuación, debe modificar manualmente el archivo *diagnostics.wadcfgx* para cada rol. Consulte [Configurar diagnósticos de Azure para enviar datos a la información de la aplicación](../azure-diagnostics-configure-applicationinsights.md) para obtener más información sobre cómo actualizar manualmente la configuración.

Una vez que el servicio de nube está configurado para enviar datos de Azure diagnósticos de impresiones de aplicación que se puede implementar en Azure como lo haría normalmente que asegurarse de que la extensión de diagnóstico de Azure está habilitado. Consulte [publicar un servicio de nube mediante Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visualización de datos de diagnóstico de Azure en perspectivas de aplicación
La telemetría diagnóstico Azure aparecerá en el recurso de aplicación perspectivas configurado para su servicio de nube.

A continuación se muestra cómo lo diversos Azure de registro de diagnóstico tipos mapa conceptos perspectivas de aplicación:  

-  Contadores de rendimiento se muestran como métricas personalizada en perspectivas de aplicación
-  Registros de eventos de Windows se muestran como seguimientos y eventos personalizados en perspectivas de aplicación
-  Registros de aplicaciones, registros de ETW y los registros de diagnósticos infraestructura se muestran como seguimientos en perspectivas de aplicación.

Para ver los datos de diagnóstico de Azure en perspectivas de aplicación:

- Use el [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md) para visualizar los contadores personalizados o recuentos de distintos tipos de eventos de registro de eventos de windows.

![Métricas personalizados en el Explorador de métricas][5]

- Use [la búsqueda](../application-insights/app-insights-diagnostic-search.md) para buscar en los distintos registros de seguimiento enviados por diagnósticos de Azure. Por ejemplo, si tiene una excepción no controlada en una función que provocó el rol bloquearse y reciclaje esa información se muestra en el canal de *aplicación* del *registro de eventos de Windows*. Puede usar la funcionalidad de búsqueda para ver el error de registro de eventos de Windows y obtener el seguimiento de pila completo para la excepción de lo que le permite buscar la causa del problema.

![Seguimientos de búsqueda][6]

## <a name="next-steps"></a>Pasos siguientes

- [Agregar el SDK de perspectivas de aplicación en el servicio de nube](../application-insights/app-insights-cloudservices.md) para enviar datos sobre solicitudes, excepciones, dependencias y cualquier telemetría personalizado desde la aplicación. Combinar con los diagnósticos de Azure datos puede obtener una vista completa de la aplicación y el sistema en el mismo recurso de conocimiento de la aplicación.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
