<properties
   pageTitle="Formato de archivo CSV para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active B2B de directorio es compatible con las relaciones de su compañía habilitando socios empresariales de forma selectiva tengan acceso a sus aplicaciones corporativas"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Vista previa colaboración de Azure AD B2B: formato de archivo CSV

La versión preliminar de colaboración de Azure AD B2B requiere un archivo CSV que especifica la información de usuario de partner cargarse a través del portal de Azure AD. El archivo CSV debe contener los campos opcionales según sea necesario y etiquetas necesarias a continuación. Modificar el archivo CSV de muestra (abajo) sin cambiar la ortografía de las etiquetas en la primera fila.

>[AZURE.NOTE] La primera fila de etiquetas (por ejemplo, correo electrónico, DisplayName y así sucesivamente) es necesaria para el archivo CSV que puede analizar correctamente. La ortografía debe coincidir con los campos especificados a continuación. Estas etiquetas identifican el contenido por debajo de ellos. Para los campos opcionales que no sean necesarios, se pueden quitar las etiquetas del archivo CSV. Toda la columna puede dejarse vacía.

## <a name="required-fields-br"></a>Campos requeridos: <br/>
**Correo electrónico:** Dirección de correo electrónico de usuarios invitados. <br/>
**DisplayName:** Nombre para mostrar para los usuarios invitados (normalmente, nombre y apellidos).<br/>


## <a name="optional-fields-br"></a>Campos opcionales: <br/>

**InvitationText:** Personalizar el texto de correo electrónico de invitación después de la aplicación de marca y antes del vínculo de amortización.<br/>
**InvitedToApplications:** AppID a aplicaciones para asignar a los usuarios de la empresa. AppID es recuperables en PowerShell llamando`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups:** Outfile para grupos para agregar usuario. Outfile es recuperables en PowerShell llamando`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL:** Dirección URL para dirigir a un usuario invitado después de la aceptación de la invitación. Debe ser una URL específicos de la empresa (por ejemplo, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si no se especifica este campo opcional, los usuarios invitados se dirigen al Panel de acceso de la aplicación donde puede navegar a sus aplicaciones corporativos elegidos. Dirección URL del Panel de acceso de aplicación tiene el formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: correo electrónico para copiar invitación por correo electrónico. Si se utiliza el campo CcEmailAddress, no se puede utilizar esta invitación para usuario verificado por correo electrónico o la creación del inquilino.<br/>
**Idioma:** Idioma de la experiencia de correo electrónico y amortización invitación, con "en" (en inglés) como el valor predeterminado cuando no se especifica. Las otras 10 compatibles son códigos de idioma:<br/>
1. de: alemán<br/>
2. es: español<br/>
3. fn: francés<br/>
4. se: italiano<br/>
5. ja: japonés<br/>
6. ko: coreano<br/>
7. pt-BR: portugués (Brasil)<br/>
8. RU: ruso<br/>
9. zh-HANS: chino simplificado<br/>
10. zh-HANT: chino tradicional<br/>

## <a name="sample-csv-file"></a>Archivo CSV de muestra
Este es un ejemplo CSV que se puede modificar.

>[AZURE.NOTE] Copie y pegue esto en el Bloc de notas y guardar con una extensión de archivo '.csv'. A continuación, editar en Excel. Se estarán estructurado en una tabla con etiquetas en la primera fila.

> Agregar más campos opcionales en Excel mediante la especificación de la etiqueta y rellenar la columna situado debajo de él.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Artículos relacionados
Examinar nuestras otros artículos de colaboración de Azure AD B2B

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributos del objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
