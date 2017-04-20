<properties 
   pageTitle="Realizar un seguimiento de mensajes de B2B en las aplicaciones de la lógica de servicio de la aplicación de Azure | Microsoft Azure" 
   description="Este tema trata sobre seguimiento de procesamiento de B2B" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="track-b2b-messages"></a>Realizar un seguimiento de mensajes de B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## <a name="b2b-tracking-information"></a>Información de seguimiento de B2B
Comunicación de B2B implica entre socios comerciales de procesamiento de mensajes. Las relaciones se definen como agreements entre dos socios. Una vez establecida la comunicación, debe haber una manera de supervisar si la comunicación está ocurriendo según lo esperado. 

Hemos implementado el seguimiento de mensajes para los siguientes escenarios B2B: AS2, EDIFACT y X12.

## <a name="as2"></a>AS2
Una vez que haya creado una instancia de una aplicación de la API de AS2, vaya a esa instancia y seleccione **seguimiento**. Aquí puede ver y filtrar la información de seguimiento de AS2:  

![][1]  

## <a name="edifact"></a>EDIFACT
Una vez que haya creado una instancia de una aplicación de la API de EDIFACT, vaya a esa instancia y seleccione **seguimiento**. Aquí puede ver y filtrar todos lo EDIFACT información de seguimiento. Además, puede ver el nivel de intercambio, el nivel de grupo y transacción establece nivel datos, todo en una sola vista. 

Si se crean lotes como parte de contratos de EDIFACT en la aplicación de la API de administración de partners de cotización asociada, la sección de procesamiento por lotes enumera todos estos lotes. Puede seleccionar un lote para ver el mensaje activo (si existe) y también la información de la completadas:  

![][2]      

## <a name="x12"></a>X12
Una vez que haya creado una instancia de un X12 API App, vaya a esa instancia y seleccione **seguimiento**. Aquí puede ver y filtrar todos los X12 información de seguimiento. Además, puede ver el nivel de intercambio, el nivel de grupo y transacción establece nivel datos, todo en una sola vista.

Si se crean lotes como parte de X12 contratos en la aplicación de la API de administración de partners de cotización asociada, a continuación, en la sección de procesamiento por lotes enumera todos estos lotes. Puede seleccionar un lote para ver el mensaje activo (si existe) y también la información de los lotes completadas.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png
