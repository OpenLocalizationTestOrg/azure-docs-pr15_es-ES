<properties
    pageTitle="Soporte de administrador de recursos para el Administrador de tráfico Azure | Microsoft Azure "
    description="Usar PowerShell para el Administrador de tráfico con el Administrador de recursos Azure"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Compatibilidad con el Administrador de recursos Azure para el administrador del tráfico de Azure

Administrador de recursos de Azure es la interfaz de administración preferido para los servicios de Azure. Perfiles de administrador de tráfico Azure pueden administrarse mediante herramientas y API basadas en el Administrador de recursos de Azure.

## <a name="resource-model"></a>Modelo de recursos

Administrador de tráfico Azure está configurado con un conjunto de valores denominado un perfil de administrador de tráfico. Este perfil contiene la configuración de DNS, configuración de enrutamiento de tráfico, configuración de supervisión de punto final y una lista de los extremos de servicio al que se enruta el tráfico.

Cada perfil de administrador de tráfico está representado por un recurso de tipo 'TrafficManagerProfiles'. En el nivel de la API de REST, el URI de cada perfil es como sigue:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Comparación con la API clásica del Administrador de tráfico de Azure

La compatibilidad del Administrador de recursos de Azure para el Administrador de tráfico utiliza terminología diferente a la del modelo de implementación clásico. La siguiente tabla muestra las diferencias entre los términos de administrador de recursos y estándar:

| Términos de administrador de recursos | Términos clásica |
|-----------------------|--------------|
| Método de enrutamiento de tráfico | Método de equilibrio de carga |
| Método de prioridad | Método de migración tras error |
| Método ponderada | Método de turnos |
| Método de rendimiento | Método de rendimiento |

En función de los comentarios de los clientes, hemos cambiado la terminología para mejorar la claridad y reducir inconvenientes comunes. No hay ninguna diferencia de funcionalidad.

## <a name="limitations"></a>Limitaciones

Al hacer referencia a un extremo de tipo 'AzureEndpoints' para una aplicación Web, Administrador de tráfico extremos sólo pueden hacer referencia el valor predeterminado (producción) [franja de Web App](../app-service-web/web-sites-staged-publishing.md). Ranuras personalizados no son compatibles. Como solución alternativa, pueden configurar ranuras personalizados utilizando el tipo de 'ExternalEndpoints'.

## <a name="setting-up-azure-powershell"></a>Configuración de PowerShell de Azure

Estas instrucciones use Microsoft Azure PowerShell. El siguiente artículo explica cómo instalar y configurar Azure PowerShell.

- [Cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md)

Los ejemplos de este artículo se suponen que tiene un grupo de recursos existente. Puede crear un grupo de recursos con el siguiente comando:

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Administrador de recursos de Azure requiere que todos los grupos de recursos tienen una ubicación. Esta ubicación se usa como el valor predeterminado para los recursos creados en ese grupo de recursos. Sin embargo, dado que global, no regional recursos de perfil de administrador de tráfico, la opción de ubicación de grupo de recursos no tiene efecto en el administrador del tráfico de Azure.

## <a name="create-a-traffic-manager-profile"></a>Crear un perfil de administrador de tráfico

Para crear un perfil de administrador de tráfico, use el cmdlet AzureRmTrafficManagerProfile nuevo:

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

La siguiente tabla describe los parámetros:

| Parámetro | Descripción |
|-----------|-------------|
| Nombre | El nombre del recurso para el recurso de perfil de administrador de tráfico. Perfiles en el mismo grupo de recursos deben tener nombres únicos. Este nombre es el nombre DNS usado para las consultas DNS independiente.|
| ResourceGroupName | El nombre del grupo de recursos que contiene el recurso de perfil.|
| TrafficRoutingMethod | Especifica el método de enrutamiento de tráfico que se utiliza para determinar el extremo que se devuelve como respuesta de una consulta DNS. Los valores posibles son 'Rendimiento', 'Weighted' o 'Priority'.|
| RelativeDnsName | Especifica la parte del nombre de host del nombre DNS proporcionada por este perfil de administrador de tráfico. Este valor se combina con el nombre de dominio DNS utilizado por el administrador del tráfico de Azure para formar el nombre de dominio completo (FQDN) del perfil. Por ejemplo, el valor de 'contoso' se convierte en 'contoso.trafficmanager.net'.|
| TTL | Especifica el DNS tiempo de vida (TTL) en segundos. Este TTL informa a los clientes DNS cuánto tiempo a las respuestas DNS de caché para este perfil de administrador de tráfico y resoluciones DNS Local.|
| MonitorProtocol | Especifica el protocolo que utilice para supervisar el mantenimiento de extremo. Los valores posibles son 'HTTP' y 'HTTPS'.|
| MonitorPort | Especifica el puerto TCP utilizado para controlar el estado de extremo.|
| MonitorPath | Especifica la ruta de acceso en relación con el nombre de dominio de extremo utilizado para sondeo de estado del extremo.|

El cmdlet crea un perfil de administrador de tráfico en Azure y devuelve un objeto de perfil correspondiente a PowerShell. En este momento, el perfil no contiene los extremos. Para obtener más información sobre cómo agregar extremos a un perfil de administrador de tráfico, consulte [Agregar extremos de administrador de tráfico](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Obtener un perfil de administrador de tráfico

Para recuperar un objeto de perfil de administrador de tráfico existente, use el cmdlet Get-AzureRmTrafficManagerProfle:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Este cmdlet devuelve un objeto de perfil de administrador de tráfico.

## <a name="update-a-traffic-manager-profile"></a>Actualizar un perfil de administrador de tráfico

Modificar perfiles de administrador de tráfico sigue un proceso paso 3:

1. Recuperar el perfil usando Get-AzureRmTrafficManagerProfile o use el perfil devuelto por AzureRmTrafficManagerProfile de nuevo.
2. Modificar el perfil. Puede agregar y quitar extremos o cambiar los parámetros del extremo o perfil. Estos cambios son operaciones fuera de línea. Solo se cambia el objeto local en la memoria que representa el perfil.
3. Confirmar los cambios mediante el cmdlet Set-AzureRmTrafficManagerProfile.

Pueden cambiar todas las propiedades de perfil excepto RelativeDnsName del perfil. Para cambiar el RelativeDnsName, debe eliminar perfil y un perfil nuevo con un nombre nuevo.

En el ejemplo siguiente se muestra cómo cambiar el período de vida del perfil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Existen tres tipos de extremos de tráfico administrador:

1. **Los extremos de Azure** son servicios alojados en Azure
2. **Extremos externos** son servicios hospedados fuera de Azure
3. **Extremos anidadas** se usan para crear jerarquías anidadas de perfiles de administrador de tráfico. Extremos anidadas Habilitar configuraciones avanzadas de enrutamiento de tráfico para aplicaciones complejas.

En los tres casos, se pueden agregar extremos de dos maneras:

1. Usar un proceso de paso 3 descrito anteriormente. La ventaja de este método es que pueden ser realizado varios cambios de punto final en una sola actualización.
2. Usar el cmdlet AzureRmTrafficManagerEndpoint de nuevo. Este cmdlet, agrega un punto final a un perfil de administrador de tráfico existente en una única operación.

## <a name="adding-azure-endpoints"></a>Agregar extremos de Azure

Extremos Azure referencia servicios alojados en Azure. Tres tipos de Azure extremos son compatibles:

1. Aplicaciones Web de Azure
2. Servicios (que pueden contener un servicio PaaS o máquinas virtuales de IaaS) de nube 'Clásica'
3. Recursos de Azure PublicIpAddress (que pueden estar asociados a un equilibrador de carga o una máquina virtual NIC). El PublicIpAddress debe tener un nombre DNS asignado que se utilizarán en el Administrador de tráfico.

En cada caso:

- El servicio se especifica mediante el parámetro 'targetResourceId' de agregar AzureRmTrafficManagerEndpointConfig o AzureRmTrafficManagerEndpoint de nuevo.
- La TargetResourceId implica la 'Destino' y 'EndpointLocation'.
- Especificar el 'peso' es opcional. Grosores solo se usan si el perfil está configurado para usar el método de enrutamiento de tráfico 'Weighted'. En caso contrario, se omiten. Si se especifica, el valor debe ser un número comprendido entre 1 y 1000. El valor predeterminado es '1'.
- Especificar la 'Priority' es opcional. Las prioridades se utilizan sólo si el perfil está configurado para usar el método de enrutamiento de tráfico 'Priority'. En caso contrario, se omiten. Los valores válidos son de 1 a 1000 con los valores más bajos que indica una prioridad más alta. Si especifica un extremo, deben especificarse para todos los extremos. Si se omite, se aplican los valores predeterminados a partir de '1' en el orden en que se enumeran los extremos.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Ejemplo 1: Agregar extremos de aplicación Web mediante Agregar AzureRmTrafficManagerEndpointConfig

En este ejemplo, creamos un perfil de administrador de tráfico y agregar los dos extremos de aplicación Web mediante el cmdlet Add-AzureRmTrafficManagerEndpointConfig.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Ejemplo 2: Agregar un extremo de servicio de nube 'clásica' mediante AzureRmTrafficManagerEndpoint de nuevo

En este ejemplo, se agrega un extremo de servicio de nube 'clásico' a un perfil de administrador de tráfico. En este ejemplo, hemos especificado el perfil con los nombres de grupo de recursos y de perfil, en lugar de pasar un objeto de perfil. Ambos métodos son compatibles.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Ejemplo 3: Agregar un extremo de publicIpAddress mediante AzureRmTrafficManagerEndpoint de nuevo

En este ejemplo, se agrega un recurso de dirección IP público del perfil de administrador de tráfico. La dirección IP pública debe tener un nombre DNS configurado y puede estar enlazada al NIC de una máquina virtual o a un equilibrador de carga.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Agregar extremos externos

Administrador de tráfico usa extremos externos para dirigir el tráfico a servicios hospedado fuera de Azure. Al igual que con los extremos de Azure, extremos externos pueden agregarse mediante Add-AzureRmTrafficManagerEndpointConfig seguido de conjunto AzureRmTrafficManagerProfile o AzureRMTrafficManagerEndpoint de nuevo.

Cuando se especifican los extremos externos:

- El nombre de dominio de extremo debe especificarse con el parámetro 'Destino'
- Si se utiliza el método de enrutamiento de tráfico de 'Rendimiento', 'EndpointLocation' es necesario. En caso contrario, es opcional. El valor debe ser un [nombre de la región de Azure válido](https://azure.microsoft.com/regions/).
- El 'grosor de ' y 'Priority' son opcionales.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Ejemplo 1: Agregar extremos externos con agregar AzureRmTrafficManagerEndpointConfig y establecer AzureRmTrafficManagerProfile

En este ejemplo, se cree un perfil de administrador de tráfico, agregue dos extremos externos y confirme los cambios.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Ejemplo 2: Agregar extremos externos con el nuevo AzureRmTrafficManagerEndpoint

En este ejemplo, se agrega un extremo externo a un perfil existente. El perfil se especifica mediante los nombres de grupo de perfil y recursos.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Agregar extremos 'Anidadas'

Cada perfil de administrador de tráfico especifica un solo método de enrutamiento de tráfico. Sin embargo, hay situaciones que requieren enrutamiento más complejas del tráfico de la ruta proporcionada por un solo perfil de administrador de tráfico. Puede anidar perfiles de administrador de tráfico para combinar las ventajas de más de un método de enrutamiento de tráfico. Perfiles anidados permiten invalidar el comportamiento de tráfico administrador predeterminado para soporte más grande y más complejas implementaciones de aplicación. Para obtener ejemplos más detallados, vea [perfiles de administrador de tráfico anidados](traffic-manager-nested-profiles.md).

Extremos anidados se configuran en el perfil de elemento primario, con un tipo de extremo específico, 'NestedEndpoints'. Cuando se especifican los extremos anidados:

- El extremo debe especificarse con el parámetro 'targetResourceId'
- Si se utiliza el método de enrutamiento de tráfico de 'Rendimiento', 'EndpointLocation' es necesario. En caso contrario, es opcional. El valor debe ser un [nombre de la región de Azure válido](http://azure.microsoft.com/regions/).
- El 'grosor de ' y 'Priority' son opcionales, como puntos finales de Azure.
- El parámetro 'MinChildEndpoints' es opcional. El valor predeterminado es '1'. Si el número de extremos disponibles está por debajo de este umbral, el perfil primario considera el perfil secundarios 'Degradado' y desvía el tráfico a los otros extremos en el perfil de primario.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Ejemplo 1: Agregar extremos anidadas con agregar AzureRmTrafficManagerEndpointConfig y establecer AzureRmTrafficManagerProfile

En este ejemplo, se crear nueva primarios y secundarios de tráfico administrador de perfiles, agrega al elemento secundario como un extremo anidado al elemento primario y confirme los cambios.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Por razones de brevedad en este ejemplo, no ha agregado los otros extremos a los perfiles secundario o principal.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Ejemplo 2: Agregar extremos anidadas con AzureRmTrafficManagerEndpoint de nuevo

En este ejemplo, agregamos un perfil secundarios existente como un extremo anidado a un perfil existente del elemento primario. El perfil se especifica mediante los nombres de grupo de perfil y recursos.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Actualizar un extremo de tráfico de administrador

Hay dos formas de actualizar un extremo de tráfico administrador existente:

1. Obtener el perfil de administrador de tráfico mediante Get-AzureRmTrafficManagerProfile, actualice las propiedades del extremo en el perfil y confirme los cambios con el conjunto AzureRmTrafficManagerProfile. Este método tiene la ventaja de poder actualizar más de un extremo en una única operación.
2. Obtener el extremo de administrador de tráfico mediante Get-AzureRmTrafficManagerEndpoint, actualice las propiedades de extremo y confirme los cambios con el conjunto AzureRmTrafficManagerEndpoint. Este método es más fácil, ya que no requiere la indización en la matriz de extremos en el perfil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Ejemplo 1: Actualizar extremos mediante Get-AzureRmTrafficManagerProfile y establecer AzureRmTrafficManagerProfile

En este ejemplo, se modifique la prioridad de dos extremos dentro de un perfil existente.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Ejemplo 2: Actualizar un extremo con Get-AzureRmTrafficManagerEndpoint y establecer AzureRmTrafficManagerEndpoint

En este ejemplo, se modifique el grosor de un extremo único en un perfil existente.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Habilitar o deshabilitar extremos y perfiles

Administrador de tráfico permite extremos individuales esté activado y desactivado, además de permitir habilitar y deshabilitar perfiles todos.
Estos cambios pueden realizarse por cómo obtener, actualizar o establecer los recursos extremo o perfil. Para facilitar estas operaciones comunes, también se admiten a través de los cmdlets dedicado.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Ejemplo 1: Habilitar y deshabilitar un perfil de administrador de tráfico

Para habilitar un perfil de administrador de tráfico, use AzureRmTrafficManagerProfile habilitar. Puede especificar el perfil mediante un objeto de perfil. El objeto de perfil se puede pasar a través de la canalización o mediante el '-TrafficManagerProfile' parámetro. En este ejemplo, hemos especificar el perfil por el nombre de grupo de perfil y recursos.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para deshabilitar un perfil de administrador de tráfico:

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

El cmdlet Disable AzureRmTrafficManagerProfile solicita confirmación. Este aviso se puede suprimir mediante la '-forzar ' parámetro.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Ejemplo 2: Activar y desactivar un extremo de tráfico administrador

Para habilitar un extremo de administrador de tráfico, use AzureRmTrafficManagerEndpoint habilitar. Hay dos formas de especificar el punto final

1. Utilizando un objeto de TrafficManagerEndpoint pasado a través de la canalización o el '-TrafficManagerEndpoint' parámetro
2. Usar el nombre del extremo de, el tipo de extremo, el nombre del perfil y el nombre del grupo de recursos:

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Del mismo modo, para deshabilitar un extremo de tráfico administrador:

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Al igual que con deshabilitar-AzureRmTrafficManagerProfile, el cmdlet deshabilitar AzureRmTrafficManagerEndpoint solicita confirmación. Este aviso se puede suprimir mediante la '-forzar ' parámetro.

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminar un extremo de tráfico de administrador

Para quitar los extremos individuales, use el cmdlet AzureRmTrafficManagerEndpoint quitar:

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Este cmdlet le pide confirmación. Este aviso se puede suprimir mediante la '-forzar ' parámetro.

## <a name="delete-a-traffic-manager-profile"></a>Eliminar un perfil de administrador de tráfico

Para eliminar un perfil de administrador de tráfico, use el cmdlet de quitar AzureRmTrafficManagerProfile, especificar los nombres de grupo de perfil y recursos:

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Este cmdlet le pide confirmación. Este aviso se puede suprimir mediante la '-forzar ' parámetro.

También se puede especificar el perfil que se va a eliminar mediante un objeto de perfil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

También se puede por departamento en esta secuencia:

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Pasos siguientes

[Administrador de tráfico de supervisión](traffic-manager-monitoring.md)

[Consideraciones de rendimiento del Administrador de tráfico](traffic-manager-performance-considerations.md)
