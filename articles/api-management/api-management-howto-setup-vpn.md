<properties
    pageTitle="Cómo configurar las conexiones de VPN en administración de la API de Azure"
    description="Obtenga información sobre cómo configurar una conexión VPN en administración de la API de Azure y servicios web de access a través de él."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Cómo configurar las conexiones de VPN en administración de la API de Azure

Soporte VPN de administración de la API le permite conectarse a la puerta de enlace de administración de la API a una red Virtual de Azure (clásico). Esto permite a los clientes de administración de la API conectar de forma segura a sus servicios web de back-end que son local o en caso contrario, pueden tener acceso a internet público.

>[AZURE.NOTE] Administración de Azure API funciona con VNETs clásicos. Para obtener información sobre cómo crear un VNET clásica, consulte [crear una red virtual (clásica) a través del Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obtener información sobre la conexión clásicos VNETs a VNETS ARM, vea [Conectar VNets clásico a VNets nuevo](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Conexiones habilitar VPN

>Conectividad VPN solo está disponible en los niveles **Premium** y **Programador** . Para cambiar a él, abra el servicio de administración de la API en el [Portal de clásico de Azure][] y, a continuación, abra la pestaña **escala** . En la sección **General** , seleccione el nivel Premium y haga clic en Guardar.

Para habilitar la conectividad VPN, abra el servicio de administración de la API en el [Portal de clásico de Azure][] y cambie a la ficha **Configurar** . 

En la sección de la VPN, cambiar **conexión VPN** para **en**.

![Configurar la ficha de administración de la API instancia][api-management-setup-vpn-configure]

Ahora verá una lista de todas las regiones donde se le proporciona un servicio de administración de la API.

Seleccione una VPN y subred para cada región. La lista de redes privadas virtuales se rellena en función de las redes virtuales disponibles en su suscripción de Azure que se configuran en la región que está configurando.

![Seleccione VPN][api-management-setup-vpn-select]

Haga clic en **Guardar** en la parte inferior de la pantalla. No podrá realizar otras operaciones en el servicio de administración de la API desde el Portal de clásico de Azure mientras está actualizando. La puerta de enlace de servicio se siguen estando disponible y no deberían verse afectadas llamadas en tiempo de ejecución.

Observe que la dirección VIP de la puerta de enlace cambiará cada vez VPN está activado o desactivado.

## <a name="connect-vpn"> </a>Conectarse a un servicio web detrás de VPN

Después de que el servicio de administración de la API está conectado a la VPN, acceso a servicios web dentro de la red virtual no es diferente de obtener acceso a servicios de público. Escriba en la dirección local o el nombre de host (si se ha configurado un servidor DNS de la red Virtual de Azure) del servicio web en el campo **dirección URL del servicio Web** al crear una nueva API o modificar uno existente.

![Agregar API de VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Puertos necesarios para la compatibilidad con API administración VPN

Cuando una instancia de servicio de administración de la API está alojada en un VNET, se usan los puertos en la tabla siguiente. Si se bloquean estos puertos, el servicio podría no funcionar correctamente. Tiene uno o varios de estos puertos bloqueados es el problema de configuración más comunes al usar la API de administración con un VNET.

| Puertos                      | Dirección        | Protocolo de transporte | Propósito                                                          | Origen y destino              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | Entrada          | TCP                | Comunicaciones de cliente a la API de administración                           | INTERNET / VIRTUAL_NETWORK        |
| 80,443                       | Saliente         | TCP                | API de dependencia de administración de almacenamiento de Azure y Bus de servicio de Azure | VIRTUAL_NETWORK O INTERNET        |
| 1433                         | Saliente         | TCP                | Dependencias de administración de la API de SQL                               | VIRTUAL_NETWORK O INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | Saliente         | TCP                | Dependencias de administración de la API de Bus de servicio                       | VIRTUAL_NETWORK O INTERNET        |
| 5671                         | Saliente         | AMQP               | Dependencia de administración de la API de registro al evento directiva de concentrador            | VIRTUAL_NETWORK O INTERNET        |
| 6381, 6382, 6383             | Entrada o salida | UDP                | Dependencias de administración de la API de caché Redis                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | Saliente         | TCP                | Dependencia de administración de API Azure recurso compartido de archivos para GIT            | VIRTUAL_NETWORK O INTERNET        |

## <a name="custom-dns"> </a>Configuración del servidor DNS personalizada

Administración de la API depende de una serie de servicios de Azure. Cuando una instancia de servicio de administración de la API está alojada en un VNET donde se usa un servidor DNS personalizado, debe poder resolver nombres de host de los servicios de Azure. Siga [esta](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) Guía de instalación personalizada de DNS.  

## <a name="related-content"> </a>Contenido relacionado


* [Crear una red virtual con una conexión VPN de sitio a sitio con el Portal de clásico de Azure][]
* [Cómo usar el API Inspector para seguimiento llamadas en administración de la API de Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Portal de clásico de Azure]: https://manage.windowsazure.com/

[Crear una red virtual con una conexión VPN de sitio a sitio con el Portal de clásico de Azure]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Cómo usar el API Inspector para seguimiento llamadas en administración de la API de Azure]: api-management-howto-api-inspector.md
