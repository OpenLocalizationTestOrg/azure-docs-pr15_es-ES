<properties
   pageTitle="Crear un clúster independiente con máquinas virtuales de Azure ejecuta Windows | Microsoft Azure"
   description="Aprenda a crear y administrar un clúster de tela de servicio de Azure en Azure máquinas virtuales que ejecutan Windows Server."
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
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Crear un clúster de servicio tela tres nodo independiente con Azure máquinas virtuales que ejecutan Windows Server

En este artículo se describe cómo crear un clúster en basado en Windows Azure máquinas virtuales de Windows (VM), con el instalador de tela de servicio independiente para Windows Server. Se trata de un caso especial de [crear y administrar un clúster en Windows Server](service-fabric-cluster-creation-for-windows-server.md) donde las máquinas virtuales son [Máquinas virtuales de Azure que ejecutan Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), aunque no va a crear [un clúster de servicio tela Azure en la nube](service-fabric-cluster-creation-via-portal.md). La diferencia es que el clúster de servicio tela independiente creado por los siguientes pasos se administra completamente por usted, mientras la Azure basada en nube servicio tela clústeres se administran y actualizados por el proveedor de recursos de servicio tela.


## <a name="steps-to-create-the-standalone-cluster"></a>Pasos para crear el clúster independiente

1. Inicie sesión en el portal de Azure y crear una nueva VM de centro de datos de Windows Server 2012 R2 en un grupo de recursos. Lea el artículo [crear una máquina virtual de Windows en el portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obtener más detalles.
2. Agregar un par más Windows Server 2012 R2 centro de datos máquinas virtuales al mismo grupo de recursos. Asegúrese de que cada una de las máquinas virtuales tiene el mismo nombre de usuario administrador y la contraseña cuando creó. Una vez creada, debería ver todos los tres máquinas virtuales en la misma red virtual.
3. Conectarse a cada una de las máquinas virtuales y desactivar el Firewall de Windows con el [Administrador del servidor, el panel de servidor Local](https://technet.microsoft.com/library/jj134147.aspx). Esto garantiza que el tráfico de red puede comunicarse entre los equipos. Mientras está conectado a cada equipo, obtener la dirección IP, abra un símbolo del sistema y escribir `ipconfig`. También puede ver la dirección IP de cada equipo, seleccione el recurso de red virtual del grupo de recursos en el portal de Azure.
4. Conectarse a una de las máquinas virtuales y probar que puede hacer ping correctamente las otras dos máquinas virtuales.
5. Conectarse a una de las máquinas virtuales y [Descargue el paquete de servicio tela independiente de Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) en una nueva carpeta en el equipo y extraer el paquete.
6. Abra el archivo *ClusterConfig.Unsecure.MultiMachine.json* en el Bloc de notas y edite cada nodo con las tres direcciones IP de los equipos. Cambiar el nombre de clúster en la parte superior y guarde el archivo.  Continuación se muestra un ejemplo del manifiesto clúster parcial.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Abra una [ventana de PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Vaya a la carpeta donde extraer el paquete de instalación independiente descargado y había guardado el archivo de clúster manifiestos. Ejecute el siguiente comando PowerShell.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Verá el PowerShell ejecutar, conectarse a cada equipo y crear un clúster. Después de aproximadamente un minuto, puede comprobar si el clúster es operativo mediante una conexión en el Explorador de tela de servicio en una de la dirección IP del equipo, por ejemplo, usando `http://10.7.0.5:19080/Explorer/index.html`. Dado que este es un clúster de independiente con máquinas virtuales de Azure, para que sea más segura se tiene para [implementar certificados para las máquinas virtuales de Azure](service-fabric-windows-cluster-x509-security.md) o configurar uno de los equipos como un [controlador de Windows Server Active Directory (AD) para la autenticación de Windows](service-fabric-windows-cluster-windows-security.md), al igual que lo haría en local.


## <a name="next-steps"></a>Pasos siguientes
- [Crear clústeres de tela de servicio independiente en Windows Server o Linux](service-fabric-deploy-anywhere.md)
- [Agregar o quitar nodos a un clúster de tela de servicio independiente](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Opciones de configuración de clúster de Windows independiente](service-fabric-cluster-manifest.md)
- [Proteger un clúster independiente en Windows con la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger un clúster independiente en Windows con X509 certificados](service-fabric-windows-cluster-x509-security.md)
