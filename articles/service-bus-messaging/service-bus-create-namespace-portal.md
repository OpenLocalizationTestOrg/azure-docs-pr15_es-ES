<properties
    pageTitle="Crear un espacio de nombres de Bus de servicio con el portal de Azure | Microsoft Azure"
    description="Para empezar con Bus de servicio, necesitará un espacio de nombres. Aquí le mostramos cómo crear una con el portal de Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Crear un espacio de nombres de Bus de servicio con el portal de Azure

Un espacio de nombres es un contenedor común para todos los componentes de mensajería. Varias colas y temas pueden encontrarse en un único espacio de nombres y espacios de nombres a menudo servir como contenedores de aplicación. Hay dos maneras de crear un espacio de nombres de Bus de servicio.

1.  Portal de Azure (en este artículo)

2.  [Plantillas de administrador de recursos][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Crear un espacio de nombres en el portal de Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

¡Felicidades! Ya ha creado un espacio de nombres de mensajería de Bus de servicio.

## <a name="next-steps"></a>Pasos siguientes

Consulte nuestra [muestras GitHub] [ github-samples] que muestran algunas de las características más avanzadas de mensajería de Bus de servicio de Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples