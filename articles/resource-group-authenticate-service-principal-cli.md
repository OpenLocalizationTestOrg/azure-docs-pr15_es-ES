<properties
   pageTitle="Crear servicio principal con Azure CLI | Microsoft Azure"
   description="Describe cómo utilizar CLI de Azure para crear una aplicación de Active Directory y el servicio principal y conceder acceso a los recursos a través de control de acceso basado en roles. Muestra cómo autenticar la aplicación con una contraseña o el certificado."
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
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Usar Azure CLI para crear una entidad de seguridad de servicio acceso a los recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Cuando tenga una aplicación o una secuencia de comandos que necesita tener acceso a los recursos, lo más probable es que no desea ejecutar este proceso en sus propias credenciales. Es posible que tenga permisos diferentes que desee para la aplicación y no desea seguir usando sus credenciales si cambian entre las responsabilidades de la aplicación. En su lugar, cree una identidad de la aplicación que incluye las credenciales de autenticación y las asignaciones de roles. Cada vez que se ejecuta la aplicación, autentica con estas credenciales. Este tema muestra cómo usar [Azure CLI para Mac, Linux y Windows](xplat-cli-install.md) para configurar una aplicación para que se ejecute en su propia identidad y las credenciales.

Con CLI de Azure, tiene dos opciones para autenticar la aplicación de AD:

 - contraseña
 - certificado

Este tema muestra cómo usar ambas opciones en Azure CLI. Si va a iniciar sesión en Azure desde un marco de programación (como Python, Ruby o Node.js), autenticación de contraseña podría ser la mejor opción. Antes de decidir si va a usar una contraseña o un certificado, vea la sección [aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de autenticación en los diferentes marcos.

## <a name="active-directory-concepts"></a>Conceptos de Active Directory

En este artículo, se crean dos objetos - la aplicación de Active Directory (AD) y el servicio principal. La aplicación de AD es la representación global de la aplicación. Contiene las credenciales (un identificador de la aplicación y una contraseña o certificado). El servicio principal es la representación de la aplicación en un Active Directory local. Contiene la asignación de roles. En este tema se centra en una aplicación de inquilinos solo donde la aplicación está diseñada para que se ejecute en sólo una organización. Normalmente se utiliza solo inquilino aplicaciones para aplicaciones de línea de negocio que se ejecutan dentro de su organización. En una aplicación de un solo inquilino, tiene una aplicación de AD y principales de un servicio.

Puede que se pregunte: ¿por qué necesito ambos objetos? Este enfoque tiene más sentido cuando considere la posibilidad de inquilinos varias aplicaciones. Utilizar normalmente inquilinos varias aplicaciones para las aplicaciones de software como servicio (SaaS), donde se ejecuta la aplicación en varias suscripciones a muchos. Para las aplicaciones de múltiples arrendatarios, tiene una aplicación de AD y varias identidades de servicio (uno en cada Active Directory que conceda acceso a la aplicación). Para configurar una aplicación de múltiples arrendatario, consulte [la Guía del desarrollador de autorización con la API del Administrador de recursos de Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permisos necesarios

Para completar este tema, debe tener permisos suficientes en Azure Active Directory y la suscripción de Azure. Más concretamente, debe poder crear una aplicación en Active Directory y asignar la entidad de seguridad de servicio a un rol. 

En Active Directory, la cuenta debe ser un administrador (como **Administrador Global** o **Administrador de usuarios**). Si su cuenta se asigna a la función de **usuario** , debe tener un administrador elevar los permisos.

En la suscripción, su cuenta debe tener `Microsoft.Authorization/*/Write` acceso, que se concede mediante la función de [propietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Si su cuenta está asignada para el rol de **Colaborador** , recibe un error al intentar asignar al capital de servicio a un rol. De nuevo, el Administrador de suscripción debe conceder acceso suficiente.

Ahora, vaya a una sección para la autenticación de [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Crear servicio principal con contraseña

En esta sección, realice los pasos para crear la aplicación de AD con una contraseña y asignar la función lector al servicio principal.

Veamos estos pasos.

1. Inicie sesión en su cuenta.

        azure login

1. Tiene dos opciones para crear la aplicación de AD. Puede crear la aplicación de AD y el servicio principal en un paso o crearlas por separado. Crearlas en un paso si no necesita especificar una página principal y el identificador URI de la aplicación. Crearlas por separado si es necesario configurar estos valores para una aplicación web. Ambas opciones se muestran en este paso.

     - Para crear la aplicación de AD y el servicio principal en un solo paso, proporcione el nombre de la aplicación y una contraseña, tal como se muestra en el siguiente comando:
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Para crear la aplicación de AD por separado, proporcione el nombre de la aplicación, una página de inicio URI, identificador URI y una contraseña, tal como se muestra en el siguiente comando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         El comando anterior devuelve un valor de Id. Para crear una entidad de seguridad de servicio, proporcione ese valor como un parámetro en el siguiente comando:
     
            azure ad sp create -a <AppId>
     
     Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica "Authentication_Unauthorized" o "No se encontró suscripción en el contexto".
    
     En ambos casos, se devuelve el nuevo principal de servicio. El **Identificador de objeto** es necesario cuando se concesión de permisos. Guid indica junto con los **Nombres a principales de servicio** es necesaria al iniciar la sesión. Este guid es el mismo valor que el identificador de aplicación. En las aplicaciones de ejemplo, este valor se conoce como el **Identificador de cliente**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceder permisos de principal de servicio de su suscripción. En este ejemplo, se agrega la entidad de seguridad de servicio la función **lector** , que concede permiso para leer todos los recursos de la suscripción. Para otras funciones, consulte [RBAC: funciones integradas](./active-directory/role-based-access-built-in-roles.md). Para el parámetro **principal de servicio** , proporcione el **Id. de objeto** que ha usado al crear la aplicación. 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que su cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' sobre el ámbito ' / suscripciones / {guid}'**. 

¡Eso es todo! La aplicación de AD y el servicio principal están configurados. En la sección siguiente se muestra cómo iniciar sesión con la credencial mediante CLI de Azure. Si desea utilizar las credenciales en la aplicación de código, no es necesario continuar con este tema. Puede ir a las [aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de iniciar sesión con su id de aplicación y la contraseña. 

### <a name="provide-credentials-through-azure-cli"></a>Proporcionar credenciales mediante CLI de Azure

Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cada vez que inicie sesión en como una entidad de seguridad de servicio, debe proporcionar el identificador del inquilino del directorio de la aplicación de AD. Un inquilino es una instancia de Active Directory. Para recuperar el identificador de inquilino para su suscripción autenticado actualmente, use:

        azure account show

     Que devuelve:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Si necesita obtener el identificador del inquilino de otra suscripción, utilice el siguiente comando:

        azure account show -s {subscription-id}

2. Si necesita recuperar el identificador de cliente para iniciar sesión, use:

        azure ad sp show -c exampleapp --json

     El valor que debe utilizar para iniciar sesión en es el guid que aparece en los nombres principales de servicio.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Inicie sesión como principal de servicio.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    Se le pedirá la contraseña. Proporcione la contraseña que especificó al crear la aplicación de AD.

        info:    Executing command login
        Password: ********

Ahora se autentican como principal de servicio para la identidad de servicio que ha creado.

## <a name="create-service-principal-with-certificate"></a>Crear servicio principal con certificado

En esta sección, realice los pasos para:

- crear un certificado autofirmado
- crear la aplicación de AD con el certificado y la entidad de seguridad de servicio
- asignar la función de lector a la identidad de servicio

Para completar estos pasos, debe tener [OpenSSL](http://www.openssl.org/) instalado.

1. Crear un certificado autofirmado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine las claves públicas y privadas.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra el archivo **examplecert.pem** y busque la secuencia larga de caracteres entre **---comenzar certificado (...)** y **---Finalizar certificado (...)**. Copie los datos del certificado. Pasar datos como un parámetro al crear el servicio principal.

1. Inicie sesión en su cuenta.

        azure login

1. Tiene dos opciones para crear la aplicación de AD. Puede crear la aplicación de AD y el servicio principal en un paso o crearlas por separado. Crearlas en un paso si no necesita especificar una página principal y el identificador URI de la aplicación. Crearlas por separado si es necesario configurar estos valores para una aplicación web. Ambas opciones se muestran en este paso.

     - Para crear la aplicación de AD y el servicio principal en un solo paso, proporcione el nombre de la aplicación y los datos del certificado, como se muestra en el siguiente comando:
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Para crear la aplicación de AD por separado, proporcione el nombre de la aplicación, una página de inicio URI, identificador URI y los datos del certificado, como se muestra en el siguiente comando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         El comando anterior devuelve un valor de Id. Para crear una entidad de seguridad de servicio, proporcione ese valor como un parámetro en el siguiente comando:
     
            azure ad sp create -a <AppId>
  
     Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica "Authentication_Unauthorized" o "No se encontró suscripción en el contexto".
    
     En ambos casos, se devuelve el nuevo principal de servicio. El identificador de objeto es necesario cuando se concesión de permisos. Guid indica junto con los **Nombres a principales de servicio** es necesaria al iniciar la sesión. Este guid es el mismo valor que el identificador de aplicación. En las aplicaciones de ejemplo, este valor se conoce como el **Identificador de cliente**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceder permisos de principal de servicio de su suscripción. En este ejemplo, se agrega la entidad de seguridad de servicio la función **lector** , que concede permiso para leer todos los recursos de la suscripción. Para otras funciones, consulte [RBAC: funciones integradas](./active-directory/role-based-access-built-in-roles.md). Para el parámetro **principal de servicio** , proporcione el **Id. de objeto** que ha usado al crear la aplicación. 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que su cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' sobre el ámbito ' / suscripciones / {guid}'**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Proporcionar certificados a través de la secuencia de comandos de Azure CLI automatizada

Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cada vez que inicie sesión en como una entidad de seguridad de servicio, debe proporcionar el identificador del inquilino del directorio de la aplicación de AD. Un inquilino es una instancia de Active Directory. Para recuperar el identificador de inquilino para su suscripción autenticado actualmente, use:

        azure account show

     Que devuelve:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Si necesita obtener el identificador del inquilino de otra suscripción, utilice el siguiente comando:

        azure account show -s {subscription-id}

1. Para recuperar la huella digital del certificado y quite los caracteres innecesarios, use:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Que devuelve un valor de huella digital similar al:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Si necesita recuperar el identificador de cliente para iniciar sesión, use:

        azure ad sp show -c exampleapp

     El valor que debe utilizar para iniciar sesión en es el guid que aparece en los nombres principales de servicio.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Inicie sesión como principal de servicio.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- Para obtener más información sobre cómo usar certificados y CLI de Azure, consulte [autenticación basada en certificado con Azure principales de servicio de la línea de comandos de Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
