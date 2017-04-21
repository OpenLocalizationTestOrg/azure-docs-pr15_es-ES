<properties
   pageTitle="Agregar o quitar nodos a un clúster de servicio tela independiente | Microsoft Azure"
   description="Obtenga información sobre cómo agregar o quitar nodos a un clúster de tela de servicio de Azure en una máquina física o virtual ejecutan Windows Server, que puede ser local o en cualquier nube."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Agregar o quitar nodos a un clúster de tela de servicio independiente en Windows Server

Una vez [creado el clúster de servicio tela independiente en equipos con Windows Server](service-fabric-cluster-creation-for-windows-server.md), pueden cambiar sus necesidades empresariales para que necesite agregar o quitar varios nodos al clúster. Este artículo proporciona los pasos detallados para lograr este objetivo.


## <a name="add-nodes-to-your-cluster"></a>Agregar nodos al clúster

1. Preparar la VM u otro equipo que desea agregar al clúster siguiendo los pasos que se mencionan en la sección [preparar los equipos para cumplir con los requisitos previos para la implementación de clúster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planear qué dominio de error y actualización de dominio que va a agregar esta VM/máquina.
3. Escritorio remoto (RDP) en la máquina virtual o de la máquina que desea agregar al clúster.
4. Copiar o [Descargue el paquete independiente para tela de servicio de Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) en este equipo de VM y descomprima el paquete.
5. Ejecutar Powershell como administrador y desplácese hasta la ubicación del paquete descomprimido.
6. Ejecutar Powershell *AddNode.ps1* con los parámetros que describe el nodo de nuevo para agregar. El ejemplo siguiente agrega un nuevo nodo llamado VM5, con un tipo NodeType0, la dirección IP 182.17.34.52 en UD1 y FD1. El *ExistingClusterConnectionEndPoint* es un extremo de conexión de un nodo está en el clúster existente. Para este extremo, puede elegir la dirección IP de *cualquier* nodo del clúster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Quitar nodos de clúster

1. Según el nivel de fiabilidad elegido para el clúster, no se pueden quitar los primeros nodos (3/5/7/9) n del tipo de nodo principal
2. Ejecutar comando RemoveNode en un clúster de desarrollo no es compatible.
2. Escritorio remoto (RDP) en la máquina virtual o de la máquina que desea quitar del clúster.
2. Copiar o [Descargue el paquete independiente para tela de servicio de Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprima el paquete de este equipo de máquina virtual.
3. Ejecutar Powershell como administrador y desplácese hasta la ubicación del paquete descomprimido.
4. Ejecutar *RemoveNode.ps1* en PowerShell. El ejemplo siguiente quita el nodo actual del clúster. El *ExistingClientConnectionEndpoint* es un extremo de la conexión de cliente para cualquier nodo que permanecerá en el clúster. Elija la dirección IP y el puerto de extremo de *cualquier* **otro nodo** del clúster. Este **otro nodo** por separado se actualizará la configuración del clúster para el nodo eliminado. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Tenga en cuenta que incluso después de quitar un nodo, puede que aparezca como hacia abajo en las consultas y SFX. Se trata de un defecto conocido y se solucionará en una versión próxima. 


## <a name="next-steps"></a>Pasos siguientes
- [Opciones de configuración de clúster de Windows independiente](service-fabric-cluster-manifest.md)
- [Proteger un clúster independiente en Windows con la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger un clúster independiente en Windows con X509 certificados](service-fabric-windows-cluster-x509-security.md)
- [Crear un clúster de tela de servicio independiente con máquinas virtuales de Azure con Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
