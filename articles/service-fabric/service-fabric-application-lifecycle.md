<properties
   pageTitle="Ciclo de vida de aplicación de servicio tela | Microsoft Azure"
   description="Describe las pruebas, desarrollo, implementación, actualizar, mantener y quitar aplicaciones de servicio tela."
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


# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida de aplicación de servicio tela
Como con otras plataformas, una aplicación en Azure servicio tela normalmente recorre las fases siguientes: diseño, desarrollo, pruebas, implementación, actualizar, mantenimiento y eliminación. Servicio tela proporciona soporte de primera clase para el ciclo de vida de la aplicación completa de aplicaciones de la nube, desde el desarrollo hasta la implementación, administración diaria y mantenimiento para retirar final. El modelo de servicio permite varias funciones diferentes para participar de forma independiente en el ciclo de vida de la aplicación. Este artículo proporciona una descripción general de las API y cómo se usan las funciones diferentes a lo largo de las fases del ciclo de vida de aplicación de servicio tela.

## <a name="service-model-roles"></a>Funciones del modelo de servicio
Las funciones de modelo de servicio son:

- **Programador de servicio**: desarrolla servicios modulares y genéricos que pueden volver a fines y utilizar en varias aplicaciones del mismo tipo o tipos diferentes. Por ejemplo, un servicio de cola puede utilizarse para crear una aplicación de vales (departamento de soporte técnico) o una aplicación de comercio electrónico (carro de la compra).

- **Desarrollador de la aplicación**: crea aplicaciones mediante la integración de una colección de servicios para cumplir con algunos escenarios o requisitos específicos. Por ejemplo, un sitio Web de comercio electrónico podría integrar "JSON de servicio front-end sin estado", "Servicio de estado de venta" y "Servicio de estado de cola" para crear una solución auctioning.

- **Administrador de la aplicación**: toma decisiones sobre la configuración de la aplicación (rellenar los parámetros de configuración de la plantilla), la implementación (asignación de recursos disponibles) y la calidad de servicio. Por ejemplo, un administrador de aplicaciones decide la configuración regional (en inglés para los Estados Unidos) o japonés de Japón, por ejemplo de la aplicación. Una aplicación implementada diferentes puede tener distintas opciones de configuración.

- **Operador**: implementa aplicaciones basadas en la configuración de la aplicación y los requisitos especificados por el Administrador de la aplicación. Por ejemplo, un operador aprovisiona implementa la aplicación y garantiza que se ejecuta en Azure. Operadores supervisión información de estado y del rendimiento de la aplicación y mantienen la infraestructura física según sea necesario.


## <a name="develop"></a>Desarrollar
1. Un *desarrollador de servicio* desarrolla diferentes tipos de servicios mediante el modelo de programación [Actores fiable](service-fabric-reliable-actors-introduction.md) o [Servicios confiables](service-fabric-reliable-services-introduction.md) .
2. Un *desarrollador de servicio* mediante declaración describe los tipos de servicio desarrollado en un archivo de manifiestos de servicio que consta de uno o varios paquetes de código, configuración y los datos.
3. A continuación, un *desarrollador de aplicaciones* crea una aplicación con distintos tipos de servicio.
4. Un *desarrollador de la aplicación* mediante declaración describe el tipo de aplicación manifiestos de aplicación al hacer referencia a los manifiestos de servicio de servicios de constituyentes y adecuadamente reemplazar y parametrizar distintos parámetros de configuración e implementación de los servicios de constituyentes.

Para obtener ejemplos, vea [Introducción a los actores confiable](service-fabric-reliable-actors-get-started.md) y [empezar a trabajar con servicios confiables](service-fabric-reliable-services-quick-start.md) .

## <a name="deploy"></a>Implementar
1. Un *Administrador de la aplicación* se ajusta el tipo de aplicación para una aplicación específica a implementará en un clúster de servicio tela especificando los parámetros adecuados del elemento **ApplicationType** manifiesto de aplicación.

2. Un *operador de* carga el paquete de aplicación en el almacén de imágenes de clúster mediante el [método **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o el [cmdlet **ServiceFabricApplicationPackage copia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). El paquete de aplicación contiene el manifiesto de aplicación y la colección de paquetes de servicio. Servicio tela implementa aplicaciones desde el paquete de aplicación almacenados en el almacén de imágenes, que puede ser un almacenamiento de blobs de Windows Azure o el servicio del sistema servicio tela.

3. El *operador* aprovisiona, a continuación, el tipo de aplicación en el clúster de destino del paquete de aplicación cargada con el [método **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), el [cmdlet de **Registrar ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)o la [operación de resto **aprovisionar una aplicación** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Si ya dispone de la aplicación, un *operador* inicia la aplicación con los parámetros proporcionados por el *Administrador de la aplicación* utilizando el [método **CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), el [cmdlet **New-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125913.aspx)o la [operación de resto de **Crear una aplicación** ](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Después de haber implementado la aplicación, un *operador* utiliza el [método **CreateServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), el [cmdlet **New-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt125874.aspx)o la [operación de **Crear servicio** REST](https://msdn.microsoft.com/library/azure/dn707657.aspx) para crear nuevas instancias de servicio para la aplicación basándose en los tipos de servicio disponible.

6. Ahora se ejecuta la aplicación en el clúster de servicio tela.

Para obtener ejemplos, vea [implementar una aplicación](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Prueba
1. Después de implementar el clúster de desarrollo local o una prueba, un *desarrollador de servicio* ejecuta el escenario de prueba integrados de migración tras error mediante las clases [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) y [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) o el [cmdlet **Invocar ServiceFabricFailoverTestScenario** ](https://msdn.microsoft.com/library/azure/mt644783.aspx). El escenario de prueba de migración tras error ejecuta un servicio especificado por transiciones importantes y migraciones tras error para asegurarse de que es todavía disponibles y en funcionamiento.

2. El *Programador de servicio* , a continuación, se ejecuta el escenario de prueba de caos integrada con las clases [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) y [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) o el [cmdlet **Invocar ServiceFabricChaosTestScenario** ](https://msdn.microsoft.com/library/azure/mt644774.aspx). El escenario de prueba de caos induce aleatoriamente varias nodo, paquete de código y errores de réplica en el clúster.

3. *Programador de servicio* [comunicación de servicio para el servicio de pruebas](service-fabric-testability-scenarios-service-communication.md) por escenarios de prueba que mover réplicas principales alrededor del clúster de creación.

Para obtener más información, vea [Introducción al servicio de análisis de errores](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Actualizar
1. Un *desarrollador de servicio* actualiza los servicios de la aplicación instancias constituyentes o corrige errores y proporciona una nueva versión del manifiesto de servicio.

2. Un *desarrollador de la aplicación* reemplaza y parametriza la configuración de implementación y configuración de los servicios coherentes y proporciona una nueva versión del manifiesto de aplicación. A continuación, el desarrollador de la aplicación incorpora las nuevas versiones de los manifiestos de servicio en la aplicación y proporciona una nueva versión del tipo de aplicación en un paquete de aplicación actualizado.

3. Un *Administrador de la aplicación* incorpora la nueva versión del tipo de aplicación en la aplicación de destino actualizando los parámetros adecuados.

5. Un *operador* carga el paquete de aplicación actualizado en el almacén de imágenes de clúster utilizando el [método **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o el [cmdlet **ServiceFabricApplicationPackage copia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). El paquete de aplicación contiene el manifiesto de aplicación y la colección de paquetes de servicio.

6. Un *operador* aprovisiona la nueva versión de la aplicación en el clúster de destino utilizando el [método **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), el [cmdlet de **Registrar ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)o la [operación de resto **aprovisionar una aplicación** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Un *operador* actualiza la aplicación de destino a la nueva versión con el [método **UpgradeApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), el [cmdlet **ServiceFabricApplicationUpgrade de inicio** ](https://msdn.microsoft.com/library/azure/mt125975.aspx)o la [operación de resto **actualizar una aplicación** ](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Un *operador* comprueba el progreso de actualización con el [método **GetApplicationUpgradeProgressAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), el [cmdlet **Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx)o la [operación de resto **Obtener progreso de la actualización de la aplicación** ](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Si es necesario, el *operador* modifica y vuelve a aplicar los parámetros de la actualización de la aplicación actual con el [método **UpdateApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), el [cmdlet **ServiceFabricApplicationUpgrade actualización** ](https://msdn.microsoft.com/library/azure/mt126030.aspx)o la [operación de **Actualización de la aplicación en la actualización** resto](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Si es necesario, el *operador* deshace la actualización de la aplicación actual con el [método **RollbackApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), el [cmdlet **ServiceFabricApplicationRollback de inicio** ](https://msdn.microsoft.com/library/azure/mt125833.aspx)o la [operación de **Deshacer aplicación actualizar** el resto](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Servicio tela actualiza la aplicación de destino que se ejecutan en el clúster sin perder la disponibilidad de cualquiera de sus servicios constituyentes.

Consulte el [tutorial de actualización de aplicación](service-fabric-application-upgrade-tutorial.md) para obtener ejemplos.

## <a name="maintain"></a>Mantener
1. Para actualizar el sistema operativo y revisiones, servicio tela interactúa con la infraestructura de Azure para garantizar la disponibilidad de todas las aplicaciones que se ejecutan en el clúster.

2. Actualizaciones y revisiones de la plataforma de servicio tela, tela de servicio se actualiza sin perder la disponibilidad de cualquiera de las aplicaciones que se ejecutan en el clúster.

3. Un *Administrador de la aplicación* aprueba la adición o eliminación de nodos en un clúster después de analizar datos de uso de la capacidad histórica y proyectado demanda futura.

4. Un *operador* agrega y quita nodos especificados por el *Administrador de la aplicación*.

5. Cuando se agregan nuevos nodos o se quitan los nodos existentes del clúster, servicio tela automáticamente equilibra la carga de las aplicaciones en ejecución en todos los nodos en el clúster para obtener un rendimiento óptimo.

## <a name="remove"></a>Quitar
1. Un *operador* puede eliminar una instancia específica de un servicio en ejecución en el clúster sin eliminar toda la aplicación utilizando el [método **DeleteServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), el [cmdlet **Quitar ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt126033.aspx)o la [operación de **Eliminar servicio** REST](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. También puede eliminar un *operador de* una instancia de la aplicación y todos sus servicios con el [método **DeleteApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), el [cmdlet **Quitar ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125914.aspx)o la [operación de resto de **Eliminar una aplicación** ](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Una vez que se han detenido las aplicaciones y los servicios, el *operador* puede deshacer el aprovisionamiento del tipo de aplicación utilizando el [método **UnprovisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), el [cmdlet de **Anular ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125885.aspx)o la [operación de resto **no aprovisionar una aplicación** ](https://msdn.microsoft.com/library/azure/dn707671.aspx). Anular el tipo de aplicación en línea, no se elimina el paquete de aplicación de la ImageStore. Debe quitar manualmente el paquete de aplicación.

4. Un *operador* quita el paquete de aplicación de la ImageStore utilizando el [método **RemoveApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) o el [cmdlet **Quitar ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Para obtener ejemplos, vea [implementar una aplicación](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo desarrollar, probar y administrar aplicaciones de servicio tela y servicios, consulte:

- [Actores confiables](service-fabric-reliable-actors-introduction.md)
- [Servicios confiables](service-fabric-reliable-services-introduction.md)
- [Implementar una aplicación](service-fabric-deploy-remove-applications.md)
- [Actualización de la aplicación](service-fabric-application-upgrade.md)
- [Información general de la capacidad de prueba](service-fabric-testability-overview.md)
- [Ejemplo de aplicación basada en el resto del ciclo de vida](service-fabric-rest-based-application-lifecycle-sample.md)
