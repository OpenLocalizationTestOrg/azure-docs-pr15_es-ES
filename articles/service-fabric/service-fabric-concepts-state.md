<properties
   pageTitle="Definir y administrar el estado | Microsoft Azure"
   description="Cómo definir y administrar el estado del servicio en tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="service-state"></a>Estado del servicio
**Estado del servicio** hace referencia a los datos que el servicio se requiere para funcionar. Incluye las estructuras de datos variables y que el servicio lee y escribe para que funcione.

Considere la posibilidad de un servicio de calculadora sencilla, por ejemplo. Este servicio recibe dos números y devuelve la suma. Esto es un servicio puramente independiente que no tiene datos asociados.

Ahora, veamos el misma Calculadora, pero además informático suma, también tiene un método para devolver la suma última que ha calculado. Este servicio es ahora con estado--contiene algún estado que escribe a (cuando se calcula una suma nueva) y se lee (cuando devuelve la suma calculada última).

En tela de servicio de Azure, el primer servicio se denomina servicio de estado. El segundo servicio se denomina servicio de estado.

## <a name="storing-service-state"></a>Estado del servicio de almacenamiento
Estado puede externalizar o ubicar con el código que se manipula el estado. Externalización de estado normalmente se realiza mediante un almacén o base de datos externa. En nuestro ejemplo Calculadora, podría ser una base de datos SQL en el que se almacena el resultado actual en una tabla. Cada solicitud para calcular la suma, realiza una actualización en esta fila.

También puede posicionar con el código que procesa este código de estado. Servicios con estado en tela de servicio se crean con este modelo. Servicio tela proporciona la infraestructura para garantizar que este estado se altamente disponible y tolerancia si se produce un error.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los conceptos de tela de servicio, consulte lo siguiente:

- [Disponibilidad de los servicios de tela de servicio](service-fabric-availability-services.md)

- [Escalabilidad de servicios de tela de servicio](service-fabric-concepts-scalability.md)

- [Partición servicios estructurales de servicio](service-fabric-concepts-partitioning.md)
