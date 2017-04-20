<properties 
    pageTitle="Obtenga más información sobre Enterprise Integration Pack descodificar AS2 mensaje Connctor | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo utilizar asociados con las aplicaciones empresariales Integration Pack y lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-decode-as2-message"></a>Introducción a descodificar AS2 mensaje

Conectarse a descodificar AS2 mensaje para establecer la seguridad y confiabilidad al transmitir mensajes. Proporciona la firma digital, descifrado y confirmaciones a través de notificaciones de disposición de mensaje (MDN).

## <a name="create-the-connection"></a>Crear la conexión

### <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)

* Se requiere una cuenta de integración utilizar descodificar AS2 mensaje conector. Ver detalles sobre cómo crear una [Cuenta de integración](./app-service-logic-enterprise-integration-create-integration-account.md), [socios](./app-service-logic-enterprise-integration-partners.md) y un [contrato AS2](./app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Conectarse a descodificar AS2 mensaje con los pasos siguientes:

1. [Crear una aplicación de lógica](./app-service-logic-create-a-logic-app.md) proporciona un ejemplo.

2. Este conector tener desencadenadores. Usar otros desencadenadores para iniciar la aplicación de la lógica, como un desencadenador de solicitud.  En el Diseñador de la aplicación de la lógica, agregar un desencadenador y agregue una acción.  Seleccione Mostrar Microsoft API administradas en el menú desplegable de la lista y, a continuación, escriba "AS2" en el cuadro de búsqueda.  Seleccione AS2: descodificar AS2 mensaje

    ![AS2 de búsqueda](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Si todavía no lo ha creado previamente las conexiones con integración de cuenta, se le pedirá los detalles de conexión

    ![Crear conexión de integración](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. Escriba los detalles de la cuenta de integración.  Propiedades con un asterisco son necesarias

  	| (Propiedad)   | Detalles |
  	| --------   | ------- |
  	| Nombre de la conexión *    | Escriba un nombre para la conexión |
  	| Integración cuenta * | Escriba el nombre de cuenta de integración. Asegúrese de que su cuenta de integración y la aplicación de la lógica se encuentran en la misma ubicación de Azure |

    Cuando haya finalizado, los detalles de la conexión ser similares al siguiente

    ![conexión de integración](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. Seleccione **crear**
    
6. Observe que se ha creado la conexión.  Ahora, continúe con el resto de los pasos en su aplicación de lógica

    ![conexión de integración creada](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 

7. Seleccione los encabezados y el cuerpo desde los resultados de la solicitud

    ![proporcionar campos obligatorios](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>La AS2 descodificar hace lo siguiente

* Procesa encabezados AS2/HTTP
* Comprueba la firma (si está configurado)
* Descifra los mensajes (si está configurado)
* Descomprime el mensaje (si está configurado)
* Reconcilia una MDN recibido con el mensaje original de salida
* Actualizaciones y establece una correlación entre registros en la base de datos no rechazo
* Escribe los registros para los informes de estado de AS2
* El contenido de carga de salida es base 64 codificado
* Determina si un MDN es necesario y si la MDN debe ser sincrónico o asincrónico según configuración AS2 contrato
* Genera una MDN sincrónico o asincrónico (según las configuraciones de contrato)
* Establece el token de correlación y propiedades de la MDN

##<a name="try-it-for-yourself"></a>Pruébelo: para usted

¿Por qué no probarlo. Haga clic [aquí](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implementar una aplicación totalmente operativa lógica de su propio con las características de lógica aplicaciones AS2 

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise") 