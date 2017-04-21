<properties
   pageTitle="Servicio Proxy inverso de tela | Microsoft Azure"
   description="Usar a proxy inverso del servicio tela para la comunicación con microservices desde dentro y fuera del clúster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Proxy inverso de tela de servicio

El servidor proxy inverso de tela del servicio es una estructura de servicio integrado en proxy inverso que permite escribir direcciones microservices en el clúster de tela de servicio que exponen extremos HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicación Microservices

Normalmente, Microservices en tela servicio ejecutar en un subconjunto de VM en el clúster y puede mover de una VM a otro por varios motivos. Para que los extremos de microservices pueden cambiar dinámicamente. El patrón típico para comunicarse con el microservice es el bucle de resolver siguiente,

1. Resolver la ubicación del servicio inicialmente mediante el servicio de nombres.
2. Conectar con el servicio.
3. Determinar la causa de errores de conexión y volver a resolver la ubicación del servicio cuando sea necesario.

Este proceso incluye, en general, ajuste las bibliotecas de comunicación de cliente de un bucle de reintento que implementa las directivas del servicio de resolución y vuelva a intentarlo.
Para obtener más información sobre este tema, vea la [comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Comunicar a través de proxy inverso de CF
Tela de servicio proxy inverso se ejecuta en todos los nodos en el clúster. Le lleva a cabo el proceso de resolución de todo el servicio en nombre del cliente y reenvía la solicitud de cliente. Así que los clientes que se ejecutan en el clúster solo pueden usar las bibliotecas de comunicación de HTTP del cliente para comunicarse con el servicio de destino a través del proxy inverso SF ejecuta localmente en el mismo nodo.

![Comunicación interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Alcanzar Microservices desde fuera del clúster
El modelo de comunicación externa predeterminado para microservices es **participar en** donde no se puede tener acceso a cada servicio de forma predeterminada directamente desde clientes externos. El [Equilibrador de carga de Azure](../load-balancer/load-balancer-overview.md) es un límite de red entre microservices y clientes externos, que realiza la traducción de direcciones de red y reenvía las solicitudes externas a interno **IP: Port** extremos. Para poder realizar el punto final de una microservice directamente accesible a clientes externos, primero debe configurarse el equilibrador de carga de Azure reenvíe el tráfico a cada puerto usado por el servicio en el clúster. Además, la mayoría microservices (ESP. con estado microservices) no live en todos los nodos del clúster y que se pueden desplazar entre los nodos de migración tras error, por lo que en estos casos, el equilibrador de carga de Azure eficaz no puede determinar el nodo de destino de las réplicas se encuentran para reenviar el tráfico a.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Comunique con Microservices por el proxy inverso SF desde fuera del clúster

En lugar de configurar los puertos del servicio individuales en el equilibrador de carga de azure, solo el puerto de proxy inverso SF se puede configurar en el equilibrador de carga de Azure. Permite a los clientes fuera del clúster llegar a los servicios en el clúster mediante el servidor proxy inverso sin un configuraciones adicionales.

![Comunicación externa][0]

>[AZURE.WARNING] Configurar el puerto del servidor proxy inverso de equilibrador de carga, hace que todos los servicios microempresas en el clúster que exponer un extremo http, se addressible desde fuera del clúster.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Formato URI para el direccionamiento servicios a través del proxy inverso

El servidor proxy inverso utiliza un formato URI específico para identificar qué partición de servicio se debería enviar la solicitud entrante:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http (s):** El servidor proxy inverso se puede configurar para que acepte tráfico HTTP o HTTPS. En caso de tráfico HTTPS, terminación SSL se produce en el servidor proxy inverso. Las solicitudes que reenvían el servidor proxy inverso a servicios en el clúster están sobre http.
 - **Clúster FQDN | IP interna:** Para los clientes externos, el servidor proxy inverso se puede configurar para que sea accesible a través del dominio de clúster (por ejemplo, mycluster.eastus.cloudapp.azure.com). De forma predeterminada, que el servidor proxy inverso se ejecuta en todos los nodos, así que para el tráfico interno se puede alcanzar en el host local o en cualquier dirección IP de nodo interno (por ejemplo, 10.0.0.1).
 - **Puerto:** El puerto que se haya especificado para el servidor proxy inverso. Por ejemplo: 19008.
 - **ServiceInstanceName:** Este es el nombre de la instancia de servicio implementado completo del servicio está intentando alcanzar la "tela: /" combinación. Por ejemplo, para llegar a servicio *tela: / myapp/myservice/*, usaría *myapp/myservice*.
 - **Ruta de acceso de sufijo:** Esta es la dirección URL real para el servicio que desea conectarse. Por ejemplo, *myapi o valores o agregar/3*
 - **PartitionKey:** Para un servicio dividido, esta es la clave de partición calculada de la partición que desee alcanzar. Tenga en cuenta que esto *no* la partición GUID de ID. Este parámetro no es necesario para los servicios con el esquema de partición singleton.
 - **PartitionKind:** El esquema de partición de servicio. Puede ser 'Int64Range' o 'Con nombre'. Este parámetro no es necesario para los servicios con el esquema de partición singleton.
 - **Tiempo de espera:**  Especifica el tiempo de espera de la solicitud de http creada por el servidor proxy inverso para el servicio en nombre de la solicitud del cliente. El valor predeterminado es 60 segundos. Se trata de un parámetro opcional.

### <a name="example-usage"></a>Ejemplo de uso

Por ejemplo, echemos servicio **tela: / MyApp/MyService** que abre una escucha HTTP en la siguiente URL:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Con los siguientes recursos:

 - `/index.html`
 - `/api/users/<userId>`

Si el servicio utiliza el esquema de particiones singleton, los parámetros de cadena de consulta *PartitionKey* y *PartitionKind* no son necesarios y el servicio se puede llegar a través de la puerta de enlace como:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - Internamente:`http://localhost:19008/MyApp/MyService`

Si el servicio utiliza el esquema de particiones Int64 uniforme, los parámetros de cadena de consulta *PartitionKey* y *PartitionKind* deben usarse para llegar a una partición del servicio:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para llegar a los recursos expuestos por el servicio, simplemente colocar la ruta de acceso de recurso después del nombre de servicio en la dirección URL:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

La puerta de enlace, a continuación, reenvía esas solicitudes a la dirección URL del servicio:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamiento especial para uso compartido de puertos servicios

La puerta de enlace de aplicación intenta volver a resolver una dirección de servicio y vuelva a intentar la solicitud cuando no se puede llegar un servicio. Esta es una de las ventajas principales de la puerta de enlace, como código de cliente no es necesario implementar su propia resolución de servicio y resolver bucle.

Generalmente cuando no se puede llegar un servicio significa que la instancia de servicio o la réplica ha movido a un nodo diferente como parte de su ciclo de vida normal. En este caso, la puerta de enlace puede recibir un error de conexión de red que indica que un extremo ya no está abierto en la dirección originalmente resuelta.

Sin embargo, réplicas o instancias de servicio, puede compartir un proceso host y también puede compartir un puerto cuando se hospeda en un servidor web basado en http.sys, incluidos:

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [WebListener principales de ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

En esta situación es probable que el servidor web está disponible en el proceso host y responder a solicitudes pero ya no está disponible en el host de la instancia de servicio resuelto o réplica. En este caso, la puerta de enlace recibirán una respuesta HTTP 404 desde el servidor web. Como resultado, HTTP 404 tiene dos significados distintos:

 1. La dirección del servicio es correcta, pero el recurso solicitado por el usuario no existe.
 2. La dirección del servicio es incorrecta, y el recurso solicitado por el usuario realmente exista en un nodo diferente.

En el primer caso, se trata de un error 404 de HTTP normal, que se considera un error de usuario. Sin embargo, en el segundo caso, el usuario ha solicitado un recurso que existen, pero la puerta de enlace no pudo encontrarlo porque se ha movido el propio servicio, en cuyo caso la puerta de enlace debe volver a resolver la dirección e inténtelo de nuevo.

La puerta de enlace, por tanto, necesita una manera de distinguir entre estos dos casos. Para poder realizar dicha distinción, se requiere una sugerencia desde el servidor.

 - De forma predeterminada, la puerta de enlace de aplicación supone caso #2 e intenta volver a resolver y volver a emitir la solicitud.
 - Para indicar caso #1 a la puerta de enlace de aplicación, el servicio debe devolver el encabezado de respuesta HTTP siguiente:

`X-ServiceFabric : ResourceNotFound`

Este encabezado de respuesta HTTP indica una situación de HTTP 404 normal en la que el recurso solicitado no existe y la puerta de enlace no intentará volver a resolver la dirección del servicio.

## <a name="setup-and-configuration"></a>Instalación y configuración
El proxy inverso de tela de servicio puede estar habilitado para el clúster a través de la [plantilla de administrador de recursos de Azure](./service-fabric-cluster-creation-via-arm.md).

Una vez que tenga la plantilla para el clúster que desea distribuir (de las plantillas de ejemplo o mediante la creación de una plantilla de administrador de recursos personalizados) el proxy inverso puede habilitarse en la plantilla mediante los siguientes pasos.

1. Definir un puerto del servidor proxy inverso en la [sección de parámetros](../resource-group-authoring-templates.md) de la plantilla.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especificar el puerto para cada uno de los objetos de nodetype en la [sección tipo de recurso](../resource-group-authoring-templates.md) de **clúster**

    Para apiVersion antes ' 2016-09-01' el puerto se identifica por el parámetro nombre ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    Para apiVersion en o tras ' 2016-09-01' el puerto se identifica por el parámetro nombre ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Dirección del proxy inverso desde fuera del clúster de azure, configurar las **reglas del equilibrador de carga de azure** para el puerto especificado en el paso 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Para configurar certificados SSL en el puerto del servidor proxy inverso, agregar el certificado de la propiedad httpApplicationGatewayCertificate en la [sección tipo de recurso](../resource-group-authoring-templates.md) de **clúster**

    Para apiVersion antes ' 2016-09-01' el certificado se identifica por el parámetro nombre ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Para apiVersion en o tras ' 2016-09-01' el certificado se identifica por el parámetro nombre ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Pasos siguientes
 - Vea un ejemplo de comunicación de HTTP entre los servicios de un [proyecto de ejemplo en GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Llamadas a procedimiento remoto con acceso remoto servicios confiables](service-fabric-reliable-services-communication-remoting.md)

 - [Web API que usa OWIN en servicios confiables](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicación de WCF mediante servicios confiable](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
