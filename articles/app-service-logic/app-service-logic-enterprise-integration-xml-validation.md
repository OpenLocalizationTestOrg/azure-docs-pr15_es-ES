<properties 
    pageTitle="Información general sobre la validación de XML en el módulo de integración de empresa | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo funciona la validación en las aplicaciones empresariales Integration Pack y lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-validation"></a>Integración de empresa con la validación de XML

## <a name="overview"></a>Información general
A menudo, en escenarios B2B, los asociados a un acuerdo necesitan validar que mensajes intercambiar entre ellos son válidos para que pueda comenzar el procesamiento de los datos. En el módulo de integración de Enterprise, puede usar el conector de validación de XML para validar documentos con un esquema predefinido.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Cómo validar un documento con el conector de validación de XML
1. Crear una aplicación de lógica y [vincularlo a su cuenta de integración](./app-service-logic-enterprise-integration-accounts.md "información sobre cómo vincular una cuenta de integración a una aplicación de la lógica") que contiene el esquema que se usa para validar los datos XML.
2. Agregar un desencadenador de **solicitud - solicitud HTTP un cuando se reciba** a su aplicación lógica  
![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. Agregar la acción de **Validación XML** por seleccionando **Agregar una acción**  
4. Escriba *xml* en el cuadro de búsqueda para filtrar todas las acciones que desea usar 
5. Seleccionar la **validación de XML**     
![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. Seleccione el cuadro de texto de **contenido**  
![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Seleccione la etiqueta de cuerpo como el contenido que se va a validar.   
![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. Seleccione el cuadro **Nombre de esquema** de la lista y elija el esquema que desea usar para validar la entrada *contenido* anterior     
![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. Guarde su trabajo  
![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

En este momento, haya terminado de configurar el conector de validación. En una aplicación del mundo real, desea almacenar los datos validados en una aplicación de LOB como SalesForce. Puede agregar fácilmente una acción para enviar los resultados de la validación a Salesforce. 

Ahora puede probar la acción de validación realizando una solicitud al extremo HTTP.  

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise")   