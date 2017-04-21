<properties
   pageTitle="Federar con AD FS de un cliente | Microsoft Azure"
   description="¿Cómo permite la federación con un cliente de AD FS en una aplicación multiempresa"
   services=""
   documentationCenter="na"
   authors="JohnPWSharp"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="v-josha"/>

# <a name="federating-with-a-customers-ad-fs-for-multitenant-apps-in-azure"></a>Federar con AD FS de un cliente para aplicaciones multiempresa en Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

Este artículo describe cómo una aplicación de SaaS varios inquilino puede admitir autenticación a través de los servicios de federación de Active Directory (AD FS), para federarse con AD FS de un cliente.

## <a name="overview"></a>Información general

Azure Active Directory (AD Azure) facilita la sesión de los usuarios de inquilinos de Azure AD, incluidos los clientes de Office 365 y Dynamics CRM Online. Pero, ¿qué hay de los clientes que usan local Active Directory en una intranet corporativa?

Una opción es para que estos clientes sincronizar su AD locales con Azure AD, con [Azure AD Connect]. Sin embargo, algunos clientes podrán usar este método, debido a la directiva de TI corporativo o cualquier otro motivo. En ese caso, otra opción es federar a través de los servicios de federación de Active Directory (AD FS).

Para habilitar este escenario:

-   El cliente debe tener un conjunto de servidores a través de Internet AD FS.
-   El proveedor de SaaS implementa su propio conjunto de servidores de AD FS.
-   El cliente y el proveedor de SaaS deben configurar la [confianza de federación]. Este es un proceso manual.

Hay tres roles principales en la relación de confianza:

-   AD FS del cliente es el [asociado de cuenta], responsable de autenticar a los usuarios del cliente de AD y crear tokens de seguridad con notificaciones de usuario.
-   AD FS su proveedor de SaaS es el [asociado de recurso], que confía al asociado de cuenta y recibe las notificaciones de usuario.
-   La aplicación está configurada como un usuario de confianza (RP) en AD FS su proveedor de SaaS.

    ![Confianza de federación](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] En este artículo, se supone utilizados en la aplicación OpenID conectarse como el protocolo de autenticación. Otra opción es utilizar la federación de WS.

> Para conectar OpenID, el proveedor de SaaS debe usar AD 4.0 FS ejecución en Windows Server 2016, que se encuentra actualmente en versión preliminar técnica. AD FS 3.0 no admite OpenID conectarse.

> Núcleo de ASP.NET 1.0 no incluye soporte técnico del cuadro para la federación de WS.

Para obtener un ejemplo de uso de WS-Federation con ASP.NET 4, vea el [ejemplo de active directory dotnet Web App wsfederation][active-directory-dotnet-webapp-wsfederation].

## <a name="authentication-flow"></a>Flujo de autenticación

1.  Cuando el usuario hace clic en "iniciar sesión", la aplicación se redirige a un extremo OpenID conectar en AD FS su proveedor de SaaS.
2.  El usuario escribe su nombre de usuario de organización ("`alice@corp.contoso.com`"). AD FS usa detección de dominio de inicio para redirigir a AD FS del cliente, donde el usuario escribe sus credenciales.
3.  AD FS del cliente envía notificaciones de usuario a AD FS su proveedor de SaaS, mediante WF federación (o SAML).
4.  Flujo de notificaciones de AD FS para la aplicación, usando OpenID conectarse. Esto requiere una transición de protocolo de WS-Federation.

## <a name="limitations"></a>Limitaciones

En el momento de escribir este documento, la aplicación recibe un conjunto limitado de reclamaciones en la id_token OpenID, tal y como aparece en la tabla siguiente. AD FS 4.0 es en aún preview, por lo que puede cambiar este conjunto. No es posible actualmente definir notificaciones adicionales:

Reclamar   | Descripción
------|-------------
y | Audiencia. La aplicación para la que la han emitido las notificaciones.
AuthenticationInstant   | [Instantánea de autenticación]. Se produjo el momento en que la autenticación.
c_hash  | Valor de código hash. Se trata de un hash del contenido del token.
EXP | [Fecha de expiración]. El tiempo después de que ya no se aceptan el token.
tabla | [Emitido en]. El tiempo cuando fue emitido el token.
ISS | Emisor. El valor de esta notificación es siempre de AD FS del asociado de recurso.
nombre    | Nombre de usuario. Ejemplo: `john@corp.fabrikam.com`.
NameIdentifier | [Identificador de nombre]. El identificador del nombre de la entidad para el que fue emitido el token.
valor de seguridad   | Valor de seguridad de la sesión. Un valor único generado por AD FS para ayudar a evitar ataques de reproducción.
UPN | Nombre principal de usuario (UPN). Ejemplo:john@corp.fabrikam.com
pwd_exp | Período de expiración de contraseña. El número de segundos hasta que la contraseña del usuario o un secreto de autenticación similares, como un PIN. caduca.

> [AZURE.NOTE] "iss" reclamar contiene del asociado de AD FS (normalmente, esta notificación identifica el proveedor de SaaS como el emisor). No se identifica de AD FS del cliente. Puede encontrar el dominio del cliente como parte del UPN.

El resto de este artículo describe cómo configurar la relación de confianza entre el punto de reunión (la aplicación) y el asociado de cuenta (el cliente).

## <a name="ad-fs-deployment"></a>Implementación de AD FS

El proveedor de SaaS puede implementar AD FS local o en máquinas virtuales de Azure. De seguridad y disponibilidad, son importantes las directrices siguientes:

-   Implementar al menos dos servidores de AD FS y dos servidores de proxy de AD FS para conseguir la mejor disponibilidad del servicio de AD FS.
-   Controladores de dominio y los servidores de AD FS nunca se deben exponer directamente a Internet y deben estar en una red virtual con acceso directo a ellos.
-   Servidores proxy de aplicaciones Web (anteriormente proxy AD FS) se deben utilizar para publicar servidores de AD FS en Internet.

Para configurar una topología similar en Azure requiere el uso de redes virtuales, del GSN, azure máquina virtual y conjuntos de disponibilidad. Para obtener más detalles, vea [las instrucciones para la implementación de Active Directory de Windows Server en Azure máquinas virtuales de Windows][active-directory-on-azure].

## <a name="configure-openid-connect-authentication-with-ad-fs"></a>Configurar la autenticación OpenID conectar con AD FS

Debe habilitar el proveedor de SaaS OpenID conectarse entre la aplicación y AD FS. Para ello, agregar un grupo de aplicaciones de AD FS.  Encontrará instrucciones detalladas en esta [entrada de blog], bajo "Configuración de una aplicación Web para conectar OpenId inicie sesión en AD FS." 

A continuación, configure el software intermedio OpenID conectarse. El extremo de metadatos es `https://domain/adfs/.well-known/openid-configuration`, donde dominio es su proveedor de SaaS AD FS.

Normalmente se puede combinar con otros extremos OpenID conectar (por ejemplo, AAD). Necesitará dos botones de inicio de sesión diferentes o alguna otra forma de distinguirlos, para que se envía al usuario al extremo autenticación correcta.

## <a name="configure-the-ad-fs-resource-partner"></a>Configurar al Partner de AD FS recursos

El proveedor de SaaS debe hacer lo siguiente para cada cliente que desea conectar vía ADFS:

1.  Agregar una relación de confianza del proveedor de notificaciones.
2.  Agregar reglas de reclamaciones.
3.  Habilitar la detección del territorio de inicio.

Estos son los pasos con más detalle.

### <a name="add-the-claims-provider-trust"></a>Agregar la confianza del proveedor de notificaciones

1.  En el administrador del servidor, haga clic en **Herramientas**y, a continuación, seleccione **Administración de AD FS**.
2.  En el árbol de consola, **AD FS**, haga clic derecho en **Confía en el proveedor de notificaciones**. Seleccione **Agregar confianza del proveedor de notificaciones**.
3.  Haga clic en **Iniciar** para iniciar al asistente.
4.  Seleccione la opción "importar datos sobre el proveedor de notificaciones publicado en línea o en una red local". Escriba el URI del extremo de metadatos de federación del cliente. (Ejemplo: `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.) Debe hacerlo desde el cliente.
5.  Complete el Asistente con las opciones predeterminadas.

### <a name="edit-claims-rules"></a>Editar reglas de notificaciones

1.  Haga clic en la confianza de proveedor de reclamaciones recién agregado y seleccione **Editar reglas de notificaciones**.
2.  Haga clic en **Agregar regla**.
3.  Seleccione "Pasar a través de o filtrar una notificación entrante" y haga clic en **siguiente**.
    ![Agregar Asistente para reglas de notificación de transformación](media/guidance-multitenant-identity/edit-claims-rule.png)
4.  Escriba un nombre para la regla.
5.  En "Tipo de notificación de correo entrante", seleccione **UPN**.
6.  Seleccione "Paso a través de todos los valores de notificaciones".
  ![Agregar Asistente para reglas de notificación de transformación](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.  Haga clic en **Finalizar**.
8.  Repita los pasos 2 y 7 y especifique **El tipo de notificación de anclaje** para el tipo de notificación entrante.
9.  Haga clic en **Aceptar** para completar al asistente.

### <a name="enable-home-realm-discovery"></a>Habilitar la detección del territorio de inicio
Ejecute el siguiente script de PowerShell:

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

donde "nombre" es el nombre descriptivo de la confianza del proveedor de notificaciones y "sufijo" es el sufijo UPN para el cliente de AD (ejemplo, "corp.fabrikam.com").

Con esta configuración, puede escribir los usuarios finales en su cuenta de la organización y AD FS selecciona automáticamente el proveedor de notificaciones correspondientes. Consulte [personalización de las páginas AD FS inicio de sesión], en la sección "Configurar el proveedor de identidades para usar determinados sufijos de correo electrónico".

## <a name="configure-the-ad-fs-account-partner"></a>Configurar al Partner de AD FS cuenta

El cliente debe hacer lo siguiente:

1.  Agregar una confianza confianza de terceros (RP).
2.  Agrega reglas de reclamaciones.

### <a name="add-the-rp-trust"></a>Agregar la confianza RP

1.  En el administrador del servidor, haga clic en **Herramientas**y, a continuación, seleccione **Administración de AD FS**.
2.  En el árbol de consola, **AD FS**, haga clic **Confiar confía de terceros**. Seleccione **Agregar usuario de confianza**.
3.  Seleccione **Cuenta de notificaciones** y haga clic en **Inicio**.
4.  En la página **Seleccionar origen de datos** , seleccione la opción "importar datos sobre el proveedor de notificaciones publicado en línea o en una red local". Escriba el URI del extremo de metadatos de federación de su proveedor de SaaS.
  ![Agregar a Asistente de fiesta de confianza de confianza](media/guidance-multitenant-identity/add-rp-trust.png)
5.  En la página **Especificar nombre para mostrar** , escriba un nombre.
6.  En la página **Elegir la directiva de Control de acceso** , elija una directiva. Podría permitir que todos los usuarios de la organización, o elija un grupo de seguridad específicos.
  ![Agregar a Asistente de fiesta de confianza de confianza](media/guidance-multitenant-identity/add-rp-trust2.png)
7.  Escriba todos los parámetros requeridos en el cuadro de la **Directiva** .
8.  Haga clic en **siguiente** para completar al asistente.

### <a name="add-claims-rules"></a>Agregar reglas de notificaciones

1.  Haga clic en la confianza de entidades de confianza recién agregado y seleccione **Editar directiva de emisión de notificación**.
2.  Haga clic en **Agregar regla**.
3.  Seleccione "Enviar LDAP atributos como reclamaciones" y haga clic en **siguiente**.
4.  Escriba un nombre para la regla, como "UPN".
5.  En el **atributo almacenar**, seleccione **Active Directory**.
  ![Agregar Asistente para reglas de notificación de transformación](media/guidance-multitenant-identity/add-claims-rules.png)
6.  En la sección **atributos de asignación de LDAP** :
  - En el **Atributo LDAP**, seleccione **Nombre de usuario Principal**.
  - En **Tipo de notificación saliente**, seleccione **UPN**.
  ![Agregar Asistente para reglas de notificación de transformación](media/guidance-multitenant-identity/add-claims-rules2.png)
7.  Haga clic en **Finalizar**.
8.  Haga clic en **Agregar regla de** nuevo.
9.  Seleccione "Enviar notificaciones usando una regla personalizada" y haga clic en **siguiente**.
10. Escriba un nombre para la regla, como "Tipo de notificación de anclaje".
11. En la **regla personalizada**, escriba lo siguiente:

    ```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
    ```

    Esta regla envía una notificación de tipo `anchorclaimtype`. La notificación indica el usuario de confianza para usar UPN como del inmutable Id.

12. Haga clic en **Finalizar**.
13. Haga clic en **Aceptar** para completar al asistente.

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [Using aserción de cliente para obtener acceso tokens de Azure AD][client assertion]

<!-- Links -->
[parte de una serie]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[confianza de federación]: https://technet.microsoft.com/library/cc770993(v=ws.11).aspx
[asociado de cuenta]: https://technet.microsoft.com/library/cc731141(v=ws.11).aspx
[asociado de recurso]: https://technet.microsoft.com/library/cc731141(v=ws.11).aspx
[Instantánea de autenticación]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[Fecha de caducidad]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[Emitido en]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[Identificador de nombre]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.nameidentifier(v=vs.110).aspx
[active-directory-on-azure]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[entrada de blog]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[Personalización de las páginas AD FS sesión]: https://technet.microsoft.com/library/dn280950.aspx
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[client assertion]: guidance-multitenant-identity-client-assertion.md
[active-directory-dotnet-webapp-wsfederation]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation
