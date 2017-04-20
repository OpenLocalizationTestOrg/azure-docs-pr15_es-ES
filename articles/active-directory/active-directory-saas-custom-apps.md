<properties 
    pageTitle="Configuración de inicio de sesión único en aplicaciones que no están en la Galería de la aplicación de Azure Active Directory | Microsoft Azure" 
    description="Obtenga información sobre cómo a autoservicio conectar aplicaciones de Azure Active Directory mediante SAML y SSO de contraseña" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configuración de inicio de sesión único en aplicaciones que no están en la Galería de la aplicación de Azure Active Directory

Este artículo trata sobre una característica que permite a los administradores configurar inicio de sesión único en aplicaciones que no están presentes en el Azure Active Directory aplicación galería *sin escribir código*. Esta característica se publicó desde la vista previa técnica de 18 de noviembre de 2015 y se incluye en [Azure Active Directory Premium](active-directory-editions.md). Si desea en su lugar para obtener instrucciones sobre cómo integrar aplicaciones personalizadas con Azure AD a través de código programador, vea [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

La Galería de la aplicación de Azure Active Directory ofrece una lista de aplicaciones que se sabe que admite un formulario de inicio de sesión único con Azure Active Directory, como se describe en [este artículo](active-directory-appssoaccess-whatis.md). Una vez (como una TI especialista o sistema de integración de su organización) ha encontrado la aplicación que desea conectarse, puede empezar por, siga las instrucciones paso a paso realizadas en el portal de administración de Azure para habilitar el inicio de sesión único.

Los clientes que tengan licencias de [Azure Active Directory Premium](active-directory-editions.md) también obtienen estas funciones adicionales:

* Integración de autoservicio de cualquier aplicación que admita proveedores de identidades SAML 2.0 (iniciado por el SP o iniciado IdP)
* Integración de autoservicio de cualquier aplicación web que tiene una basado en HTML sesión página utilizando [SSO de contraseña](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Conexión de autoservicio de aplicaciones que usen el protocolo SCIM para usuario aprovisionamiento ([descrito a continuación](active-directory-scim-provisioning.md))
* Capacidad para agregar vínculos a cualquier aplicación en el [iniciador de aplicaciones de Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o en el [panel de acceso de Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Esto puede incluir aplicaciones SaaS que utilizan pero ha no aún se han en-efectuar a la Galería de la aplicación de Azure AD, pero no sólo aplicaciones web de terceros que su organización ha implementado los servidores de que control, en la nube o local.

Estas capacidades, también conocido como *plantillas de integración de aplicaciones*, proporcionan puntos de conexión basada en estándares para las aplicaciones que admiten SAML, SCIM o autenticación basada en formularios y a continuación, incluyen flexibles opciones y configuración de compatibilidad con un gran número de aplicaciones. 

##<a name="adding-an-unlisted-application"></a>Agregar una aplicación que no figuran

Para conectar una aplicación con una plantilla de integración de aplicación, inicie sesión en el portal de administración de Azure con su cuenta de administrador de Azure Active Directory y busque el **Active Directory > [directorio] > aplicaciones** , seleccione **Agregar**y, a continuación, **Agregar una aplicación de la Galería**. 

![][1]

En la Galería de la aplicación, puede agregar una aplicación que no figuran con la categoría **personalizada** en la parte izquierda, o seleccionando el vínculo **Agregar una aplicación que no figuran** que se muestra en los resultados de búsqueda si no ha encontrado la aplicación que desee. Después de escribir un nombre para la aplicación, puede configurar las opciones de inicio de sesión único y comportamiento. 

**Sugerencia**: como práctica recomendada, use la función HALLAR para comprobar si la aplicación ya existe en la Galería de aplicación. Si se encuentra la aplicación y su descripción menciones "solo inicio de sesión", a continuación, en la aplicación ya es compatible para un inicio de sesión único federado. 

![][2]

Agregar una aplicación de este modo, ofrece una experiencia muy similar a la disponible para las aplicaciones integradas previamente. Para empezar, seleccione **Configurar inicio de sesión único**. La siguiente pantalla, presenta las tres opciones siguientes para configurar el inicio de sesión único, que se describen en las secciones siguientes.

![][3]


##<a name="azure-ad-single-sign-on"></a>En el inicio de sesión único de Azure AD

Seleccione esta opción para configurar la autenticación basada en SAML para la aplicación. Esto requiere que la aplicación y soporte técnico de SAML 2.0, debe recopilar información acerca de cómo usar las capacidades SAML de la aplicación antes de continuar. Después de seleccionar **siguiente**, se le pedirá introducir tres diferentes direcciones URL correspondientes a los extremos SAML para la aplicación. 

![][4]
 
Estos son:

* **Inicio de sesión en la dirección URL (iniciado por SP solo)** : cuando el usuario va a iniciar sesión en esta aplicación. Si la aplicación está configurada para llevar a cabo servicios iniciados por el proveedor solo inicio de sesión, a continuación, cuando un usuario se desplaza a esta dirección URL, el proveedor de servicios lo la redirección necesaria para Azure AD para autenticar y el usuario de inicio de sesión. Si este campo se rellena, Azure AD usará esta dirección URL para iniciar la aplicación de Office 365 y el Panel de Azure AD acceso. Si este campo es ommited, Azure AD en su lugar realizará el proveedor de identidades-inicio de sesión iniciada cuando se inicia la aplicación de Office 365, el Panel de Azure AD Access o de Azure AD un solo inicio de sesión de dirección URL (copiable desde la ficha tablero).

* **Dirección URL del emisor** - el emisor URL debe identificar la aplicación para que solo inicio de sesión en se está configurando. Este es el valor que Azure AD se envía a la aplicación como el parámetro de la **audiencia** del token SAML y la aplicación se espera para validarla. Este valor también aparece como el **Id. de entidad** en los metadatos SAML proporcionado por la aplicación. Consultar la documentación de SAML de la aplicación para obtener más información sobre lo que es el identificador de entidad o es el valor de la audiencia. A continuación se muestra un ejemplo de cómo aparece la dirección URL de la audiencia en el token SAML devuelve a la aplicación:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **Dirección URL de respuesta** : la dirección URL de respuesta es donde la aplicación espera recibir el token de SAML. También se conoce como la **dirección URL del servicio de consumidores de aserción (ACS)**. Consultar la documentación de SAML de la aplicación para obtener más información sobre las novedades de su respuesta de token SAML dirección URL o ACS.
 Después de que se han introducido, haga clic en **siguiente** para pasar a la pantalla siguiente. Esta pantalla proporciona información sobre lo que debe estar configurado en el lado de la aplicación para que pueda aceptar un símbolo SAML de Azure AD. 

![][5]

Los valores que son necesarios se varían en función de la aplicación, así que compruebe la documentación de SAML de la aplicación para obtener información detallada. **Inicio de sesión** y **cierre** ambas resuelven al mismo extremo, que es el extremo de control de solicitud SAML de la instancia de Azure AD de dirección URL del servicio. La dirección URL del emisor es el valor que aparece como "Emisor" dentro del token SAML emitido a la aplicación. 

Después de configurar la aplicación, haga clic en el botón **siguiente** y, a continuación, el **completado** para cerrar el cuadro de diálogo. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Asignar los usuarios y grupos a la aplicación de SAML 

Una vez que la aplicación se ha configurado para usar Azure AD como un proveedor de identidades basada en SAML, es casi preparado para probar. Como un control de seguridad, Azure AD no emite un token de lo que les permite iniciar sesión en la aplicación a menos que se les ha concedido acceso a con Azure AD. Los usuarios pueden tener concedidos acceso directamente, o a través de un grupo que sean miembros de. 

Para asignar un usuario o grupo a la aplicación, haga clic en el botón **Asignar usuarios** . Seleccione el usuario o grupo que desea asignar y, a continuación, seleccione el botón **asignar** . 

![][6]

Asignación de un usuario le permitirá Azure AD a emitir un token para el usuario, así como la causa de un mosaico para esta aplicación aparezca en el Panel de acceso del usuario. Mosaico de la aplicación también aparecerán en el iniciador de aplicaciones de Office 365 si el usuario está utilizando Office 365. 

Puede cargar un logotipo de mosaico de la aplicación utilizando el botón **Cargar logotipo** en la ficha **Configurar** de la aplicación. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizar las notificaciones en el token de SAML emitidas 

Cuando un usuario se autentica a la aplicación, Azure AD emite un token SAML a la aplicación que contiene información (o reclamaciones) sobre el usuario que identifica de ellos. De forma predeterminada, esto incluye nombre de usuario, dirección de correo electrónico, nombre y apellido del usuario. 

Puede ver o editar las notificaciones enviadas en el token de SAML con la aplicación en la ficha **atributos** . 

![][7]

Existen dos posibles motivos por qué podría necesitar editar las notificaciones en el token de SAML emitidas: •el aplicación se ha escrito para requerir un conjunto diferente de Reclamación URI o valores de notificación •su aplicación se ha implementado en un modo que requiere la NameIdentifier dicen que algo que no sea el nombre de usuario (nombre principal de usuario también conocida como) almacenado en Azure Active Directory. 

Para obtener información sobre cómo agregar y editar notificaciones para estos escenarios, consulte este [artículo sobre la personalización de notificaciones](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Probar la aplicación de SAML 

Una vez que se han configurado las direcciones URL de SAML y el certificado en Azure AD y en la aplicación, los usuarios o grupos que se han asignado a la aplicación en Azure y las notificaciones se han revisado y editar si es necesario, el usuario está listo para iniciar sesión en la aplicación. 

Para probar, basta con inicio de sesión en el panel de acceso de Azure AD en https://myapps.microsoft.com con una cuenta de usuario que se asigna a la aplicación y, a continuación, haga clic en el mosaico de la aplicación para empezar el proceso de inicio de sesión único. Como alternativa, puede ir directamente a la dirección URL de inicio de sesión de la aplicación y el inicio de sesión desde allí. 

Sugerencias de depuración, vea este [artículo sobre cómo depurar basada en SAML inicio de sesión único para aplicaciones](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>Contraseña de inicio de sesión único

Seleccione esta opción para configurar [basada en la contraseña de inicio de sesión único](active-directory-appssoaccess-whatis.md) para una aplicación web que tiene una página de inicio de sesión de HTML. SSO basado en contraseña, también conocida como contraseña depósito, le permite administrar el acceso de usuario y contraseñas de aplicaciones web que no son compatibles con la federación de identidades. También es útil para escenarios donde varios usuarios necesitan para compartir una sola cuenta, como a las cuentas de aplicación de redes sociales de su organización. 

Después de seleccionar **siguiente**, se le pedirá introducir la dirección URL de la página de la aplicación basada en web iniciar sesión. Tenga en cuenta que debe ser la página que incluye los campos de entrada de usuario y contraseña. Una vez introducida, Azure AD inicia un proceso para analizar la página de inicio de sesión para una entrada de nombre de usuario y una contraseña de entrada. Si el proceso no es correcto, a continuación, le guiará por un proceso alternativo de instalar una extensión de explorador (requiere Internet Explorer, Chrome o Firefox) que le permita capturar manualmente los campos.

Una vez que se genera la página de inicio de sesión, pueden asignar usuarios y grupos y directivas de credencial pueden configurarse como normal [contraseña SSO aplicaciones](active-directory-appssoaccess-whatis.md).

Nota: Puede cargar un logotipo de mosaico de la aplicación utilizando el botón **Cargar logotipo** en la ficha **Configurar** de la aplicación. 

##<a name="existing-single-sign-on"></a>Un inicio de sesión único existente

Seleccione esta opción para agregar un vínculo a la aplicación portal de Panel de Azure AD acceso u Office 365 de su organización. Puede usar para agregar vínculos a aplicaciones web personalizados que usan actualmente Azure Active Directory Federation Services (u otro servicio de federación) en lugar de Azure AD para la autenticación. O bien, puede agregar profundidad vínculos a páginas específicas de SharePoint o a otras páginas web que desea que aparezca en paneles de acceso del usuario. 

Después de seleccionar **siguiente**, se le pedirá que escriba la dirección URL de la aplicación para vincular a. Una vez completada, usuarios y grupos pueden asignarse a la aplicación, que hace que la aplicación aparezca en el [iniciador de aplicaciones de Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o en el [panel de acceso de Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) para esos usuarios.

Nota: Puede cargar un logotipo de mosaico de la aplicación utilizando el botón **Cargar logotipo** en la ficha **Configurar** de la aplicación.

## <a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Cómo personalizar notificaciones emitidas el token SAML aplicaciones integradas previamente](active-directory-saml-claims-customization.md)
- [Solución de problemas basada en SAML inicio de sesión único](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
