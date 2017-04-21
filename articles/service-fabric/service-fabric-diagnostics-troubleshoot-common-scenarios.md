<properties
   pageTitle="Solución de problemas con el seguimiento de eventos | Microsoft Azure"
   description="Los problemas más comunes que puedan surgir al implementar servicios en tela de servicio de Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Solucionar problemas comunes al implementar servicios en tela de servicio de Azure

Cuando se está ejecutando servicios en el equipo del programador, es fácil de usar [Herramientas de depuración de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Para clústeres remotos, [informes de estado](service-fabric-view-entities-aggregated-health.md) son siempre un buen lugar para empezar. Son las formas más fáciles de obtener acceso a ellos a través de PowerShell o [SFX](service-fabric-visualizing-your-cluster.md). En este artículo se supone que va a depurar un clúster remoto y comprender los fundamentos de cómo usar cualquiera de estas herramientas.

##<a name="application-crash"></a>Bloqueo de la aplicación
La "partición está por debajo de recuento de réplica o instancia de destino" informe es una buena indicación de que el servicio se bloquea. Para averiguar dónde se bloquea el servicio le lleva algo más investigación. Cuando se ejecuta el servicio en escala, su mejor amigo será un conjunto de trazas bien planeada fuera.  Le recomendamos que intente [Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md) para recopilar los seguimientos y utilizar una solución como la [Búsqueda elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md) para ver y buscar los seguimientos.

![Estado de partición SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Durante la inicialización del servicio o actor
Las excepciones antes de que el tipo de servicio se ha inicializado hará que el proceso se bloquea. Para estos tipos de bloqueos, el registro de eventos de la aplicación mostrará el error de su servicio.
Estos son las excepciones más comunes para ver antes de inicializarse el servicio.

***System.IO.FileNotFoundException***

Este error suele ser debido a que faltan las dependencias de ensamblado. Compruebe la propiedad CopyLocal en Visual Studio o la caché de ensamblado global para el nodo.

***System.Runtime.InteropServices.COMException***
 *en System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Esto indica que el nombre del tipo de servicio registrados no coincide con el manifiesto del servicio.

[Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md) puede estar configurado para cargar el registro de eventos de aplicación para todos los nodos automáticamente.

###<a name="runasync-or-onactivateasync"></a>RunAsync() o OnActivateAsync()
Si el bloqueo ocurre durante la inicialización o la ejecución de su tipo de servicio registrados o actor, tela de servicio de Azure capturará la excepción. Puede ver estos de los proveedores de origen de eventos detallados en la sección "Próximos pasos".

## <a name="next-steps"></a>Pasos siguientes

Más información sobre diagnósticos existentes proporcionadas por la estructura de servicio:

* [Diagnósticos de actores confiables](service-fabric-reliable-actors-diagnostics.md)
* [Diagnósticos de servicios confiables](service-fabric-reliable-services-diagnostics.md)
