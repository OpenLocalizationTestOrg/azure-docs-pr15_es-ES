<properties
    pageTitle="Lista de servicios de BizTalk de tareas de administración y desarrollo | Microsoft Azure"
    description="Planificación y trabajo de ayuda para la implementación de Azure BizTalk Services."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administración y la lista de tareas de desarrollo de servicios de BizTalk  

## <a name="getting-started"></a>Introducción
Cuando se trabaja con servicios de Microsoft Azure BizTalk, hay varios local y componentes basados en la nube para tener en cuenta. Para empezar, tenga en cuenta el flujo de proceso siguientes:  

|Paso|¿Quién es responsable|Tarea|Vínculos relacionados|
|----|----|----|----|
|1.|Administrador|Crear la suscripción a Microsoft Azure con una cuenta de Microsoft o una cuenta profesional|[Portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Administrador|Crear o aprovisionar un BizTalk Service.|[Crear un BizTalk Service con Azure portal clásica](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Administrador|Registrar que o implementación de servicios de BizTalk de la empresa|[Registrar y actualizar una implementación de servicio de BizTalk en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Administrador|Se aplica si la aplicación utiliza el servicio de adaptador de BizTalk para conectarse a un sistema de línea de negocio (LOB) local o utiliza una cola o un tema de destino.  Crear el Namespace de Bus de servicio de Azure. Asigne a este espacio de nombres, nombre del emisor de Bus de servicio y valores de clave de emisor de Bus de servicio para el desarrollador.|[Cómo: crear o modificar un Namespace de servicio de Bus de servicio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) y [obtener el nombre del emisor y los valores de clave del emisor](biztalk-issuer-name-issuer-key.md)|
|5.|Programador|Instale el SDK y cree el proyecto de BizTalk Service en Visual Studio.|[Instale los servicios de BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) y [crear puntos finales de mensajería enriquecidos en Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Programador|Implementar el BizTalk Service proyecto a su BizTalk Service alojado en Azure.|[Implementar y actualizar el proyecto de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Administrador|Se aplica si está utilizando EDI.  Puede agregar a socios y crear Agreements en el Portal de servicios de Microsoft Azure BizTalk. Cuando se crea un contrato, puede agregar el puente o transformaciones creadas por el programador a la configuración de contrato.|[Configuración de EDI, AS2 y EDIFACT en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Administrador|Con el Portal de clásico Azure, supervisar el estado de su BizTalk Service, incluidos los indicadores de rendimiento.|[Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Administrador|Con el Portal de servicios de Microsoft Azure BizTalk, administrar los artefactos usados servicios BizTalk y seguimiento de mensajes cuando se procesan los archivos de puente.|[Con el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Administrador|Crear un plan de copia de seguridad para realizar copias de seguridad BizTalk Service.|[Continuidad empresarial y recuperación en servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Pasos siguientes
[Tutoriales y ejemplos](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Crear el proyecto de Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instale los servicios de BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Conceptos
[Crear el proyecto de Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 y EDIFACT mensajería (para empresas)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Otros recursos  
[Agregar origen de destino y puente mensajería extremos](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Obtenga más información y crear mapas de mensajes y transformaciones](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Usar el servicio de adaptador BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Servicios de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)
