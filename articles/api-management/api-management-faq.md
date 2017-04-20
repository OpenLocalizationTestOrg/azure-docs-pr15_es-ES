<properties
    pageTitle="Preguntas más frecuentes sobre administración de Azure API | Microsoft Azure"
    description="Obtenga información sobre las respuestas a preguntas habituales, patrones y prácticas recomendadas de administración de la API de Azure."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Preguntas más frecuentes de administración de API de Azure

Obtenga respuestas a preguntas habituales, patrones y procedimientos recomendados para la administración de la API de Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

-   [¿Cómo puedo realizar el equipo de administración de Microsoft Azure API una pregunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [¿Qué significa cuando una característica no está en la vista previa?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [¿Cómo puedo proteger la conexión entre la puerta de enlace API administración y Mis servicios back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [¿Cómo puedo copiar mi instancia de servicio de administración de la API a una nueva instancia?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [¿Puedo administrar mi instancia de administración de la API mediante programación?](#can-i-manage-my-api-management-instance-programmatically)
-   [¿Cómo puedo agregar un usuario al grupo de administradores?](#how-do-i-add-a-user-to-the-administrators-group)
-   [¿Por qué es la directiva que deseo agregar no está disponible en el editor de directivas?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [¿Cómo se puede usar control de versiones de API API de administración de?](#how-do-i-use-api-versioning-in-api-management)
-   [¿Cómo configuro múltiples entornos en una sola API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [¿Puedo usar SOAP con la API de administración?](#can-i-use-soap-with-api-management)
-   [¿Es la constante de dirección IP de puerta de enlace API administración? ¿Puedo usarla en las reglas de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [¿Puedo configurar un servidor de autorización de OAuth 2.0 con la seguridad de AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [¿Qué método de enrutamiento usar API de administración de implementaciones a varias ubicaciones geográficas?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [¿Puedo usar una plantilla de administrador de recursos de Azure para crear una instancia de servicio de administración de la API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [¿Puedo usar un certificado SSL autofirmado para un back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [¿Por qué recibo un error de autenticación cuando intento clonar un repositorio GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [¿Administración de la API con funciona Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
-   [¿Puedo mover un servicio de administración de la API de una suscripción a otro?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>¿Cómo puedo realizar el equipo de administración de Microsoft Azure API una pregunta?

Puede ponerse en contacto con nosotros mediante una de estas opciones:

-   Publique sus preguntas en el [foro de la API de administración de MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Enviar un correo electrónico a <apimgmt@microsoft.com>.
-   Envíenos una solicitud de característica en el [foro de comentarios de Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>¿Qué significa cuando una característica no está en la vista previa?

Cuando una característica no está en la vista previa, significa que nos estamos buscando conocidas comentarios sobre cómo funciona la característica para usted. Una característica de vista previa es funcionalidad completa, pero es posible que va a realizar un salto de cambiar en respuesta a los comentarios de los clientes. Le recomendamos que no dependen de una característica que está en vista previa de su entorno de producción. Si tiene cualquier comentarios sobre las características de vista previa, háganoslo saber a través de una de las opciones de contacto en [¿Cómo puedo realizar el equipo de administración de Microsoft Azure API una pregunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>¿Cómo puedo proteger la conexión entre la puerta de enlace API administración y Mis servicios back-end?

Tiene varias opciones para proteger la conexión entre la puerta de enlace de administración de la API y los servicios de back-end. Puedes:

-   Usar autenticación básica HTTP. Para obtener más información, consulte [configuración de la API de configurar](api-management-howto-create-apis.md#configure-api-settings).
- Usar autenticación mutua de SSL como se describe en [cómo proteger los servicios de back-end utilizando la autenticación de certificado de cliente en la administración de la API de Azure](api-management-howto-mutual-certificates.md).
- Use la lista blanca IP de su servicio de back-end. Si tiene una instancia de administración de la API de nivel estándar o Premium, la dirección IP de la puerta de enlace permanece constante. Puede configurar su lista blanca para permitir que esta dirección IP. Puede obtener la dirección IP de la instancia de administración de la API en el panel en el portal de Azure.
- Conectar la instancia de administración de la API a una red Virtual de Azure. Para obtener más información, consulte [cómo configurar las conexiones de VPN en administración de la API de Azure](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>¿Cómo puedo copiar mi instancia de servicio de administración de la API a una nueva instancia?

Tiene varias opciones si desea copiar una instancia de administración de la API a una nueva instancia. Puedes:

-   Use la copia de seguridad y restaurar la función en la API de administración. Para obtener más información, vea [cómo implementar mediante copia de seguridad de servicio de recuperación y restaurar en administración de la API de Azure](api-management-howto-disaster-recovery-backup-restore.md).
-   Crear su propia copia de seguridad y restaurar la característica mediante la [API de REST API de administración](https://msdn.microsoft.com/library/azure/dn776326.aspx). Usar la API de REST para guardar y restaurar las entidades desde la instancia de servicio que desee.
-   Descargar la configuración del servicio mediante Git y luego cárguelo en una nueva instancia. Para obtener más información, vea [cómo guardar y configurar la configuración del servicio de administración de la API usando Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>¿Puedo administrar mi instancia de administración de la API mediante programación?

Sí, puede administrar API administración mediante programación utilizando:

-   La [administración de la API REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   La [biblioteca de administración de ApiManagement servicio de Microsoft Azure SDK](http://aka.ms/apimsdk).
-   Los cmdlets de PowerShell de [administración de servicio](https://msdn.microsoft.com/library/mt613507.aspx) e [implementación de servicio](https://msdn.microsoft.com/library/mt619282.aspx) .

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>¿Cómo puedo agregar un usuario al grupo de administradores?

Le mostramos cómo puede agregar un usuario al grupo Administradores:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Vaya al grupo de recursos que tiene la instancia de administración de la API que desea actualizar.
3. En administración de la API, asignar el rol de **Colaborador de administración de Api** para el usuario.

Ahora el colaborador recién agregado puede usar [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx)de PowerShell de Azure. Aquí le mostramos cómo iniciar sesión como administrador:

1. Usar el `Login-AzureRmAccount` cmdlet para iniciar sesión.
2. Establecer el contexto de la suscripción que tenga el servicio mediante `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obtener una URL de inicio de sesión único mediante `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Usar la dirección URL para acceder al portal de administración.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>¿Por qué es la directiva que deseo agregar no está disponible en el editor de directivas?

Si la directiva que desea agregar aparece atenuado o sombreado en el editor de directivas, asegúrese de que está en el ámbito correcto para la directiva. Cada declaración de directiva está diseñada para que su uso en ámbitos específicos y las secciones de la directiva. Para revisar las secciones de directiva y los ámbitos de una directiva, vea la sección de uso de la directiva de [directivas de administración de la API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>¿Cómo se puede usar control de versiones de API API de administración de?

Tiene varias opciones para usar las versiones de la API de administración de la API:

-   En administración de la API, puede configurar las API para representar las distintas versiones. Por ejemplo, es posible que tenga dos API diferentes, MyAPIv1 y MyAPIv2. Un desarrollador puede elegir la versión que el programador que desea usar.
-   También puede configurar la API con una dirección URL del servicio que no incluye un segmento de versión, por ejemplo, https://my.api. A continuación, configure un segmento de la versión de plantilla de [Reescritura de URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) de la operación. Por ejemplo, puede tener una operación con una [plantilla de dirección URL](api-management-howto-add-operations.md#url-template) denominado/Resource y una plantilla de [Reescritura de URL](api-management-howto-add-operations.md#rewrite-url-template) denominada /v1/Resource. Puede cambiar el valor de segmento versión por separado para cada operación.
-   Si le gustaría tener un segmento de la versión de "predeterminado" en la dirección URL del servicio de la API en operaciones seleccionado, establezca una directiva que usa la directiva de [Configurar el servicio de back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) para cambiar la ruta de acceso de solicitud de back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>¿Cómo configuro múltiples entornos en una sola API?

Para configurar varios entornos, por ejemplo, un entorno de prueba y un entorno de producción en una sola API, tiene dos opciones. Puedes:

-   Host API diferentes en el mismo inquilino.
-   Hospedar las mismas API de inquilinos diferentes.

### <a name="can-i-use-soap-with-api-management"></a>¿Puedo usar SOAP con la API de administración?

Compatibilidad de [paso a través SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) ahora está disponible. Los administradores pueden importar WSDL de su servicio SOAP y administración de la API de Azure, se creará un front-end de SOAP. Documentación de portal de desarrollador, consola de prueba, directivas y análisis están disponible para los servicios SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>¿Es la constante de dirección IP de puerta de enlace API administración? ¿Puedo usarla en las reglas de firewall?

En las capas estándar y Premium, la dirección IP pública (VIP) del inquilino de administración de la API es estática durante la duración del inquilino, con algunas excepciones. Los cambios de direcciones IP en estos casos:

-   El servicio se elimina y, a continuación, volver a crear.
-   La suscripción del servicio es suspendida (por ejemplo, para nonpayment) y, a continuación, en Restablecer.
-   Agregar o quitar una red Virtual Azure (puede utilizar una red Virtual solo en el nivel de Premium).

Para implementaciones de múltiples región, la dirección regional cambia si el área es vacías y, a continuación, reanude (puede utilizar la implementación de múltiples región solo en el nivel de Premium).

Los inquilinos de nivel Premium que se han configurado para su implementación de región múltiples le asigna una dirección IP pública por región.

Puede obtener la dirección IP (o direcciones en una implementación de región múltiple) en la página del inquilino en el portal de Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>¿Puedo configurar un servidor de autorización de OAuth 2.0 con la seguridad de AD FS?

Para obtener información sobre cómo configurar un servidor de autorización de OAuth 2.0 con la seguridad de los servicios de federación de Active Directory (AD FS), consulte [Usar ADFS en administración de la API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>¿Qué método de enrutamiento usar API de administración de implementaciones a varias ubicaciones geográficas?

Administración de la API utiliza el [método de enrutamiento de tráfico de rendimiento](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) en implementaciones a varias ubicaciones geográficas. El tráfico entrante se dirige a la puerta de enlace API más cercano. Si una región se desconecta, el tráfico entrante se enruta automáticamente a la siguiente puerta de enlace más cercano. Más información sobre métodos de enrutamiento de [métodos de enrutamiento de tráfico de administrador](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>¿Puedo usar una plantilla de administrador de recursos de Azure para crear una instancia de servicio de administración de la API?

Sí. Ver las plantillas de [Servicio de administración de Azure API](http://aka.ms/apimtemplate) tutorial rápido.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>¿Puedo usar un certificado SSL autofirmado para un back-end?

Sí. Aquí le mostramos cómo usar un certificado autofirmado de capa de Sockets seguros (SSL) para un back-end:

1. Crear una entidad de [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) mediante la API de administración.
2. Establezca la propiedad **skipCertificateChainValidation** en **true**.
3. Si ya no desea permitir que los certificados autofirmados, eliminar la entidad de back-end o establezca la propiedad **skipCertificateChainValidation** en **Falso**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>¿Por qué recibo un error de autenticación cuando intento clonar un repositorio Git?

Si utiliza el Administrador de credenciales Git, o si está intentando clonar un repositorio Git mediante Visual Studio, que podría surgir un problema conocido con el cuadro de diálogo de credenciales de Windows. El cuadro de diálogo limita la longitud de contraseña a 127 caracteres y, a continuación, se trunca la contraseña generados por Microsoft. Estamos trabajando en acortar la contraseña. Por ahora, utilice Git fiesta clonar repositorio Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>¿Administración de la API con funciona Azure ExpressRoute?

Sí. Administración de la API funciona con Azure ExpressRoute.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>¿Puedo mover un servicio de administración de la API de una suscripción a otro?

Sí. Para obtener información, consulte [mover recursos a un nuevo grupo de recursos o suscripción](../resource-group-move-resources.md).
