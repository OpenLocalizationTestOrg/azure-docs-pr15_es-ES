<properties
   pageTitle="Configurar el clúster independiente | Microsoft Azure"
   description="En este artículo se describe cómo configurar su clúster de servicio tela privado o independiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Opciones de configuración de clúster de Windows independiente

En este artículo se describe cómo configurar un clúster de tela de servicio independiente con el archivo _**ClusterConfig.JSON**_ . Puede usar este archivo para especificar información como los nodos de tela de servicio y sus direcciones IP, los distintos tipos de nodos en el clúster, las configuraciones de seguridad, así como la topología de red en términos de errores o actualizar dominios, para el clúster independiente.

Cuando [Descargue el paquete de tela de servicio independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), algunos ejemplos del archivo ClusterConfig.JSON se descargan en el equipo de trabajo. Los ejemplos que tengan *DevCluster* en sus nombres le ayudará a crear un clúster con los tres nodos en el mismo equipo, como los nodos lógicos. Fuera de ellas, al menos un nodo debe marcarse como un nodo principal. Este clúster es útil para un entorno de desarrollo o de prueba y no se admite como un clúster de producción. Los ejemplos que tengan *MultiMachine* en sus nombres, le ayudará a crear un clúster de calidad de producción con cada nodo en un equipo distinto. El número de nodos principales para estos clúster dependerá del [nivel de confiabilidad](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* y *ClusterConfig.Unsecure.MultiMachine.JSON* muestran cómo crear un clúster de producción o de prueba no seguro respectivamente. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* y *ClusterConfig.Windows.MultiMachine.JSON* muestran cómo crear clúster de producción o de prueba, protegida mediante la [seguridad de Windows](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* y *ClusterConfig.X509.MultiMachine.JSON* muestran cómo crear clúster de producción o de prueba, protegida mediante [X509 la seguridad basada en el certificado](service-fabric-windows-cluster-x509-security.md). 


Ahora se le examinar las distintas secciones de un archivo de _**ClusterConfig.JSON**_ como debajo.

## <a name="general-cluster-configurations"></a>Configuraciones de clúster general
Se tratan las configuraciones de clúster amplia específicas, como se muestra en el siguiente fragmento de JSON.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Puede dar cualquier nombre descriptivo para el clúster de servicio tela asignando a la variable de **nombre** . El **clusterConfigurationVersion** es el número de versión del clúster; se debe aumentar cada vez actualiza el clúster de servicio tela. Sin embargo, debe dejar el **apiVersion** al valor predeterminado.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nodos en el clúster
Puede configurar los nodos en el clúster de servicio tela mediante el uso de la sección de **nodos** , como se muestra en el fragmento de código siguiente.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un clúster de tela de servicio debe contener al menos 3 nodos. Puede agregar más nodos a esta sección según su configuración. En la tabla siguiente se explica la configuración de cada nodo.

|**Configuración de nodo**|**Descripción**|
|-----------------------|--------------------------|
|nombreDeNodo|Puede dar cualquier nombre descriptivo para el nodo.|
|dirección IP|Averiguar la dirección IP de su nodo abriendo una ventana de comandos y escribiendo `ipconfig`. Anote la dirección IPV4 y asignar a la variable de **dirección IP** .|
|nodeTypeRef|Puede asignar a cada nodo un tipo de nodo diferente. Los [tipos de nodo](#nodetypes) se definen en la sección siguiente.|
|faultDomain|Dominios de error permiten a los administradores de clúster definir los nodos físicos que pueden producir un error al mismo tiempo debido a las dependencias físicos compartidos.|
|upgradeDomain|Actualización dominios describen conjuntos de nodos que se cierran para actualizaciones de servicio tela en aproximadamente al mismo tiempo. Puede elegir los nodos que desea asignar a los dominios de actualización, mientras que no están limitados por los requisitos físicos.| 


## <a name="cluster-properties"></a>Propiedades de clúster

La sección **Propiedades** en el ClusterConfig.JSON se usa para configurar el clúster de manera.

<a id="reliability"></a>
### <a name="reliability"></a>Confiabilidad 
La sección **reliabilityLevel** define el número de copias de los servicios del sistema que se pueden ejecutar en los nodos del clúster principales. Esto aumenta la confiabilidad de estos servicios y por tanto el clúster. Puede establecer esta variable de *Bronce*, *plata*, *Gold* o *Platinum* para las copias de 3, 5, 7 o 9 de estos servicios respectivamente. Vea un ejemplo a continuación.

    "reliabilityLevel": "Bronze",
    
Tenga en cuenta que dado que el nodo principal ejecuta una única copia de los servicios del sistema, necesitará un mínimo de 3 nodos principales para *Bronze*, 5 para *plata*7 de *oro* y 9 para los niveles de confiabilidad de *Platinum* .


### <a name="diagnostics"></a>Diagnósticos
La sección **diagnosticsStore** le permite configurar parámetros para habilitar diagnóstico y solución de problemas de nodo o el clúster, como se muestra en el fragmento de código siguiente. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Los **metadatos** es una descripción de los diagnósticos de clúster y se pueden establecer según su configuración. Estas variables de ayuda en la recopilación de ETW registros de seguimiento, volcados, así como contadores de rendimiento. Leer [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) y [Seguimiento de ETW](https://msdn.microsoft.com/library/ms751538.aspx) para obtener más información sobre los registros de seguimiento ETW. Todos los registros incluidos [volcados de sucesos](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) y [contadores de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) se pueden dirigir a la carpeta de la **cadena de conexión** en el equipo. También puede usar *AzureStorage* para almacenar diagnósticos. Siga leyendo para un fragmento de código de ejemplo.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Seguridad 
La sección **seguridad** es necesaria para un clúster de servicio tela seguro independiente. El fragmento de código siguiente muestra una parte de esta sección.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Los **metadatos** es una descripción de su clúster segura y se pueden establecer según su configuración. La **ClusterCredentialType** y **ServerCredentialType** determinan el tipo de seguridad que se implementará el clúster y los nodos. Se pueden establecer para cualquier *X509* de un valor bursátil basada en certificados o *Windows* para la seguridad basada en Azure Active Directory. El resto de la sección de **seguridad** se se basa en el tipo de la seguridad. Lea la [seguridad basada en certificados en un clúster independiente](service-fabric-windows-cluster-x509-security.md) o la [seguridad de Windows en un clúster independiente](service-fabric-windows-cluster-windows-security.md) para obtener información sobre cómo rellenar el resto de la sección **seguridad** .


<a id="nodetypes"></a>
### <a name="node-types"></a>Tipos de nodo
La sección **nodeTypes** describe el tipo de los nodos que tiene el clúster. Debe especificar el tipo de al menos un nodo en un clúster, como se muestra en el fragmento de código siguiente. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

El **nombre** es el nombre descriptivo para este tipo de nodo concreto. Para crear un nodo de este tipo de nodo, asignar su nombre descriptivo a la variable **nodeTypeRef** para ese nodo como mencionados [arriba](#clusternodes). Para cada tipo de nodo, defina los extremos de la conexión que se usará. Puede elegir cualquier número de puerto para estos extremos de la conexión, siempre y cuando no entran en conflicto con otros extremos de este clúster. En un clúster de varios nodos, habrá uno o más nodos principales (es decir, **isPrimary** establecida en *true*), dependiendo de la [**reliabilityLevel**](#reliability). Lea las [Consideraciones de planeación de la capacidad de clúster de tela de servicio](service-fabric-cluster-capacity.md) para obtener información sobre valores **nodeTypes** y **reliabilityLevel** y sepa cuáles son principales y los tipos de nodo no principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Extremos que se usa para configurar los tipos de nodo

- *clientConnectionEndpointPort* es el puerto que utiliza el cliente para conectarse al clúster, cuando se usa la API de cliente. 
- *clusterConnectionEndpointPort* es el puerto en el que los nodos comunican entre sí.
- *leaseDriverEndpointPort* es el puerto que se utiliza el controlador de concesión de clúster para averiguar si los nodos están aún activos. 
- *serviceConnectionEndpointPort* es el puerto utilizado por las aplicaciones y servicios implementados en un nodo, para comunicarse con el cliente de servicio tela en ese nodo concreto.
- *httpGatewayEndpointPort* es el puerto que se utiliza el Explorador de tela de servicio para conectarse al clúster.
- *ephemeralPorts* son los [puertos dinámicos que utiliza el sistema operativo](https://support.microsoft.com/kb/929851). Servicio tela usará un elemento como puertos de la aplicación y el restante estará disponible para el sistema operativo. También se asignará este intervalo al rango existente presentes en el sistema operativo, para poder usar los intervalos en los archivos de ejemplo JSON para todos los fines. Debe asegurarse de que la diferencia entre el inicio y los puertos de finalización es al menos de 255. 
- *applicationPorts* son los puertos que va a utilizar las aplicaciones de servicio tela. Deben ser un subconjunto de la *ephemeralPorts*, suficiente para cubrir el requisito de punto final de las aplicaciones. Servicio tela usará estos siempre nuevos puertos necesarios, así como ocupan de abrir el firewall para estos puertos. 
- *reverseProxyEndpointPort* es un extremo proxy inverso opcional. Para obtener más detalles, vea [Servicio tela invertir Proxy](service-fabric-reverseproxy.md) . 


### <a name="other-settings"></a>Otras opciones de configuración
La sección **fabricSettings** permite establecer los directorios raíz para los datos de tela de servicio y los registros. Puede personalizar estos solo durante la creación de clúster inicial. Siga leyendo para un fragmento de código de ejemplo de esta sección.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Se recomienda utilizando una unidad no OS como FabricDataRoot y FabricLogRoot ya que proporciona que más confiabilidad contra OS se bloquea. Tenga en cuenta que si personaliza la raíz de datos, a continuación, la raíz de registro se colocarán un nivel por debajo de la raíz de datos.


## <a name="next-steps"></a>Pasos siguientes

Una vez que tenga un archivo completo de ClusterConfig.JSON configurado según la configuración de clúster independiente, puede implementar el clúster siguiendo el artículo [crear un clúster de Azure servicio tela local o en la nube](service-fabric-cluster-creation-for-windows-server.md) y, a continuación, vaya al [visualizar el clúster con el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md).


