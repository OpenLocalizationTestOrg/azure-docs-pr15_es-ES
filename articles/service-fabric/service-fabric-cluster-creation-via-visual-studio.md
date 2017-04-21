<properties
   pageTitle="Configurar un clúster de servicio tela mediante Visual Studio | Microsoft Azure"
   description="Describe cómo configurar un clúster de servicio tela mediante plantilla de administrador de recursos de Azure creado por un proyecto del grupo de recursos de Azure en Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurar un clúster de servicio tela mediante Visual Studio
En este artículo se describe cómo configurar un clúster de Azure servicio tela usando Visual Studio y una plantilla de administrador de recursos de Azure. Se usará un proyecto del grupo de recursos de Azure de Visual Studio para crear la plantilla. Después de crear la plantilla, puede implementar directamente a Azure desde Visual Studio. También puede utilizarse desde una secuencia de comandos o como parte de las instalaciones de integración continua (elemento de configuración).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Crear una plantilla de clúster de servicio tela utilizando un proyecto del grupo de recursos de Azure
Para empezar, abra Visual Studio y crear un proyecto de grupo de recursos de Azure (está disponible en la carpeta de **nube** ):

![Cuadro de diálogo nuevo proyecto con el proyecto del grupo de recursos de Azure seleccionado][1]

Puede crear una solución para este proyecto de Visual Studio o agregar a una solución existente.

>[AZURE.NOTE] Si no ve el proyecto del grupo de recursos de Azure bajo el nodo de la nube, no tiene instalado el SDK de Azure. Inicie el instalador de plataforma Web ([Instalar ahora](http://www.microsoft.com/web/downloads/platform.aspx) si aún no tiene) y, a continuación, busque "Azure SDK para .NET" e instale la versión que sea compatible con su versión de Visual Studio.

Después de presionar el botón Aceptar, Visual Studio le pedirá que seleccione la plantilla de administrador de recursos que desea crear:

![Seleccione el cuadro de diálogo de Azure plantilla con la plantilla de servicio tela clúster seleccionada][2]

Seleccione la plantilla de servicio tela clúster y pulsa el botón Aceptar de nuevo. Ahora se han creado el proyecto y la plantilla de administrador de recursos.

## <a name="prepare-the-template-for-deployment"></a>Preparar la plantilla para su implementación
Antes de implementa la plantilla para crear el clúster, debe proporcionar los valores para los parámetros de plantilla necesaria. Estos valores de parámetro se leen desde la `ServiceFabricCluster.parameters.json` archivo, que se encuentra en la `Templates` carpeta del proyecto del grupo de recursos. Abra el archivo y proporciona los siguientes valores:

|Nombre de parámetro           |Descripción|
|-----------------------  |--------------------------|
|adminUserName            |El nombre de la cuenta de administrador para equipos de servicio tela (nodos).|
|certificateThumbprint    |La huella digital del certificado que protege el clúster.|
|sourceVaultResourceId    |*Id. de recurso* del depósito clave donde se encuentra almacenado el certificado que protege el clúster.|
|certificateUrlValue      |La dirección URL del certificado de seguridad de clúster.|

La plantilla de administrador de recursos de Visual Studio servicio tela crea un clúster seguro que está protegido por un certificado. Este certificado se identifica mediante los parámetros de tres últimas plantilla (`certificateThumbprint`, `sourceVaultValue`, y `certificateUrlValue`), y debe permanecer en un **Depósito de clave de Azure**. Para obtener más información sobre cómo crear el certificado de seguridad de clúster, consulte el artículo de [servicio tela clúster escenarios de seguridad](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Opcional: cambiar el nombre de clúster
Cada clúster de servicio tela tiene un nombre. Cuando se crea un clúster de tela en Azure, nombre de clúster determina (junto con la región de Azure) el nombre de sistema de nombres de dominio (DNS) para el clúster. Por ejemplo, si el nombre del clúster `myBigCluster`y la ubicación (región Azure) del grupo de recursos que va a hospedar el nuevo clúster es oriental de Estados Unidos, el nombre DNS del clúster será `myBigCluster.eastus.cloudapp.azure.com`.

De forma predeterminada el nombre de clúster se genera automáticamente y realizado único adjuntando un sufijo aleatorio a un prefijo "clúster". Resulta muy fácil de utilizar la plantilla como parte de un sistema de **integración continua** (elemento de configuración). Si desea usar un nombre para el clúster específico, uno que tenga sentido para usted, establezca el valor de la `clusterName` variable en el archivo de plantilla de administrador de recursos (`ServiceFabricCluster.json`) con su nombre elegido. Es la primera variable definida en ese archivo.

## <a name="optional-add-public-application-ports"></a>Opcional: agregar puertos aplicación pública
También puede cambiar los puertos aplicación público para el clúster antes de implementarlo. De forma predeterminada, la plantilla abre sólo dos puertos TCP públicos (80 y 8081). Si necesita más información para sus aplicaciones, modifique la definición del equilibrador de carga de Azure en la plantilla. La definición se almacena en el archivo de plantilla principal (`ServiceFabricCluster.json`). Abrir el archivo y busque `loadBalancedAppPort`. Cada puerto está asociado a tres artefactos:

1. Variable de plantilla que define el valor de puerto TCP para el puerto:

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. *Sondeo* que define con qué frecuencia y cuánto la carga Azure equilibrador intenta utilizar un nodo de servicio tela específico antes de conmutar a otro. Los sondeos forman parte de los recursos del equilibrador de carga. Aquí es la definición de sondeo para el primer puerto de la aplicación predeterminada:

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. Una *regla de equilibrio de carga* que reúne el puerto y el sondeo, lo que permite a través de un conjunto de nodos de clúster de tela de servicio de equilibrio de carga:

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Si las aplicaciones que piensa para implementar el clúster necesitan más puertos, puede agregarlos crear sondeo adicional y las definiciones de reglas de equilibrio de carga. Para obtener más información sobre cómo trabajar con equilibrador de carga de Azure a través de plantillas de administrador de recursos, vea [empezar a crear un equilibrador de carga interno usando una plantilla](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Implementar la plantilla mediante Visual Studio
Después de guardar todos los valores de parámetro necesario de la`ServiceFabricCluster.param.dev.json` archivo, está listo para implementar la plantilla y crear el clúster de servicio tela. Haga clic en el proyecto del grupo de recursos en el Explorador de soluciones de Visual Studio y elija **implementar | Nueva implementación...**. Si es necesario, Visual Studio mostrará el cuadro de diálogo **implementar al grupo de recursos** , que le pide que autenticarse en Azure:

![Implementar en el cuadro de diálogo grupo de recursos][3]

El cuadro de diálogo le permite elegir entre un grupo de recursos de administrador de recursos existente para el clúster y le ofrece la opción de crear una nueva. Normalmente tiene sentido utilizar un grupo de recursos independiente para un clúster de tela de servicio.

Después de pulsar el botón de implementar, Visual Studio le pedirá que confirme los valores de parámetro de plantilla. Haga clic en el botón **Guardar** . Un parámetro no tiene un valor persistente: la contraseña de la cuenta administrativa para el clúster. Debe proporcionar un valor de contraseña cuando Visual Studio le solicita uno.

>[AZURE.NOTE] A partir de Azure SDK 2,9, Visual Studio admite contraseñas de lectura de la **Cámara de clave de Azure** durante la implementación. En el cuadro de diálogo de parámetros de plantilla, observe que la `adminPassword` cuadro de texto de parámetro tiene un pequeño icono de "clave" a la derecha. Este icono le permite seleccionar un secreto depósito clave existente como la contraseña administrativa para el clúster. Asegúrese de que para habilitar el acceso de administrador de recursos de Azure para implementación de plantilla en las directivas de acceso avanzado de su depósito de clave. 

Puede supervisar el progreso del proceso de implementación en la ventana de resultados de Visual Studio. Una vez completada la implementación de plantilla, está listo para usar su nuevo clúster!

>[AZURE.NOTE] Si nunca se ha utilizado PowerShell para administrar Azure desde el equipo que está utilizando ahora, debe hacer un poco mantenimiento.
>1. Habilitar secuencias de comandos de PowerShell, ejecute el [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) comando. Para equipos de desarrollo, la directiva "sin restricciones" es aceptable normalmente.
>2. Decidir si desea permitir la recopilación de datos de diagnóstico de comandos de PowerShell de Azure y ejecutar [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) o [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) según sea necesario. Esto evitará indicaciones innecesarios durante la implementación de la plantilla.

Si hay errores, vaya al [portal de Azure](https://portal.azure.com/) y abra el grupo de recursos que se implementa en. Haga clic en **toda la configuración**, haga clic en **implementaciones** en el módulo de la configuración. Error en la implementación de grupo de recursos deja hay información de diagnóstico detallada.

>[AZURE.NOTE] Servicio tela clústeres requieren un número determinado de nodos estar hacia arriba mantener la disponibilidad y mantener el estado - se denomina "mantenimiento quórum". Por lo tanto, no es seguro apagar todos los equipos del clúster a menos que primero ha realizado una [copia de seguridad completa de su estado](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Pasos siguientes
- [Obtenga información sobre la configuración de clúster de tela de servicio con el portal de Azure](service-fabric-cluster-creation-via-portal.md)
- [Obtenga información sobre cómo administrar e implementar aplicaciones de servicio tela mediante Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
