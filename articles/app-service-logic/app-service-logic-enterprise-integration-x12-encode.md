<properties 
    pageTitle="Obtenga más información sobre Enterprise Integration Pack codificar X12 mensaje Connctor | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-encode-x12-message"></a>Introducción a codificar X12 mensaje

Valide EDI y propiedades específicas del partner, convierte los mensajes con codificación XML en conjuntos de transacciones EDI en el intercambio y solicita una confirmación de técnicas o funcional

## <a name="create-the-connection"></a>Crear la conexión

### <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)

* Se requiere una cuenta de integración usar codificar x12 conector de mensaje. Ver detalles sobre cómo crear una [Cuenta de integración](./app-service-logic-enterprise-integration-create-integration-account.md), [socios](./app-service-logic-enterprise-integration-partners.md) y [X12 contrato](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>Conectar con mensaje codificar X12 con los pasos siguientes:

1. [Crear una aplicación de lógica](./app-service-logic-create-a-logic-app.md) proporciona un ejemplo

2. Este conector tener desencadenadores. Usar otros desencadenadores para iniciar la aplicación de la lógica, como un desencadenador de solicitud.  En el Diseñador de la aplicación de la lógica, agregar un desencadenador y agregue una acción.  Seleccione Mostrar Microsoft API administradas en el menú desplegable de la lista y, a continuación, escriba "x12" en el cuadro de búsqueda.  Seleccione cualquier X12-codificar X12 mensaje por nombre de contrato o X12-codificar a 12 X mensaje identidades.  

    ![Buscar x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 

3. Si todavía no lo ha creado previamente las conexiones con integración de cuenta, se le pedirá los detalles de conexión

    ![conexión de la cuenta de integración](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 


4. Escriba los detalles de la cuenta de integración.  Propiedades con un asterisco son necesarias

  	| (Propiedad) | Detalles |
  	| -------- | ------- |
  	| Nombre de la conexión * | Escriba un nombre para la conexión |
  	| Integración cuenta * | Escriba el nombre de cuenta de integración. Asegúrese de que su cuenta de integración y la aplicación de la lógica se encuentran en la misma ubicación de Azure |

    Cuando haya finalizado, los detalles de la conexión ser similares al siguiente

    ![conexión de la cuenta de integración creado](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 


5. Seleccione **crear**

6. Observe que se ha creado la conexión.

    ![detalles de conexión de cuenta de integración](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12-codificar X12 mensaje por nombre de acuerdo

7. Seleccione X12 contrato desde el mensaje de la lista desplegable y xml para codificar.

    ![proporcionar campos obligatorios](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12-codificar X12 mensaje de identidades

7.  Proporcionar identificador de remitente, calificador de remitente, el identificador de receptor y calificador receptor tal como está configurado en el X12 contrato.  Seleccione el mensaje de xml para codificar

    ![proporcionar campos obligatorios](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>Codificar X12 sirve siguientes:

* Resolución de contrato comparando las propiedades de contexto de remitente y el destinatario.
* Serializa el intercambio EDI, convertir mensajes con codificación XML en conjuntos de transacciones EDI en el intercambio.
* Se aplica a segmentos de encabezado y final del conjunto de transacción
* Genera un número de control de intercambio, un número de control de grupo y un número de control de transacciones establecido para cada intercambio saliente
* Reemplaza los separadores en los datos de carga
* Valide EDI y propiedades específicas del partner
    * Validación de esquema de los elementos de datos del conjunto de transacciones contra el esquema de mensaje
    * Validación de EDI se realiza en elementos de datos del conjunto de transacciones.
    * Validación extendida realizada en elementos de datos del conjunto de transacciones
* Solicita una confirmación de técnicas o funcionales (si está configurado).
    * Una confirmación técnica genera como resultado de la validación de encabezado. La confirmación técnica informa del estado de la transformación de un encabezado de intercambio y final por el receptor de dirección
    * Una confirmación funcional genera como resultado de la validación de cuerpo. Cada error al procesamiento del documento recibido informes de la confirmación funcional

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise") 

