<properties 
    pageTitle="Configurar un servidor de aplicaciones Web (WAFS) en el entorno de servicio de aplicaciones" 
    description="Obtenga información sobre cómo configurar un servidor de aplicaciones web delante de su entorno de servicio de aplicación." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurar un servidor de aplicaciones Web (WAFS) en el entorno de servicio de aplicaciones

## <a name="overview"></a>Información general ##
Web firewalls de aplicación como [Barracuda WAFS para Azure](https://www.barracuda.com/programs/azure) que está disponible en la Ayuda de [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) seguro de que las aplicaciones web mediante la inspección el tráfico web para bloquear las inserciones de SQL, las secuencias de comandos, malware cargas & aplicación DDoS y otros ataques. También inspecciona las respuestas de los servidores web back-end para la prevención de pérdida de datos (DLP). Combina con aislamiento y escala adicional proporcionada por la aplicación de servicio entornos, esto proporciona un entorno ideal para aplicaciones web crítica de host empresarial que necesita a solicitudes malintencionadas y tráfico de gran volumen.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Programa de instalación ##
Para este documento para configurar nuestro entorno de servicio de aplicación detrás de varios carga equilibrada instancias de Barracuda WAFS para que únicamente el tráfico de la WAFS puede ponerse en contacto el entorno de aplicación de servicio y no será accesible desde la DMZ. También tendremos Administrador de tráfico de Azure delante nuestras instancias Barracuda WAFS a equilibrar la carga entre los centros de datos de Azure y regiones. Un diagrama de alto nivel de la configuración tendría un aspecto similar a lo que se muestra a continuación.

![Arquitectura][Architecture] 

> Nota: Con la introducción de [ILB de soporte técnico para el entorno de aplicación de servicio](app-service-environment-with-internal-load-balancer.md), puede configurar la ASE para ser accesible desde la DMZ y sólo está disponible a la red privada. 

## <a name="configuring-your-app-service-environment"></a>Configurar el entorno de servicio de aplicaciones ##
Para configurar un entorno de servicio de aplicación consulte [nuestra documentación](app-service-web-how-to-create-an-app-service-environment.md) sobre el asunto. Una vez creado un entorno de servicio de aplicación, puede crear [Aplicaciones Web](app-service-web-overview.md), [Aplicaciones de la API](../app-service-api/app-service-api-apps-why-best-platform.md) y [Aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md) en este entorno que se puedan proteger detrás de la WAFS se configura en la sección siguiente.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurar el servicio de nube WAFS Barracuda ##
Barracuda tiene un [artículo detallada](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sobre cómo implementar su WAFS en una máquina virtual en Azure. Pero porque desea redundancia y no introducir un punto único de error, que desea implementar máquinas virtuales de instancia de WAFS al menos 2 en el mismo servicio de nube cuando siguiendo estas instrucciones.

### <a name="adding-endpoints-to-cloud-service"></a>Agregar extremos al servicio de nube ###
Una vez que tenga 2 o más instancias de WAFS VM en el servicio de nube puede usar el [portal de Azure](https://portal.azure.com/) para agregar extremos HTTP y HTTPS que utilizan la aplicación como se muestra en la imagen siguiente.

![Configurar extremo][ConfigureEndpoint]

Si sus aplicaciones utilizan otros extremos, asegúrese de incluirlos en esta lista también. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurar Barracuda WAFS a través de su Portal de administración ###
Barracuda WAFS usa 8000 de puerto TCP para la configuración a través de su portal de administración. Puesto que tenemos varias instancias de la VM WAFS debe Repita estos pasos para cada instancia de máquina virtual. 


> Nota: Una vez que haya terminado con la configuración de WAFS, quite el extremo TCP/8000 todas sus máquinas virtuales WAFS para proteger su WAFS.

Agregar el extremo de administración tal como se muestra en la imagen siguiente para configurar su WAFS Barracuda.

![Agregar extremo de administración][AddManagementEndpoint]
 
Use un explorador para examinar el extremo de la administración de su servicio de nube. Si su servicio de nube se llama test.cloudapp.net, debería tener acceso a este extremo desplazándose hasta http://test.cloudapp.net:8000. Debería ver una página de inicio de sesión como las siguientes que puede iniciar sesión con credenciales especificadas en la fase de instalación WAFS VM.

![Página de inicio de sesión de administración][ManagementLoginPage]

Una vez que inicie sesión debería ver un panel como la de la imagen siguiente que se presentan las estadísticas básicas sobre la protección de WAFS.

![Panel de administración][ManagementDashboard]

Al hacer clic en la pestaña servicios le permitirá configurar su WAFS para los servicios que protege. Para obtener más detalles acerca de cómo configurar su WAFS Barracuda puede consultar la [documentación correspondiente](https://techlib.barracuda.com/waf/getstarted1). En el ejemplo siguiente de una aplicación Web de Azure se ha configurado servir el tráfico de HTTP y HTTPS.

![Administración de agregar servicios][ManagementAddServices]

> Nota: Dependiendo de cómo se configuran las aplicaciones y qué características se utilizan en su entorno de servicio de aplicación, deberá reenviar el tráfico de TCP puertos que no sea 80 y 443, por ejemplo, si tiene el programa de instalación de IP SSL para una aplicación Web. Para obtener una lista de puertos de red usados en entornos de aplicación de servicio, consulte la sección de puertos de red de [documentación de tráfico de entrada de Control](app-service-app-service-environment-control-inbound-traffic.md) .

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configuración del Administrador de tráfico de Microsoft Azure (opcional) ##
Si la aplicación está disponible en varias regiones, a continuación, puede que desee cargar saldo detrás del [Administrador de tráfico de Azure](../traffic-manager/traffic-manager-overview.md). No puede agregar un extremo en el [portal de clásica Azure](https://manage.azure.com) utilizando el nombre de servicio de nube para su WAFS en el perfil de administrador de tráfico, como se muestra en la imagen siguiente. 

![Punto final del Administrador de tráfico][TrafficManagerEndpoint]

Si la aplicación requiere autenticación, asegúrese de que tiene algunos recursos que no se requieren ninguna autenticación para el Administrador de tráfico para hacer ping a la disponibilidad de la aplicación. Puede configurar la dirección URL en la sección Configurar en el [portal de clásica Azure](https://manage.azure.com) tal como se muestra a continuación.

![Configurar el tráfico de administrador][ConfigureTrafficManager]

Para reenviar los ping Administrador de tráfico de su WAFS a la aplicación, tendrá que configurar traducciones de sitio Web en su WAFS Barracuda para reenviar el tráfico a su aplicación como se muestra en el ejemplo siguiente.

![Traducciones de sitio Web][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Proteger el tráfico al entorno de servicio de aplicaciones mediante grupos de seguridad de red (NSG)##
Siga la [documentación de tráfico de Control de entrada](app-service-app-service-environment-control-inbound-traffic.md) para obtener información detallada de restringir el tráfico a su entorno de servicio de la aplicación desde la WAFS sólo mediante la dirección VIP de su servicio de nube. Aquí tiene un comando de Powershell de ejemplo para realizar esta tarea para el puerto TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Reemplazar la SourceAddressPrefix con la dirección IP Virtual (VIP) del servicio de nube de su WAFS.

> Nota: La dirección VIP de su servicio de nube cambiará al eliminar y volver a crear el servicio de nube. Asegúrese de actualizar la dirección IP en el grupo de recursos de red, una vez que lo haga. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
