## <a name="prerequisites"></a>Requisitos previos

Antes de que se puede escribir código de administración de CDN, debemos realizar algunos preparación para habilitar el código interactuar con el Administrador de recursos de Azure.  Para ello, necesitará:

* Crear un grupo de recursos para que contenga el perfil CDN que creamos en este tutorial
* Configurar Azure Active Directory para proporcionar autenticación para nuestra aplicación
* Aplicar permisos al grupo de recursos para que solo los usuarios autorizados de nuestra inquilino de Azure AD pueden interactuar con nuestro perfil CDN

### <a name="creating-the-resource-group"></a>Crear el grupo de recursos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el botón **nuevo** de la esquina superior izquierda, a continuación, **administración**y **Grupo de recursos**.
    
    ![Crear un nuevo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Llame a su grupo de recursos *CdnConsoleTutorial*.  Seleccione la suscripción y elija una ubicación cercana.  Si lo desea, puede hacer clic en la casilla de verificación de **Anclar en escritorio** para anclar el grupo de recursos en el panel en el portal.  Esto le será más fácil encontrarlos.  Cuando haya realizado las selecciones, haga clic en **crear**.

    ![El grupo de recursos de nombres](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Después de crea el grupo de recursos, si no anclarlo al escritorio, puede encontrar haciendo clic en **Examinar**y luego en **Grupos de recursos**.  Haga clic en el grupo de recursos para abrirlo.  Anote el **Identificador de suscripción**.  Se deberá más adelante.

    ![El grupo de recursos de nombres](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Crear la aplicación de Azure AD y aplicar permisos

Existen dos métodos de autenticación de la aplicación con Azure Active Directory: los usuarios individuales o una entidad de seguridad del servicio. Un servicio principal es similar a una cuenta de servicio de Windows.  En lugar de conceder permisos para interactuar con los perfiles CDN de un usuario concreto, en su lugar se conceder los permisos para el servicio principal.  Principales de servicio generalmente se utilizan para procesos automatizados, no interactivo.  Aunque este tutorial está escribiendo una aplicación de consola interactivas, nos centraremos en el enfoque principal del servicio.

Creación de una entidad de seguridad de servicio consta de varios pasos, incluida la creación de una aplicación de Azure Active Directory.  Para ello, vamos a [seguir este tutorial](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Asegúrese de seguir todos los pasos en el [tutorial vinculado](../articles/resource-group-create-service-principal-portal.md).  Es *muy importante* que la complete exactamente como se describe.  Asegúrese de que tenga en cuenta el **identificador del inquilino**, **nombre de dominio inquilino** (normalmente un *. onmicrosoft.com* dominio a menos que haya especificado un dominio personalizado), **ID de cliente**y la **clave de autenticación de cliente**, como necesitamos más adelante.  Asegúrese de proteger su **ID de cliente** y la **clave de autenticación de cliente**, como estas credenciales pueden utilizarse por cualquier usuario ejecutar acciones como principal de servicio. 
>   
> Cuando llegue al paso denominado [Configurar la aplicación de múltiples arrendatario](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), seleccione **No**.
> 
> Cuando llegue al paso [asignar la aplicación de la función](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), utilice el grupo de recursos que se creó anteriormente, *CdnConsoleTutorial*, pero en lugar de la función **lector** , asignar el rol de **Colaborador de perfil de CDN** .  Después de asignar el rol de **Colaborador de CDN perfil** de la aplicación en el grupo de recursos, vuelva a este tutorial. 

Una vez que haya creado su servicio principal y asigna el rol de **Colaborador de perfil de CDN** , el módulo de **los usuarios** de su grupo de recursos debe tener un aspecto similar al siguiente.

![Módulo de usuarios](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Autenticación de usuario interactivo

Si prefiere que autenticación de usuario individual interactivo en lugar de una entidad de seguridad de servicio, el proceso es muy similar de una entidad de seguridad del servicio.  De hecho, debe seguir el mismo procedimiento, pero realizar algunos cambios menores.

> [AZURE.IMPORTANT] Siga estos pasos únicamente si elige usar autenticación de usuario individual en lugar de un servicio principal.

1. Al crear la aplicación, en lugar de la **Aplicación Web**, elija la **aplicación nativa**. 
    
    ![Aplicación nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. En la página siguiente, se le pedirá para una **redirección URI**.  La URI no se valida, pero recuerde lo que ha escrito.  Necesitará más adelante. 

3. No es necesario crear una **clave de autenticación de cliente**.

4. En lugar de asignar una entidad de seguridad de servicio para el rol de **Colaborador de perfil de CDN** , vamos a asignar usuarios o grupos individuales.  En este ejemplo, puede ver que he asignado *CDN demostración usuario* al rol de **Colaborador de perfil de CDN** .  
    
    ![Acceso de usuarios individuales](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

