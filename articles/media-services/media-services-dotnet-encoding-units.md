<properties 
    pageTitle="Cómo agregar unidades de codificación" 
    description="Obtenga información sobre cómo agregar unidades de codificación con .NET"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Cómo ampliar la codificación de .NET SDK

> [AZURE.SELECTOR]
- [Portal](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Información general

>[AZURE.IMPORTANT] Asegúrese de revisar el tema de [Introducción](media-services-scale-media-processing-overview.md) para obtener más información sobre la escala media procesamiento tema.
 
Para cambiar el tipo de unidad reservadas y el número de unidades reservados con .NET SDK de codificación, haga lo siguiente:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Abrir una incidencia de soporte técnico

De forma predeterminada cada cuenta Media Services puede escalar hasta 25 codificación y transmisión reservado unidades de 5 a petición. Puede solicitar un límite superior al abrir una incidencia de soporte técnico.

###<a name="open-a-support-ticket"></a>Abrir una incidencia de soporte técnico

Para abrir un soporte vale haga lo siguiente:

1. Haga clic en [obtener soporte técnico](https://manage.windowsazure.com/?getsupport=true). Si no ha iniciado sesión, se le pedirá que introduzca sus credenciales.

1. Seleccione la suscripción.

1. En tipo de soporte, seleccione "Técnicos".

1. Haga clic en "Crear vale".

1. Seleccione "Azure Media Services" en la lista de productos aparecerán en la página siguiente.

1. Seleccione un tipo de problema"" que es adecuado para el problema.

1. Haga clic en continuar.

1. Siga las instrucciones que aparecen en la página siguiente y, a continuación, escriba los detalles sobre el problema.

1. Haga clic en Enviar para abrir el vale.



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
