<properties
    pageTitle="Usar el conector de archivos en las aplicaciones de lógica | Servicio de aplicaciones de Microsoft Azure"
    description="Cómo crear y configurar el conector de archivo o la aplicación de la API y usarla en una aplicación de la lógica de servicio de la aplicación de Azure"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Empezar a trabajar con el conector de archivo y lo agrega a su aplicación lógica
>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica.

Conectarse a un sistema de archivos para cargar, descargar y mucho más a sus archivos en un equipo host. Pueden activar aplicaciones lógica basada en una gran variedad de orígenes de datos y conectores de oferta para obtener y procesar datos. Puede agregar el conector de archivos a los datos de flujo de trabajo y proceso de negocio como parte de este flujo de trabajo dentro de una aplicación de lógica. 

El conector de archivos utiliza el Administrador de conexión híbrido conectividad híbrido en el sistema de archivos de host.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Creación de un conector de archivo para la aplicación de lógica ##
Para usar el conector de archivos, debe crear una instancia de la aplicación de API de conector de archivo. Esto se puede hacer como sigue:

1.  Abrir el catálogo de soluciones de Azure con el signo + nueva opción en el lado izquierdo del Portal de Azure.
2.  Buscar "conector de archivos".
3.  Seleccione **Archivo conector (preview)** de los resultados de búsqueda.
4.  Seleccione el botón **crear**
5.  Configurar el conector de archivo como sigue:  
![][1]

    - **Nombre** : dé un nombre para el conector de archivos
    - **Configuración del paquete**
        - **Carpeta raíz** : especificar la ruta de acceso de la carpeta raíz en su equipo de host. Por ejemplo. D:\FileConnectorTest
        - **Cadena de conexión de Bus de servicio** : proporcione una cadena de conexión de Bus de servicio. Asegúrese de que es el espacio de nombres de bus de servicio de tipo estándar y no básicas para permitir el uso de transmisiones de Bus de servicio.  Retransmisión de Bus de servicio se usa para conectarse al administrador de conexión de híbrido.
    - **Plan de servicios de aplicación** - seleccionar o crear un plan de servicios de aplicación
    - **Nivel de precios** - elija un nivel de precios para el conector
    - **Grupo de recursos** - seleccionar o crear un grupo de recursos reside de conector
    - **Suscripción** : elija una suscripción que desea crear en este conector
    - **Ubicación** : elija la ubicación geográfica donde desea que el conector para su implementación

4. Haga clic en crear. Se creará un nuevo conector de archivo

## <a name="configure-hybrid-connection-manager"></a>Configurar el Administrador de conexión híbrido ##
Una vez creada la instancia de App API, vaya a su escritorio.  Esto se puede hacer clic en Examinar > aplicaciones API > Seleccionar el conector de archivo API App.  Desde aquí, el Administrador de conexión híbrido debe configurarse.
Para obtener más información sobre la configuración y el Administrador de conexión híbrido de solución de problemas, consulte [con el Administrador de conexión híbrido].

## <a name="using-the-file-connector-in-your-logic-app"></a>Usar el conector de archivos en la aplicación de lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de archivos como una acción de la aplicación de la lógica. Para ello, debe:

1.  Crear una nueva aplicación de lógica y elija el mismo grupo de recursos que tiene el conector de archivos. Siga las instrucciones para [crear una nueva aplicación de lógica].

2.  Abrir "Desencadenadores y acciones" dentro de la aplicación de lógica creada para abrir el Diseñador de aplicaciones de lógica y configure el flujo.

3.  El conector de archivo aparecerán en la sección "API aplicaciones en este grupo de recursos" en la galería en el lado derecho.

4.  Puede anular la aplicación de API de conector de archivo en el editor haciendo clic en el conector de archivo"". Conector de archivos expone un desencadenador y 4 acciones:  
![][5]

6.  Cada uno de estos expone algunas propiedades. La imagen siguiente muestra las propiedades del desencadenador y obtener el archivo de acción:  
![][6]

7. Una vez configurados estos, el desencadenador y la acción pueden usarse en el flujo. Del mismo modo, también se pueden configurar otras acciones.

> [AZURE.NOTE] El desencadenador de archivo elimina el archivo después de que se lee correctamente desde la carpeta.

## <a name="file-connector-rest-apis"></a>Conector API de REST de archivos ##
Para usar el conector fuera de una aplicación de lógica, puede aprovechar las API de REST expuestas por el conector. Puede ver este definiciones de API mediante Examinar -> Api aplicación -> conector de archivos. Ahora haga clic en la lente de definición de la API en la sección de resumen para ver todas las API expuestas por este conector:  
![][7]

Detalles de la API pueden encontrarse en la [definición de la API de conector de archivos].

## <a name="do-more-with-your-connector"></a>Hacer más cosas con el conector
Una vez creado el conector, puede agregar un flujo de trabajo empresarial con una aplicación de lógica. Consulte [¿qué aplicaciones de lógica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si desea empezar a trabajar con aplicaciones de Azure lógica antes de suscribirse para una cuenta de Azure, vaya a la [aplicación intente lógica](https://tryappservice.azure.com/?appservice=logic), donde puede crear inmediatamente una aplicación de la lógica de inicio de corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

Ver la referencia de la API de REST Swagger en [conectores y API de aplicaciones de referencia](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede revisar la seguridad de las estadísticas y control de rendimiento para el conector. Consulte [administrar y supervisar la API de aplicaciones y conector integrados](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Crear una nueva aplicación de lógica]: app-service-logic-create-a-logic-app.md
[Definición de la API de conector de archivos]: https://msdn.microsoft.com/library/dn936296.aspx
[Uso del Administrador de conexión híbrido]: app-service-logic-hybrid-connection-manager.md
