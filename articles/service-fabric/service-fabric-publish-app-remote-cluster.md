<properties
    pageTitle="Publicar una aplicación en un clúster remoto con Visual Studio | Microsoft Azure"
    description="Obtenga información sobre cómo publicar una aplicación en un clúster de tela de servicio remota mediante Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Publicar una aplicación en un clúster remoto mediante Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

La extensión de tela de servicio de Azure para Visual Studio proporciona una manera fácil, puede repetir y secuencias de comandos para publicar una aplicación a un clúster de tela de servicio.

## <a name="the-artifacts-required-for-publishing"></a>Los artefactos necesarios para publicación

### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 implementar

Se trata de una secuencia de comandos de PowerShell que usa una ruta de acceso del perfil de publicación como un parámetro para publicar aplicaciones de servicio tela. Dado que este script es parte de la aplicación, se encuentra Bienvenido a modificar según sea necesario para su aplicación.

### <a name="publish-profiles"></a>Perfiles de publicación

Una carpeta en el proyecto de aplicación de servicio tela denominado **PublishProfiles** contiene los archivos XML que almacenan información esencial para publicar una aplicación, como por ejemplo:

- Parámetros de conexión de servicio tela clúster
- Ruta de acceso a un archivo de parámetros de la aplicación
- Configuración de actualización

De forma predeterminada, la aplicación incluirá dos perfiles de publicación: Local.xml y Cloud.xml. Puede agregar más perfiles copiando y pegando uno de los archivos de forma predeterminada.

### <a name="application-parameter-files"></a>Archivos de parámetro de la aplicación

Una carpeta en el proyecto de aplicación de servicio tela denominado **ApplicationParameters** contiene archivos XML para los valores de parámetro manifiestos de aplicación especificada por el usuario. Archivos de manifiestos de aplicación pueden tener parámetros para que puedan utilizar diferentes valores de configuración de implementación. Para obtener más información sobre cómo parametrizar la aplicación, vea [administrar varios entornos en tela de servicio](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] Servicios de actor, debe crear el proyecto antes de intentar editar el archivo en un editor o mediante el cuadro de diálogo Publicar. Esto es porque parte de los archivos de manifiestos se generará durante la compilación.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Para publicar una aplicación mediante el cuadro de diálogo Publicar aplicación de servicio tela

Los pasos siguientes muestran cómo publicar una aplicación mediante el cuadro de diálogo **Publicar aplicación de servicio tela** proporcionado por las herramientas de Visual Studio servicio tela.

1. En el menú contextual del proyecto de aplicación de servicio tela, elija **publicar...** Para ver el cuadro de diálogo **Publicar aplicación de servicio tela** .

    ![La ** el cuadro de diálogo Publicar servicio tela aplicación **][0]

    El archivo seleccionado en el cuadro de lista desplegable de **perfil de destino** es donde se guardan todos los valores, salvo **las versiones de manifiestos**. Puede volver a utilizar un perfil existente o cree uno nuevo eligiendo **< administrar perfiles... >** en el cuadro de lista desplegable de **perfil de destino** . Al elegir un perfil de publicación, su contenido aparece en los campos correspondientes del cuadro de diálogo. Para guardar los cambios en cualquier momento, elija el vínculo **Guardar perfil** .    

2. En la sección de **extremo de la conexión** , especifique el extremo de publicación local o remota tela de servicio de un clúster. Para agregar o cambiar el extremo de la conexión, haga clic en la lista desplegable de **Extremo de la conexión** . La lista muestra el clúster tela servicio disponible extremos de la conexión a los que puede publicar en función de sus suscripciones de Azure. Tenga en cuenta que si no aún ha iniciado Visual Studio, se le pedirá que lo haga.

    Use el cuadro de diálogo de selección de clúster para elegir en el conjunto de suscripciones disponibles y clústeres.

    ![La ** el cuadro de diálogo Seleccione servicio tela clúster **][1]

    >[AZURE.NOTE] Si desea publicar en un extremo arbitrario (por ejemplo, un clúster de terceros), consulte la sección **publicación a un extremo de clúster arbitrario** .

    Una vez que elija un extremo, Visual Studio valida la conexión con el clúster de tela de servicio seleccionado. Si el clúster no está seguro, Visual Studio puede conectarse a él inmediatamente. Sin embargo, si el clúster es seguro, debe instalar un certificado en el equipo local antes de continuar. Para obtener más información, consulte [cómo configurar conexiones seguras](service-fabric-visualstudio-configure-secure-connections.md) . Cuando haya terminado, elija el botón **Aceptar** . El clúster seleccionado aparece en el cuadro de diálogo **Publicar aplicación de servicio tela** .

3. En el cuadro de lista desplegable de **Archivo de parámetro de la aplicación** , vaya a un archivo de parámetros de la aplicación. Un archivo de parámetros de la aplicación contiene valores especificados por el usuario para los parámetros del archivo de manifiestos de aplicación. Para agregar o cambiar un parámetro, seleccione el botón **Editar** . Especifique o cambie el valor del parámetro en la cuadrícula de **parámetros** . Cuando haya terminado, seleccione el botón **Guardar** .

    ![La ** el cuadro de diálogo Editar parámetros **][2]

4. Use la casilla de verificación **actualizar la aplicación** para especificar si esta acción de publicar es una actualización. Actualización de publicar acciones difieren normal Publicar acciones. Para obtener una lista de las diferencias, vea [Servicio de actualización de aplicaciones de tela](service-fabric-application-upgrade.md) . Para configurar las opciones de actualización, elija el vínculo **Configurar opciones de actualización** . Aparece el editor de parámetro de actualización. Consulte [Configurar la actualización de una aplicación de servicio de tela](service-fabric-visualstudio-configure-upgrade.md) para obtener más información acerca de los parámetros de actualización.

5. Elija la **manifiestos versiones...** botón para ver el cuadro de diálogo **Editar versiones** . Necesita actualizar la aplicación y versiones de servicio para que tenga lugar una actualización. Consulte [actualizar el tutorial de aplicación de servicio tela](service-fabric-application-upgrade-tutorial.md) para obtener información sobre cómo la aplicación y servicio manifiestos impacto un proceso de actualización de versiones.

    ![La ** el cuadro de diálogo Editar versiones **][3]

    Si la aplicación y versiones de servicio usan las versiones semántico como 1.0.0 o valores numéricos en el formato de 1.0.0.0, seleccione la opción **actualizar automáticamente las aplicaciones y versiones de servicio** . Se actualiza cuando se elige esta opción, el servicio y números de versión de la aplicación se actualizan automáticamente cada vez que un código, configuración o datos de versión del paquete. Si prefiere editar las versiones manualmente, desactive la casilla de verificación para deshabilitar esta característica.

    >[AZURE.NOTE] Para que todas las entradas de paquete aparezca para un proyecto de actor, cree primero el proyecto para generar las entradas en los archivos de manifiestos de servicio.

6. Cuando haya terminado que especifica todos los valores es necesarios, elija el botón **Publicar** publicar su aplicación en el clúster de tela de servicio seleccionado. La configuración que ha especificado se aplica al proceso de publicación.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicar en un extremo de clúster arbitrario (incluidos clústeres de terceros)

La experiencia de publicación de Visual Studio está optimizado para la publicación para clústeres remotos asociada a una de las suscripciones de Azure. Sin embargo, es posible publicar en arbitrarios extremos (como servicio tela fiesta clústeres) editando directamente el perfil de publicación XML. Como se describió anteriormente, publicación dos perfiles proporcionan por defecto:**Local.xml** y **Cloud.xml**, pero se Bienvenido a crear perfiles adicionales para distintos entornos. Por ejemplo, que desea crear un perfil de publicación para clústeres de terceros, denominado **Party.xml**.

Si se está conectando a un clúster no seguro, todo lo necesario es el extremo de la conexión de clúster, como `partycluster1.eastus.cloudapp.azure.com:19000`. En ese caso, el extremo de conexión en el perfil de publicación sería algo similar a esta:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Si se está conectando a un clúster protegido, también debe proporcionar los detalles del certificado de cliente desde el almacén local que se usará para la autenticación. Para obtener más detalles, vea [Configurar conexiones seguras a un clúster de tela de servicio](service-fabric-visualstudio-configure-secure-connections.md).

  Una vez configurada su perfil de publicación, puede hacer referencia en el cuadro de diálogo Publicar como se muestra a continuación.

  ![Publicar nuevo perfil en el cuadro de diálogo Publicar][4]

  Tenga en cuenta que en este caso, el nuevo perfil de publicación apunta a uno de los archivos de parámetro de la aplicación de forma predeterminada. Esto es adecuado si desea publicar la misma configuración de aplicación a un número de entornos. Por el contrario, en los casos donde desea que tengan configuraciones diferentes para cada entorno que desea publicar en, tendría sentido para crear un archivo de parámetros de aplicación correspondiente.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo automatizar el proceso de publicación en un entorno de integración continua, vea [Configurar la integración continua tela de servicio](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
