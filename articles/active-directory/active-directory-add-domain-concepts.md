<properties
    pageTitle="Información general y conceptual de nombres de dominio personalizado en Azure Active Directory | Microsoft Azure"
    description="Se explica el marco conceptual para el uso de nombres de dominio personalizado en Azure Active directory, incluida la federación de inicio de sesión único"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Información general y conceptual de nombres de dominio personalizado en Azure Active Directory

Un nombre de dominio es una parte importante del identificador de muchos de los recursos de directorio: forma parte de una dirección de correo electrónico o de nombre de usuario para un usuario, parte de la dirección de un grupo y puede que forme parte de la aplicación URI de identificador para una aplicación. Un recurso de Azure Active Directory (AD Azure) puede incluir un nombre de dominio que ya se comprueba que sea propietario del directorio que contiene el recurso. Solo un administrador global puede realizar las tareas de administración de dominios en Azure AD.

Nombres de dominio en Azure AD son globalmente únicos. Un nombre de dominio puede ser usado por una sola Azure AD. Si un directorio de Azure AD ha comprobado un nombre de dominio, ningún otro directorio de Azure AD puede comprobar o use ese mismo nombre de dominio.

## <a name="initial-and-custom-domain-names"></a>Nombres de dominio inicial y personalizados

Cada nombre de dominio en Azure AD es un nombre de dominio inicial, o un nombre de dominio personalizado.

Cada Azure AD viene con un nombre de dominio inicial en el formulario de contoso.onmicrosoft.com. Este nombre de dominio de nivel tercer, en este ejemplo, "contoso.onmicrosoft.com", se estableció cuando se creó el directorio, normalmente por el administrador que creó el directorio. El nombre de dominio inicial de un directorio no puede ser cambiado o eliminado. El nombre de dominio inicial, aunque es totalmente funcional, pretende principalmente para usarse como mecanismo de inicio hasta que se comprueba un nombre de dominio personalizado.

En la mayoría de los entornos de producción, un directorio tiene al menos un dominio personalizado comprobado, como "contoso.com", y es ese dominio personalizado que está visible para los usuarios finales. Un nombre de dominio personalizado es un nombre de dominio al que pertenece y por la organización, como "contoso.com", para usos como su sitio web de hospedaje. Este nombre de dominio es familiar para empleados porque es parte del nombre de usuario que usar para iniciar sesión en la red corporativa, o para enviar y recuperar correo electrónico.

Antes de poder usarlo con Azure AD, debe ser agregado al directorio de y comprobar el nombre de dominio personalizado.

## <a name="verified-and-unverified-domain-names"></a>Nombres de dominio comprobado y no verificada

El nombre de dominio inicial de un directorio implícitamente se evalúa como verificado por Azure AD. Cuando un administrador agrega un nombre de dominio personalizado a un anuncio Azure, inicialmente se encuentra en un estado no verificado. Azure AD no permite los recursos de directorio a usar un nombre de dominio sin verificar. Esto garantiza que solo directorio puede utilizar un nombre de dominio en particular, y que la organización usa el nombre de dominio es realmente la propietaria ese nombre de dominio.

Azure AD comprueba la propiedad del nombre de dominio mediante la búsqueda de un valor determinado en el archivo de zona de servicio (DNS) de nombre de dominio para el nombre de dominio. Para comprobar la propiedad de nombre de dominio, un administrador obtiene la entrada DNS de Azure AD que Azure AD buscará y agrega esa entrada en el archivo de zona DNS para el nombre de dominio. El registrador de nombres de dominio para ese dominio mantiene el archivo de zona DNS. Se muestran los pasos para comprobar un dominio en el artículo para [Agregar un dominio personalizado al directorio de Azure AD](active-directory-add-domain.md).

Agregar una entrada DNS en el archivo de zona del nombre de dominio no afecta a otros servicios de dominio, como correo electrónico o el hospedaje web.

## <a name="federated-and-managed-domain-names"></a>Nombres de dominio federado y administradas

Un nombre de dominio personalizado en Azure AD puede estar configurado para conceder a los usuarios federado un inicio de sesión en experiencia entre su local de Active Directory y Azure AD. Configurar un dominio para la federación requiere las actualizaciones de recursos con privilegios de Azure AD y también a su servidor de Active Directory de Windows. Configurar que un dominio federado debe llevar a cabo de Azure AD Connect o con PowerShell. Federar un dominio personalizado no se puede iniciar desde el portal de clásico de Azure. [Vea este vídeo para obtener información sobre la configuración de AD FS para iniciar sesión de usuario con Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Dominios que no están federados se denominan dominios administrados. El dominio inicial de un directorio de Azure AD implícitamente se evalúa como un dominio administrado.

## <a name="primary-domain-names"></a>Nombres de dominio principal

El nombre de dominio principal para un directorio es el nombre de dominio que se ha seleccionado previamente como valor predeterminado para la parte de 'dominio' el nombre de usuario, cuando un administrador crea un nuevo usuario en el [portal de clásica Azure](https://manage.windowsazure.com/) o en otro portal como el portal de administración de Office 365. Un directorio puede tener solo un nombre de dominio principal. Un administrador puede cambiar el nombre de dominio principal sea cualquiera comprobado personalizado dominio no federado, o para el dominio inicial.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Nombres de dominio en Azure AD y otros servicios en línea de Microsoft

Un nombre de dominio debe comprobarse en Azure AD antes de que se puede usar otro servicio en línea de Microsoft, como Exchange Online, SharePoint Online y Intune. Estos otros servicios generalmente requieren un administrador agregar entradas DNS de uno o más que son específicas del servicio.

Una aplicación web de Azure usa su propio mecanismo para comprobar la propiedad de un dominio. Es necesario comprobar un dominio para su uso con Azure AD incluso si se ha previamente comprobado para su uso por una aplicación web de Azure en una suscripción que se basa en esa Azure AD. Una aplicación web de Azure puede usar un nombre de dominio que se ha comprobado en un directorio diferente desde el directorio que protege la aplicación web.

## <a name="managing-domain-names"></a>Administrar nombres de dominio

Pueden completar las tareas de administración de dominios desde el portal de clásico Azure y de PowerShell. Pueden realizar muchas de las tareas con la API de Azure AD gráfico (en la versión preliminar pública).

-   [Agregar y verificar un nombre de dominio personalizado](active-directory-add-domain.md)

-   [Administración de dominios en el portal de clásico de Azure](active-directory-add-manage-domain-names.md)

-   [Usar PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Uso de la API de Azure AD Graph para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
