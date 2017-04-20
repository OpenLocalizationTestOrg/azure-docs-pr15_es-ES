<properties
    pageTitle="Trabajar con dominios personalizados en Proxy de la aplicación de Azure AD | Microsoft Azure"
    description="Describe cómo trabajar con dominios personalizados en Proxy de aplicación de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Trabajar con dominios personalizados en Proxy de aplicación de Azure AD

Usar un dominio predeterminado permite establecer la misma dirección URL como dirección URL para tener acceso a la aplicación para que los usuarios solo tienen una dirección URL de recordar tener acceso a la aplicación, independientemente de dónde tienen acceso desde interna y externa. Esto también le permite crear un método abreviado solo en el Panel de acceso de la aplicación. Si usa el dominio predeterminado proporcionado por Proxy de aplicación de Azure AD, no hay ninguna configuración adicional que necesario para habilitar el dominio. En caso de que use un dominio personalizado, hay algunas cosas que debe hacer para asegurarse de que Proxy de aplicación reconoce su dominio y valida sus certificados.

## <a name="selecting-your-custom-domain"></a>Seleccionar el dominio personalizado

1. Publicar la aplicación según las instrucciones de [publicar aplicaciones con Proxy de la aplicación](active-directory-application-proxy-publish.md).
2. Después de la aplicación aparece en la lista de aplicaciones, selecciónelo y haga clic en **Configurar**.
3. En la **Dirección URL externa**, escriba el dominio personalizado.
4. Si la dirección URL externa es https, se le pedirá que cargue un certificado para que ese Azure puede validar la dirección URL de la aplicación. También puede cargar un certificado comodín que coincida con la dirección URL externa de la aplicación. En este dominio debe estar dentro de la lista de sus [Azure había comprobado dominios](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure debe tener un certificado para la dirección URL del dominio de la aplicación o un certificado comodín que coincida con la dirección URL externa para la aplicación.
5. Asegúrese de agregar un registro DNS que dirige a la dirección URL interna a la aplicación que le permite tener la misma dirección URL para el acceso interno y externo y un único método abreviado de la aplicación en la lista de aplicaciones del usuario.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Preguntas más frecuentes sobre cómo trabajar con dominios personalizados

P: ¿puedo seleccionar un certificado ya cargado sin cargar de nuevo?  
R: previamente cargados certificados se enlazan automáticamente a una aplicación y hay exactamente un certificado que coincidan con el nombre de host de la aplicación.  

P: ¿Cómo puedo agregar un certificado y el formato que puede cargar el certificado exportado en?  
R: el certificado debe cargarse desde la página de configuración de aplicación. El certificado debe ser un archivo PFX.  

P: ¿certificados ECC se pueden usar?  
R: hay ninguna limitación explícita en métodos de firma.  

P: ¿certificados de SAN se pueden utilizar?  
R: Sí.  

P: ¿certificados comodín se pueden usar?  
R: Sí.  

P: ¿un certificado diferente se puede utilizar en cada aplicación?  
R: Sí, a menos que las dos aplicaciones comparten el mismo host externo.  

P: si registrar un dominio nuevo, ¿puedo usar ese dominio?  
R: Sí, se introduce la lista de dominios de la lista de dominio comprobado del inquilino.  

P: ¿qué sucede cuando expira un certificado?  
R: obtendrá una advertencia en la sección de certificado en la página de configuración de aplicación. Cuando un usuario intenta obtener acceso a la aplicación, aparecerá una advertencia de seguridad.  

P: ¿qué debo hacer si deseo reemplazar un certificado para una aplicación determinada?  
R: cargar un certificado nuevo desde la página de configuración de aplicación.  

P: ¿puedo eliminar un certificado y reemplazarla?  
R: cuando se carga un certificado nuevo, si el certificado antiguo no está en uso por otra aplicación, se eliminarán automáticamente.  

P: ¿qué sucede cuando se revoca un certificado?  
A: no se realizan comprobaciones de revocación de certificados. Cuando un usuario intenta obtener acceso a la aplicación, dependiendo del explorador, puede aparecer una advertencia de seguridad.  

P: ¿puedo usar un certificado autofirmado?  
R: Sí, se permiten los certificados de firma automática. Tenga en cuenta que, si usa una entidad de certificación privada, CDP (punto de distribución de punto de revocación de certificados) para el certificado debe ser público.  

P: ¿hay un lugar para ver todos los certificados de mi inquilino?  
R: esto no es compatible con la versión actual.  


## <a name="see-also"></a>Vea también

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Agregar nombre de dominio personalizado a Azure AD](active-directory-add-domain.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)
