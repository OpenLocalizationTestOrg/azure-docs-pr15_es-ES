<properties
   pageTitle="Administrar varios entornos en tela de servicio | Microsoft Azure"
   description="Aplicaciones de servicio tela se pueden ejecutar en clústeres que varían en tamaño de un equipo a miles de equipos. En algunos casos, desea configurar la aplicación de forma diferente para los distintos entornos. En este artículo trata sobre cómo definir los parámetros de aplicación diferente por entorno."
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
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Administrar los parámetros de la aplicación para múltiples entornos

Puede crear clústeres de Azure servicio tela mediante cualquier parte de uno a varios miles de equipos. Mientras archivos binarios de aplicación pueden ejecutarse sin modificaciones en esta amplia gama de entornos, que a menudo que desea configurar la aplicación de forma diferente, dependiendo del número de equipos que vaya a implementar.

Por ejemplo, considere la posibilidad de `InstanceCount` de un servicio de estado. Cuando se ejecutan aplicaciones en Azure, que generalmente que desea establecer este parámetro en el valor especial "-1". Esto garantiza que el servicio se está ejecutando en todos los nodos en el clúster. Sin embargo, esta configuración no es adecuada para un clúster de un solo equipo ya no puede tener varios procesos de escucha en el extremo del mismo en un único equipo. En su lugar, normalmente se establece `InstanceCount` en "1".

## <a name="specifying-environment-specific-parameters"></a>Especificar los parámetros de específicas del entorno

La solución para este problema de configuración es un conjunto de servicios predeterminados parametrizadas y archivos de parámetro de aplicación que rellenan los valores de parámetro para un entorno determinado. Servicios predeterminados y los parámetros de la aplicación se configuran en los manifiestos de aplicaciones y servicios. Definición del esquema para los archivos ServiceManifest.xml y ApplicationManifest.xml se instala con el servicio tela SDK y herramientas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Servicios predeterminados

Aplicaciones de servicio tela se componen de una colección de instancias de servicio. Si es posible para crear una aplicación vacía y, a continuación, crear todas las instancias de servicio dinámicamente, mayoría de las aplicaciones tiene un conjunto de servicios principales que siempre se debe crear cuando se crea una instancia de la aplicación. Estos se denominan "servicios predeterminados". Se especifican en el manifiesto de aplicación, con marcadores de posición para la configuración de cada entorno incluidas entre corchetes:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Cada uno de los parámetros con nombre debe definirse en el elemento de parámetros del manifiesto de aplicación:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

El atributo DefaultValue especifica el valor que se usará en ausencia de un parámetro más concretos para un entorno determinado.

>[AZURE.NOTE] No todos los parámetros de la instancia de servicio son adecuados para la configuración de cada entorno. En el ejemplo anterior, se definen explícitamente los valores LowKey y HighKey de esquema de particiones del servicio para todas las instancias del servicio desde el intervalo de partición es una función del dominio de datos, no el entorno.


### <a name="per-environment-service-configuration-settings"></a>Configuración de servicio de cada entorno

El [modelo de aplicación de servicio tela](service-fabric-application-model.md) habilita servicios incluir los paquetes de configuración que contienen pares de valor clave personalizados que se pueda leer en tiempo de ejecución. Los valores de estas opciones también se diferencian por entorno especificando una `ConfigOverride` manifiesto de aplicación.

Suponga que tiene el siguiente valor en el archivo Config\Settings.xml para el `Stateful1` servicio:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Para reemplazar este valor para un par de entorno o aplicación específica, cree un `ConfigOverride` al importar el manifiesto del servicio en el manifiesto de aplicación.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Este parámetro puede, a continuación, configurarse entorno como se indicó anteriormente. Puede hacerlo declarar en la sección parámetros del manifiesto de aplicación y especificando valores específicos del entorno en los archivos de parámetros de la aplicación.

>[AZURE.NOTE] En el caso de los valores de configuración de servicio, hay tres lugares donde se puede establecer el valor de una clave: el paquete de configuración de servicio, el manifiesto de aplicación y el archivo de parámetros de la aplicación. Servicio tela siempre podrán elegir entre el archivo de parámetros de la aplicación en primer lugar (si se especifica), a continuación, el manifiesto de aplicación y, finalmente, el paquete de configuración.


### <a name="application-parameter-files"></a>Archivos de parámetro de la aplicación

El proyecto de aplicación de servicio tela puede incluir uno o más archivos de parámetro de la aplicación. Cada uno de ellos define los valores específicos para los parámetros que se definen en el manifiesto de aplicación:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

De forma predeterminada, una nueva aplicación incluye dos archivos de parámetro de aplicación, llamados Local.xml y Cloud.xml:

![Archivos de parámetro de la aplicación en el Explorador de soluciones][app-parameters-solution-explorer]

Para crear un nuevo archivo de parámetro, simplemente copiar y pegar una existente y asígnele un nombre nuevo.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificar parámetros específicos del entorno durante la implementación

En tiempo de implementación, debe elegir el archivo de parámetro adecuado para aplicar con la aplicación. Puede hacer esto mediante el cuadro de diálogo Publicar en Visual Studio o a través de PowerShell.

### <a name="deploy-from-visual-studio"></a>Implementar desde Visual Studio

Puede elegir de la lista de archivos de parámetro disponible cuando se publique la aplicación en Visual Studio.

![Elija un archivo de parámetros en el cuadro de diálogo Publicar][publishdialog]

### <a name="deploy-from-powershell"></a>Implementar de PowerShell

La `Deploy-FabricApplication.ps1` script de PowerShell que se incluyen en la plantilla de proyecto de la aplicación acepta un perfil de publicación como un parámetro y la PublishProfile contiene una referencia al archivo de parámetros de la aplicación.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre algunos de los conceptos básicos que se describen en este tema, vea la [información general técnica de tela de servicio](service-fabric-technical-overview.md). Para obtener información sobre otras capacidades de administración de la aplicación que están disponibles en Visual Studio, vea [administrar las aplicaciones de servicio tela en Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
