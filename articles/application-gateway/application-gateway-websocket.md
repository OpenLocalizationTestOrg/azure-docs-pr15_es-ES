<properties
   pageTitle="Soporte de aplicaciones de puerta de enlace WebSocket | Microsoft Azure"
   description="Esta página proporciona una descripción general de la aplicación WebSocket de puerta de enlace del soporte técnico."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Compatibilidad con WebSocket de puerta de enlace de aplicaciones

Puerta de enlace de aplicación proporciona compatibilidad nativa para WebSocket de todos los tamaños de puerta de enlace. No hay ningún valor configurable por el usuario para habilitar o deshabilitar WebSocket soporte de forma selectiva. Puede seguir usando un HTTPListener estándar en el puerto 80 y 443 para recibir el tráfico de WebSocket. Tráfico de WebSocket, a continuación, se dirige al servidor WebSocket habilitado back-end mediante el grupo de back-end adecuados según lo especificado en reglas de la puerta de enlace de aplicaciones. Protocolo WebSocket normalizado en [RFC6455](https://tools.ietf.org/html/rfc6455) permite una comunicación dúplex completa entre servidor y cliente sobre una conexión TCP larga. Esta característica permite una comunicación más interactiva entre el servidor web y de cliente, que puede ser bidireccional sin necesidad de sondeo como necesarios en implementaciones basadas en HTTP.  WebSocket tiene baja sobrecarga a diferencia de HTTP y volver a utilizar la misma conexión TCP para varias solicitud y respuestas de una utilización más eficaz de los recursos. Protocolos de WebSocket están diseñados para trabajar en los puertos HTTP tradicionales de 80 y 443.

El servidor back-end debe responder a sondeos de puerta de enlace de aplicación, que se describen en la sección [información general de sondeo de estado](application-gateway-probe-overview.md) . Comprobaciones de estado de puerta de enlace de aplicación HTTP/HTTPS solo, esto implica que cada servidor back-end debe responder a sondeos HTTP de puerta de enlace de aplicación enrutar el tráfico de WebSocket en el servidor.

## <a name="listener-configuration-element"></a>Elemento de configuración de escucha

HTTPListener existente puede utilizarse para admitir WebSocket. A continuación se muestra un fragmento de código del elemento HttpListeners de un archivo de plantilla de ejemplo. ¿Necesita escucha HTTP y HTTPS para admitir WebSocket y proteger el tráfico de WebSocket. Asimismo puede usar el [portal](application-gateway-create-gateway-portal.md) o [PowerShell](application-gateway-create-gateway-arm.md) para crear una puerta de enlace de la aplicación con escucha en el puerto 80 y 443 para permitir el tráfico de WebSocket.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuración de la regla de BackendAddressPool, BackendHttpSetting y enrutamiento

BackendAddressPool debe utilizarse para definir un grupo de back-end con los servidores de WebSocket habilitado. BackendHttpSetting deben definirse con back-end puertos 80 y 443 solo. Propiedades de afinidad basada en cookies y requestTimeouts no son relevantes para el tráfico de WebSocket. No hay ningún cambio requerido en regla de enrutamiento. Regla de enrutamiento 'Básica' debe seguir usarse para vincular la escucha correspondiente al grupo de direcciones de back-end correspondiente. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>WebSocket habilitado back-end

El back-end debe tener un servidor web HTTP/HTTPS con el configurado el puerto (normalmente 80 y 443) para que WebSocket para que funcione. Este requisito es porque WebSocket protocolo requiere el protocolo de enlace inicial sea HTTP actualización de protocolo WebSocket como un campo de encabezado.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Otra razón es que sondeo de salud de back-end de puerta de enlace de aplicación admite únicamente los protocolos HTTP/HTTPS. Si el servidor no responde a HTTP/HTTPS sondeos, debería tomarse fuera del grupo de back-end y sin solicitudes incluidas las solicitudes de WebSocket, alcance este back-end.

## <a name="next-steps"></a>Pasos siguientes

Después de aprendizaje sobre la compatibilidad de WebSocket, vaya a [crear una puerta de enlace de aplicación](application-gateway-create-gateway.md) para empezar con una aplicación web de WebSocket habilitado.
