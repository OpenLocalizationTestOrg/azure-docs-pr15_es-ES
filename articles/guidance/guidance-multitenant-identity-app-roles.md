<properties
   pageTitle="Funciones de aplicación | Microsoft Azure"
   description="Cómo llevar a cabo la autorización mediante funciones de aplicación"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

#  <a name="application-roles-in-multitenant-applications"></a>Funciones de aplicación en aplicaciones multiempresa

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

Funciones de aplicación se utilizan para asignar permisos a los usuarios. Por ejemplo, las [Encuestas Tailspin] [ Tailspin] aplicación define los siguientes roles:

- Administrador. Puede realizar todas las operaciones de CRUD en cualquier encuesta que pertenece a ese inquilino.
- Creador. Puede crear nuevas encuestas.
- Lector. Puede leer las encuestas que pertenecen a ese inquilino.

Puede ver que roles finalmente traducir en permisos, durante la [autorización]. Pero la primera pregunta es cómo asignar y administrar roles. Hemos identificado tres opciones principales:

-   [Funciones de aplicación de Azure AD](#roles-using-azure-ad-app-roles)
-   [Grupos de seguridad de Azure AD](#roles-using-azure-ad-security-groups)
-   [Administrador de funciones de aplicación](#roles-using-an-application-role-manager).

## <a name="roles-using-azure-ad-app-roles"></a>Funciones con las funciones de aplicación de Azure AD

Este es el enfoque que se usa en la aplicación Tailspin encuestas.

En este enfoque SaaS el proveedor define las funciones de aplicación agregando el manifiesto de aplicación. Después de un cliente se inscribe, un administrador de directorio de AD del cliente asigna a los usuarios a los roles. Cuando un usuario inicia sesión, las funciones del usuario asignado se envían como notificaciones.

> [AZURE.NOTE] Si el cliente tiene Azure AD Premium, el administrador puede asignar a un grupo de seguridad a un rol y los miembros del grupo heredan el rol de aplicación. Esto es una forma conveniente para administrar roles, porque el propietario del grupo no necesita ser un administrador de AD.

Ventajas de este enfoque:

-   Modelo de programación simple.
-   Roles son específicos de la aplicación. No se envían las notificaciones de función para una aplicación a otra aplicación.
-   Si el cliente quita la aplicación de su inquilino AD, los roles desaparecen.
-   La aplicación no tenga los permisos de Active Directory adicionales, que no sea el perfil de usuario de lectura.

Inconvenientes:

- Clientes sin Azure AD Premium no pueden asignar roles de grupos de seguridad. Para estos clientes, todas las asignaciones de usuario deben realizarlo un administrador de AD.
- Si tiene un sitio web de back-end API, que es independiente de la aplicación web, a continuación, las asignaciones de roles para la aplicación web no se aplican a la API de web. Para obtener más información sobre este punto, vea [proteger un servidor web API].

### <a name="implementation"></a>Implementación

**Definir las funciones.** El proveedor de SaaS declara las funciones de aplicación de la [aplicación de manifiestos]. Por ejemplo, aquí es la entrada para la aplicación de encuestas manifiesta:

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

La `value` propiedad aparece en la notificación de la función. La `id` propiedad es el identificador único para la función definida. Generar siempre un nuevo valor GUID para `id`.

**Asignar a los usuarios**. Cuando se conecte un cliente nuevo, la aplicación está registrada en el inquilino del cliente AD. En este momento, un administrador de AD para ese inquilino puede asignar a usuarios a roles.

> [AZURE.NOTE] Como se mencionó anteriormente, los clientes con Azure AD Premium también pueden asignar a grupos de seguridad a roles.

La siguiente captura de pantalla desde el portal de Azure muestra tres usuarios. Ana se ha asignado directamente a un rol. Bob hereda una función como miembro de un grupo de seguridad denominado "Administrador de encuestas", que está asignado a un rol. Charles no está asignado a ninguna función.

![Usuarios asignados](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] Como alternativa, la aplicación puede asignar roles mediante programación, con la API de Azure AD Graph.  Sin embargo, esto requiere la aplicación para obtener los permisos de escritura para el directorio de AD del cliente. Puede hacer una aplicación con los permisos de una gran cantidad de engaño &mdash; el cliente es confiar en la aplicación para no perder tiempo su directorio. Muchos clientes pueden no estar dispuestos conceder este nivel de acceso.

**Obtener notificaciones de rol**. Cuando un usuario inicia sesión, la aplicación recibe asignadas funciones del usuario en una solicitud de tipo `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.  

Un usuario puede tener varias funciones o sin rol. En el código de autorización no suponga que el usuario tiene exactamente un rol de notificación. En su lugar, escribir código para comprobar si existe un valor de reclamación determinado:

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## <a name="roles-using-azure-ad-security-groups"></a>Funciones de uso de los grupos de seguridad de Azure AD

En este enfoque se representan funciones como grupos de seguridad de AD. La aplicación asigna los permisos a los usuarios según sus pertenencias a grupos de seguridad.

Ventajas:

-   Los clientes que no tengan Azure AD Premium, este enfoque permite al cliente usar grupos de seguridad para administrar las asignaciones de roles.

Desventajas:

- Complejidad. Dado que cada inquilino envía notificaciones de grupo diferente, la aplicación debe realizar un seguimiento de grupos de seguridad correspondientes a las funciones de la aplicación, para cada inquilino.
- Si el cliente quita la aplicación de su inquilino AD, los grupos de seguridad se mantienen en su directorio de AD.

### <a name="implementation"></a>Implementación

En el manifiesto de aplicación, establezca el `groupMembershipClaims` propiedad a "SecurityGroup". Esto es necesario para obtener notificaciones de pertenencia a un grupo de AAD.

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

Cuando se conecte un cliente nuevo, la aplicación indica que el cliente puede crear grupos de seguridad de los roles necesarios para la aplicación. A continuación, el cliente debe escribir los identificadores de objeto de grupo en la aplicación. La aplicación almacena en una tabla que asigna identificadores de grupo a las funciones de la aplicación, por cada inquilino.

> [AZURE.NOTE] Como alternativa, la aplicación podría crear los grupos mediante programación, con la API de Azure AD Graph.  Esto es menos susceptible de error. Sin embargo, requiere la aplicación obtener "leer y escribir todos los grupos de" permisos de directorio de AD del cliente. Muchos clientes pueden no estar dispuestos conceder este nivel de acceso.

Cuando un usuario inicia sesión en:

1.  La aplicación recibe los grupos del usuario como notificaciones. El valor de cada notificación es el identificador de objeto de un grupo.
2.  Azure AD limita el número de grupos que se envían en el token. Si el número de grupos supera este límite, Azure AD envía una notificación de "exceso" especial. Si está presente la reclamación, la aplicación debe consultar la API de Azure AD gráfico para obtener todos los grupos a los que pertenece ese usuario. Para obtener información detallada, vea [autorización en las aplicaciones de nube con los grupos de AD], en la sección titulada "Grupos reclamación excedente".
3.  La aplicación busca los identificadores de objeto en su propia base de datos, para buscar las funciones de aplicación correspondiente para asignar al usuario.
4.  La aplicación agrega un valor de notificación personalizado a la identidad de usuario que exprese la función de aplicación. Por ejemplo: `survey_role` = "SurveyAdmin".

Directivas de autorización deben utilizar la notificación de la función personalizada, no al grupo de notificación.

## <a name="roles-using-an-application-role-manager"></a>Funciones con el Administrador de funciones de la aplicación

Con este método, las funciones de aplicación no se almacenan en Azure AD en absoluto. En su lugar, la aplicación almacena las asignaciones de roles para cada usuario en su propia base de datos &mdash; por ejemplo, mediante la clase **RoleManager** identidad ASP.NET.

Ventajas:

-   La aplicación tiene control total sobre los roles y las asignaciones de usuario.

Inconvenientes:

- Más complejos, más difícil de mantener.
- No puede usar grupos de seguridad de AD para administrar las asignaciones de roles.
- Almacena información de usuario en la base de datos de aplicación, donde puede llegar a sincronizar con el directorio de AD del inquilino, como agregar o quitar usuarios.   

Hay muchos ejemplos existentes para este método. Por ejemplo, vea [crear una aplicación de ASP.NET MVC con auth y DB de SQL e implementar en servicio de la aplicación de Azure].

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [autorización basado en roles y recursos en las aplicaciones de multiempresa][autorización]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de una serie]: guidance-multitenant-identity.md
[autorización]: guidance-multitenant-identity-authorize.md
[Proteger un web API de back-end]: guidance-multitenant-identity-web-api.md
[Crear una aplicación de ASP.NET MVC con auth y DB de SQL e implementar en servicio de la aplicación de Azure]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[manifiesto de aplicación]: ../active-directory/active-directory-application-manifest.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
