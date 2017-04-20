<properties 
    pageTitle="Cómo guardar y configurar la configuración del servicio de administración de la API con Git" 
    description="Aprenda a guardar y configurar la configuración del servicio de administración de la API con Git." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Cómo guardar y configurar la configuración del servicio de administración de la API con Git

>[AZURE.IMPORTANT] Configuración de GIT para la administración de la API está actualmente en vista previa. Es la funcionalidad completa, pero está en vista previa porque esperamos conocidas recibir comentarios acerca de esta característica. Es posible que podemos hacer una hora cambiar en respuesta a los comentarios de los clientes, por lo que recomendamos no dependiendo de la característica para su uso en entornos de producción. Si tienes comentarios o preguntas, háganoslo saber en `apimgmt@microsoft.com`.

Cada instancia de servicio de administración de la API mantiene una base de datos de configuración que contiene información sobre la configuración y los metadatos de la instancia de servicio. Pueden realizar cambios a la instancia de servicio cambiar un valor en el portal de publisher, con un cmdlet de PowerShell o realizar una llamada de la API de REST. Además de estos métodos, también puede administrar la configuración de la instancia de servicio usando Git, habilitar escenarios de administración de servicios como:

-   Control de versiones de configuración: descargar y almacenar diferentes versiones de la configuración del servicio
-   Cambios de configuración en masa: realizar cambios en varias partes de la configuración del servicio en el repositorio local e integrar los cambios al servidor con una única operación
-   Familiar toolchain Git y flujo de trabajo - usar la Git herramientas y flujos de trabajo que ya está familiarizado con

El siguiente diagrama muestra una descripción general de las diferentes formas de configurar la instancia de servicio de administración de la API.

![Configurar GIT][api-management-git-configure]

Cuando realice cambios en el servicio con el portal de publisher, cmdlets de PowerShell o la API de REST, administra su base de datos de configuración de servicio mediante la `https://{name}.management.azure-api.net` extremo, tal como se muestra en el lado derecho del diagrama. El lado izquierdo del diagrama muestra cómo administrar la configuración del servicio mediante Git y Git repositorio para el servicio que se encuentra en `https://{name}.scm.azure-api.net`.

Los pasos siguientes proporcionan información general de la administración de la instancia de servicio de administración de la API con Git.

1.  Habilitar el acceso de Git en el servicio
2.  Guardar la base de datos de configuración de servicio en el repositorio de Git
3.  Clonar la repo Git en el equipo local
4.  Extraer la última repo hacia abajo hasta el equipo local y los cambios de confirmación e inserción vuelve a su repo
5.  Implementar los cambios de su repo en la base de datos de configuración de servicio

En este artículo se describe cómo habilitar y usar Git para administrar la configuración del servicio y proporciona una referencia de los archivos y carpetas en el repositorio de Git.

## <a name="to-enable-git-access"></a>Para habilitar el acceso de Git

Puede ver rápidamente el estado de la configuración de Git viendo el icono Git en la esquina superior derecha del portal de publisher. En este ejemplo, access Git ha no se ha habilitado.

![Estado de GIT][api-management-git-icon-enable]

Para ver y configurar las opciones de configuración de Git, puede haga clic en el icono Git, o haga clic en el menú de **seguridad** y vaya a la ficha **repositorio de configuración** .

![Habilitar GIT][api-management-enable-git]

Para habilitar el acceso de Git, active la casilla de verificación **Habilitar Git acceso** .

Tras unos instantes se guarda el cambio y se muestra un mensaje de confirmación. Tenga en cuenta que el icono de Git ha cambiado a color para indicar que access Git está habilitado y ahora el mensaje de estado indica que hay cambios no guardados en el repositorio. Esto es porque la base de datos de configuración de servicio de administración de la API aún no se ha guardado en el repositorio.

![GIT habilitado][api-management-git-enabled]

>[AZURE.IMPORTANT] Cualquier información confidencial que no se define como propiedades se almacenan en el repositorio y permanecerán en su historial hasta deshabilita y volver a habilita acceso Git. Propiedades proporcionan un lugar seguro para administrar los valores de cadena constante, incluidas información confidencial, en todos los de configuración de la API y directivas, para que no tenga que almacenan directamente en las instrucciones de la directiva. Para obtener más información, vea [cómo usar las propiedades de las directivas de administración de la API de Azure](api-management-howto-properties.md).

Para obtener información sobre habilitar o deshabilitar el acceso a la Git con la API de REST, vea [Habilitar o deshabilitar el acceso a la Git con la API de REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para guardar la configuración del servicio en el repositorio de Git

Es el primer paso antes de clonar el repositorio guardar el estado actual de la configuración del servicio en el repositorio. Haga clic en **Guardar configuración al repositorio**.

![Guardar la configuración][api-management-save-configuration]

Realice los cambios que desee en la pantalla de confirmación y haga clic en **Aceptar** para guardar.

![Guardar la configuración][api-management-save-configuration-confirm]

Pasados unos momentos se guarda la configuración y se muestra el estado de la configuración del repositorio, incluyendo la fecha y hora del último cambio de configuración y la última sincronización entre la configuración del servicio y el repositorio.

![Estado de la configuración][api-management-configuration-status]

Una vez que la configuración se guarda en el repositorio, puede clonar.

Para obtener información sobre cómo realizar esta operación con la API de REST, vea [Confirmar instantánea de la configuración con la API de REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Clonar el repositorio en el equipo local

Para clonar un repositorio, necesitará la dirección URL del repositorio, un nombre de usuario y una contraseña. El nombre de usuario y la dirección URL se muestran en la parte superior de la ficha **repositorio de configuración** .

![Clonar GIT][api-management-configuration-git-clone]

La contraseña se genera en la parte inferior de la ficha **repositorio de configuración** .

![Generar contraseña][api-management-generate-password]

Para generar una contraseña, primero asegúrese de que la **expiración** se establece en la fecha de caducidad deseada y la hora y, a continuación, haga clic en **Generar Token**.

![Contraseña][api-management-password]

>[AZURE.IMPORTANT] Anote esta contraseña. Una vez que sale de esta página no se mostrará nuevamente la contraseña.

Los ejemplos siguientes usan la herramienta de Git fiesta de [Git para Windows](http://www.git-scm.com/downloads) , pero puede usar cualquier herramienta Git que está familiarizado con.

Abra la herramienta Git en la carpeta que desee y ejecute el comando siguiente para clonar el repositorio git a su equipo local, con el comando proporcionado por el portal de publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Proporcione el nombre de usuario y contraseña cuando se le solicite.

Si recibe errores, pruebe a modificar el `git clone` comando para incluir el nombre de usuario y contraseña, tal como se muestra en el ejemplo siguiente.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Si proporciona un error, pruebe a la parte de la contraseña del comando de codificación de direcciones URL. Una forma rápida de hacerlo es abrir Visual Studio y a continuación, ejecute el comando siguiente en la **Ventana Inmediato**. Para abrir la **Ventana Inmediato**, abra cualquier solución o un proyecto de Visual Studio (o cree una nueva aplicación de consola vacía) y elija **Windows**, **inmediato** en el menú **Depurar** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Utilice la contraseña codificada junto con la ubicación de repositorio y nombre de usuario para construir el comando git.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Una vez que el repositorio es clonar puede ver y trabajar con él en el sistema de archivos local. Para obtener más información, vea [referencia de estructura de archivos y carpetas de repositorio Git local](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para actualizar su repositorio local con la configuración de la instancia de servicio más reciente

Si realiza cambios en la instancia de servicio de administración de la API en el portal de publisher o con la API de REST, debe guardar estos cambios en el repositorio antes de que se puede actualizar su repositorio local con los cambios más recientes. Para ello, haga clic en **Guardar configuración al repositorio** en la ficha **repositorio de configuración** en el portal de publisher y, a continuación, ejecute el siguiente comando en el repositorio local.

    git pull

Antes de ejecutar `git pull` Asegúrese de que está en la carpeta de repositorio local. Si acaba de completar la `git clone` comando, a continuación, debe cambiar el directorio a su repo ejecutando un comando parecido al siguiente.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para aplicar los cambios de su repo local a repo de servidor

Para aplicar los cambios en el repositorio local al repositorio del servidor, debe confirmar los cambios y, a continuación, las notificaciones al repositorio del servidor. Para confirmar los cambios, abra la herramienta de comando Git, cambie al directorio del repositorio local y emita los siguientes comandos.

    git add --all
    git commit -m "Description of your changes"

Para insertar todas las confirmaciones en el servidor, ejecute el siguiente comando.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implementar los cambios de configuración de servicio del servicio de administración de la API

Una vez que los cambios locales se confirmada y se inserta al repositorio del servidor, se puede implementar a su instancia de servicio de administración de la API.

![Implementar][api-management-configuration-deploy]

Para obtener información sobre cómo realizar esta operación con la API de REST, vea [implementar Git cambios a la base de datos de configuración con la API de REST](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referencia de estructura de archivos y carpetas de repositorio Git local

Los archivos y carpetas en el repositorio local git contienen la información de configuración acerca de la instancia de servicio.

| Elemento                       | Descripción                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| carpeta de administración de la api de raíz | Contiene la configuración de nivel superior de la instancia de servicio                                  |
| carpeta de API                | Contiene la configuración de las API de la instancia de servicio                            |
| carpeta de grupos              | Contiene la configuración de los grupos de la instancia de servicio                          |
| carpeta directivas            | Contiene las directivas de la instancia de servicio                                              |
| carpeta portalStyles        | Contiene la configuración de las personalizaciones de portal del desarrollador de la instancia de servicio |
| carpeta de productos            | Contiene la configuración de los productos de la instancia de servicio                        |
| carpeta de plantillas           | Contiene la configuración de las plantillas de correo electrónico de la instancia de servicio                 |

Cada carpeta puede contener uno o más archivos y, en algunos casos, una o varias carpetas, por ejemplo una carpeta para cada API, producto o grupo. Los archivos dentro de cada carpeta son específicos para el tipo de entidad descrito por el nombre de carpeta.

| Tipo de archivo | Propósito                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Información de configuración de la entidad correspondiente                  |
| HTML      | Descripciones de la entidad, con qué frecuencia se muestran en el portal de desarrollador |
| XML       | Instrucciones de directiva                                                      |
| CSS       | Hojas de estilos para la personalización de portal de desarrollador                        |

Estos archivos pueden crear, eliminados, editar y administrados en el sistema de archivos local y los cambios implementados vuelve a la instancia del servicio de administración de la API.

>[AZURE.NOTE] Las siguientes entidades no están en el repositorio de Git y no se pueden configurar mediante Git.
>
>-    Usuarios
>-    Suscripciones
>-    Propiedades
>-    Entidades de portal de desarrollador distintas de estilos

### <a name="root-api-management-folder"></a>Carpeta de administración de la api de raíz

La raíz `api-management` carpeta contiene un `configuration.json` archivo que contiene información acerca de la instancia de servicio en el siguiente formato de nivel superior.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

La configuración de cuatro primeros (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, y `UserRegistrationTermsConsentRequired`) se asignan a la siguiente configuración en la pestaña **identidades** en la sección **seguridad** .

| Configuración de identidad                     | Se asigna a                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | Casilla de verificación **redirigir a los usuarios anónimos a página de inicio de sesión** |
| UserRegistrationTerms                | Cuadro de texto de **términos de uso en el registro de usuario**               |
| UserRegistrationTermsEnabled         | Casilla de verificación **Mostrar términos de uso en la página de suscripción**         |
| UserRegistrationTermsConsentRequired | Casilla de verificación **Requerir consentimiento**                          |

![Configuración de identidad][api-management-identity-settings]

La configuración de cuatro (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, y `DelegationValidationKey`) se asignan a la siguiente configuración en la ficha **delegación** en la sección **seguridad** .

| Configuración de la delegación           | Se asigna a                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Casilla de verificación de **delegado de inicio de sesión y suscripción**    |
| DelegationUrl                | Cuadro de texto **dirección URL del extremo de la delegación**        |
| DelegatedSubscriptionEnabled | Casilla de verificación de **suscripción de producto de delegado** |
| DelegationValidationKey      | Cuadro de texto de **Tecla de validación de delegado**        |

![Configuración de la delegación][api-management-delegation-settings]

La configuración final, `$ref-policy`, asigna el archivo de instrucciones de directiva global de la instancia de servicio.

### <a name="apis-folder"></a>carpeta de API

La `apis` carpeta contiene una carpeta para cada API de la instancia de servicio que contiene los elementos siguientes.

-   `apis\<api name>\configuration.json`-se trata de la configuración de la API y contiene información sobre la dirección URL del servicio back-end y las operaciones. Esta es la misma información que se devolverá si desea llamar a [obtener una determinada API](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) `export=true` en `application/json` formato.
-   `apis\<api name>\api.description.html`-Esta es la descripción de la API y corresponde a la `description` propiedad de la [entidad de API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-Esta carpeta contiene `<operation name>.description.html` archivos que se asignan a las operaciones de la API. Cada archivo contiene la descripción de una única operación en la API que se asigna a la `description` propiedad de la [entidad de operación](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) en la API de REST.

### <a name="groups-folder"></a>carpeta de grupos

La `groups` carpeta contiene una carpeta para cada grupo definido en la instancia de servicio.

-   `groups\<group name>\configuration.json`-Esta es la configuración del grupo. Esta es la misma información que se devolverá si desea llamar a la operación de [obtener un grupo específico](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
-   `groups\<group name>\description.html`-Esta es la descripción del grupo y corresponde a la `description` propiedad de la [entidad de grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>carpeta directivas

La `policies` carpeta contiene las instrucciones de la directiva de la instancia de servicio.

-   `policies\global.xml`-contiene directivas definidas en el ámbito global de la instancia de servicio.
-   `policies\apis\<api name>\`-Si tiene cualquier directivas definidas en el ámbito de la API, se encuentran en esta carpeta.
-   `policies\apis\<api name>\<operation name>\`carpeta: si tiene cualquier directivas definidas en el ámbito de la operación, se encuentran en esta carpeta en `<operation name>.xml` archivos que se asignan a las instrucciones de directiva para cada operación.
-   `policies\products\`-Si tiene cualquier directivas definidas en el ámbito del producto, se encuentran en esta carpeta, que contiene `<product name>.xml` archivos que se asignan a las instrucciones de directiva para cada producto.

### <a name="portalstyles-folder"></a>carpeta portalStyles

La `portalStyles` carpeta contiene hojas de estilo y la configuración para personalizaciones del portal para programadores de la instancia de servicio.

-   `portalStyles\configuration.json`-contiene los nombres de las hojas de estilos usados por el portal de programadores
-   `portalStyles\<style name>.css`-cada `<style name>.css` archivo contiene estilos para el portal de programadores (`Preview.css` y `Production.css` de forma predeterminada).

### <a name="products-folder"></a>carpeta de productos

La `products` carpeta contiene una carpeta para cada producto que se definen en la instancia de servicio.

-   `products\<product name>\configuration.json`-Esta es la configuración para el producto. Esta es la misma información que se devolverá si desea llamar a la operación de [obtener un producto específico](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-Esta es la descripción del producto y corresponde a la `description` propiedad de la [entidad de producto](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) en la API de REST.

### <a name="templates"></a>plantillas

La `templates` carpeta contiene la configuración de las [plantillas de correo electrónico](api-management-howto-configure-notifications.md) de la instancia de servicio.

-   `<template name>\configuration.json`-Esta es la configuración de la plantilla de correo electrónico.
-   `<template name>\body.html`-Éste es el cuerpo de la plantilla de correo electrónico.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras formas de administrar la instancia de servicio, consulte:

-   Administrar la instancia de servicio mediante los siguientes cmdlets de PowerShell
    -   [Implementación del servicio de referencia de cmdlet de PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Administración del servicio de referencia de cmdlet de PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Administrar la instancia de servicio en el portal de publisher
    -   [Administrar su primera API](api-management-get-started.md)
-   Administrar la instancia de servicio con la API de REST
    -   [Referencia de la API de REST API administración](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Ver un vídeo de introducción

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




