<properties
   pageTitle="Pasos siguientes de creación de proyecto de tela de servicio | Microsoft Azure"
   description="Este artículo contiene vínculos a un conjunto de tareas de desarrollo principales para tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>La aplicación de servicio tela y los pasos siguientes
Se ha creado la aplicación de Azure servicio tela. Este artículo describe la estructura de su proyecto y algunos pasos posibles.

## <a name="your-application"></a>La aplicación
Cada nueva aplicación incluye un proyecto de aplicación. Es posible que uno o dos proyectos adicionales, según el tipo de servicio elegido.

### <a name="the-application-project"></a>El proyecto de aplicación
El proyecto de aplicación consiste en:

- Un conjunto de referencias a los servicios que componen la aplicación.

- Dos publicación perfiles (Local y nube) que puede usar para mantener las preferencias para trabajar con diferentes entornos, como las preferencias relacionadas con un extremo de clúster y si va a realizar la implementación de actualizaciones de forma predeterminada.

- Dos parámetros archivos (Local y nube) que puede usar para mantener las configuraciones de aplicación específicas del entorno, como el número de particiones para crear un servicio.

- Un script de implementación que puede utilizar para implementar la aplicación desde la línea de comandos o como parte de una canalización de integración e implementación continua automatizada.

- El manifiesto de aplicación, que describe la aplicación. Puede encontrar el manifiesto en la carpeta ApplicationPackageRoot.

### <a name="stateless-service"></a>Servicio de estado
Al agregar un nuevo servicio independiente, Visual Studio agrega un proyecto de servicio para la solución que incluya un tipo descendientes del `StatelessService`. El servicio incrementa una variable local en un contador.

### <a name="stateful-service"></a>Servicio de estado
Al agregar un nuevo servicio con estado, Visual Studio agrega un proyecto de servicio para la solución que incluya un tipo descendientes del `StatefulService`. El servicio incrementa un contador en su `RunAsync` método y almacena el resultado en una `ReliableDictionary`.

### <a name="actor-service"></a>Servicio de actor
Cuando agrega un nuevo actor confiable, Visual Studio agrega dos proyectos a la solución: un proyecto de actor y una interfaz.

El proyecto de actor proporciona métodos para la configuración y obtener el valor de un contador que se conserva confiable dentro de estado de actor. El proyecto de interfaz ofrece una interfaz que pueden utilizar otros servicios para invocar actor.

### <a name="stateless-web-api"></a>API de Web sin estado
El proyecto de Web API sin estado proporciona un servicio web básicas que puede usar para abrir la aplicación a los clientes externos. Para obtener más información sobre cómo el proyecto estructurado, vea [Servicios de API del servicio Web de tela con OWIN automática de hospedaje](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>Núcleo ASP.NET

El SDK de tela de servicio proporciona el mismo conjunto de ASP.NET Core plantillas que están disponibles para independiente proyectos principales de ASP.NET: vaciar [Web API][aspnet-webapi]y la [Aplicación Web][aspnet-webapp].

## <a name="next-steps"></a>Pasos siguientes
### <a name="create-an-azure-cluster"></a>Crear un clúster de Azure
El SDK de tela de servicio proporciona un clúster local para desarrollo y pruebas. Para crear un clúster de Azure, vea [configurar un clúster de tela de servicio desde el portal de Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Pruebe la implementación en Azure de forma gratuita con clústeres de fiesta

Si desea probar la implementación y administración de aplicaciones en Azure sin necesidad de configurar sus propios clústeres, puede usar el [servicio de clúster de terceros](http://aka.ms/tryservicefabric)de gratuita.

### <a name="publish-your-application-to-azure"></a>Publicar su aplicación en Azure
Puede publicar la aplicación directamente desde Visual Studio a un clúster de Azure. Para obtener información, consulte [publicar su aplicación en Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Utilice el Explorador de tela de servicio para visualizar el clúster
Servicio tela Explorer ofrece una forma fácil de visualizar el clúster, incluyendo aplicaciones implementadas y diseño físico. Para obtener más información, consulte [visualizar el clúster mediante el Explorador de servicio tela][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Versión y actualizar sus servicios
Tela de servicio permite crear versiones independientes y la actualización de servicios independientes en una aplicación. Para obtener más información, consulte [actualizar los servicios y control de versiones][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurar la integración continua con Visual Studio Team Services
Para obtener información sobre cómo pueden configurar un proceso de integración continua para la aplicación de servicio tela, vea [Configurar la integración continua con Visual Studio Team Services][ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
