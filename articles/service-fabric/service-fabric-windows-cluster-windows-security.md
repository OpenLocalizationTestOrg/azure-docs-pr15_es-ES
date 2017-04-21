<properties
   pageTitle="Proteger un clúster de ejecución en Windows con la seguridad de Windows | Microsoft Azure"
   description="Obtenga información sobre cómo configurar la seguridad de nodo a nodo y nodo de cliente en un clúster independiente en Windows con la seguridad de Windows."
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


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Proteger un clúster independiente en Windows con la seguridad de Windows

Para evitar el acceso no autorizado a un clúster de tela de servicio debe seguro, especialmente cuando tenga las cargas de trabajo de producción ejecutando en él. En este artículo se describe cómo configurar la seguridad de nodo a nodo y nodo de cliente con seguridad de Windows en el archivo *ClusterConfig.JSON* y se corresponde con el paso de seguridad configurar de [crear un clúster independiente en ejecución en Windows](service-fabric-cluster-creation-for-windows-server.md). Para obtener más información sobre cómo tela servicio utiliza la seguridad de Windows, vea [escenarios de seguridad de clúster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Debe tener en cuenta la selección de seguridad para la seguridad de nodo a otro con cuidado, ya que no hay ninguna actualización de clúster de opciones de seguridad de uno a otro. Cambiar la selección de seguridad, se requeriría una regeneración de clúster completa.

## <a name="configure-windows-security"></a>Configurar la seguridad de Windows
Descarga el archivo de configuración de *ClusterConfig.Windows.JSON* de ejemplo con la [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) paquete de clúster independiente contiene una plantilla para configurar la seguridad de Windows.  Seguridad de Windows se configura en la sección **Propiedades** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Valor de configuración**|**Descripción**|
|-----------------------|--------------------------|
|ClusterCredentialType|Seguridad de Windows está habilitada estableciendo el parámetro **ClusterCredentialType** en *Windows*.|
|ServerCredentialType|Seguridad de Windows para clientes está habilitada estableciendo el parámetro **ServerCredentialType** en *Windows*. Esto indica que se están ejecutando los clientes del clúster y el clúster propiamente dicho, dentro de un dominio de Active Directory.|
|WindowsIdentities|Contiene las identidades de clúster y del cliente.|
|ClusterIdentity|Configura la seguridad de nodo a otro. Una lista de valores separados por comas del grupo administra cuentas de servicio o nombres de equipo.|
|ClientIdentities|Configura la seguridad de nodo de cliente. Matriz de cuentas de usuario del cliente.|
|Identidad|La identidad del cliente, un usuario de dominio.|
|IsAdmin|True especifica que el usuario de dominio tiene acceso de cliente de administrador, false para el acceso de cliente del usuario.|

[Nodo de seguridad de nodo](service-fabric-cluster-security.md#node-to-node-security) está configurado por valor usando **ClusterIdentity**. Para crear relaciones de confianza entre los nodos, debe ser conscientes de entre sí. Esto se puede conseguir de dos maneras diferentes: especifique el grupo de cuenta de servicio administrada que incluye todos los nodos en el clúster o las identidades de nodo de dominio de todos los nodos en el clúster. Se recomienda usar el enfoque de [Cuenta de servicio administrada de grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , especialmente para clústeres más grandes (más de 10 nodos) o para clústeres que pueden aumentar o reducir.
Este enfoque permite nodos pueden agregar o quitar de la gMSA, sin necesidad de cambios en el manifiesto de clúster. Este enfoque no requiere la creación de un grupo de dominio para que los administradores de clústeres conceden derechos de acceso para agregar y quitar a miembros. Para obtener más información, vea [Introducción a las cuentas de servicio administradas de grupo](http://technet.microsoft.com/library/jj128431.aspx).

[Cliente de seguridad de nodo](service-fabric-cluster-security.md#client-to-node-security) está configurado con **ClientIdentities**. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para saber a qué cliente identidades que puede confiar en. Esto puede hacerse de dos maneras diferentes: especifique los usuarios del grupo de dominio que se pueden conectar o especificar los usuarios de nodo de dominio que se pueden conectar. Tela de servicio es compatible con dos tipos de control de acceso diferente para los clientes que están conectados a un clúster de tela de servicio: administrador y usuario. Control de acceso proporciona la capacidad para el Administrador de clústeres limitar el acceso a determinados tipos de operaciones de clúster para los diferentes grupos de usuarios, hacer que el clúster sea más segura.  Los administradores tienen acceso completo a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen sólo acceso de lectura a las capacidades de administración (por ejemplo, capacidades de consulta) y la capacidad para resolver las aplicaciones y servicios.

La siguiente sección **seguridad** de ejemplo configura la seguridad de Windows y especifica que los equipos de *ServiceFabric/clusterA.contoso.com* forman parte del clúster y que *CONTOSO\usera* tenga acceso de cliente de administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Pasos siguientes

Después de configurar la seguridad de Windows en el archivo *ClusterConfig.JSON* , reanudar el proceso de creación de clúster en [crear un clúster independiente que se ejecute en Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obtener más información acerca de cómo seguridad de nodo a otro, seguridad de nodo de cliente y control de acceso basado en roles, vea [escenarios de seguridad de clúster](service-fabric-cluster-security.md).

Para obtener ejemplos de conexión mediante PowerShell o FabricClient, vea [conectarse a un clúster seguro](service-fabric-connect-to-secure-cluster.md) .
