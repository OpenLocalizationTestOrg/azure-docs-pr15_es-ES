<properties
   pageTitle="Introducción al enrutamiento contenido basado en la dirección URL | Microsoft Azure"
   description="Esta página proporciona información general sobre el enrutamiento de contenido basado en la dirección URL de la puerta de enlace de aplicación, la configuración de UrlPathMap y la regla de PathBasedRouting."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="url-path-based-routing-overview"></a>Información general de URL enrutamiento de ruta de acceso

Dirección URL de ruta de enrutamiento permite para enrutar el tráfico a grupos de servidor back-end basado en rutas de acceso de dirección URL de la solicitud. Uno de los casos es solicitudes de ruta para distintos tipos de contenido a los grupos de servidores diferente back-end.
En el ejemplo siguiente, la puerta de enlace de aplicación sirve tráfico de contoso.com de tres grupos de servidor back-end por ejemplo: VideoServerPool, ImageServerPool y DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Las solicitudes de http://contoso.com/video* se enrutan a VideoServerPool y http://contoso.com/images* se enrutan a ImageServerPool. DefaultServerPool está seleccionado si coincide con ninguno de los modelos de ruta de acceso.

## <a name="urlpathmap-configuration-element"></a>Elemento de configuración de UrlPathMap

Elemento de UrlPathMap se utiliza para especificar los patrones de ruta de acceso a las asignaciones de grupo de servidor back-end. En el ejemplo siguiente se es el fragmento de código del elemento urlPathMap del archivo de plantilla.

    "urlPathMaps": [
    {
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
    }
    

>[AZURE.NOTE] PathPattern: Esta opción es una lista de los patrones de ruta para que coincida con. Cada uno de ellos debe comenzar con / y el único lugar un "*" está permitido en las siguientes final un "/". ¿La cadena apto para el buscador de coincidencias de ruta de acceso no incluye cualquier texto después de la primera? o # y los caracteres no se permiten aquí. 

Puede ver una [plantilla de administrador de recursos con enrutamiento basado en la dirección URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) para obtener más información.

## <a name="pathbasedrouting-rule"></a>Regla de PathBasedRouting

RequestRoutingRule de tipo PathBasedRouting se usa para enlazar una escucha un urlPathMap. Todas las solicitudes que llegan para esta escucha se enrutan en función de la directiva especificada en urlPathMap.
Fragmento de código de PathBasedRouting regla:

    "requestRoutingRules": [
    {

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
    
## <a name="next-steps"></a>Pasos siguientes

Después de aprendizaje sobre enrutamiento de contenido basado en la dirección URL, vaya a [crear una puerta de enlace de aplicación utilizando el enrutamiento basado en la dirección URL](application-gateway-create-url-route-portal.md) para crear una puerta de enlace de la aplicación con las reglas de enrutamiento de direcciones URL.
