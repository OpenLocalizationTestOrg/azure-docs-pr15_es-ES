## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparar autenticar las solicitudes del Administrador de recursos de Azure

Debe autenticar todas las operaciones que realizan sobre los recursos mediante el [Administrador de recursos de Azure] [ lnk-authenticate-arm] con Azure Active Directory (AD). La forma más sencilla de hacerlo es usar PowerShell o CLI de Azure.

Debe instalar [Azure PowerShell 1.0] [ lnk-powershell-install] o posterior antes de continuar.

Los pasos siguientes muestran cómo configurar la autenticación de contraseña de una aplicación de AD mediante PowerShell. Puede ejecutar estos comandos en una sesión de PowerShell estándar.

1. Inicie sesión en su suscripción de Azure usando el siguiente comando:

    ```
    Login-AzureRmAccount
    ```

2. Tome nota de sus **TenantId** y **SubscriptionId**. Necesitará más adelante.

3. Cree una nueva aplicación de Azure Active Directory mediante el siguiente comando, sustituyendo los marcadores de posición:

    - **{Mostrar nombre}:** un nombre para mostrar de la aplicación como **MySampleApp**
    - **{URL de página principal}:** la dirección URL de la página principal de su aplicación como **http://mysampleapp/home**. Esta dirección URL no es necesario para que señale a una aplicación real.
    - **{Identificador}:** Un identificador único, como **http://mysampleapp**. Esta dirección URL no es necesario para que señale a una aplicación real.
    - **{Contraseña}:** Contraseña que se va a utilizar para autenticar con su aplicación.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Anote el **ApplicationId** de la aplicación que creó. Lo necesitará más adelante.

5. Crear un nuevo servicio principal usando el siguiente comando, sustituyendo **{MyApplicationId}** por el **ApplicationId** del paso anterior:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Configurar una asignación de función mediante el siguiente comando, sustituyendo su **ApplicationId**a **{MyApplicationId}** .

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Ahora ha terminado de crear la aplicación de Azure AD que permiten autenticar desde su aplicación personalizada C#. Tendrá los siguientes valores más adelante en este tutorial:

- TenantId
- SubscriptionId
- ApplicationId
- Contraseña

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
