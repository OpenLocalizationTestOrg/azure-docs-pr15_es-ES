<properties
   pageTitle="Introducción al servidor de aplicaciones Web (WAFS) de puerta de enlace de aplicación | Microsoft Azure"
   description="Esta página proporciona una descripción general de Firewall de aplicación Web (WAFS) de puerta de enlace de aplicación "
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-web-application-firewall-preview"></a>Servidor de aplicaciones Web de aplicación puerta de enlace (vista preliminar)

Servidor de aplicaciones Web (WAFS) es una característica de puerta de enlace de aplicación azure que ofrece una protección a aplicaciones web que aprovechar la puerta de enlace de aplicación para las funciones de Control de entrega de la aplicación (ADC) estándar. Para ello, el servidor de aplicaciones Web protegerlas contra mayoría OWASP superior 10 comunes web vulnerabilidad. Aplicaciones Web son cada vez más destinos de ataques que aprovechan comunes puntos débiles conocidos. Comunes entre estos ataques son ataques de inyección SQL, ataques de scripts de sitios para nombrar algunos. Evitar estos ataques en código de la aplicación puede ser difícil y puede requerir rigurosa mantenimiento, revisiones y supervisión en varias capas de la topología de aplicación. Un servidor de aplicaciones web centralizada a protegerse frente a ataques web facilita mucho la administración de seguridad y le da mejor assurance a la aplicación frente a las amenazas de intrusiones. Una solución WAFS puede ante una amenaza de seguridad más rápida por revisiones una vulnerabilidad conocida en una ubicación central en lugar de protección de cada una de las aplicaciones web individuales. Las puertas de enlace de aplicación existente se pueden convertir en una puerta de enlace de la aplicación con el servidor de aplicaciones web fácilmente.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/WAF1.png)

Puerta de enlace de aplicación funciona como un controlador de entrega de la aplicación y ofrece terminación SSL, afinidad sesión basada en cookies, la carga de turnos distribución contenido según enrutamiento, capacidad para hospedar varias mejoras de seguridad y sitios Web. Mejoras de seguridad ofrecidas por la puerta de enlace de aplicación incluyen administración de directivas de SSL compatibilidad con SSL de llevar a cabo. Capacidades de seguridad de la aplicación de nuestro servicio nos estamos consolidación introduciendo WAFS (servidor de aplicaciones web) que se integran en ADC oferta. Proporciona un fácil de configurar ubicación central para administrar y proteger sus aplicaciones web contra vulnerabilidad comunes de web.

Configurar WAFS en puerta de enlace de aplicación proporciona las siguientes ventajas:

- Proteger la aplicación web de vulnerabilidad web y ataques sin modificar el código de back-end.
- Proteger varias aplicaciones web al mismo tiempo detrás de una puerta de enlace de aplicación. Puerta de enlace de aplicación admite hasta 20 sitios Web detrás de una sola puerta de enlace que podría estar protegida ataques web de hospedaje.
- Supervisar las aplicaciones web contra ataques mediante informes en tiempo real generadas por los registros de puerta de enlace WAFS de aplicación.
- Algunos controles de cumplimiento requieren todos los puntos de final hacia internet esté protegido por una solución WAFS. Mediante el uso de puerta de enlace de aplicación con WAFS habilitada, puede cumple estos requisitos de cumplimiento.

## <a name="overview"></a>Información general

WAFS de puerta de enlace de aplicación se ofrece en un nuevo SKU (WAFS SKU) y está preconfigurado con ModSecurity y conjunto de reglas de OWASP Core ofrecen protección de línea base contra mayoría OWASP superior 10 comunes web vulnerabilidad.

- Protección de la inserción de SQL
- Cruzado de protección de secuencias de comandos de sitio
- Protección común ataques Web como inyección de comandos, solicitudes HTTP no autorizadas, respuesta HTTP dividir y ataque de inclusión de archivo remoto
- Protección contra infracción de protocolo HTTP
- Protección contra anomalías de protocolo HTTP como la falta de host de agente de usuario y acepte los encabezados
- Protección de DoS HTTP incluidos saturación de HTTP y prevención de HTTP DoS lenta
- Prevención contra robots, rastreadores y escáneres
- Detección de incorrectas de aplicación (es decir, Apache, IIS, etcetera)

## <a name="waf-modes"></a>Modos de WAFS

WAFS de puerta de enlace de aplicación puede estar configurado para ejecutar en los dos modos siguientes:

- **Modo de detección** : cuando está configurado para ejecutarse en modo de detección, WAFS de puerta de enlace de aplicación supervisa y registra todas las alertas de amenaza en un archivo de registro. Debe asegurarse de que el registro de diagnósticos de puerta de enlace de aplicación está activado en la sección diagnóstico. También debe asegurarse de que el registro WAFS está seleccionado y activado.
- **El modo Prevención** : cuando está configurado para ejecutarse en modo de prevención, puerta de enlace de aplicación conocidas bloquea intrusiones y ataques detectados por las reglas. El intruso recibe una excepción de acceso no autorizado 403 y finaliza la conexión. El modo Prevención continúa iniciar estos ataques en los registros de WAFS.

## <a name="application-gateway-waf-reports"></a>Informes de WAFS de puerta de enlace de aplicación

WAFS de puerta de enlace de aplicación proporciona informes detallados de cada amenaza detecta. Registro de información se integra con los registros de diagnósticos de Azure y alertas se guardan en un formato json.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/waf2.png)

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="application-gateway-waf-sku-pricing"></a>Precios de la puerta de enlace WAFS SKU en la aplicación

En vista previa, no hay cargos adicionales para el uso de la aplicación WAFS de puerta de enlace. Seguir se cargará a los gastos de SKU básicas existentes. Se comunicará los cargos WAFS SKU en el momento de disponibilidad general. Empiezan a los clientes que han elegido implementar aplicación de puerta de enlace WAFS SKU acumulan SKU WAFS precios solo después de anuncio de disponibilidad general.

## <a name="next-steps"></a>Pasos siguientes

Después de obtener más información sobre las capacidades de WAFS, visite [cómo configurar el servidor de aplicaciones Web de puerta de enlace de aplicación](application-gateway-web-application-firewall-portal.md).
