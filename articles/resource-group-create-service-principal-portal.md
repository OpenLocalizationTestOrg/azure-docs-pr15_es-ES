<properties
   pageTitle="Crear principal en el portal de servicio | Microsoft Azure"
   description="Describe cómo crear una nueva aplicación de Active Directory y principales de servicio que pueden usarse con el control de acceso basado en roles de administrador de recursos de Azure para administrar el acceso a los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Usar el portal para crear la aplicación de Active Directory y principales de servicio que pueden tener acceso a los recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Si tiene una aplicación que necesita para tener acceso o modificar recursos, debe configurar una aplicación de Active Directory (AD) y asignarles los permisos necesarios. En este tema se muestra cómo llevar a cabo los pasos a través del portal. Actualmente, debe usar el portal clásico para crear una nueva aplicación de Active Directory y, a continuación, cambie el portal de Azure para asignar un rol a la aplicación. 

> [AZURE.NOTE] Los pasos de este tema solo se aplican al usar el **portal clásico** para crear la aplicación de AD. **Si utiliza el portal de Azure para crear la aplicación de AD, estos pasos no se realizará correctamente.** 
>
> Le resultará más fácil de configurar la aplicación de AD y el servicio principal a través de [PowerShell](resource-group-authenticate-service-principal.md) o [CLI de Azure](resource-group-authenticate-service-principal-cli.md), especialmente si desea usar un certificado para la autenticación. En este tema no se muestra cómo usar un certificado.

Para obtener una explicación de los conceptos de Active Directory, vea [objetos de la aplicación y Principal del servicio](./active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación para Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Para conocer los pasos detallados sobre la integración de una aplicación en Azure para administrar los recursos, consulte [la Guía del desarrollador de autorización con la API del Administrador de recursos de Azure](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Crear una aplicación de Active Directory

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/). 

2. Asegúrese de que saber el valor predeterminado de Active Directory para la suscripción. Solo puede conceder acceso a las aplicaciones en el mismo directorio que la suscripción. Seleccione **configuración** y busque el nombre del directorio asociado a su suscripción.  Para obtener más información, vea [cómo Azure suscripciones están asociadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![Buscar directorio predeterminado](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Seleccione **Active Directory** en el panel izquierdo.

     ![Seleccione Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Seleccione Active Directory que desea usar para crear la aplicación. Si tiene más de un Active Directory, cree la aplicación en el directorio predeterminado para la suscripción.   

     ![Seleccionar directorio](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Para ver las aplicaciones en el directorio, seleccione **aplicaciones**.

     ![ver aplicaciones](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Si todavía no lo ha creado una aplicación en el directorio antes de, verá algo similar a la siguiente imagen. Seleccione **Agregar una aplicación**

     ![Agregar aplicación](./media/resource-group-create-service-principal-portal/create-application.png)

     O bien, haga clic en **Agregar** en el panel inferior.

     ![Agregar](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Seleccione el tipo de aplicación que desea crear. En este tutorial, seleccione **Agregar una aplicación se desarrolla mi organización**. 

     ![nueva aplicación](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Proporcione un nombre para la aplicación y seleccione el tipo de aplicación que desea crear. En este tutorial, cree una **Aplicación o WEB API de WEB** y haga clic en el botón siguiente. Si selecciona la **Aplicación nativa de cliente**, los pasos restantes de este artículo no coincidirán su experiencia.

     ![aplicación de nombre](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Rellene las propiedades de la aplicación. Para la **Dirección URL de inicio de sesión**, proporcione la URI a un sitio web que describa la aplicación. No se valida la existencia del sitio web. **URI de ID de aplicación**, proporcione el URI que identifica la aplicación.

     ![propiedades de la aplicación](./media/resource-group-create-service-principal-portal/app-properties.png)

Ha creado la aplicación.

## <a name="get-client-id-and-authentication-key"></a>Obtener clave id y la autenticación de cliente

Al registro mediante programación en, necesita el identificador de la aplicación. Si la aplicación se ejecuta en sus propia credenciales, necesita una clave de autenticación.

1. Seleccione la ficha **Configurar** para configurar la contraseña de la aplicación.

     ![configurar la aplicación](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copie el **identificador de cliente**.
  
     ![identificador de cliente](./media/resource-group-create-service-principal-portal/client-id.png)

3. Si la aplicación se ejecuta en sus propia credenciales, desplácese hacia abajo hasta la sección de **claves** y seleccione cuánto tiempo desea que la contraseña sea válido.

     ![teclas](./media/resource-group-create-service-principal-portal/create-key.png)

4. Seleccione **Guardar** para crear la clave.

     ![Guardar](./media/resource-group-create-service-principal-portal/save-icon.png)

     Se muestra la clave guardada y se puede copiar. No es posible recuperar la clave posteriormente para copiar ahora.

     ![Guardar clave](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Obtener el identificador de inquilinos

Iniciar sesión mediante programación, debe pasar el identificador del inquilino a la solicitud de autenticación. Para aplicaciones Web y aplicaciones de la API de Web, puede recuperar el identificador del inquilino seleccionando **extremos de vista** en la parte inferior de la pantalla y recuperar el identificador tal como se muestra en la siguiente imagen.  

   ![Id. de inquilinos](./media/resource-group-create-service-principal-portal/save-tenant.png)

También puede recuperar el identificador del inquilino a través de PowerShell:

    Get-AzureRmSubscription

O bien, CLI Azure:

    azure account show --json

## <a name="set-delegated-permissions"></a>Establecer delegar permisos

Si su aplicación tiene acceso a los recursos en nombre de un usuario que ha iniciado sesión, debe conceder a su aplicación delegado permiso de acceso a otras aplicaciones. Conceder este acceso en la sección **permisos a otras aplicaciones** de la ficha **Configurar** . De forma predeterminada, un permiso delegado ya está habilitado para Azure Active Directory. Deje este permiso delegado sin cambios.

1. Seleccione **Agregar aplicación**.

2. En la lista, seleccione la **API de administración de servicio de Windows Azure**. A continuación, seleccione el icono completo.

      ![Seleccione la aplicación](./media/resource-group-create-service-principal-portal/select-app.png)

3. En la lista desplegable para permisos de delegado, seleccione **Administración de servicios de Access Azure como organización**.

      ![Seleccione el permiso](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Guardar el cambio.

## <a name="assign-application-to-role"></a>Asignar la aplicación de la función

Si la aplicación se ejecuta en sus propia credenciales, debe asignar la aplicación a un rol. Decida qué función representa los permisos adecuados para la aplicación. Para obtener información sobre las funciones disponibles, vea [RBAC: integrado en funciones](./active-directory/role-based-access-built-in-roles.md). 

Para asignar un rol a una aplicación, debe tener los permisos correctos. En concreto, debe tener `Microsoft.Authorization/*/Write` acceso que se concede mediante la función de [propietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . El rol de colaborador no tiene el acceso correcto.

Puede establecer el ámbito en el nivel de la suscripción, el grupo de recursos o el recurso. Permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, agregar una aplicación a la función Lector de un grupo de recursos, puede leer el grupo de recursos y los recursos que contiene.

1. Para asignar la aplicación a un rol, cambiar desde el portal de clásico en el [portal de Azure](https://portal.azure.com).

1. Compruebe sus permisos para asegurarse de que la entidad de seguridad de servicio puede asignar a un rol. Seleccione **Mis permisos** de la cuenta.

    ![Seleccione Mis permisos](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Ver los permisos asignados para su cuenta. Como se mencionó anteriormente, debe pertenecer a las funciones de propietario o el Administrador de acceso de usuario o tiene una función personalizada que conceda acceso de escritura para Microsoft.Authorization. La imagen siguiente muestra una cuenta que se ha asignado el rol de colaborador para la suscripción, lo que no es de los permisos adecuados para asignar una aplicación a un rol.

    ![Mostrar mis permisos](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Si no tiene los permisos correctos para conceder acceso a una aplicación, que debe cualquier solicitud de que el Administrador de la suscripción agrega a la función de administrador de acceso de usuario o solicitar un administrador concede acceso a la aplicación.

1. Desplácese hasta el nivel de ámbito que desea asignar la aplicación. Para asignar un rol en el ámbito de la suscripción, seleccione **suscripciones**.

     ![Seleccione la suscripción](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Seleccione la suscripción determinada para asignar la aplicación.

     ![Seleccione la suscripción de asignación](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Seleccione el icono de **Access** en la esquina superior derecha.

     ![Seleccione acceso](./media/resource-group-create-service-principal-portal/select-access.png)
     
     O bien, para asignar un rol en el ámbito de grupo de recursos, desplácese a un grupo de recursos. En el módulo de grupo de recursos, seleccione el **control de acceso**.

     ![Seleccione usuarios](./media/resource-group-create-service-principal-portal/select-users.png)

     Los siguientes pasos son los mismos para cualquier ámbito.

2. Haga clic en **Agregar**.

     ![Seleccione Agregar](./media/resource-group-create-service-principal-portal/select-add.png)

3. Seleccione el rol **lector** (o cualquier función que desea asignar la aplicación a).

     ![Seleccione el rol](./media/resource-group-create-service-principal-portal/select-role.png)

4. Cuando vea la lista de usuarios que puede agregar a la función en primer lugar, no verá las aplicaciones. Solo verá grupo y los usuarios.

     ![mostrar a los usuarios](./media/resource-group-create-service-principal-portal/show-users.png)

5. Para buscar la aplicación, debe buscar para buscarlo. Comience a escribir el nombre de la aplicación y cambiará la lista de opciones disponibles. Seleccione la aplicación cuando lo vea en la lista.

     ![asignar rol](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Seleccione **bien** a fin de asignar el rol. Ahora debe ver la aplicación en la lista de usos asignados a una función para el grupo de recursos.


Para obtener más información sobre cómo asignar los usuarios y roles a través del portal de aplicaciones, vea [las asignaciones de roles de uso para administrar el acceso a los recursos de suscripción de Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

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

- Para obtener información sobre cómo especificar directivas de seguridad, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).  
- Para una demostración en vídeo de estos pasos, vea [Habilitar la administración de programación de un recurso de Azure con Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

