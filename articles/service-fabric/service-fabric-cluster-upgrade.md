<properties
   pageTitle="Actualizar un clúster de tela de servicio de Azure | Microsoft Azure"
   description="Actualizar el código de servicio tela o la configuración que se ejecuta un clúster de tela de servicio, incluida la configuración de modo de actualización de clúster, actualizar certificados, agregar puertos de la aplicación, realice correcciones de sistemas operativos, y así sucesivamente. ¿Qué sucederá cuando se realizan las actualizaciones?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Actualizar un clúster de tela de servicio de Azure

> [AZURE.SELECTOR]
- [Clúster de Azure](service-fabric-cluster-upgrade.md)
- [Clúster independiente](service-fabric-cluster-upgrade-windows-server.md)

Para cualquier sistema moderna, diseño para la actualización es clave para tener éxito a largo plazo de su producto. Un clúster de tela de servicio de Azure es un recurso que el propietario, pero parcialmente es administrada por Microsoft. Este artículo describe lo que se administra automáticamente y lo que puede configurar usted mismo.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar la versión de tela que se ejecuta en el clúster

Puede configurar el clúster para recibir actualizaciones automáticas tela, cuando Microsoft publica una versión nueva o seleccionar una versión compatible tela que desea en el clúster.

Para ello, establecer la configuración del clúster "upgradeMode" en el portal o mediante el Administrador de recursos en el momento de creación o versiones posteriores en un clúster de live 

>[AZURE.NOTE] Asegúrese de mantener el clúster que se ejecute una versión compatible de tela siempre. Como y cuando se anunciar el lanzamiento de una nueva versión de tela de servicio, la versión anterior se marca de fin de soporte después de un mínimo de 60 días de la fecha. las nuevas versiones están anunciada [en el blog del equipo de tela de servicio](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nueva versión está disponible para, a continuación, elija. 

14 días antes de la expiración de la versión que se está ejecutando el clúster, estado se genera un evento que coloca el clúster en un estado de advertencia. El clúster permanece en un estado de advertencia hasta que actualice a una versión compatible de tela.


### <a name="setting-the-upgrade-mode-via-portal"></a>Establecer el modo de actualización a través del portal 

Puede establecer el clúster en automática o manual cuando se crea el clúster.

![Create_Manualmode][Create_Manualmode]

Puede configurar el clúster para automática o manual cuando se encuentra en un clúster directo, utilizando la experiencia de administrar. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Actualizar a una nueva versión en un clúster que se establece en modo Manual a través del portal.
 
Para actualizar a una nueva versión, todo lo que necesita hacer es seleccionar la versión disponible en la lista desplegable y guardar. La actualización de tela obtiene dio automáticamente. Las directivas de mantenimiento de clúster (una combinación de nodo salud y la salud todas las aplicaciones que se ejecutan en el clúster) se cumplen durante la actualización.

Si no se cumplen las directivas de mantenimiento de clúster, se deshace la actualización. Desplácese hacia abajo por este documento para obtener más información sobre cómo configurar las directivas de estado personalizado. 

Una vez que se han corregido los problemas que ha generado la restauración, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Establecer el modo de actualización a través de una plantilla de administrador de recursos 

Agregar la configuración de "upgradeMode" a la definición de recursos de Microsoft.ServiceFabric/clusters y establecer "clusterCodeVersion" a una de las versiones de tela compatibles, como se muestra a continuación y, a continuación, implementar la plantilla. Los valores válidos para "upgradeMode" son "Manual" o "Automático"
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Actualizar a una nueva versión en un clúster que se establece en modo Manual a través de una plantilla de administrador de recursos.
 
Cuando el clúster está en modo Manual, para actualizar a una nueva versión, cambie la "clusterCodeVersion" a una versión compatible e implementarlo. Implementación de la plantilla, se activa de la actualización de tela obtiene dio automáticamente. Las directivas de mantenimiento de clúster (una combinación de nodo salud y la salud todas las aplicaciones que se ejecutan en el clúster) se cumplen durante la actualización.

Si no se cumplen las directivas de mantenimiento de clúster, se deshace la actualización. Desplácese hacia abajo por este documento para obtener más información sobre cómo configurar las directivas de estado personalizado. 

Una vez que se han corregido los problemas que ha generado la restauración, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obtener la lista de todas las versiones disponibles para todos los entornos de una suscripción determinada

Ejecute el siguiente comando y obtendrá un resultado similar al siguiente.

"supportExpiryUtc" indica el cuando una versión determinada va a expirar o ha expirado. La versión más reciente no tiene una fecha válida, tiene un valor de "9999-12-31T23:59:59.9999999", que significa que la fecha de caducidad no está establecida todavía.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportamiento de actualización de tela cuando el modo de actualización de clúster es automático

Microsoft mantiene la configuración que se ejecute en un clúster de Azure y el código de tela. Realizar actualizaciones automáticas de supervisados al software según sea necesario. Estas actualizaciones podrían ser código, configuración o ambos. Para asegurarse de que la aplicación se ve afectado sin impacto o impacto mínimo debido a estas actualizaciones, realizar las actualizaciones en las siguientes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Una actualización se realiza con todas las directivas de mantenimiento de clúster

Durante esta fase, las actualizaciones durante el proceso de un dominio de actualización en un momento y continuarán con las aplicaciones que se ejecutan en el clúster para ejecutarse sin ningún tiempo de inactividad. Las directivas de mantenimiento de clúster (una combinación de nodo salud y la salud todas las aplicaciones que se ejecutan en el clúster) se cumplen durante la actualización.

Si no se cumplen las directivas de mantenimiento de clúster, se deshace la actualización. A continuación, se envía un correo electrónico al propietario de la suscripción. El correo electrónico contiene la siguiente información:

- Notificación que tenemos revertir una actualización de clúster.
- Sugerido acciones para solucionarlos, si corresponde.
- El número de días (n) hasta que se ejecute la fase 2.

Intentar ejecutar el mismo upgrade algunas veces más en caso de que las actualizaciones no se pudo por motivos de infraestructura. Después de la n días desde la fecha en que se envió el correo electrónico, proceder a la fase 2.

Si se cumplen las directivas de mantenimiento de clúster, la actualización se considera satisfactoria y se marca como completada. Esto puede suceder durante la actualización inicial o cualquiera de los vuelve a ejecutar la actualización en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta. Esto es para evitar enviar que demasiados mensajes de correo electrónico, recibe un correo electrónico debe aparecer como una excepción normal. Esperamos que la mayoría de las actualizaciones de clúster correctamente sin afectar a la disponibilidad de las aplicaciones.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Una actualización se realiza con solo las directivas de mantenimiento de forma predeterminada

Las directivas de mantenimiento en esta fase se establecen de manera que el número de aplicaciones que estaban en buen estado al principio de la actualización de la misma permanece durante el proceso de actualización. Como en la fase 1, las actualizaciones de la fase 2 durante el proceso de un dominio de actualización en un momento y continuarán con las aplicaciones que se ejecutan en el clúster para ejecutarse sin ningún tiempo de inactividad. Las directivas de mantenimiento de clúster (una combinación de nodo salud y la salud todas las aplicaciones que se ejecutan en el clúster) se cumplen para la duración de la actualización.

Si las directivas de mantenimiento de clúster no se cumplen en vigor, se deshace la actualización. A continuación, se envía un correo electrónico al propietario de la suscripción. El correo electrónico contiene la siguiente información:

- Notificación que tenemos revertir una actualización de clúster.
- Sugerido acciones para solucionarlos, si corresponde.
- El número de días (n) hasta que se ejecute la fase 3.

Intentar ejecutar el mismo upgrade algunas veces más en caso de que las actualizaciones no se pudo por motivos de infraestructura. Un correo electrónico de aviso se envía un par de días antes n días. Después de la n días desde la fecha en que se envió el correo electrónico, continuar a la fase 3. Los mensajes de correo electrónico que le enviaremos en la fase 2 deben tomará con seriedad y deben realizarse acciones correctoras.

Si se cumplen las directivas de mantenimiento de clúster, la actualización se considera satisfactoria y se marca como completada. Esto puede suceder durante la actualización inicial o cualquiera de los vuelve a ejecutar la actualización en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Una actualización se realiza con las directivas de mantenimiento agresiva

Estas directivas de mantenimiento en esta fase están dirigidas a la finalización de la actualización en lugar de en el estado de las aplicaciones. Actualizaciones de clúster muy pocos terminen en esta fase. Si obtiene el clúster para esta fase, hay una buena posibilidad de que la aplicación se convierte en mal estada o perder disponibilidad.

Fase 3 actualizaciones similares a las otras dos fases, continúe un dominio de actualización en un momento.

Si no se cumplen las directivas de mantenimiento de clúster, se deshace la actualización. Intentar ejecutar el mismo upgrade algunas veces más en caso de que las actualizaciones no se pudo por motivos de infraestructura. Después, el clúster está anclado, para que ya no recibirá soporte técnico y actualizaciones.

Se envía un correo electrónico con esta información al propietario de la suscripción, junto con las acciones para solucionarlos. Esperamos que no los clústeres para llegar a un estado donde ha fallado fase 3.

Si se cumplen las directivas de mantenimiento de clúster, la actualización se considera satisfactoria y se marca como completada. Esto puede suceder durante la actualización inicial o cualquiera de los vuelve a ejecutar la actualización en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta.

## <a name="cluster-configurations-that-you-control"></a>Configuraciones de clúster que puede controlar

Además de la capacidad de configurar el clúster de actualización modo, estas son las configuraciones que puede cambiar en un clúster de live.

### <a name="certificates"></a>Certificados

Puede agregar nuevos o eliminar los certificados para el cliente a través del portal y clúster fácilmente. Consulte [este documento para obtener instrucciones detalladas](service-fabric-cluster-security-update-certs-azure.md)

![Captura de pantalla que muestra los certificados de huella digital en el portal de Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Puertos de la aplicación

Puede cambiar puertos de la aplicación cambiando las propiedades del recurso equilibrador de carga que están asociadas con el tipo de nodo. Puede usar el portal o puede usar el Administrador de recursos PowerShell directamente.

Para abrir un nuevo puerto en todas las máquinas virtuales en un tipo de nodo, haga lo siguiente:

1. Agregar un nuevo sondeo al equilibrador de carga correspondiente.

    Si implementa el clúster a través del portal, los equilibradores de carga se denominan "Kg-nombre de la NodeTypename de grupo de recursos", uno para cada tipo de nodo. Puesto que los nombres de equilibrador de carga son exclusivos sólo dentro de un grupo de recursos, es mejor que una búsqueda en un grupo de recursos específico.

    ![Captura de pantalla que muestra la adición de un sondeo a un equilibrador de carga en el portal.][AddingProbes]

2. Agregar una nueva regla al equilibrador de carga.

    Agregar una nueva regla al mismo equilibrador de carga mediante el sondeo que creó en el paso anterior.

    ![Agregar una nueva regla a un equilibrador de carga en el portal.][AddingLBRules]


### <a name="placement-properties"></a>Propiedades de colocación

Para cada uno de los tipos de nodo, puede agregar propiedades de colocación personalizada que desea usar en las aplicaciones. NodeType es una propiedad predeterminada que se puede usar sin agregarla explícitamente.

>[AZURE.NOTE] Para obtener información detallada sobre el uso de las restricciones de posición, propiedades de nodo y cómo se definen, consulte la sección "Restricciones y nodo Propiedades de colocación" en el documento de administrador de recursos de clúster de tela de servicio en la [Que describe el clúster](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Métrica de capacidad

Para cada uno de los tipos de nodo, puede agregar métricas de capacidad personalizado que desea usar en las aplicaciones de carga del informe. Para obtener información detallada sobre el uso de las estadísticas de capacidad al informe de carga, consulte los documentos de administrador de recursos de clúster de tela de servicio en la [Que describe el clúster](service-fabric-cluster-resource-manager-cluster-description.md) y [métricas y carga](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Configuración de actualización de tela - las directivas de mantenimiento

Puede especificar directivas de estado personalizados para la actualización de tela. Si ha establecido el clúster a las actualizaciones automáticas tela, estas directivas se aplican a la fase 1 de las actualizaciones automáticas tela.
Si ha establecido el clúster por tela Manual de actualizaciones, estas directivas se aplican cada vez que seleccione una nueva versión desencadenante del sistema para empezar la actualización de tela en el clúster. Si no se reemplaza las directivas, se usan los valores predeterminados.

Puede especificar las directivas de estado personalizados o revisar la configuración actual en el módulo "actualización tela", seleccionando la configuración avanzada de actualización. Revise la siguiente imagen sobre cómo. 

![Administrar directivas de estado personalizados][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar la configuración de tela para el clúster

Consulte [Configuración del servicio tela clúster tela](service-fabric-cluster-fabric-settings.md) en qué y cómo se puede personalizar.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Revisiones de sistema operativo en las máquinas virtuales que constituyen el clúster

Esta capacidad está planificada para el futuro como un automática. Pero actualmente, es responsable de sus máquinas virtuales de revisiones. Debe hacer este una VM a la vez, para que no tenga hacia abajo más de uno en uno.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Actualizaciones de sistema operativo en las máquinas virtuales que constituyen el clúster

Si necesita actualizar la imagen del SO en los equipos virtuales del clúster, se debe hacer una VM a la vez. Usted es responsable esta actualización--no hay actualmente ningún automatización para este.

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a personalizar parte de la [Configuración del servicio tela clúster tela](service-fabric-cluster-fabric-settings.md)
- Obtenga información sobre cómo [ajustar el clúster de entrada y salida](service-fabric-cluster-scale-up-down.md)
- Obtenga más información sobre [las actualizaciones de la aplicación](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG