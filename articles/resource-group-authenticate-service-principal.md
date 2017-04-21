<properties
   pageTitle="Crear servicio Azure principal con PowerShell | Microsoft Azure"
   description="Describe cómo usar PowerShell de Azure para crear una aplicación de Active Directory y el servicio principal y conceder acceso a los recursos a través de control de acceso basado en roles. Muestra cómo autenticar la aplicación con una contraseña o el certificado."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usar PowerShell de Azure para crear una entidad de seguridad de servicio acceso a los recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Cuando tenga una aplicación o una secuencia de comandos que necesita tener acceso a los recursos, lo más probable es que no desea ejecutar este proceso en sus propias credenciales. Es posible que tenga permisos diferentes que desee para la aplicación y no desea seguir usando sus credenciales si cambian entre las responsabilidades de la aplicación. En su lugar, cree una identidad de la aplicación que incluye las credenciales de autenticación y las asignaciones de roles. Cada vez que se ejecuta la aplicación, autentica con estas credenciales. Este tema muestra cómo usar [PowerShell Azure](powershell-install-configure.md) para configurar todo lo que necesita ejecutar en su propia identidad y las credenciales de una aplicación.

Con PowerShell, tiene dos opciones para autenticar la aplicación de AD:

 - contraseña
 - certificado

Este tema muestra cómo usar ambas opciones en PowerShell. Si va a iniciar sesión en Azure desde un marco de programación (como Python, Ruby o Node.js), autenticación de contraseña podría ser la mejor opción. Antes de decidir si va a usar una contraseña o un certificado, vea la sección [aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de autenticación en los diferentes marcos.

## <a name="active-directory-concepts"></a>Conceptos de Active Directory

En este artículo, se crean dos objetos - la aplicación de Active Directory (AD) y el servicio principal. La aplicación de AD es la representación global de la aplicación. Contiene las credenciales (un identificador de la aplicación y una contraseña o certificado). El servicio principal es la representación de la aplicación en un Active Directory local. Contiene la asignación de roles. En este tema se centra en una aplicación de inquilinos solo donde la aplicación está diseñada para que se ejecute en sólo una organización. Normalmente se utiliza solo inquilino aplicaciones para aplicaciones de línea de negocio que se ejecutan dentro de su organización. En una aplicación de un solo inquilino, tiene una aplicación de AD y principales de un servicio.

Puede que se pregunte: ¿por qué necesito ambos objetos? Este enfoque tiene más sentido cuando considere la posibilidad de inquilinos varias aplicaciones. Utilizar normalmente inquilinos varias aplicaciones para las aplicaciones de software como servicio (SaaS), donde se ejecuta la aplicación en varias suscripciones a muchos. Para las aplicaciones de múltiples arrendatarios, tiene una aplicación de AD y varias identidades de servicio (uno en cada Active Directory que conceda acceso a la aplicación). Para configurar una aplicación de múltiples arrendatario, consulte [la Guía del desarrollador de autorización con la API del Administrador de recursos de Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permisos necesarios

Para completar este tema, debe tener permisos suficientes en Azure Active Directory y la suscripción de Azure. Más concretamente, debe poder crear una aplicación en Active Directory y asignar la entidad de seguridad de servicio a un rol. 

En Active Directory, la cuenta debe ser un administrador (como **Administrador Global** o **Administrador de usuarios**). Si su cuenta se asigna a la función de **usuario** , debe tener un administrador elevar los permisos.

En la suscripción, su cuenta debe tener `Microsoft.Authorization/*/Write` acceso, que se concede mediante la función de [propietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Si su cuenta está asignada para el rol de **Colaborador** , recibe un error al intentar asignar al capital de servicio a un rol. De nuevo, el Administrador de suscripción debe conceder acceso suficiente.

Ahora, vaya a una sección para la autenticación de [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Crear servicio principal con contraseña

En esta sección, realice los pasos para:

- crear la aplicación de AD con una contraseña
- crear al principal de servicio
- asignar la función de lector a la identidad de servicio

Para realizar rápidamente estos pasos, consulte los siguientes tres cmdlets. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Veamos estos pasos más detenidamente para asegurarse de que comprender el proceso.

1. Inicie sesión en su cuenta.

        Add-AzureRmAccount

1. Crear una nueva aplicación de Active Directory al proporcionar un nombre para mostrar el URI que describa la aplicación, los URI para identificar la aplicación y la contraseña para la identidad de la aplicación.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Para las aplicaciones de un solo inquilino, no se valida los URI.
     
     Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica "Authentication_Unauthorized" o "No se encontró suscripción en el contexto".

1. Examine el objeto de la aplicación. 

        $app
        
     Tenga en cuenta la propiedad **ApplicationId** , que es necesaria para crear a principales de servicio, las asignaciones de roles y para adquirir el token de acceso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Crear una entidad de seguridad de servicio de la aplicación pasando el identificador de aplicación de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Conceder permisos de principal de servicio de su suscripción. En este ejemplo, se agrega la entidad de seguridad de servicio la función **lector** , que concede permiso para leer todos los recursos de la suscripción. Para otras funciones, consulte [RBAC: funciones integradas](./active-directory/role-based-access-built-in-roles.md). Para el parámetro **principal de servicio** , proporcione **ApplicationId** que usó al crear la aplicación. 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que su cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' sobre el ámbito ' / suscripciones / {guid}'**. 

¡Eso es todo! La aplicación de AD y el servicio principal están configurados. En la sección siguiente se muestra cómo iniciar sesión con las credenciales a través de PowerShell. Si desea utilizar las credenciales en la aplicación de código, puede saltar a las [aplicaciones de ejemplo](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Proporcionar credenciales a través de PowerShell

Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Crear un objeto **PSCredential** que contiene las credenciales, ejecute el comando **Get-Credential** . Necesita la **ApplicationId** antes de ejecutar este comando así que asegúrese de que tiene disponible para pegar.

        $creds = Get-Credential

2. Se le pide que escriba sus credenciales. El nombre de usuario, use **ApplicationId** que usó al crear la aplicación. La contraseña, use el que especificó al crear la cuenta.

     ![introducir credenciales](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Cada vez que inicie sesión en como una entidad de seguridad de servicio, debe proporcionar el identificador del inquilino del directorio de la aplicación de AD. Un inquilino es una instancia de Active Directory. Si solo tiene una suscripción, puede usar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Si tiene más de una suscripción, especifique la suscripción que reside Active Directory. Para obtener más información, vea [cómo Azure suscripciones están asociadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Inicie sesión como el principal servicio especificando que esta cuenta es una entidad de seguridad de servicio y proporcionando el objeto de credenciales. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Ahora se autentican como servicio principal para la aplicación de Active Directory que ha creado.

### <a name="save-access-token-to-simplify-log-in"></a>Guardar el token de acceso para simplificar el inicio de sesión

Para evitar que proporciona las credenciales de servicio principal cada vez que necesita para iniciar sesión, puede guardar el token de acceso.

1. Para usar el token de acceso actual en una sesión posterior, guarde el perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Abra el perfil y examinar su contenido. Observe que contiene un token de acceso. 
        
2. En lugar de forma manual a iniciar la sesión, basta con cargar el perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Expira el token de acceso, en el usando un perfil guardado solo funciona para como el token no es válido.
        
## <a name="create-service-principal-with-certificate"></a>Crear servicio principal con certificado

En esta sección, realice los pasos para:

- crear un certificado autofirmado
- crear la aplicación de AD con el certificado
- crear al principal de servicio
- asignar la función de lector a la identidad de servicio

Para realizar estos pasos con Azure PowerShell 2.0 de rápidamente en Windows 10 o Windows Server 2016 Technical Preview, vea los siguientes cmdlets. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Veamos estos pasos más detenidamente para asegurarse de que comprender el proceso. En este artículo también se muestra cómo llevar a cabo las tareas cuando se usen versiones anteriores de sistemas operativos o PowerShell de Azure.

### <a name="create-the-self-signed-certificate"></a>Crear el certificado autofirmado

La versión de PowerShell disponible con Windows 10 y Windows Server 2016 Technical Preview tiene un cmdlet de **Nuevo SelfSignedCertificate** actualizado para generar un certificado autofirmado. Sistemas operativos anteriores tiene el cmdlet New-SelfSignedCertificate pero no ofrece los parámetros necesarios para este tema. En su lugar, debe importar un módulo para generar el certificado. Este tema muestra ambos métodos para generar el certificado basado en el sistema operativo que tiene. 

- Si tiene **Windows 10 o Windows Server 2016 Technical Preview**, ejecute el comando siguiente para crear un certificado autofirmado: 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Si **no tiene Windows 10 o Windows Server 2016 Technical Preview**, debe descargar el [autofirmado generador de certificados](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) de Microsoft Script Center. Extraer su contenido e importar el cmdlet que necesita.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     A continuación, generar el certificado.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Tiene el certificado y puede continuar con la creación de la aplicación de AD.

### <a name="create-the-active-directory-app-and-service-principal"></a>Crear la aplicación de Active Directory y el servicio principal

1. Recuperar el valor de clave de certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Inicie sesión en su cuenta de Azure.

        Add-AzureRmAccount

3. Crear una nueva aplicación de Active Directory al proporcionar un nombre para mostrar el URI que describa la aplicación, los URI para identificar la aplicación y la contraseña para la identidad de la aplicación.

     Si tiene Azure PowerShell 2.0 uso (agosto 2016 o posterior), el siguiente cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Si tiene Azure PowerShell 1.0, use el siguiente cmdlet:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Para las aplicaciones de un solo inquilino, no se valida los URI.
    
    Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica "Authentication_Unauthorized" o "No se encontró suscripción en el contexto".
        
    Examine el objeto de la aplicación. 

        $app

    Observe que la propiedad **ApplicationId** , que es necesaria para crear a principales de servicio, las asignaciones de roles y adquirir tokens de acceso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Crear una entidad de seguridad de servicio de la aplicación pasando el identificador de aplicación de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Conceder permisos de principal de servicio de su suscripción. En este ejemplo, se agrega la entidad de seguridad de servicio la función **lector** , que concede permiso para leer todos los recursos de la suscripción. Para otras funciones, consulte [RBAC: funciones integradas](./active-directory/role-based-access-built-in-roles.md). Para el parámetro **principal de servicio** , proporcione **ApplicationId** que usó al crear la aplicación.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que su cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' sobre el ámbito ' / suscripciones / {guid}'**.

¡Eso es todo! La aplicación de AD y el servicio principal están configurados. En la sección siguiente se muestra cómo iniciar sesión con el certificado a través de PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Proporcionar certificados a través de la secuencia de comandos de PowerShell automatizada

Cada vez que inicie sesión en como una entidad de seguridad de servicio, debe proporcionar el identificador del inquilino del directorio de la aplicación de AD. Un inquilino es una instancia de Active Directory. Si solo tiene una suscripción, puede usar:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Si tiene más de una suscripción, especifique la suscripción que reside Active Directory. Para obtener más información, vea [administrar el directorio de Azure AD](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Para autenticar en la secuencia de comandos, especifique la cuenta es un servicio principal y proporcione la huella digital del certificado, el identificador de la aplicación y el identificador de inquilinos. Para automatizar la secuencia de comandos, puede almacenar estos valores como variables de entorno y recuperarlas durante la ejecución, o bien, puede incluir en la secuencia de comandos.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Ahora se autentican como servicio principal para la aplicación de Active Directory que ha creado.

## <a name="sample-applications"></a>Aplicaciones de ejemplo

Las siguientes aplicaciones de ejemplo muestran cómo iniciar sesión en como principal de servicio.

**.NET**

- [Implementar un SSH habilitado VM con una plantilla con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Administrar Azure y grupos de recursos con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introducción a recursos - implementar con Azure plantilla de administrador de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introducción a recursos - gestionar el grupo de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implementar un SSH habilitado VM con una plantilla en Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Administrar grupos de recursos con Python y recursos de Azure](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Implementar un SSH habilitado VM con una plantilla en Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Administrar grupos de recursos con Node.js y recursos de Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implementar un SSH habilitado VM con una plantilla de rubí](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Administrar grupos de recursos con Ruby y recursos de Azure](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Pasos siguientes
  
- Para conocer los pasos detallados sobre la integración de una aplicación en Azure para administrar los recursos, consulte [la Guía del desarrollador de autorización con la API del Administrador de recursos de Azure](resource-manager-api-authentication.md).
- Para obtener información más detallada de las aplicaciones y principales de servicio, vea [objetos de la aplicación y Principal del servicio](./active-directory/active-directory-application-objects.md). 
- Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación para Azure AD](./active-directory/active-directory-authentication-scenarios.md).



