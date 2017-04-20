<properties
    pageTitle="Active Directory administración de servicios de federación y personalización con Azure AD Connect | Microsoft Azure"
    description="AD FS una administración con Azure AD Connect y personalización de AD FS sesión experiencia del usuario con Azure AD Connect y PowerShell."
    keywords="AD FS, ADFS, AD FS administración, conectar AAD, conectar, iniciar sesión, AD FS personalización, repare la federación de confianza, O365, usuario de confianza"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Administración de Active Directory Federation Services y personalización con Azure AD Connect

Esta tareas de detalles de artículo relacionado con Active Directory Federation Services (AD FS) que pueden realizarse usando Microsoft Azure Active Directory conectar, además de otras tareas comunes de AD FS que podrían ser necesarios para completar la configuración de un conjunto de servidores de AD FS.

| Tema | ¿Qué cubre |
|:------|:-------------|
|**Administración de AD FS**|
|[Reparar la confianza](#repairthetrust)| Reparar la confianza de federación con Office 365 |
|[Agregar un servidor de AD FS](#addadfsserver) | Expandir el conjunto de servidores de AD FS con un servidor de AD FS adicional|
|[Agregar un servidor proxy de AD FS web application](#addwapserver) | Expandir el conjunto de servidores de AD FS con un servidor WAP adicional|
|[Agregar un dominio federado](#addfeddomain)| Agregar un dominio federado|
| **Personalización de AD FS**|
|[Agregar un logotipo personalizado o ilustración](#customlogo)| Personalizar una página de inicio de sesión de AD FS con un logotipo de la empresa y una ilustración |
|[Agregar una descripción de inicio de sesión](#addsignindescription) | Agregar una descripción de la página de inicio de sesión |
|[Modificar las reglas de notificación de AD FS](#modclaims) | Modificación de notificaciones de AD FS para varios escenarios de federación |

## <a name="ad-fs-management"></a>Administración de AD FS

Azure AD Connect proporciona varias tareas relacionadas con AD FS que se pueden realizar con el Asistente de Azure AD Connect con mínima intervención del usuario. Cuando haya terminado la instalación de Azure AD Connect, ejecute al asistente, puede ejecutar el Asistente para realizar tareas adicionales.

### Reparar la confianza<a name=repairthetrust></a>

Azure AD Connect puede comprobar el estado actual de la confianza de AD FS y Azure Active Directory y tomar acciones apropiadas para reparar la confianza. Siga estos pasos para reparar su Azure AD y confianza de AD FS.

1. Seleccione **Reparar AAD y ADFS confiar** en la lista de tareas adicionales.
![Reparar AAD y ADFS confiar](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. En la página **conectarse a Azure AD** , proporcione sus credenciales de administrador global para Azure AD y haga clic en **siguiente**.
![Conectarse a Azure AD](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. En la página de **credenciales de acceso remoto** , escriba las credenciales de administrador de dominio.
![Credenciales de acceso remoto](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Después de hacer clic en **siguiente**, Azure AD Connect se comprobar estado del certificado y mostrar los problemas.

    ![Estado de certificados](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    La página **listo para configurar** mostrará la lista de acciones que se realizarán para reparar la confianza.

    ![¿Está listo para configurar](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Haga clic en **instalar** para reparar la confianza.

>[AZURE.NOTE] Azure AD Connect pueden sólo reparación o actuar en certificados de firma automática. Azure AD Connect no puede reparar certificados de terceros.

### Agregar un servidor de AD FS<a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect requiere el archivo de certificado PFX para agregar un servidor de AD FS. Por lo tanto, puede realizar esta operación solo si configura el conjunto de servidores de AD FS mediante Azure AD Connect.

1. Seleccione **implementar un servidor de federación adicional** y haga clic en **siguiente**.
![Servidor de federación adicionales](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. En la página **conectarse a Azure AD** , escriba sus credenciales de administrador global para Azure AD y haga clic en **siguiente**.
![Conectarse a Azure AD](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Proporcione credenciales de administrador de dominio.
![Credenciales de administrador de dominio](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect pedirá la contraseña del archivo PFX que proporcionan al configurar su nuevo conjunto de AD FS con Azure AD Connect. Haga clic en **Escriba la contraseña** para proporcionar la contraseña para el archivo PFX.
![Contraseña de certificado](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Especifique el certificado SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. En la página de **AD FS servidores** , escriba el nombre del servidor o la dirección IP se agregue a la granja de servidores de AD FS.
![Servidores de AD FS](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Haga clic en **siguiente** y vaya a través de la página **Configurar** final. Después de Azure AD Connect ha terminado de agregar los servidores de la granja de AD FS, se le ofrecerá la opción para comprobar la conectividad.
![¿Está listo para configurar](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Complete la instalación](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Agregar un servidor proxy de AD FS web application<a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect requiere el archivo de certificado PFX para agregar un servidor de proxy de aplicación web. Por lo tanto, podrá realizar esta operación solo si configura el conjunto de servidores de AD FS mediante Azure AD Connect.

1. Seleccione **Proxy de implementar la aplicación Web** de la lista de tareas disponibles.
![Implementar a proxy de aplicación web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Proporcione las credenciales de administrador global Azure.
![Conectarse a Azure AD](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. En la página **certificado SSL de especificar** , proporcione la contraseña para el archivo PFX que proporcionan al configurar el conjunto de servidores de AD FS con Azure AD Connect.
![Contraseña de certificado](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Especifique el certificado SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Agregue el servidor que se agregará como un proxy de aplicación web. Porque el servidor proxy de aplicaciones de web no puede unirse al dominio, el asistente le pedirá credenciales administrativas en el servidor que se va a agregar.
![Credenciales administrativas del servidor](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. En la página de **credenciales de confianza de servidor Proxy** , proporcione credenciales administrativas para configurar la confianza de proxy y obtener acceso al servidor principal en el conjunto de servidores de AD FS.
![Credenciales de confianza de proxy](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. En la página **listo para configurar** , el asistente muestra la lista de acciones que se realizarán.
![¿Está listo para configurar](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Haga clic en **instalar** para finalizar la configuración. Una vez completada la configuración, el asistente le ofrece la opción para comprobar la conectividad a los servidores. Haga clic en **verificar** para comprobar la conectividad.
![Complete la instalación](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Agregar un dominio federado<a name=addfeddomain></a>

Es fácil agregar un dominio a ser federado con Azure AD usando Azure AD Connect. Azure AD Connect agrega el dominio para la federación y modifica las reglas de notificación para reflejar correctamente el emisor cuando tiene varios dominios federados con Azure AD.

1. Para agregar un dominio federado, seleccione la tarea **Agregar una adicional dominio Azure AD**.
![Adicionales dominio Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. En la siguiente página del asistente, proporcione las credenciales de administrador global de Azure AD.
![Conectarse a Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. En la página de **credenciales de acceso remoto** , proporcione el dominio credenciales de administrador.
![Credenciales de acceso remoto](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. En la página siguiente, el asistente le proporcionará una lista de dominios de Azure AD con el que puede federarse su directorio local. Seleccione el dominio de la lista.
![Dominio de Active Directory de Azure](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Después de elegir el dominio, el asistente le proporcionará con la información adecuada en relación con más acciones que realizará el asistente y el impacto de la configuración. En algunos casos, si selecciona un dominio que no se ha comprobado en Azure AD, el asistente le proporcionará información para ayudarle a comprobar el dominio. Para obtener más detalles, vea [agregar su nombre de dominio personalizado para Azure Active Directory](active-directory-add-domain.md) .

5. Haga clic en **siguiente**, y la página **preparado para configurar** mostrará la lista de acciones que realizará Azure AD Connect. Haga clic en **instalar** para finalizar la configuración.
![¿Está listo para configurar](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalización de AD FS

Las secciones siguientes proporcionan detalles acerca de algunas de las tareas comunes que puede que tenga que realizar al personalizar la página de inicio de sesión de AD FS.

### Agregar un logotipo personalizado o ilustración<a name=customlogo></a>

Para cambiar el logotipo de la compañía que se muestra en la página de **Inicio de sesión** , use el siguiente cmdlet de Windows PowerShell y sintaxis.

> [AZURE.NOTE] Las dimensiones recomendadas para el logotipo son 260 x 35 @ 96 PPP con un tamaño de archivo que no sea superior a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Se requiere el parámetro de *nombre de destino* . El tema predeterminado que se lanzaron con AD FS se denomina predeterminado.


### Agregar una descripción de inicio de sesión<a name=addsignindescription></a>

Para agregar una descripción de la página de inicio de sesión a la **página de inicio de sesión**, use el siguiente cmdlet de Windows PowerShell y sintaxis.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificar las reglas de notificación de AD FS<a name=modclaims></a>

AD FS es compatible con un idioma de Reclamación enriquecido que puede usar para crear reglas de notificación personalizada. Para obtener más información, vea [La función del lenguaje de regla de notificación](https://technet.microsoft.com/library/dd807118.aspx).

Las secciones siguientes describen cómo puede escribir reglas personalizadas para algunos escenarios relacionados con Azure AD y federación de AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Identificador inmutable condicional en un valor que esté presente en el atributo

Azure AD Connect le permite especificar un atributo que se utilizará como un delimitador de origen cuando se sincronizan los objetos a Azure AD. Si el valor de atributo personalizado no está vacío, desea emitir una notificación de identificador inmutable. Por ejemplo, puede seleccionar **ms-ds-consistencyguid** como el atributo para el anclaje de origen y desea emitir **ImmutableID** como **ms-ds-consistencyguid** en caso de que el atributo tiene un valor en él. Si no hay ningún valor en el atributo, emitir **GUID de objeto** como el identificador inmutable.  Puede crear el conjunto de reglas de notificación personalizada como se describe en la sección siguiente.

**Regla 1: Atributos de la consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

En esta regla se consultar los valores de **ms-ds-consistencyguid** y **GUID de objeto** de Active Directory para el usuario. Cambiar el nombre de la tienda a un nombre de almacén apropiado en la implementación de AD FS. También puede cambiar el tipo de reclamaciones a un tipo de reclamaciones adecuada para la federación según se define para **GUID de objeto** y **ms-ds-consistencyguid**.

Además, mediante **Agregar** y no el **problema**, procure no agregar un problema de salida de la entidad y puede usar los valores como valores intermedios. Va a emitir la notificación en una regla posterior después de establecer qué valor desea usar como el identificador inmutable.

**Regla 2: Comprobar si existe ms-ds-consistencyguid para el usuario**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regla define una marca temporal llamada **idflag** que se establece en **useguid** si no hay ningún **ms-ds-concistencyguid** rellenado para el usuario. La lógica de esto es el hecho de que AD FS no permite reclamaciones vacíos. Así que cuando agregue notificaciones http://contoso.com/ws/2016/02/identity/claims/objectguid y http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid en la regla 1, terminar con un **msdsconsistencyguid** reclamar solo si el valor se rellena para el usuario. Si no se rellena, AD FS ve que tendrá un valor vacío y coloca inmediatamente. Todos los objetos tendrá **GUID de objeto**, por lo que Reclamación siempre estará disponible después de ejecutar la regla 1.

**Regla 3: Emitir ms-ds-consistencyguid como identificador inmutable si está presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Se trata de una comprobación de **existen** implícita. Si existe el valor de la notificación, emitir como el identificador inmutable. En el ejemplo anterior, se utiliza la notificación **nameidentifier** . Debe cambiar este valor para el tipo de notificación adecuada para identificador inmutable en su entorno.

**Regla 4: Emitir GUID de objeto como identificador inmutable si no hay ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

En esta regla, basta con que comprueba la marca temporal **idflag**. Decida si desea emitir la notificación en función de su valor.

> [AZURE.NOTE] La secuencia de estas reglas es importante.

#### <a name="sso-with-a-subdomain-upn"></a>SSO con un subdominio UPN

Puede agregar varios dominios a ser federado usando Azure AD Connect, tal como se describe en [Agregar un nuevo dominio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Debe modificar la notificación de UPN para que el identificador del emisor que se corresponda con el dominio raíz y no el subdominio, ya que el dominio raíz federados también cubre a los secundarios.

De forma predeterminada, la regla de notificación de Id. del emisor se establece como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Notificación de identificador de emisor predeterminado](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

La regla predeterminada simplemente toma el sufijo UPN y usa en la notificación de identificador del emisor. Por ejemplo, John es un usuario de sub.contoso.com y contoso.com se federa con Azure AD. Escribe John john@sub.contoso.com como el nombre de usuario al iniciar sesión en Azure AD y el ID de emisor reclamar la regla en AD FS lo tratará de la siguiente manera.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**El valor de notificación:** http://sub.contoso.com/adfs/services/trust/

Para que solo el dominio raíz en el valor de la reclamación de emisor, cambie la regla de notificación para que coincida con el siguiente.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las [Opciones de inicio de sesión de usuario](active-directory-aadconnect-user-signin.md).
