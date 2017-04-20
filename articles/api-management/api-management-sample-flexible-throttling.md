<properties
    pageTitle="Solicitud avanzada límite con la administración de la API de Azure"
    description="Aprenda a crear y aplicar cuota flexible y directivas de administración de la API de Azure de limitación de velocidad."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>Solicitud avanzada límite con la administración de la API de Azure

Para limitar las solicitudes entrantes es una función clave de la administración de la API de Azure. Ya sea controlando la tasa de solicitudes o solicitudes/datos total transferidos, administración de la API permite a los proveedores de API proteger sus API de abuso y crear valor para diferentes niveles de producto de API.

## <a name="product-based-throttling"></a>Limitación en función del producto
Hasta la fecha, el límite de la velocidad capacidades han limitado para que sea el ámbito de una determinada suscripción de producto (esencialmente una clave), definido en el portal de administración de la API de publisher. Esto es útil para el proveedor de API aplicar los límites en los desarrolladores que se hayan registrado para usar su API, sin embargo, no ayuda, por ejemplo, en usuarios finales individuales de la API de limitación. Es posible que para un solo usuario de la aplicación del desarrollador para consumir la cuota toda y, a continuación, impedir que otros clientes del desarrollador pueda usar la aplicación. Además, varios clientes que pueden generar un gran volumen de solicitudes pueden limitar el acceso a los usuarios ocasionales.

## <a name="custom-key-based-throttling"></a>Clave personalizada en función de límite
Las directivas de [tasa límite por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [cuota por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) nuevas proporcionan una solución significativamente más flexible al control de tráfico. Estas nuevas directivas permiten definir expresiones para identificar las teclas que se usan para realizar un seguimiento de uso del tráfico. El modo de funcionamiento más fácil se muestra con un ejemplo. 

## <a name="ip-address-throttling"></a>Limitación de la dirección IP
Las siguientes directivas restringen una dirección IP de solo cliente a sólo 10 llamadas cada minuto, con un total de 1.000.000 llamadas y 10.000 kilobytes de ancho de banda por mes. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Si todos los clientes de Internet utilizan una dirección IP única, puede una manera eficaz de limitar uso por usuario. Sin embargo, es muy probable que varios usuarios compartir una única dirección IP pública debido a que el acceso a Internet a través de un dispositivo NAT. A pesar de esto, API que permiten el acceso no autenticado el `IpAddress` podría ser la mejor opción.

## <a name="user-identity-throttling"></a>Limitación de identidad de usuario
Si un usuario se autentica, a continuación, puede generar una clave de limitación en función de la información que identifica un que usuario.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

En este ejemplo se extraer el encabezado de autorización, conviértalo a `JWT` objeto y use el asunto del token para identificar el usuario y usarlo como la limitación clave de velocidad. Si la identidad del usuario se almacena en el `JWT` como una de las otras reclamaciones, a continuación, ese valor se utilizará en su lugar.

## <a name="combined-policies"></a>Directivas de combinada
Aunque las directivas límites nuevas proporcionan más control que las directivas de limitación existentes, sigue siendo valor la combinación de ambas capacidades. Límite de clave de producto suscripción ([tasa de llamada límite por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y [establecer la cuota de uso de suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) es una excelente forma de habilitar monetización de una API con cargo basándose en los niveles de uso. La proporciona más control de poder limitar por usuario complementario e impide que el comportamiento de un usuario degradar la experiencia de otro. 

## <a name="client-driven-throttling"></a>Condicionada por el límite de cliente
Cuando la tecla de aceleración está definida mediante una [expresión de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx), es el proveedor de API que es elegir cómo el límite está en el ámbito. Sin embargo, un desarrollador que desee controlar cómo puntuar límite sus propios clientes. Esto podría habilitarse por el proveedor de API mediante la introducción de un encabezado personalizado para permitir que la aplicación del desarrollador cliente comunicarse la clave a la API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Esto permite la aplicación del desarrollador cliente elegir cómo desea crear la limitación clave de velocidad. Con un poco de ingenio desarrollador cliente podría crear sus propios niveles de tasa y asignar conjuntos de claves a los usuarios y girar el uso de la clave.

## <a name="summary"></a>Resumen
Administración de API Azure proporciona tasa y limitación para proteger y agregar valor a su servicio de la API de presupuesto. Las directivas límites nuevas con las reglas de ámbito personalizadas permitir que proporciona más control sobre las directivas para habilitar los clientes generar aplicaciones aún mejores. Los ejemplos en este artículo muestran el uso de estas nuevas directivas por claves con direcciones IP de cliente, identidad de usuario y valores de cliente generado de limitación de la velocidad de fabricación. Sin embargo, hay muchas otras partes del mensaje que se pueden utilizar como agente de usuario, fragmentos de ruta de acceso de dirección URL, tamaño del mensaje.

## <a name="next-steps"></a>Pasos siguientes
Por favor, envíenos sus comentarios en el subproceso Disqus para este tema. Es ideal obtener información acerca de otros valores de clave posibles que han sido una opción lógica en los escenarios.

## <a name="watch-a-video-overview-of-these-policies"></a>Ver un vídeo de introducción de estas directivas
Para obtener más información sobre las directivas de [tasa límite por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [cuota por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) de este artículo, por favor, vea el siguiente vídeo.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
