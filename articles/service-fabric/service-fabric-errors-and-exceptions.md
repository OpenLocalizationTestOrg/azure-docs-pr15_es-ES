<properties
   pageTitle="Comunes FabricClient excepciones | Microsoft Azure"
   description="Describe las excepciones y los errores que se pueden producir por las APIs FabricClient mientras lleva a cabo operaciones de administración de clúster y aplicación comunes."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Excepciones y errores al trabajar con las APIs FabricClient comunes
Las API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permiten a los administradores de clúster y la aplicación realizar tareas administrativas en un clúster, servicio o aplicación de servicio tela. Por ejemplo, implementación de aplicaciones, actualización y eliminación, comprobar el estado de un clúster o probar un servicio. Los desarrolladores de aplicaciones y los administradores de clústeres pueden usar las APIs FabricClient para desarrollar herramientas para administrar el clúster de tela de servicio y las aplicaciones.

Existen varios tipos de operaciones que se pueden realizar con FabricClient diferentes.  Cada método puede producir excepciones para errores debido a la entrada incorrecta, runtime errores o problemas de infraestructura transitorias.  Consulte la documentación de referencia de la API para buscar las excepciones que se producen por un método específico. Existen algunas excepciones, sin embargo, que se pueden producir muchas de las API de [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) diferentes. La siguiente tabla enumeran las excepciones que son comunes en las APIs FabricClient.

|Excepción| Inicia cuando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|El objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) se encuentra en estado cerrado. Eliminar el objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) está utilizando y crear una instancia de un nuevo objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) . |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|La operación ha agotado el tiempo de espera. Cuando la operación tarda más MaxOperationTimeout para completar, se devuelve [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|Error en la comprobación de acceso para la operación. Se devuelve E_ACCESSDENIED.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Se produjo un error de tiempo de ejecución durante la operación. Cualquiera de los métodos de FabricClient puede producir potencialmente [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), la propiedad de [código de error](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica la causa exacta de la excepción. Códigos de error se definen en la enumeración [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|Debido a una condición de error transitorias algún tipo de error en la operación. Por ejemplo, una operación puede fallar porque un quórum de réplicas temporalmente no es accesible. Excepciones transitorias corresponden a las operaciones error que pueden volver a intentar.|

Algunos errores comunes de [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) que se pueden devolver en un [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Error| Condición|
|---------|:-----------|
|CommunicationError|Un error de comunicación causado la operación no se realice correctamente, vuelva a intentar la operación.|
|InvalidCredentialType|El tipo de credencial no es válido.|
|InvalidX509FindType|La X509FindType no es válido.|
|InvalidX509StoreLocation|La X509 ubicación de almacenamiento no es válida.|
|InvalidX509StoreName|El X509 nombre de la tienda no es válido.|
|InvalidX509Thumbprint|La X509 cadena de huella digital de certificado no es válida.|
|InvalidProtectionLevel|El nivel de protección no es válido.|
|InvalidX509Store|La X509 no se puede abrir el almacén de certificados.|
|InvalidSubjectName|El nombre del asunto no es válido.|
|InvalidAllowedCommonNameList|El formato de cadena comunes de nombre de lista no es válido. Debe ser una lista de valores separados por comas.|
