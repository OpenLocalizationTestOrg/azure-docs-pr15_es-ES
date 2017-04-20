<properties
   pageTitle="Solucionar problemas de la puerta de enlace de aplicación puerta de enlace incorrecta (502) | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar errores de 502 puerta de enlace de aplicación"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solución de errores de puerta de enlace incorrecta de puerta de enlace de aplicación

## <a name="overview"></a>Información general

Después de configurar una puerta de enlace de aplicación de Azure, uno de los errores que se pueden encontrar los usuarios es "Error de servidor: 502 - el servidor Web ha recibido una respuesta no válida actuando como un servidor proxy o puerta de enlace". Esto puede ocurrir debido a las siguientes razones principales:

- Grupo de back-end de Azure aplicación la puerta de enlace no está configurada o está vacío.
- Ninguno de los máquinas virtuales o instancias de conjunto de escala de VM son correcto.
- Máquinas virtuales o instancias del conjunto de máquina virtual de escala de back-end no responde a la comprobación de estado de forma predeterminada.
- Configuración no válida o incorrecta de estado personalizados sondeos.
- Solicitar tiempo de espera o problemas de conectividad con las solicitudes de usuario.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vacía

### <a name="cause"></a>Causa

Si la puerta de enlace de aplicación no tiene máquinas virtuales o VM escala establecer configurado en el grupo de direcciones de back-end, no puede redirigir las solicitudes de cliente y produce un error de puerta de enlace incorrecta.

### <a name="solution"></a>Solución

Asegúrese de que el grupo de direcciones de back-end no está vacío. Esto puede hacerse a través de PowerShell, CLI o portal.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

El resultado del cmdlet anterior debe contener el conjunto de direcciones de back-end no vacía. A continuación se muestra un ejemplo donde se devuelven dos grupos que estén configurados con FQDN o las direcciones IP para máquinas virtuales de back-end. El estado de aprovisionamiento de la BackendAddressPool debe ser 'se realizó correctamente'.
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Instancias negativos en BackendAddressPool

### <a name="cause"></a>Causa

Si todas las instancias de BackendAddressPool están negativos, puerta de enlace de aplicación no tendría cualquier back-end a petición de usuario de ruta. También se podría ser el caso cuando existen instancias de back-end, pero no tiene la aplicación necesaria implementada.

### <a name="solution"></a>Solución

Asegúrese de que las instancias son correcta y la aplicación está configurada correctamente. Compruebe si las instancias de back-end se puede responder a un ping desde otra VM en el mismo VNet. Si ha configurado con un punto final público, compruebe que una solicitud de explorador para la aplicación web útiles para.

## <a name="problems-with-default-health-probe"></a>Problemas con el sondeo de estado de forma predeterminada

### <a name="cause"></a>Causa

502 errores también pueden ser indicadores frecuentes que el sondeo de estado predeterminado no tiene acceso a máquinas virtuales de back-end. Cuando se aprovisiona una instancia de puerta de enlace de aplicación, configurará automáticamente un sondeo de mantenimiento predeterminada para cada BackendAddressPool mediante las propiedades de la BackendHttpSetting. Entrada de usuario no es necesario para establecer este sondeo. Más concretamente, cuando se configura la regla de equilibrio de carga, se realiza una asociación entre un BackendHttpSetting y BackendAddressPool. Un sondeo predeterminado está configurado para cada una de estas asociaciones y puerta de enlace de aplicación inicia una conexión de comprobación de estado periódicas a cada instancia en el BackendAddressPool en el puerto especificado en el elemento BackendHttpSetting. La tabla siguiente muestra los valores asociados con el sondeo de estado de forma predeterminada.


|Sondeo (propiedad) | Valor | Descripción|
|---|---|---|
| Dirección URL de sondeo| http://127.0.0.1/ | Dirección URL |
| Intervalo | 30 | Intervalo de sondeo en segundos |
| Tiempo de espera  | 30 | Tiempo de espera de sondeo en segundos |
| Umbral de mal estado | 3 | Número de reintentos de sondeo. El servidor back-end se marca hacia abajo después de que el recuento de errores consecutivos sondeo alcanza el umbral de mal estado. |

### <a name="solution"></a>Solución

- Asegúrese de que un sitio predeterminado está configurado y escucha en 127.0.0.1.
- Si BackendHttpSetting especifica un puerto distinto de 80, el sitio predeterminado debe estar configurado para escuchar en ese puerto.
- La llamada a http://127.0.0.1:port debe devolver un código de resultado HTTP de 200. Esto se devuelve dentro del período de tiempo de espera de 30 segundos.
- Asegúrese de que el puerto configurado está abierto y que no existen reglas de firewall o grupos de seguridad de red de Azure, que bloquea el tráfico entrante y saliente en el puerto configurado.
- Si se utiliza Azure VM clásicas o servicio de nube con FQDN o IP pública, asegúrese de que se abre el correspondiente [extremo](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) .
- Si la máquina virtual se configura mediante el Administrador de recursos de Azure y se encuentra fuera de la VNet donde se implementa la puerta de enlace de aplicación, [Grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) debe estar configurado para permitir el acceso en el puerto que desee.


## <a name="problems-with-custom-health-probe"></a>Problemas con el sondeo de estado personalizados

### <a name="cause"></a>Causa

Comprobaciones de estado personalizados permiten flexibilidad adicional para el comportamiento de búsqueda de predeterminadas. Al usar sondeos personalizados, los usuarios pueden configurar el intervalo de sondeo, la dirección URL y la ruta de acceso para probar y cuántas respuestas erróneas para aceptar antes de marcar la instancia de grupo de back-end como en mal estado. Se agregan las siguientes propiedades adicionales.

|Sondeo (propiedad)| Descripción|
|---|---|
| Nombre | Nombre del sondeo. Este nombre se usa para hacer referencia al sondeo en la configuración de HTTP back-end. |
| Protocolo | Protocolo utilizado para enviar el sondeo. El sondeo usará el protocolo definido en la configuración de HTTP back-end |
| Host |  Nombre de host para enviar el sondeo. Se aplica únicamente cuando está configurado varios sitios de puerta de enlace de aplicación. Esto es diferente del nombre de host de máquina virtual.  |
| Ruta de acceso | Ruta de acceso relativa del sondeo. La ruta de acceso válida comienza desde '/'. El sondeo se envía a \<protocolo\>://\<host\>:\<puerto\>\<ruta de acceso\> |
| Intervalo | Sondeo intervalo en segundos. Este es el intervalo de tiempo entre dos sondeos consecutivos.|
| Tiempo de espera | Sondeo de tiempo de espera en segundos. El sondeo se marcará como error si no se recibe una respuesta válida dentro de un período de tiempo de espera. |
| Umbral de mal estado | Número de reintentos de sondeo. El servidor back-end se marca hacia abajo después de que el recuento de errores consecutivos sondeo alcanza el umbral de mal estado. |


### <a name="solution"></a>Solución

Validar que el sondeo de estado personalizado está correctamente configurado como la tabla anterior. Además de los pasos de solución de problemas anteriores, asegurarse de lo siguiente:

- Asegúrese de que el sondeo se ha especificado correctamente según la [Guía](application-gateway-create-probe-ps.md).
- Si la puerta de enlace de aplicación está configurada para un solo sitio, de forma predeterminada, el Host de nombre debe especificarse como '127.0.0.1', a menos que lo contrario configurado en sondeo personalizada.
- Asegúrese de que una llamada a http://\<host\>:\<puerto\>\<ruta\> devuelve un código de resultado HTTP de 200.
- Asegúrese de que el intervalo de tiempo de espera y UnhealtyThreshold están dentro de los rangos aceptables.

## <a name="request-time-out"></a>Tiempo de espera de solicitud

### <a name="cause"></a>Causa

Cuando se recibe una solicitud de usuario, puerta de enlace de aplicación aplica las reglas configuradas a la solicitud y redirige a una instancia de grupo de back-end. Espera un intervalo configurable de tiempo una respuesta de la instancia de back-end. De forma predeterminada, este intervalo es **30 segundos**. Si la puerta de enlace de aplicación no reciba una respuesta de la aplicación de back-end en este intervalo, solicitud de usuario verá un error 502.

### <a name="solution"></a>Solución

Puerta de enlace de aplicación permite a los usuarios esta configuración a través de BackendHttpSetting, que, a continuación, se puede aplicar a diferentes grupos. Pueden tener diferentes conjuntos de back-end BackendHttpSetting diferente y solicitud diferente, por tanto, el tiempo de espera configurado.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Pasos siguientes

Si los pasos anteriores no resuelven el problema, abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).
