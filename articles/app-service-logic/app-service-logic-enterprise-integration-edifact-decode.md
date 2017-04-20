<properties 
    pageTitle="Obtenga más información sobre Enterprise Integration Pack descodificar EDIFACT mensaje conector | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-decode-edifact-message"></a>Introducción a descodificar EDIFACT mensaje

Valide EDI y propiedades específicas del partner, genera documento XML para cada conjunto de transacciones y genera confirmación para transacción procesada.

## <a name="create-the-connection"></a>Crear la conexión

### <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure puede crear una [cuenta gratuita](https://azure.microsoft.com/free)

* Se requiere una cuenta de integración utilizar descodificar EDIFACT mensaje conector. Ver detalles sobre cómo crear una [Cuenta de integración](./app-service-logic-enterprise-integration-create-integration-account.md), [socios](./app-service-logic-enterprise-integration-partners.md) y [contrato EDIFACT](./app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Conectarse a descodificar EDIFACT mensaje con los pasos siguientes:

1. [Crear una aplicación de lógica](./app-service-logic-create-a-logic-app.md) proporciona un ejemplo.

2. Este conector tener desencadenadores. Usar otros desencadenadores para iniciar la aplicación de la lógica, como un desencadenador de solicitud.  En el Diseñador de la aplicación de la lógica, agregar un desencadenador y agregue una acción.  Seleccione Mostrar Microsoft API administradas en el menú desplegable de la lista y, a continuación, escriba "EDIFACT" en el cuadro de búsqueda.  Seleccione descodificar mensaje EDIFACT

    ![búsqueda EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
    
3. Si todavía no lo ha creado previamente las conexiones con integración de cuenta, se le pedirá los detalles de conexión

    ![Crear cuenta de integración](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)  

4. Escriba los detalles de la cuenta de integración.  Propiedades con un asterisco son necesarias

  	| (Propiedad) | Detalles |
  	| -------- | ------- |
  	| Nombre de la conexión * | Escriba un nombre para la conexión |
  	| Integración cuenta * | Escriba el nombre de cuenta de integración. Asegúrese de que su cuenta de integración y la aplicación de la lógica se encuentran en la misma ubicación de Azure |

    Cuando haya finalizado, los detalles de la conexión ser similares al siguiente

    ![cuenta de integración creada](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)  

5. Seleccione **crear**

6. Observe que la conexión se ha creado

    ![detalles de conexión de cuenta de integración](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

7. Haga clic en mensaje de archivo plano EDIFACT descodificar

    ![proporcionar campos obligatorios](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>Descodificar EDIFACT hace siguiendo

* Resolver el contrato comparando el calificador de remitente & identificador y calificador receptor & identificador
* Divide varios intercambios en un único mensaje en distintas.
* Valide el envolvente contra cotización de acuerdo con el socio
* Separa el intercambio.
* Valide EDI y propiedades específicas del partner incluye
    * Validación de la estructura de los sobres de intercambio.
    * Validación de esquema de los sobres con el esquema de control.
    * Validación de esquema de los elementos de datos del conjunto de transacciones con el esquema de mensaje.
    * Validación de EDI realizada en elementos de datos del conjunto de transacciones
* Comprueba si los números de control de conjunto de intercambio, grupo y transacción no son duplicados (si está configurado) 
    * Comprueba el número de control de intercambio contra intercambios ya ha recibido. 
    * Comprueba el número de control de grupo con otros números de control de grupo en el intercambio. 
    * Comprueba que la transacción establece el número de control con otros números de control de conjunto de transacciones de ese grupo.
* Genera un documento XML para cada conjunto de transacciones.
* Convierte el intercambio completo en XML 
    * Intercambio de división como conjuntos de transacciones - suspender conjuntos de transacciones en error: analiza cada transacción establecer en un intercambio en otro documento XML. Si uno o varios conjuntos de transacciones en el intercambio de errores de validación, a continuación, descodificar EDIFACT suspende solo los conjuntos de transacciones. 
    * Intercambio de división como conjuntos de transacciones - suspender intercambio error: analiza cada transacción establecer en un intercambio en otro documento XML.  Si uno o varios conjuntos de transacciones en el intercambio errores de validación, a continuación, descodificar EDIFACT suspende el intercambio todo.
    * Mantener el intercambio - suspender conjuntos de transacciones en error: crea un documento XML para el intercambio por lotes completo. Descodificar EDIFACT suspende los conjuntos de transacciones que un error de validación, mientras sigue procesar todos los otros conjuntos de transacciones
    * Mantener el intercambio - suspender intercambio error: crea un documento XML para el intercambio por lotes completo. Si uno o varios conjuntos de transacciones en el intercambio no validación, a continuación, descodificar EDIFACT suspende el intercambio de todo 
* Genera una técnicas (control) o confirmación funcional (si está configurado).
    * Una confirmación técnica o el ACK CONTRL informa de los resultados de una comprobación de sintaxis del intercambio recibido completa.
    * Una confirmación funcional reconoce Aceptar o rechazar un intercambio recibido o un grupo

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise") 