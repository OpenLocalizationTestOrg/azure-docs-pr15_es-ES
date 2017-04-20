<properties
   pageTitle="Usando el conector y escucha HTTP en las aplicaciones de lógica | Servicio de aplicaciones de Microsoft Azure "
   description="Cómo crear y configurar la escucha HTTP y la aplicación de conector o API de acción de HTTP y usar en una aplicación de la lógica de servicio de la aplicación de Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Introducción a la acción de HTTP y escucha HTTP y agregar a su aplicación lógica

> [AZURE.NOTE]Nos estamos fin de soporte para este conector porque su funcionalidad ahora se incluye de forma predeterminada como el **desencadenador manual** al crear nuevas aplicaciones de lógica.  Se recomienda que actualice todas las aplicaciones de lógica que utilicen este conector.  
> Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica.

Conectar directamente a recursos HTTP para escuchar las solicitudes HTTP y configurar las peticiones web HTTP. Hay algunos escenarios donde tendrá que trabajar con las conexiones HTTP directas, incluidos:

1.  Desarrollar una aplicación que es compatible con un sitio web o usuario móvil interactiva front-end de lógica.
2.  Para obtener y procesar datos de un servicio web que no tiene una fuera del conector de cuadro.
3.  Para realizar acciones que ya están expuesta como un servicio web, pero no está disponible como una aplicación de API.

Para estos escenarios, hay dos opciones:

1. **Escucha HTTP**: este conector actúa como desencadenador y escucha peticiones HTTP en un extremo configurado. Cuando se recibe una llamada en el extremo configurado, activa una nueva instancia del flujo y pasa los datos recibidos en la solicitud al flujo de proceso. También puede configurarse para responder automáticamente a la solicitud entrante cuando ha iniciado el flujo de o le permiten crear una respuesta basándose en la ejecución del flujo y enviar una respuesta a la persona que llama.
2. **Acción de HTTP**: Esto le permite configurar una solicitud de web a cualquier extremo disponible en internet, recibe una respuesta y hace que esté disponible para acciones adicionales en el flujo de consumir.

Pueden activar aplicaciones lógica basada en una gran variedad de orígenes de datos y conectores de oferta para obtener y procesar datos como parte del flujo. Puede agregar el conector HTTP a los datos de flujo de trabajo y proceso de negocio como parte de este flujo de trabajo dentro de una aplicación de lógica. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Crear una escucha HTTP para la aplicación de lógica
Un conector puede crear dentro de una aplicación de lógica o directamente desde el catálogo de soluciones de Azure. Para crear un conector desde el catálogo de soluciones:  

1. En la startboard Azure, seleccione **Marketplace**.
2. Busque "HTTP", seleccione escucha HTTP y seleccione **crear**.
3.  Configurar la escucha HTTP como sigue:  
![][1]

4.  Al establecer la configuración del paquete, verá la opción siguiente en si debería responder automáticamente o requieren que el oyente para enviar una respuesta explícita. Establecer en **False** para enviar su propia respuesta:  
![][2]

5.  Haga clic en **Aceptar** para crear.
6.  Una vez creada la instancia de aplicación de la API, abra la configuración para configurar la seguridad. Escucha HTTP es compatible actualmente con autenticación básica. Configurar mediante la opción de seguridad al abrir la escucha HTTP:  
![][3]
  
    **Problema conocido** Configuración de *de la seguridad de mostrar "Ninguno" como el valor predeterminado, pero está definido. Debe cambiar la configuración a Basic y volver a ninguno antes de guardarla para asegurarse de que el oyente de HTTP está configurado correctamente.*  

7. Por último, establezca la configuración de seguridad de la API de App público (anónimo) para permitir que los clientes externos tengan acceso el punto final. Esta opción está disponible en "toda la configuración > configuración de la aplicación" de la aplicación de API de escucha de HTTP:![][10]

Una vez que haya terminado, ahora puede crear una aplicación de lógica para usar la escucha HTTP.

## <a name="using-the-http-listener-in-your-logic-app"></a>Usar la escucha HTTP en su aplicación de lógica
Una vez creada la aplicación de la API, ahora puede usar la escucha HTTP como un desencadenador para la aplicación de la lógica. Para ello, debe:

4.  Crear una nueva aplicación de lógica.
5.  Abrir "Desencadenadores y acciones" para abrir el Diseñador de lógica de aplicaciones y configure el flujo. Escucha HTTP aparece en la galería. Seleccionar.
6.  Ahora puede establecer el método HTTP y la dirección URL relativa en el que requieren el oyente desencadenar el flujo:  
![][4]  
![][5]

7.  Para obtener el URI completo, haga clic en la escucha HTTP para ver sus valores de configuración y copie la dirección URL para el Host de"" de la aplicación de la API:  
![][6]
8.  Ahora puede usar los datos recibidos en la solicitud HTTP en otras acciones en el flujo como sigue:  
![][7]  
![][8]
9.  Por último, para enviar una respuesta, agregue otra escucha HTTP y seleccione la acción enviar respuesta de HTTP. Establezca el identificador de solicitud en la ID obtenida de la escucha HTTP y rellenar el cuerpo de la respuesta y el estado HTTP que desea volver atrás:  
![][9]

## <a name="using-the-http-action"></a>Uso de la acción de HTTP
La acción de HTTP nativa es compatible con aplicaciones de lógica y no requiere una aplicación de API que debe crearse primero para que pueda usarlo. Puede insertar una acción de HTTP en cualquier punto de la aplicación de lógica y elija la URI, encabezados y el cuerpo de la llamada.
La acción HTTP admite varias opciones de seguridad del lado cliente. Vea las [Opciones de seguridad del lado cliente](../scheduler/scheduler-outbound-authentication.md).

El resultado de la acción HTTP es encabezados y el cuerpo, que se puede utilizar más abajo en el flujo similar a cómo se consume salida de otras acciones y conectores.

## <a name="do-more-with-your-connector"></a>Hacer más cosas con el conector
Una vez creado el conector, puede agregar un flujo de trabajo empresarial con una aplicación de lógica. Consulte [¿qué aplicaciones de lógica?](app-service-logic-what-are-logic-apps.md).

Ver la referencia de la API de REST Swagger en [conectores y API de aplicaciones de referencia](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede revisar la seguridad de las estadísticas y control de rendimiento para el conector. Consulte [administrar y supervisar la API de aplicaciones y los conectores integrados](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Si desea empezar a trabajar con aplicaciones de Azure lógica antes de suscribirse para una cuenta de Azure, vaya a [Probar la aplicación de lógica](https://tryappservice.azure.com/?appservice=logic). Inmediatamente, puede crear una aplicación de la lógica de corta duración starter en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
