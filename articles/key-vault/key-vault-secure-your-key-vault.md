<properties
    pageTitle="Proteger su cámara clave | Microsoft Azure"
    description="Administrar permisos de acceso para depósito clave para administrar depósitos y teclas e información confidencial. Modelo de autenticación y la autorización de depósito clave y cómo proteger su cámara clave"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>Proteger su cámara clave

Depósito de clave de Azure es un servicio de nube que protege la información confidencial (como certificados, cadenas de conexión, contraseñas) para las aplicaciones de la nube y claves de cifrado. Puesto que estos datos son importantes y críticas para el negocio, que desea proteger el acceso a su claves depósitos para que solo los autorizados aplicaciones y los usuarios pueden acceder a depósito clave. Este artículo ofrece una descripción general del modelo de acceso de clave depósito, explica la autenticación y la autorización y describe cómo proteger el acceso a depósito clave para las aplicaciones de nube con un ejemplo.

## <a name="overview"></a>Información general

Acceso a un depósito clave está controlado mediante dos interfaces diferentes: plano de administración y plano de datos. Para ambos planos autorización y la autenticación apropiada es necesario para que una llamada (un usuario o una aplicación) puede obtener acceso a depósito clave. Autenticación establece la identidad del llamador, mientras que la autorización determina las operaciones que puede realizar la llamada.

Autenticación de plano de administración y plano de datos usan Azure Active Directory. Para la autorización, plano de administración utiliza control de acceso basado en roles (RBAC) mientras plano de datos utiliza la directiva de acceso de la clave de cámara.

Aquí es un breve resumen de los temas tratados:

[Autenticación con Azure Active Directory](#authentication-using-azure-active-direcrory) : en esta sección se explica cómo se autentica una llamada con Azure Active Directory para tener acceso a un depósito clave a través de plano de administración y plano de datos. 

[Plano de administración y plano de datos](#management-plane-and-data-plane) - plano de administración y plano de datos son dos planos de access utilizados para acceder a su cámara clave. Cada plano de acceso es compatible con operaciones específicas. Esta sección describen los extremos de access, operaciones compatibles y el método de control por cada plano de acceso. 

[Control de acceso de plano de administración](#management-plane-access-control) - en esta sección que veremos permitir el acceso a las operaciones de plano de administración con control de acceso basado en roles.

[Plano de datos de control de acceso](#data-plane-access-control) : esta sección describe cómo usar la directiva de acceso de la clave de cámara para controlar el acceso de plano de datos.

Por [ejemplo](#example) , en este ejemplo se describe cómo configurar el control de acceso para su depósito clave permitir que los tres diferentes equipos (grupo de seguridad, los desarrolladores y operadores y auditores) para realizar tareas específicas para desarrollar, administrar y supervisar una aplicación en Azure.


## <a name="authentication-using-azure-active-directory"></a>Autenticación con Azure Active Directory

Cuando se crea un depósito clave en una suscripción de Azure, se asocian automáticamente con el inquilino de Azure Active Directory de la suscripción. Todos los autores de llamadas (usuarios y aplicaciones) deben estar registrados en este inquilino para tener acceso a este depósito clave. Una aplicación o un usuario debe autenticar con Azure Active Directory para tener acceso a depósito clave. Esto se aplica a ambas acceso de plano de plano y datos de administración. En ambos casos, una aplicación puede tener acceso clave depósito de dos maneras:

-  **usuario + acceso de la aplicación** - normalmente es para las aplicaciones que tienen acceso a depósito clave en el nombre de un usuario que ha iniciado sesión. PowerShell Azure, Portal de Azure son ejemplos de este tipo de acceso. Hay dos formas para conceder acceso a los usuarios: es una forma de conceder acceso a los usuarios para que pueden acceder a depósito clave desde cualquier aplicación y la otra forma es conceder acceso a un usuario a depósito clave solo al usar una aplicación específica (denominada identidad compuesto). 
-  **acceso de solo aplicación** - para las aplicaciones que se ejecutan servicios daemon, trabajos etcetera en segundo plano. Identidad de la aplicación tiene acceso a la clave depósito.

En ambos tipos de aplicaciones, la aplicación autentica con Azure Active Directory con cualquiera de los [métodos de autenticación compatibles](../active-directory/active-directory-authentication-scenarios.md) y adquiere un símbolo. El método de autenticación depende del tipo de aplicación. A continuación, la aplicación usa este token y envía la solicitud de API de REST en depósito de clave. En el caso de administración de acceso de plano las solicitudes se enrutan a través de extremo del Administrador de recursos de Azure. Al acceder a plano de datos, la aplicaciones se comunica directamente con una clave de depósito extremo. Ver más detalles sobre el [flujo de autenticación todo](../active-directory/active-directory-protocols-oauth-code.md). 

El nombre del recurso para el que la aplicación solicita un token es diferente en función de si tiene acceso a la aplicación de plano de administración o plano de datos. Por lo tanto, el nombre del recurso es cualquier extremo administración plano o datos plano descrito en la tabla en una sección posterior, según el entorno de Azure.

Tener un único mecanismo para la autenticación de administración y plano de datos tiene sus propias ventajas:

- Organizaciones centralmente pueden controlar el acceso a todos los depósitos claves de su organización
- Si un usuario deja, al instante pierdan acceso a todos los depósitos claves de la organización
- Las organizaciones pueden personalizar la autenticación a través de las opciones de Azure Active Directory (por ejemplo, habilitar autenticación multifactor para una mayor seguridad)

## <a name="management-plane-and-data-plane"></a>Plano de administración y plano de datos

Depósito de clave Azure es un servicio de Azure disponible a través del modelo de implementación de administrador de recursos de Azure. Cuando se crea un depósito clave, obtendrá un contenedor virtual dentro de la que puede crear otros objetos como claves, información confidencial y certificados. A continuación, obtener acceso a su clave depósito con plano de administración y plano de datos para realizar operaciones específicas. Interfaz de administración de plano se usa para administrar su cámara clave propia, como crear, eliminar, actualizar los atributos de clave de cámara y configuración de directivas de acceso para plano de datos. Interfaz de plano de datos se utiliza para agregar, eliminar, modificar y usar las teclas, información confidencial y certificados almacenados en su cámara clave.

Se tiene acceso a las interfaces de plano de plano y datos de administración a través de los extremos diferentes (consulte la tabla). La segunda columna de la tabla describe los nombres DNS para estos extremos en diferentes entornos de Azure. La tercera columna describe las operaciones que se pueden realizar desde cada plano de acceso. Cada plano de acceso también tiene su propio mecanismo de control de acceso: de control de acceso de plano de administración se establece con Control de acceso de Azure Resource Manager Role-Based (RBAC), mientras que para control de acceso de plano de datos se establece mediante la directiva de acceso de depósito clave.

| Plano de acceso | Extremos de Access | Operaciones | Mecanismo de control de acceso|
|--------------|------------------|--------------------|--------|
| Plano de administración|**Global:**<br> Management.Azure.com:443<br><br> **China Azure:**<br> Management.chinacloudapi.CN:443<br><br> **Azure gobierno:**<br> Management.usgovcloudapi.NET:443<br><br> **Alemania Azure:**<br> Management.microsoftazure.de:443 | Crear, leer o actualizar o eliminar clave depósito <br> Establecer directivas de acceso para depósito clave<br>Conjunto de etiquetas para depósito clave | Control de acceso basado en roles de administrador de recursos de Azure (RBAC) |
| Plano de datos | **Global:**<br> &lt;nombre de depósito&gt;. vault.azure.net:443<br><br> **China Azure:**<br> &lt;nombre de depósito&gt;. vault.azure.cn:443<br><br> **Azure gobierno:**<br> &lt;nombre de depósito&gt;. vault.usgovcloudapi.net:443<br><br> **Alemania Azure:**<br> &lt;nombre de depósito&gt;. vault.microsoftazure.de:443 | Para las claves: Descifrar, cifrar, UnwrapKey, WrapKey, comprobar, inicie sesión, obtener, lista, actualizar, crear, importar, eliminar, copia de seguridad, restaurar<br><br> Para información confidencial: obtener, lista, conjunto, eliminar | Directiva de acceso de la clave de cámara|

Los controles de acceso administración plano y datos plano transmiten de forma independiente. Por ejemplo, si desea conceder acceso a una aplicación para usar las teclas de un depósito clave, solo debe conceder permisos de acceso de plano de datos usando directivas de acceso de la clave de cámara y acceso de plano de administración no es necesario para esta aplicación. Por el contrario, si desea que un usuario pueda leer las propiedades de la cámara y etiquetas, pero no tiene acceso a las teclas, el secreto o certificados, puede conceder a este usuario 'acceso de lectura' utilizando RBAC y acceso al plano de datos no es necesario.

## <a name="management-plane-access-control"></a>Control de acceso de plano de administración

Plano de administración consta de las operaciones que afectan a la cámara clave propiamente dicho. Por ejemplo, puede crear o eliminar un depósito clave. Puede obtener una lista de depósitos en una suscripción. Puede recuperar las propiedades de cámara clave (como etiquetas SKU) y establecer clave depósito directivas de acceso que los usuarios y las aplicaciones que pueden tener acceso a claves y la información confidencial en la caja fuerte de clave de control. Control de acceso de plano de administración usa RBAC. Vea la lista completa de las operaciones de depósito clave que pueden realizarse a través de plano de administración de la tabla en la sección anterior. 

### <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)
Cada suscripción Azure tiene Azure Active Directory. Usuarios, grupos y aplicaciones desde este directorio pueden tener concedidas acceso para administrar los recursos en la suscripción de Azure que usan el modelo de implementación de administrador de recursos de Azure. Este tipo de control de acceso se conoce como Control de acceso basado en roles (RBAC). Para administrar este acceso, puede usar el [portal de Azure](https://portal.azure.com/), las [Herramientas de Azure CLI](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)o las [API de REST de administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Con el modelo de administrador de recursos de Azure, cree su cámara clave en un grupo y control de acceso a los recursos el plano de administración de la caja fuerte clave mediante Azure Active Directory. Por ejemplo, puede asignar usuarios o una capacidad de grupo para administrar claves depósitos en un grupo de recursos específico.

Puede conceder acceso a los usuarios, grupos y aplicaciones en un ámbito específico asignando funciones RBAC adecuadas. Por ejemplo, para conceder acceso a un usuario para administrar depósitos claves tendría que asignar una función predefinida 'clave depósito colaborador' a este usuario en un ámbito específico. El ámbito en este caso sería una suscripción, un grupo de recursos o simplemente un depósito de clave específico. Una función asignada en el nivel de la suscripción se aplica a todos los grupos de recursos y recursos de la suscripción. Una función asignada en el nivel de grupo de recursos se aplica a todos los recursos de ese grupo de recursos. El rol asignado un recurso específico solo se aplica a ese recurso. Hay varias funciones predefinidas (consulte [RBAC: funciones integradas](../active-directory/role-based-access-built-in-roles.md)), y si los roles predefinidos no se ajusten a sus necesidades también puede definir sus propios roles.

>[AZURE.IMPORTANT] Tenga en cuenta que si un usuario tiene colaborador permisos (RBAC) a un plano de administración de claves depósito, puede conceden su propio acceso al plano de datos, establecer clave depósito directiva de acceso, que controla el acceso al plano de datos. Por lo tanto, se recomienda estrechamente los controlar quién tiene acceso 'Colaborador' a su claves depósitos garantizar que sólo las personas autorizadas pueden acceder y administrar depósitos claves, las claves, información confidencial y certificados.

## <a name="data-plane-access-control"></a>Control de acceso de datos plano

El plano de datos de depósito clave consta de las operaciones que afectan a los objetos en un depósito clave, como las teclas, información confidencial y certificados.  Esto incluye clave operaciones como crean, importar, actualizar, lista, copia de seguridad y restauración de teclas, operaciones cifradas como inicio de sesión, compruebe, cifrar, descifrar, ajustar y desajustar y establecer etiquetas y otros atributos de teclas. Del mismo modo, para información confidencial incluye, obtener, establecer, lista, eliminar.

Acceso de plano de datos se concede mediante la configuración de directivas de acceso para un depósito clave. Un usuario, grupo o una aplicación debe tener permisos de colaborador (RBAC) para plano de administración para un depósito clave poder establecer directivas de acceso para ese depósito clave. Un usuario, grupo o aplicación puede acceder para realizar operaciones específicas para claves o información confidencial en un depósito clave. depósito clave admite hasta 16 entradas de directiva de acceso para un depósito clave. Crear un grupo de seguridad de Azure Active Directory y agregar usuarios a ese grupo para conceder acceso de plano de datos a varios usuarios a un depósito clave.

### <a name="key-vault-access-policies"></a>depósito clave directivas de acceso

las directivas de acceso de clave depósito concesión de permisos para claves, información confidencial y certificados por separado. Por ejemplo, puede dar acceso a claves solo un usuario, pero sin permisos para la información confidencial. Sin embargo, los permisos de acceso a claves o información confidencial o certificados se encuentran en el nivel de cámara. En otras palabras, la directiva de acceso de clave depósito no admite permisos de nivel de objeto. Puede usar [portal Azure](https://portal.azure.com/), las [Herramientas de Azure CLI](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)o la [cámara clave API de REST de administración](https://msdn.microsoft.com/library/azure/mt620024.aspx) para configurar directivas de acceso para un depósito clave.

>[AZURE.IMPORTANT] Tenga en cuenta que las directivas de acceso de clave depósito se aplican en el nivel de la cámara. Por ejemplo, cuando un usuario tiene permiso para crear y eliminar claves, puede realizar estas operaciones en todas las claves en ese depósito clave.

## <a name="example"></a>Ejemplo

Supongamos que el desarrollo de una aplicación que utiliza un certificado SSL, almacenamiento de Azure para almacenar datos y una clave RSA 2048 bits para operaciones de inicio de sesión. Supongamos que esta aplicación se ejecuta en una máquina virtual (o un conjunto de escala de VM). Puede usar un depósito clave para almacenar la información de confidencial de aplicación y usar depósito clave para almacenar el certificado de arranque que utiliza la aplicación para autenticar con Azure Active Directory.

Por lo tanto, le presentamos un resumen de todas las claves y la información confidencial se almacenen en un depósito clave.

- **Certificado SSL** - para SSL
- **Clave de almacenamiento** - utilizada para obtener acceso a la cuenta de almacenamiento
- **Clave RSA 2048 bits** - utilizada para operaciones de inicio de sesión
- **Certificado de inicio** : utilizado para autenticar a Azure Active Directory para obtener acceso a un depósito clave para obtener la clave de almacenamiento y utilizar la clave RSA para firmar

Ahora vamos a cumplir con las personas que va a administrar, implementar y auditoría de esta aplicación. En este ejemplo vamos a utilizar tres roles.

- **Grupo de seguridad** - estos suelen personal de TI de la oficina de la CSO (Director de seguridad) o equivalente, responsable de la conservación adecuada de información confidencial, como los certificados SSL, las claves RSA utilizados para la firma, cadenas de conexión para bases de datos, las claves de cuenta de almacenamiento.
- **Los programadores y operadores** - son las personas que desarrollan esta aplicación y, a continuación, implementación en Azure. Normalmente, no forman parte del equipo de seguridad y, por tanto, no tendrán acceso a información confidencial, como los certificados SSL, las claves RSA, pero la aplicación que implemente debe tener acceso a los.
- **Auditores** - suele ser un conjunto diferente de personas, aislado de los desarrolladores y el personal de TI general. Es su responsabilidad revisar uso correcto y el mantenimiento de certificados, claves, etc. y garantizar el cumplimiento de estándares de seguridad de datos. 

Hay un rol más que está fuera del alcance de esta aplicación, pero aquí relevante mencionar y que sería la suscripción (o grupo de recursos) Administrador. Administrador de la suscripción se configura los permisos de acceso inicial para el grupo de seguridad. Aquí se asume que el Administrador de la suscripción ha concedido el acceso al equipo de seguridad a un grupo de recursos en la que todos los recursos necesarios para este residen de aplicación.

Ahora vamos a ver qué acciones realiza cada rol en el contexto de esta aplicación.

- **Grupo de seguridad**
    - Crear claves depósitos
    - Activa clave depósito registro
    - Agregar claves o información confidencial
    - Crear una copia de seguridad de las claves de recuperación
    - Establecer la directiva de acceso de depósito clave para conceder permisos a los usuarios y aplicaciones para realizar operaciones específicas
    - Deshacer periódicamente las claves o información confidencial
- **Los programadores y operadores**
    - Obtener referencias a iniciar y certificados SSL (huellas digitales), la clave de almacenamiento (secreto URI) e iniciar sesión (URI de clave) de la clave de grupo de seguridad
    - Desarrollar e implementar aplicación que tiene acceso a las claves y la información confidencial mediante programación
- **Auditores**
    - Examinar los registros de uso para confirmar uso adecuado de clave o secreto y cumplimiento de normas de seguridad de datos

Ahora vamos a ver qué permisos de acceso a depósito clave son necesarios por cada rol (y la aplicación) para realizar las tareas asignadas. 

| Rol de usuario    | Permisos de administración de plano | Permisos de plano de datos |
|--------------|------------------------------|------------------------|
|Grupo de seguridad|depósito clave colaborador|Teclas: hacer copia de seguridad, crear, eliminar, obtener, importar, lista, restaurar <br> Información confidencial: todos|
|Los desarrolladores/operador| depósito clave implementar permisos para que las VM implementan pueden tener acceso a información confidencial desde la cámara clave | Ninguno |
|Auditores| Ninguno | Teclas: lista<br>Información confidencial: lista|
|Aplicación| Ninguno | Teclas: inicio de sesión<br>Información confidencial: obtener |

>[AZURE.NOTE] Auditores necesitan enumerar permisos para claves y la información confidencial para que puedan inspeccionar atributos de claves y la información confidencial que no se emite en los registros, como etiquetas, fechas de activación y expiración.

Además de los permisos a depósito clave, todos los roles de tres también necesitan tener acceso a otros recursos. Por ejemplo, para que pueda implementar máquinas virtuales (o etcetera de aplicaciones Web). Los programadores y operadores también necesitan acceso de 'Colaborador' a esos tipos de recursos. Auditores tenga acceso de lectura a la cuenta de almacenamiento donde se almacenan los registros de depósito clave.

Dado que el enfoque de este artículo es proteger el acceso a su cámara clave, nos ilustrar las partes relevantes relacionados con este tema solo y omitir los detalles de implementación de certificados, obtener acceso a las claves y la información confidencial mediante programación etcetera. Esos detalles ya están cubiertos en otra aplicación. Implementar certificados almacenados en depósito de clave a máquinas virtuales se aborda con una [entrada de blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)y hay [código de ejemplo](https://www.microsoft.com/download/details.aspx?id=45343) disponibles que muestra cómo utilizar inicio certificado para autenticar a Azure AD para obtener acceso a la cámara clave.

La mayoría de los permisos de acceso se puede conceder con Azure portal, pero para conceder permisos específicos necesita usar PowerShell Azure (o CLI de Azure) para obtener el resultado deseado. 

Suponen que los fragmentos de código de PowerShell siguientes:

- El Administrador de Azure Active Directory ha creado grupos de seguridad que representan los tres roles, es decir, grupo de seguridad de Contoso, Devops de la aplicación de Contoso, Contoso App Auditors. El administrador también ha agregado usuarios a los grupos que pertenecen.

- **ContosoAppRG** es el grupo de recursos donde se encuentran todos los recursos. **contosologstorage** es donde se almacenan los registros. 

- Cuenta de **ContosoKeyVault** y almacenamiento de depósito clave utilizada para depósito clave registros **contosologstorage** debe estar en la misma ubicación de Azure


En primer lugar el Administrador de suscripción asigna roles ' Administrador de acceso de usuario ' y 'clave depósito colaborador' al equipo de seguridad. Esto permite al equipo de seguridad administrar el acceso a otros recursos y administrar depósitos claves en el grupo de recursos ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

La siguiente secuencia de comandos ilustra cómo el equipo de seguridad puede crear un depósito clave, configuración de registro y establecer permisos de acceso para otras funciones y la aplicación. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

El rol personalizado definido, es solo puede asignar a la suscripción donde se crea el grupo de recursos de ContosoAppRG. Si se usan los mismos roles personalizados para otros proyectos en otras suscripciones, su ámbito puede tener más suscripciones agregadas.

La asignación de roles personalizados para los programadores y operadores para el permiso de "Implementar/acción" ámbito es el grupo de recursos. De este modo, solo las VM creadas en el grupo de recursos 'ContosoAppRG' obtendrá la información confidencial (certificado SSL y certificados inicio). Cualquier máquinas virtuales de un miembro del equipo de desarrollo y operadores crea en otro grupo de recursos no podrán obtener estas información confidencial, incluso si conocían a los URI secreto.

Este ejemplo muestra un escenario simple. Escenarios de vida real pueden ser más complejos y necesita ajustar los permisos a su clave depósito según sus necesidades. Por ejemplo, en nuestro ejemplo, se supone ese grupo de seguridad le proporcionará la clave y referencias secretas (URI y huellas digitales) ese grupo de programadores y operadores necesita hacer referencia en sus aplicaciones. Por lo tanto, no necesitan conceder a los desarrolladores y operadores cualquier acceso de plano de datos. Además, tenga en cuenta que este ejemplo se centra en proteger su cámara clave. Debe tenerse similar para proteger [sus máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/security/), [cuentas de almacenamiento](../storage/storage-security-guide.md) y otros recursos de Azure demasiado.

>[AZURE.NOTE] Nota: Este ejemplo muestra cómo clave depósito access se bloqueará hacia abajo en producción. Los desarrolladores deberían tener sus propios suscripción o resourcegroup donde tiene todos los permisos para administrar sus depósitos, máquinas virtuales y cuenta de almacenamiento donde de desarrollo de la aplicación.


## <a name="resources"></a>Recursos

-   [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    En este artículo se explica el Control de acceso basado en roles de Azure Active Directory y cómo funciona.

-   [RBAC: Integrado en Roles](../active-directory/role-based-access-built-in-roles.md)

    Este artículo describe todas las funciones integradas disponibles en RBAC.

-   [Descripción de administrador de recursos e implementación clásica](../resource-manager-deployment-model.md)

    Este artículo explica la implementación del Administrador de recursos y los modelos de implementación clásica y las ventajas de usar los grupos de administrador de recursos y recursos

-    [Administrar el Control de acceso basado en roles con PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md)

     En este artículo se explica cómo administrar el control de acceso basado en roles con PowerShell de Azure

-   [Administración de Control de acceso basado en roles con la API de REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Este artículo le muestra cómo usar la API de REST para administrar RBAC.

-   [Control de acceso basado en roles de Microsoft Azure de Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Esto es un vínculo a un vídeo en canal 9 de la conferencia de Ignite MS 2015. En esta sesión, hablan sobre acceso a capacidades de administración y generación de informes en Azure y explorar las prácticas recomendadas de proteger el acceso a Azure suscripciones con Azure Active Directory.

-   [Autorizar el acceso a las aplicaciones web mediante OAuth 2.0 y Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    Este artículo describe completa flujo OAuth 2.0 para autenticar con Azure Active Directory.

-   [depósito clave API de REST de administración](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Este documento es la referencia de las API de REST administrar su cámara clave mediante programación, incluida la configuración de directiva de acceso de la clave de cámara.

-   [depósito clave API de REST](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    Vínculo a la documentación de referencia de la API de REST de depósito clave.

-   [Control de acceso a la clave](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Vínculo a la documentación de referencia de control de acceso de clave.

-   [Control de acceso secreta](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Vínculo a la documentación de referencia de control de acceso de clave.

-   [Establecer](https://msdn.microsoft.com/library/mt603625.aspx) y [Quitar](https://msdn.microsoft.com/library/mt619427.aspx) directiva de acceso de clave depósito con PowerShell

    Vínculos a la documentación de cmdlets de PowerShell administrar la directiva de acceso de clave depósito de referencia.

## <a name="next-steps"></a>Pasos siguientes

Para obtener un tutorial de introducción al obtener un administrador, consulte [Introducción a Azure depósito clave](key-vault-get-started.md).

Para obtener más información sobre el uso del registro para depósito clave, vea [registro de Azure depósito clave](key-vault-logging.md).

Para obtener más información sobre el uso de claves y la información confidencial con Azure depósito clave, vea [acerca de las claves y la información confidencial](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Si tiene preguntas sobre depósito clave, visite la [cámara clave Azure foros](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
