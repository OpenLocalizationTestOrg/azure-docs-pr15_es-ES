<properties
    pageTitle="Aplicación de servicio API aplicaciones - qué ha cambiado | Microsoft Azure"
    description="Obtenga información sobre novedades para las aplicaciones de la API de servicio de aplicaciones de Azure."
    services="app-service\api"
    documentationCenter=".net"
    authors="mohitsriv"
    manager="wpickett"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps---whats-changed"></a>Aplicación de servicio API aplicaciones - qué ha cambiado

En el evento Connect() en noviembre de 2015, un número de mejoras en la aplicación de servicio de Azure estaba [anuncia](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/). Estas mejoras incluyen subyacente cambia a aplicaciones API mejor alinear con Mobile y aplicaciones Web, reducir el número de concepto y mejorar el rendimiento de tiempo de ejecución y la implementación. Iniciar el 30 de noviembre de 2015, nueva API aplicaciones se crean con el portal de administración de Azure o las últimas herramientas reflejarán estos cambios. En este artículo se describe estos cambios, así como cómo volver a implementar aplicaciones existentes para aprovechar las ventajas de las funciones.

## <a name="feature-changes"></a>Cambios de las características
Las características clave de aplicaciones de la API: autenticación, CORS y las API de metadatos: se han movido directamente en la aplicación de servicio. Con este cambio, las características están disponibles a través de Web, móvil y aplicaciones de la API. De hecho, los tres compartan el mismo tipo de recurso de **Microsoft.Web/sites** en el Administrador de recursos. La puerta de enlace API aplicaciones ya no es necesario ni ofrece con la API de aplicaciones. Esto también facilita utilizar administración de la API de Azure, ya que habrá solo la puerta de enlace API administración única.

![Introducción a aplicaciones de la API](./media/app-service-api-whats-changed/api-apps-overview.png)

Principio de diseño clave con la actualización de aplicaciones de la API es para que pueda llevar su API como, en el idioma de elección.  Si ya se ha implementado la API como una aplicación Web o una aplicación móvil, no debe volver a instalar la aplicación para aprovechar las nuevas características. Si se encuentra en la vista previa de aplicaciones de la API, Guía de migración se detalla a continuación.

### <a name="authentication"></a>Autenticación
El existente listo para usar aplicaciones de la API, aplicaciones y servicios de Mobile y aplicaciones Web de autenticación características se han unificado y están disponibles en un módulo de autenticación de servicio de la aplicación de Azure único en el portal de administración. Para obtener una introducción a los servicios de autenticación en la aplicación de servicio, consulte [autenticación de servicio de aplicación expandir / autorización](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Escenarios de la API, hay un número de nuevas capacidades relevantes:

- **Compatibilidad con Azure Active Directory directamente**, sin tener que intercambiar el token AAD para un símbolo de sesión de código de cliente: el cliente puede incluir solo los tokens AAD en el encabezado de autorización, según la especificación de token portador. Esto también significa que no es necesario ningún SDK App específicos del servicio en el lado cliente o servidor. 
- **Servicio de servicio o acceso "Internal"**: si tiene un proceso de demonio o algún otro cliente que necesitan acceso a la API sin una interfaz, puede solicitar un símbolo de una entidad de seguridad del servicio AAD y pasar a la aplicación de servicio para la autenticación con la aplicación.
- **Autorización aplazado**: muchas aplicaciones tienen restricciones de acceso diferentes para las distintas partes de la aplicación. Tal vez desee algunas API esté disponible públicamente, mientras que otros requieren iniciar sesión. La característica de autenticación o autorización original fue nada, con todo el sitio que requieren iniciar sesión. Esta opción todavía existe, pero también puede permitir que el código de la aplicación representar las decisiones de acceso después de aplicación de servicio ha autenticado al usuario.
 
Para obtener más información sobre las nuevas características de autenticación, vea [autenticación y la autorización para las aplicaciones de la API de servicio de aplicaciones de Azure](app-service-api-authentication.md). Para obtener información sobre cómo migrar aplicaciones de API existentes desde el modelo de aplicaciones de API anterior a la nueva, vea [migrar aplicaciones de API existentes](#migrating-existing-api-apps) más adelante en este artículo.
 
### <a name="cors"></a>CORS
En lugar de una configuración de aplicación **MS_CrossDomainOrigins** delimitado por comas, ahora hay un módulo en el portal de administración de Azure para configurar CORS. Como alternativa, puede configurar con el Administrador de recursos de herramientas como Azure PowerShell, CLI o [Explorador de recursos](https://resources.azure.com/). Establecer la propiedad **cors** en el tipo de recurso **Microsoft.Web/sites/config** para su ** &lt;nombre del sitio&gt;/web** recursos. Por ejemplo:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>API metadatos
El módulo de definición de la API ahora está disponible a través de Web, móvil y aplicaciones de la API. En el portal de administración, puede especificar una dirección url relativa o una dirección url absoluta que apunta a un extremo dicha representación hosts a 2.0 Swagger de la API. Como alternativa, puede configurar el uso de herramientas de administrador de recursos. Establecer la propiedad **apiDefinition** en el tipo de recurso **Microsoft.Web/sites/config** para su ** &lt;nombre del sitio&gt;/web** recursos. Por ejemplo:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

En este momento, debe ser accesible públicamente sin autenticación muchos clientes de nivel inferior (por ejemplo, Visual Studio API de REST cliente de generación y flujo de "Agregar API" PowerApps) para utilizarla del extremo de metadatos. Esto significa que si está utilizando la autenticación de servicio de aplicaciones y desea exponer la definición de API de la aplicación propio, debe usar la opción de autenticación aplazado descrita anteriormente para que la ruta a los metadatos de Swagger es pública.

## <a name="management-portal"></a>Portal de administración
Seleccionar **Nuevo > Web + Mobile > API App** en el portal creará aplicaciones API que reflejen las nuevas capacidades descritas en el artículo. **Examinar > aplicaciones de la API** mostrará solamente estas aplicaciones API nuevas. Una vez que vaya a una aplicación de API, el módulo comparte el mismo diseño y las capacidades a los de Web y aplicaciones móviles. Las únicas diferencias son contenido tutorial rápido y ordenación de la configuración.

Aplicaciones de API existentes (o aplicaciones de la API de catálogo de soluciones creadas desde aplicaciones de lógica) con la versión preliminar anterior capacidades seguirán siendo visibles en el Diseñador de aplicaciones de lógica y al explorar todos los recursos en un grupo de recursos.

## <a name="visual-studio"></a>Visual Studio

La mayoría de herramientas de Web Apps funcionarán con nuevas aplicaciones API ya que comparten el mismo tipo de recurso **Microsoft.Web/sites** subyacente. Azure Visual Studio herramientas, sin embargo, debe ser actualizado a la versión 2.8.1 o posterior puesto que expone un número de capacidades específicas de la API. Descargue el SDK de la [página de descargas de Azure](https://azure.microsoft.com/downloads/).

Con la racionalización de los tipos de servicio de aplicaciones, publicar también está unificado en **Publicar > aplicación de servicio de Microsoft Azure**:

![Publicación aplicaciones API](./media/app-service-api-whats-changed/api-apps-publish.png)

Para obtener más información sobre SDK 2.8.1, lea el anuncio [de blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Como alternativa, puede importar el perfil de publicación manualmente desde el portal de administración para habilitar la publicación. Sin embargo, Explorer nube, generación de código y API aplicación selección/creación requieren SDK 2.8.1 o superior.

## <a name="migrating-existing-api-apps"></a>Migrar aplicaciones API existentes
Si se implementa la API personalizada a la versión anterior de la vista previa de aplicaciones de la API, solicitamos migrar al nuevo modelo de aplicaciones de la API de 31 de diciembre de 2015. Dado que tanto el modelo de nuevo y antiguo se basa en las API de Web alojado en la aplicación de servicio, puede reutilizar la mayoría del código existente.

### <a name="hosting-and-redeployment"></a>Hospedaje y redistribución
Los pasos para volver a implementar están la misma que implementar cualquier API Web existente al servicio de aplicación. Pasos siguientes:

1. Crear una aplicación de API vacía. Esto puede realizarse en el portal de nuevo > API App, en Visual Studio de publicar o de herramientas de administrador de recursos. Si usa herramientas de administrador de recursos o plantillas, establezca el valor de **tipo** en la **api** en el tipo de recurso **Microsoft.Web/sites** tener la configuración y tutoriales en el portal de administración orientado escenarios de la API.
2. Conectar e implemente el proyecto a la aplicación API vacía con cualquiera de los mecanismos de implementación compatibles con la aplicación de servicio. Lea la [documentación de implementación de Azure aplicación de servicio](../app-service-web/web-sites-deploy.md) para obtener más información. 
  
### <a name="authentication"></a>Autenticación
Los servicios de autenticación de la aplicación servicio compatible con las mismas capacidades que estaban disponibles con el modelo de aplicaciones de la API anterior. Si está utilizando símbolos de sesión y necesita SDK, use el SDK de cliente y de servidor siguiente:

- [Cliente móvil Azure SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) de cliente:
- [Extensión de Microsoft Azure aplicación móvil .NET autenticación](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) del servidor: 

Si estaba utilizando el SDK alfa de servicio de aplicaciones en su lugar, estos se han quedado obsoletas:

- Cliente: [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Servidor: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

En particular con Azure Active Directory, sin embargo, no específicos del servicio App se requiere si está utilizando el token AAD directamente.

### <a name="internal-access"></a>Acceso interno
El modelo de aplicaciones de la API anterior incluye un nivel de acceso interno integrado. Uso del SDK esto necesario para las solicitudes de inicio de sesión. Como se describió anteriormente, con el nuevo modelo de aplicaciones de la API principales de servicio AAD pueden utilizarse como alternativa para la autenticación de servicio de servicio sin necesidad de tener una App específicos del servicio SDK. Obtenga más del [servicio de autenticación principal para las aplicaciones de la API de servicio de aplicaciones de Azure](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Detección
El modelo de aplicaciones de la API anterior tenía API para descubrir otras aplicaciones de la API en tiempo de ejecución en el mismo grupo de recursos detrás de la misma puerta de enlace. Esto es especialmente útil en arquitecturas que implementan modelos microservice. Aunque no es compatible directamente, un número de opciones está disponible:

1. Usar la API de administrador de recursos de Azure para detección.
2. Coloque la administración de la API de Azure delante de la API hospedado por el servicio de aplicación. Administración de Azure API actúa como un frontal y puede proporcionar una dirección url de opuestas externos estable incluso si cambia de topología interna.
3. Crear su propia aplicación de la API de detección y tiene otras aplicaciones API registrar con la aplicación de descubrimiento en Inicio.
4. En tiempo de implementación, rellenar la configuración de la aplicación de todas las aplicaciones de la API (y clientes) con los extremos de las otras aplicaciones de la API. Esto es viable en implementaciones de plantilla y desde aplicaciones de la API ahora le ofrecen control de la dirección url.

## <a name="using-api-apps-with-logic-apps"></a>Uso de las aplicaciones de la API con aplicaciones de lógica

El nuevo modelo de aplicaciones de la API funciona bien con [Aplicaciones de lógica de 2015-08-01 la versión del esquema](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, lea los artículos de la [sección de la documentación de API de aplicaciones](https://azure.microsoft.com/documentation/services/app-service/api/). Se actualizaron para reflejar el nuevo modelo de aplicaciones de la API. Además, póngase en contacto en los foros para obtener más información o instrucciones sobre la migración:

- [Foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
- [Desbordamiento de pila](http://stackoverflow.com/questions/tagged/azure-api-apps)
