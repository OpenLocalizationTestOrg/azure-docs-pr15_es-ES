<properties
   pageTitle="Supervisión de estado en tela de servicio | Microsoft Azure"
   description="Introducción a la supervisión de modelo, que proporciona la supervisión de clúster y sus aplicaciones y servicios de estado de Azure servicio tela."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introducción a la supervisión de estado de tela de servicio
Azure tela servicio presenta un modelo de estado que proporciona informes y evaluación de mantenimiento enriquecido, flexible y extensible. El modelo permite supervisión casi en tiempo real del estado del clúster y los servicios que se ejecutan en él. Puede obtener la información de estado y corregir fácilmente posibles problemas antes de que en cascada y producen interrupciones masivas. En el modelo típico, servicios envían informes basados en sus vistas locales y que la información se agrega para proporcionar general clúster de nivel de vista.

Componentes de servicio tela utilizan este modelo de salud enriquecidos para informar de su estado actual. Puede usar el mismo mecanismo a estado del informe de las aplicaciones. Si invierte en informes sobre el estado de alta calidad que capture sus condiciones personalizadas, puede detectar y corregir problemas de la aplicación en ejecución mucho más fácilmente.

> [AZURE.NOTE] Se inicia el subsistema de estado para responder a una necesidad para actualizaciones supervisadas. Servicio tela proporciona actualizaciones de aplicación y clúster supervisadas que garantizar disponibilidad completa, sin tiempo de inactividad y mínima o sin intervención del usuario. Para lograr estos objetivos, la actualización comprueba estado basado en configura directivas de actualización y permite una actualización continuar solo cuando el estado respeta umbrales deseados. En caso contrario, la actualización se automáticamente deshecha o en pausa para dar a los administradores de la oportunidad de solucionar los problemas. Para obtener más información acerca de las actualizaciones de la aplicación, consulte [este artículo](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Almacén de estado
El almacén de estado mantiene relacionados con el estado de la información sobre las entidades en el clúster para facilitar la recuperación y evaluación. Se implementa como una estructura de servicio conserva el servicio con estado para garantizar alta disponibilidad y escalabilidad. El almacén de estado es parte de la **tela: / sistema** aplicación y está disponible cuando el clúster está activo y en funcionamiento.

## <a name="health-entities-and-hierarchy"></a>Jerarquía y entidades de estado
Las entidades de salud se organizan en una jerarquía lógica que capture interacciones y las dependencias entre las diferentes entidades. La jerarquía y entidades se generan automáticamente el almacén de estado basado en los informes de componentes de servicio tela.

Las entidades de salud reflejan las entidades de tela de servicio. (Por ejemplo, **entidad de aplicación de estado** coincide con una instancia de la aplicación implementada en el clúster, mientras que **entidad de nodo de estado** coincide con un nodo de clúster de servicio tela.) La jerarquía de salud captura las interacciones de entidades del sistema y es la base de la evaluación de mantenimiento avanzadas. Puede obtener información sobre conceptos clave de tela de servicio de [información general técnica de tela de servicio](service-fabric-technical-overview.md). Para obtener más información sobre la aplicación, vea [modelo de aplicación de servicio tela](service-fabric-application-model.md).

Las entidades de salud y la jerarquía de permitir que el clúster y las aplicaciones que notificar, depurar y supervisar eficazmente. El modelo de estado proporciona una representación precisa, *pormenorizado* del estado de las piezas moviendo en el clúster.

![Entidades de estado.][1]
Las entidades de estado, se organizan en una jerarquía basada en relaciones de elementos primarios y secundarios.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Las entidades de estado son:

- **Clúster**. Representa el estado de un clúster de tela de servicio. Informes de estado de clúster describen las condiciones que afectan a todo el clúster y no se pueden reducir a uno o más secundarios mal Estados. Por ejemplo, el cerebro del clúster dividir debido a problemas de particiones o comunicaciones de red.

- **Nodo**. Representa el estado de un nodo de servicio tela. Informes de estado de nodo describen las condiciones que afectan a la funcionalidad de nodo. Normalmente afecta a todas las entidades implementadas ejecutando en él. Por ejemplo, cuando un nodo está fuera del espacio de disco (u otra propiedad de todo el equipo, como la memoria, conexiones) y cuando el nodo está desactivado. La entidad de nodo se identifica por el nombre de nodo (cadena).

- En la **aplicación**. Representa el estado de una instancia de aplicación que se ejecutan en el clúster. Informes de estado de la aplicación, describen las condiciones que afectan al estado general de la aplicación. No pueden acotar a sus hijos (servicios o aplicaciones implementadas). Algunos ejemplos son la interacción de llevar a cabo entre los distintos servicios en la aplicación. La aplicación se identifica por el nombre de la aplicación (URI).

- **Servicio**. Representa el estado de un servicio que se ejecutan en el clúster. Informes de estado del servicio describen las condiciones que afectan al estado general del servicio y no puede restringirse hacia abajo a una partición o una réplica. Por ejemplo, una configuración de servicio (como puerto o recurso compartido de archivos externos) que está causando problemas de todas las particiones. La entidad de servicio se identifica por el nombre de servicio (URI).

- **Partición**. Representa el estado de una partición de servicio. Informes de estado de partición describen las condiciones que afectan al conjunto completo de réplica. Por ejemplo, cuando el número de réplicas es inferior al recuento de destino y cuando es una partición de pérdida de quórum. La entidad de partición se identifica mediante la partición ID (GUID).

- **Réplica**. Representa el estado de una réplica de estado del servicio o una instancia de estado del servicio. La unidad más pequeña que watchdogs y componentes del sistema pueden informar sobre una aplicación. Para los servicios con estado, por ejemplo, una réplica principal informes cuando no podrá replicar operaciones secundarios y cuando la replicación no es continuar el ritmo. Además, puede informar una instancia de estado cuando se está quedando sin recursos o tiene problemas de conectividad. La entidad de réplica se identifica mediante la partición ID (GUID) y el identificador de réplica o instancia (long).

- **DeployedApplication**. Representa el estado de una *aplicación que se ejecuta en un nodo*. Informes de estado de la aplicación implementada, describen las condiciones específicas de la aplicación en el nodo que no se pueden reducir a paquetes de servicio implementados en el mismo nodo. Por ejemplo, cuando no se puede descargar el paquete de aplicación en ese nodo y cuando hay una configuración de emisión de entidades de seguridad de la aplicación en el nodo. La aplicación implementada se identifica por el nombre de la aplicación (URI) y el nombre de nodo (cadena).

- **DeployedServicePackage**. Representa el estado de un paquete de servicio en un nodo del clúster. Describe las condiciones específicas de un paquete de servicio que no afectan a los demás paquetes de servicio en el mismo nodo de la misma aplicación. Algunos ejemplos son un paquete de código en el paquete de servicio que no se puede iniciar y un paquete de configuración que no se puede leer. El paquete de servicio implementado se identifica por el nombre de la aplicación (URI), el nombre de nodo (cadena) y nombre de servicio manifiestos (cadena).

El nivel de detalle del modelo de salud facilita la detectar y corregir problemas. Por ejemplo, si un servicio no responde, es factible para informar de que la instancia de la aplicación está en mal estada. Los informes de que nivel no es ideal, sin embargo, dado que el problema podría no afectar a todos los servicios de dicha aplicación. El informe se debe aplicar al servicio negativo o a una partición secundarios específicos, si dicha partición de destino más información. Los datos automáticamente expone a través de la jerarquía y una partición mal estada se hace visible en los niveles de servicio y aplicación. Esta agregación ayuda a identificar y resolver la causa del problema más rápidamente.

La jerarquía de estado se compone de relaciones de elementos primarios y secundarios. Un clúster se compone de los nodos y aplicaciones. Aplicaciones de servicios y desarrollan aplicaciones. Aplicaciones distribuidas han implementado paquetes de servicio. Servicios tienen particiones, y cada partición tiene una o más réplicas. Hay una relación entre los nodos y entidades implementadas especial. Si un nodo está en mal estado notificado por su componente del sistema de entidad emisora (servicio de administrador de migración tras error), afecta a las aplicaciones distribuidas, paquetes de servicio y réplicas implementadas en él.

La jerarquía de salud representa el estado del sistema basándose en los informes de estado más recientes, que es casi en tiempo real información más reciente.
Watchdogs internos y externos pueden informar sobre las mismas entidades basadas en lógica específica de la aplicación o condiciones supervisadas personalizadas. Informes de usuario coexisten con los informes del sistema.

Plan invertir en forma de informe y responder a estado durante el diseño de un servicio de nube grande para facilitar la depuración, supervisar y trabajar con el servicio.

## <a name="health-states"></a>Estados de mantenimiento
Servicio tela usa tres estados de salud para describir si una entidad es correcta o no: Aceptar, advertencia y error. Los informes que envía a la tienda de salud deben especificar uno de estos Estados. El resultado de la evaluación de mantenimiento es uno de estos Estados.

Los posibles [Estados](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) son:

- **OK**. La entidad es correcto. No existen problemas conocidos informados sobre él o sus elementos secundarios (si procede).

- **Mensaje de advertencia**. Experiencias de la entidad algunos problemas, pero no está mal estado (por ejemplo, hay retrasos, pero no producen problemas funcionales todavía). En algunos casos, la condición de advertencia se puede corregir sin intervención especial y resulta útil proporcionar visibilidad qué está ocurriendo. En los demás casos, la condición de advertencia puede degradar un problema graves sin intervención del usuario.

- **Error**. La entidad está en mal estada. Deben ser tomar medidas para corregir el estado de la entidad, porque no puede funcionar correctamente.

- **Desconocido**. La entidad no existe en el almacén de estado. Este resultado puede obtenerse de las consultas distribuidas que combinación los resultados de varios componentes. Por ejemplo, obtener la consulta en la lista nodo va a **FailoverManager** y **HealthManager**, obtener consulta de la lista de aplicaciones que se va a **ClusterManager** y **HealthManager**. Estas consultas combinación los resultados de varios componentes del sistema. Si otro componente del sistema devuelve una entidad que no se ha alcanzado o limpia de la tienda de estado, el resultado de la combinación tiene desconocido estado de mantenimiento.

## <a name="health-policies"></a>Directivas de mantenimiento
El almacén de estado aplica a las directivas de mantenimiento para determinar si una entidad es correcta basándose en los informes y sus elementos secundarios.

> [AZURE.NOTE] Las directivas de mantenimiento se pueden especificar en el manifiesto de clúster (de evaluación de mantenimiento de clúster y nodo) o en el manifiesto de aplicación (para la evaluación de la aplicación y cualquiera de sus elementos secundarios). También pueden pasar solicitudes de evaluación de mantenimiento en directivas de evaluación de estado personalizados, que se usa solamente para que la evaluación.

De forma predeterminada, servicio tela se aplica reglas estrictas (todo lo que debe ser correcto) para la relación jerárquica de elementos primarios y secundarios. Si alguno de los elementos secundarios tiene un evento negativo, el elemento primario se considera mal estado.

### <a name="cluster-health-policy"></a>Directiva de mantenimiento de clúster
La [Directiva de mantenimiento de clúster](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) se usa para evaluar el estado de mantenimiento de clúster y Estados de mantenimiento de nodo. La directiva puede definirse en el manifiesto de clúster. Si no está presente, se utiliza la directiva predeterminada (cero errores permitidos).
Contiene la directiva de mantenimiento de clúster:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Especifica si tratar el estado de advertencia notifica como errores durante la evaluación de mantenimiento. Valor predeterminado: false.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Especifica el porcentaje máximo permitido de aplicaciones que puede estar mal estado antes de que el clúster se considera error.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Especifica el porcentaje máximo permitido de nodos que puede estar mal estado antes de que el clúster se considera error. En grandes clústeres, algunos nodos son siempre hacia abajo o fuera reparaciones, por lo que este porcentaje debe estar configurado para tolerar.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). El mapa de directiva de estado de tipo de aplicación puede usarse durante la evaluación de mantenimiento de clúster para describir tipos de aplicación especial. De forma predeterminada, todas las aplicaciones se coloca en un grupo y se evalúa con MaxPercentUnhealthyApplications. Si algunos tipos de aplicación se deben tratar de forma diferente, es posible fuera del grupo global. En su lugar, se evalúan contra los porcentajes asociados con su nombre de tipo de aplicación en el mapa. Por ejemplo, en un clúster hay miles de aplicaciones de los diferentes tipos y algunas instancias de la aplicación de control de un tipo de aplicación especial. Las aplicaciones de control nunca debería error. Puede especificar MaxPercentUnhealthyApplications global a 20% tolerar algunos errores, pero para el tipo de aplicación "ControlApplicationType" establece la MaxPercentUnhealthyApplications en 0. De este modo, si algunas de las muchas aplicaciones no funcionan correctamente, pero por debajo del porcentaje de mal estado global, el clúster debería evaluarse como advertencia. Estado de mantenimiento de advertencia no se verá afectada la actualización de clúster u otros supervisión desencadenados por estado de Error. Pero la aplicación de control de un solo error haga clúster mal estado, que se activa revertir o se detiene la actualización de clúster, dependiendo de la configuración de actualización.
Para los tipos de aplicación definidos en el mapa, se toman todas las instancias de aplicación en el grupo global de aplicaciones. Se evalúan en función del número total de aplicaciones del tipo de aplicación, usando la MaxPercentUnhealthyApplications específica del mapa. El resto de las aplicaciones permanecen en el grupo global y se evalúan con MaxPercentUnhealthyApplications.

El ejemplo siguiente es un extracto de un manifiesto de clúster. Para definir las entradas en el mapa de tipo de aplicación, prefijo del nombre de parámetro con "ApplicationTypeMaxPercentUnhealthyApplications-", seguido del nombre del tipo de aplicación.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Directiva de mantenimiento de la aplicación
La [Directiva de mantenimiento de la aplicación](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) se describe cómo se debe realizar la evaluación de eventos y agregación de estados secundarios para las aplicaciones y sus elementos secundarios. Se puede definir en el manifiesto de aplicación, **ApplicationManifest.xml**, en el paquete de aplicación. Si no se especifican ningún directivas, servicio tela supone que la entidad es negativa si tiene un elemento secundario o un informe de estado en el estado de advertencia o error.
Las directivas que se pueden configurar son:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Especifica si tratar el estado de advertencia notifica como errores durante la evaluación de mantenimiento. Valor predeterminado: false.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Especifica el porcentaje máximo permitido de implementada aplicaciones que puede estar mal estado antes de que la aplicación se considera error. Este porcentaje se calcula dividiendo el número de aplicaciones implementadas negativos sobre el número de nodos que actualmente se implementan las aplicaciones en el clúster. El cálculo se redondea hacia arriba para tolerar un error en el pequeño número de nodos. Porcentaje de predeterminado: cero.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Especifica la directiva predeterminada servicio tipo estado, lo que reemplaza la directiva de mantenimiento de forma predeterminada para todos los tipos de servicio en la aplicación.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Proporciona un mapa de directivas de mantenimiento de servicio por tipo de servicio. Estas directivas reemplazar las directivas de estado del tipo de servicio predeterminado para cada tipo de servicio especificado. Por ejemplo, si una aplicación tiene un tipo de servicio de estado de la puerta de enlace y un tipo de servicio motor con estado, puede configurar las directivas de mantenimiento de evaluación diferente. Al especificar directiva por tipo de servicio, puede tener un control más detallado del estado del servicio.

### <a name="service-type-health-policy"></a>Directiva de mantenimiento de tipo de servicio
La [Directiva de mantenimiento de tipo de servicio](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) especifica cómo evaluar y agregar los servicios y los elementos secundarios de servicios. La directiva contiene:

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Especifica el porcentaje máximo permitido de particiones mal Estados antes de que un servicio se considera en mal estado. Porcentaje de predeterminado: cero.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Especifica el porcentaje máximo permitido de mal Estados réplicas antes de una partición se considera en mal estada. Porcentaje de predeterminado: cero.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Especifica el porcentaje máximo permitido de los servicios antes de que la aplicación se considera en mal estada. Porcentaje de predeterminado: cero.

El ejemplo siguiente es un extracto de un manifiesto de aplicación:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Evaluación de estado
Usuarios y servicios automatizados pueden evaluar el estado de cualquier entidad en cualquier momento. Para evaluar el estado de la entidad, los agregados de almacén de estado estado de todos los informes de la entidad y evalúa a todos sus elementos secundarios (si procede). El algoritmo de agregación de salud utiliza directivas de estado que especifiquen cómo se evalúa los informes de estado y cómo agregar estados de salud secundarios (si procede).

### <a name="health-report-aggregation"></a>Agregación de informe de estado
Una entidad puede tener varios informes de estado enviados por los redactores diferentes (componentes del sistema o watchdogs) en distintas propiedades. La agregación utiliza las directivas de estado asociada en particular el miembro ConsiderWarningAsError de aplicación o clúster de directiva de mantenimiento. ConsiderWarningAsError especifica cómo se evalúa advertencias.

El estado de mantenimiento agregado se activa mediante el *peor* informes de estado de la entidad. Si hay un informe de estado de al menos un error, el estado de mantenimiento agregado es un error.

![Agregación de informe de estado con informe de errores.][2]

Una entidad de estado que tiene uno o varios informes de estado de error se evalúa como Error. El mismo es true para un informe de estado caducado, independientemente de su estado de salud.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Si hay ningún informes de errores y advertencias de uno o más, el estado de mantenimiento agregado es advertencia o error, dependiendo de la marca de la directiva de ConsiderWarningAsError.

![Agregación de informe de estado con informe de advertencia y ConsiderWarningAsError falso.][3]

Agregación de informe de estado con informe de advertencia y ConsiderWarningAsError se establece en false (predeterminado).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregación de salud secundarios
El estado de mantenimiento agregado de una entidad refleja los Estados de salud secundario (si procede). El algoritmo para la agregación de Estados de salud secundarios utiliza las directivas de salud aplicables en función del tipo de entidad.

![Agregación de salud de entidades secundarios.][4]

Agregación de secundarios en función de las directivas de mantenimiento.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Después de la tienda de estado evalúa a todos los elementos secundarios, agrega su estado de salud según el porcentaje máximo configurado de los niños mal Estados. Este porcentaje procede de la directiva en función del tipo de entidad y secundarios.

- Si todos los elementos secundarios tienen estados Aceptar, el estado de mantenimiento de agregados secundarios es correcto.

- Si los niños tienen Aceptar y Estados de advertencia, el estado de mantenimiento de agregados secundarios es advertencia.

- Si hay elementos secundarios con estados de error que no respetan el máximo permitido de porcentaje de elementos secundarios mal Estados, el estado de mantenimiento agregado es un error.

- Si los elementos secundarios con estados de error respetan el porcentaje máximo permitido de los niños mal Estados, el estado de mantenimiento agregado es advertencia.

## <a name="health-reporting"></a>Informes de estado
Componentes del sistema, las aplicaciones de sistema tela y watchdogs interna o externa pueden informar contra entidades de tela de servicio. Los redactores realizar decisiones *local* del estado de las entidades supervisadas, en función de las condiciones que va a supervisar. No necesitan ver cualquier estado global o agregar datos. El comportamiento deseado es que redactores simples y organismos no complejos que deben mirar muchas cosas para deducir qué información se debe enviar.

Para enviar los datos de estado a la tienda de mantenimiento, un reporter debe identificar la entidad afectada y crear un informe de estado. El informe, a continuación, se envía mediante la API usando [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), PowerShell o resto.

### <a name="health-reports"></a>Informes de estado
Los [informes de estado](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) para cada una de las entidades de clúster contienen la información siguiente:

- **SourceId**. Una cadena que identifica el redactor del evento de estado.

- **Identificador de la entidad**. Identifica la entidad donde se aplica el informe. Varía en función de la [entidad escriba](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Clúster. Ninguno.

  - Nodo. Nombre de nodo (cadena).

  - Aplicación. Nombre de la aplicación (URI). Representa el nombre de la instancia de aplicación implementada en el clúster.

  - Servicio. Nombre de servicio (URI). Representa el nombre de la instancia de servicio implementado en el clúster.

  - Partición. Id. de partición (GUID). Representa el identificador único de partición.

  - Réplica. El identificador de réplica con estado del servicio o el identificador de instancia de estado del servicio (INT64).

  - DeployedApplication. Nombre de la aplicación (URI) y el nombre de nodo (cadena).

  - DeployedServicePackage. Nombre de la aplicación (URI), el nombre de nodo (cadena) y servicio de manifiestos nombre (cadena).

- **Propiedad**. *Cadena* (no una enumeración fija) que permite el redactor clasificar el evento de estado de una propiedad específica de la entidad. Por ejemplo, reporter A puede informar que el estado de la propiedad Node01 "almacenamiento" y reporter B puede informar del estado de la propiedad Node01 "conectividad". En el almacén de estado, estos informes se tratan como eventos de estado independiente de la entidad Node01.

- **Descripción**. Una cadena que permite una reporter proporcionar información detallada sobre el evento de estado. **SourceId**, **propiedad**y **HealthState** deberían describir completamente el informe. La descripción agrega información legible sobre el informe. El texto facilita a los administradores y usuarios a comprender el informe de estado.

- **HealthState**. [Enumeración](service-fabric-health-introduction.md#health-states) que describe el estado de mantenimiento del informe. Los valores aceptados son Aceptar, advertencia y Error.

- **Período de Vida**. Intervalo de tiempo que indica cuánto tiempo es válido el informe de estado. Junto con **RemoveWhenExpired**, permite la tienda de salud saber cómo evaluar eventos expirados. De forma predeterminada, el valor es infinito y el informe es válido para siempre.

- **RemoveWhenExpired**. Un valor Boolean. Si se eliminan automáticamente establecida en true, el informe de estado de expiración de la tienda de salud y el informe no afecta evaluación de mantenimiento de la entidad. Usar cuando el informe es válido durante un período específico de tiempo solo y notificador no es necesario que desactive explícitamente. También se utiliza para eliminar los informes de la tienda de estado (por ejemplo, a continuación se proporciona cambia y deja de enviar informes con origen anterior y propiedades). Puede enviar un informe con un período de vida breve junto con RemoveWhenExpired para eliminar cualquier estado anterior desde el almacén de estado. Si el valor se establece en Falso, el informe caducado se trata como un error en la evaluación de mantenimiento. Las señales de valor false para el almacén de estado del origen debe informar periódicamente en esta propiedad. Si no es así, a continuación, debe haber algún error con vigilar. Estado de vigilar se genera teniendo en cuenta el evento como un error.

- **SequenceNumber**. Un entero positivo que debe ser creciente, que representa el orden de los informes. Se usa el almacén de estado para detectar desfasados informes que se reciben más tarde debido a retrasos en la red u otros problemas. Un informe se rechaza si el número de secuencia es que menor o igual que la mayoría aplicada recientemente número para la misma entidad, el origen y la propiedad. Si no se especifica, el número se genera automáticamente. Es necesario colocar en el número de secuencia solo al informe de transiciones de estado. En este caso, el origen debe recordar que envían los informes y mantener la información de recuperación de migración tras error.

Estos cuatro partes de la información: SourceId, identificador de entidad, propiedades y HealthState--son necesarios para todos los informes de estado. SourceId cadena no puede comenzar con el prefijo "**del sistema.**", que está reservado para el sistema de informes. Para la misma entidad, hay solo un informe para el mismo origen y la propiedad. Varios informes para el mismo origen y propiedad reemplazan entre sí, en estado del cliente (si se procesan por lotes) o en el estado del almacén. La sustitución se basa en números de secuencia; informes más recientes (con números de secuencia superiores) reemplazar informes anteriores.

### <a name="health-events"></a>Eventos de estado
Internamente, el almacén de estado mantiene [eventos de estado](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), que contienen toda la información de los informes y metadatos adicionales. Los metadatos incluyen el tiempo que se ha proporcionado el informe al cliente de estado y la hora en que se modificó en el servidor. Los eventos de estado se devuelven por [las consultas de estado](service-fabric-view-entities-aggregated-health.md#health-queries).

Contienen los metadatos agregado:

- **SourceUtcTimestamp**. El informe se ha proporcionado el tiempo en el cliente de estado (hora Universal).

- **LastModifiedUtcTimestamp**. La hora en que se modificó por última vez el informe en el servidor (hora Universal).

- **IsExpired**. Una marca para indicar si el informe se ha expirado cuando la consulta se ejecuta el almacén de estado. Un evento puede ser expirado solo si RemoveWhenExpired es false. En caso contrario, el evento no devuelta por la consulta y se quita de la tienda.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. La última vez para transiciones de error, advertencia o Aceptar. Estos campos ofrecen el historial de la salud transiciones de estado para el evento.

Los campos de la transición de estado se pueden utilizar para las alertas de manera más inteligentes o la información de eventos de estado "histórica". Habilitan escenarios como:

- Recibir una alerta cuando una propiedad se ha en advertencia o error de más de X minutos. Comprobación de la condición durante un período de tiempo evita alertas en condiciones temporales. Por ejemplo, puede traducirse una alerta si el estado de mantenimiento ha sido advertencia durante más de cinco minutos (HealthState == advertencia y ahora - LastWarningTransitionTime > 5 minutos).

- Alerta solo de las condiciones que han cambiado en los últimos X minutos. Si un informe ya estaba error antes de la hora especificada, puede omite porque ya se ha señalado anteriormente.

- Si una propiedad es alternar entre advertencia y error, determinar cuánto ha sido mal estado (es decir, no Aceptar). Por ejemplo, puede traducirse una alerta si la propiedad no se ha buen estada durante más de cinco minutos (HealthState! = Aceptar y ahora - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Ejemplo: Informe y evaluar el estado de la aplicación
En el ejemplo siguiente se envía un informe de estado a través de PowerShell en la aplicación **tela: / WordCount** del origen **MyWatchdog**. El informe de estado contiene información acerca de la propiedad de estado "disponibilidad" en un estado de error, con el período de vida infinito. A continuación, consulta el estado de la aplicación, que devuelve habían agregado errores de estado de salud y los eventos de estado notificado en la lista eventos de estado.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Uso del modelo de estado
El modelo de mantenimiento permite servicios en la nube y la plataforma de servicio tela subyacente escalar, porque se distribuye determinación de supervisión y estado entre los distintos monitores dentro del clúster.
Otros sistemas de tengan un servicio único centralizado en el nivel de clúster que analiza toda la *potencialmente* información útil emitida por servicios. Este enfoque dificulta su escalabilidad. También no permitirles recopilar información muy específica para ayudar a identificar problemas y posibles problemas como cerca de la causa posible.

El modelo de estado se usa intensamente para supervisión y diagnóstico, para evaluar el estado de clúster y la aplicación y actualizaciones supervisadas. Otros servicios de usar datos de estado para realizar reparaciones automáticas, generar historial de estado de clúster y emitir alertas en determinadas condiciones.

## <a name="next-steps"></a>Pasos siguientes
[Ver informes de mantenimiento de tela de servicio](service-fabric-view-entities-aggregated-health.md)

[Usar informes de estado del sistema para solucionar este problema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Cómo informar y comprobar el estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Agregar los informes de estado de servicio tela personalizados](service-fabric-report-health.md)

[Supervisar y diagnosticar servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de servicio tela](service-fabric-application-upgrade.md)
