<properties
   pageTitle="Disponibilidad de los servicios de servicio tela | Microsoft Azure"
   description="Describe la detección de errores, migración tras error y recuperación para los servicios"
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

# <a name="availability-of-service-fabric-services"></a>Disponibilidad de los servicios de tela de servicio
Servicios de servicio tela Azure pueden ser con estado o sin estado. Este artículo ofrece información general sobre cómo servicio tela mantiene la disponibilidad de un servicio en caso de errores.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidad de los servicios de estado de tela de servicio
Un servicio independiente es un servicio de aplicación que no tiene ningún [estado persistente local](service-fabric-concepts-state.md).

Creación de un servicio independiente requiere la definición de una cuenta de instancia, que es el número de instancias del servicio de estado que debe estar ejecutándose en el clúster. Este es el número de copias de la lógica de aplicación que se pueden crear instancias en el clúster. Aumentar el número de instancias de la forma recomendada de escalado de un servicio de estado.

Cuando se detecta un error en cualquier instancia de un servicio independiente, se crea una nueva instancia en algún otro nodo elegible en el clúster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidad de los servicios con estado de tela de servicio
Un servicio con estado tiene algún estado asociado a ella. En tela de servicio, un servicio de estado se basa como un conjunto de réplicas. Cada réplica es una instancia del código del servicio que tiene una copia del estado. Operaciones de lectura y escritura se realizan en una réplica (denominada principal). Cambios de estado de escriben operaciones son *replicar* a varias otras réplicas (denominados secundarios activas). La combinación de principales y activas réplicas secundarias es el conjunto de réplica del servicio.

Puede ser solo una réplica principal mantenimiento leer y escribir solicitudes, pero puede haber varias réplicas secundarias activas. El número de réplicas secundarios activos es configurable y un mayor número de réplicas puede tolerar un mayor número de errores de hardware y software simultánea.

En el caso de un error (cuando la réplica principal deja de funcionar), servicio tela realiza una de las réplicas secundarias activas la nueva réplica principal. Esta réplica secundaria activada ya tiene la versión actualizada del estado (mediante *replicación*) y puede continuar procesando más lectura y las operaciones de escritura.

Este concepto--de una réplica está principal ni activo secundario: se conoce como la función de réplica.

### <a name="replica-roles"></a>Funciones de réplica
La función de una réplica se usa para administrar el ciclo de vida del estado administrando réplica. Las solicitudes de lectura de una réplica cuyo rol es servicios principales. También servicios solicitudes de escritura al actualizar su estado y replicar los cambios en los activos secundarios en su conjunto de réplica. La función de un activo secundario es recibir cambios de estado que ha replicar la réplica principal y actualizar su vista del estado.

>[AZURE.NOTE] Modelos de programación de nivel superior como [marco de actores confiable](service-fabric-reliable-actors-introduction.md) resumen mucho el concepto de rol de réplica del desarrollador.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los conceptos de tela de servicio, consulte lo siguiente:

- [Escalabilidad de servicios de tela de servicio](service-fabric-concepts-scalability.md)

- [Partición servicios estructurales de servicio](service-fabric-concepts-partitioning.md)

- [Definir y administrar el estado](service-fabric-concepts-state.md)
