<properties
    pageTitle="Usar el servidor de Azure AMF con AD FS 2.0 | Microsoft Azure"
    description="Esta es la página de autenticación multifactor de Azure que describe cómo empezar a trabajar con Azure AMF y AD FS 2.0."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Proteger recursos de nube y locales con el servidor de autenticación multifactor de Azure con AD FS 2.0

En este artículo es para las organizaciones que están federadas con Azure Active Directory y desea proteger recursos que sean local o en la nube. Proteger los recursos con el servidor de autenticación multifactor de Azure y configurarlo para que funcione con AD FS para que activó la verificación se ha activado para puntos finales de valor alto.

Esta documentación abarca con el servidor de autenticación multifactor de Azure AD FS 2.0.  Obtenga más información sobre [proteger recursos de nube y locales con el servidor de autenticación multifactor de Azure con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteger AD FS 2.0 con un servidor proxy
Para proteger AD FS 2.0 con un servidor proxy, instalar al servidor de autenticación multifactor de Azure en el servidor proxy de ADFS y configurar el servidor.

### <a name="configure-iis-authentication"></a>Configurar la autenticación de IIS

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de **Autenticación de IIS** en el menú de la izquierda.
2. Haga clic en la pestaña **Basadas en formularios** .
3. Haga clic en el **Agregar...** botón.
<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Detectar automáticamente el nombre de usuario, contraseña y variables de dominio, escriba la dirección URL de inicio de sesión (por ejemplo, https://sso.contoso.com/adfs/ls) en el cuadro de diálogo del sitio Web de Auto-Configure Form-Based y haga clic en Aceptar.
5. Active la casilla **Requerir autenticación multifactor de Azure usuario coincide con** si todos los usuarios han sido o se importarán en el servidor y están sujetos a verificación en dos pasos. Si un gran número de usuarios no se han importado en el servidor o estará exento de verificación en dos pasos, deje la casilla desactivada. Para obtener información adicional acerca de esta característica, consulte el archivo de ayuda.
6. Si no pueden detectar automáticamente las variables de la página, haga clic en el **Especificar manualmente...** botón en el cuadro de diálogo del sitio Web de Auto-Configure Form-Based.
7. En el cuadro de diálogo del sitio Web de Add Form-Based, escriba la dirección URL a la página de inicio de sesión ADFS en el campo dirección URL de envío (por ejemplo, https://sso.contoso.com/adfs/ls) y escriba un nombre de la aplicación (opcional). El nombre de la aplicación aparece en los informes de autenticación multifactor de Azure y se mostrará dentro de los mensajes de autenticación de SMS o aplicación móvil. Vea el archivo de ayuda para obtener más información sobre la dirección URL de envío.
8. Establecer el formato de solicitud "Publicar u obtener".
9. Escriba el nombre de usuario variable (ctl00$ ContentPlaceHolder1$ UsernameTextBox) y contraseña (ctl00$ ContentPlaceHolder1$ cuadro de texto contraseña). Si su página de inicio de sesión basadas en formularios, muestra un cuadro de texto dominio, escriba también la variable del dominio. Debe ir a la página de inicio de sesión en un explorador web, haga clic en la página y seleccione **Ver código fuente** para buscar los nombres de los cuadros dentro de la página de inicio de sesión.
10. Active la casilla **Requerir autenticación multifactor de Azure usuario coincide con** si todos los usuarios han sido o se importarán en el servidor y están sujetos a verificación en dos pasos. Si un gran número de usuarios no se han importado en el servidor o estará exento de verificación en dos pasos, deje la casilla desactivada.
<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Haga clic en **Avanzadas...** botón para revisar la configuración avanzada. Puede configurar incluida la capacidad para seleccionar un archivo de página personalizado denegación, en la memoria caché autenticaciones correctas para el sitio Web con las cookies y que seleccione cómo desea autenticar las credenciales principales.
12. Dado que el servidor proxy de ADFS no es probable que se ha unido al dominio, puede usar LDAP para conectar con el controlador de dominio para la importación de usuario y la autenticación previa. En el cuadro de diálogo del sitio Web de Advanced Form-Based, haga clic en la pestaña **Autenticación principal** y seleccione **Enlazar LDAP** para el tipo de autenticación de autenticación previa.
13. Cuando haya terminado, haga clic en el botón **Aceptar** para volver al cuadro de diálogo de sitio Web de Add Form-Based. Consulte el archivo de ayuda para obtener más información sobre la configuración avanzada.
14. Haga clic en el botón **Aceptar** para cerrar el cuadro de diálogo.
15. Una vez que se han detectado o especificado las variables URL y páginas, los datos del sitio Web se muestran en el panel basadas en formularios.
16. Haga clic en la ficha **Módulo nativo** y seleccione el servidor, el sitio Web que se ejecuta el proxy ADFS en (como "sitio Web predeterminado") o la aplicación de proxy ADFS (como "ls" en "adfs") para habilitar el complemento de IIS en el nivel que desee.
17. Haga clic en el cuadro **autenticación habilitar IIS** en la parte superior de la pantalla.
18. La autenticación de IIS está habilitada.

### <a name="configure-directory-integration"></a>Configurar la integración de directorios

Habilitó la autenticación de IIS, pero para realizar la autenticación previa a su Active Directory (AD) a través de LDAP debe configurar la conexión LDAP con el controlador de dominio.

1. Haga clic en el icono de **Integración de directorios** .
2. En la pestaña Configuración, seleccione el botón de opción **utilizar la configuración de LDAP específica** .
<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Haga clic en la **modificar...** botón.
4. En el cuadro de diálogo Editar configuración de LDAP, rellene los campos con la información necesaria para conectar con el controlador de dominio de AD. Descripciones de los campos se incluyen en la tabla siguiente. Esta información también se incluye en el archivo de Ayuda de servidor de autenticación multifactor de Azure.
5. Probar la conexión LDAP haciendo clic en el botón de **prueba** .
<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Si la prueba de conexión LDAP se realizó correctamente, haga clic en el botón **Aceptar** .

### <a name="configure-company-settings"></a>Establecer la configuración de la compañía

1. A continuación, haga clic en el icono de **Configuración de la compañía** y seleccione la ficha **Resolución de nombre de usuario** .
2. Seleccione el botón de opción **utilizar LDAP atributo de identificador único para la coincidencia de nombres de usuario** .
3. Si los usuarios escribir su nombre de usuario en formato "dominio\nombreDeUsuario", el servidor necesita poder eliminar el dominio desactivar el nombre de usuario cuando se crea la consulta LDAP. Es posible que a través de un valor del registro.
4. Abra el editor del registro y vaya a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positivo redes/PhoneFactor en un servidor de 64 bits. Si se encuentra en un servidor de 32 bits, tomar "Wow6432Node" fuera de la ruta de acceso. Crear una clave de Registro DWORD denominada "UsernameCxz_stripPrefixDomain" y establezca el valor en 1. La autenticación multifactor Azure ahora es proteger al proxy ADFS.

Asegúrese de que los usuarios se han importado desde Active Directory en el servidor. Vea la [sección de direcciones IP de confianza](#trusted-ips) si las direcciones IP internas blanca que le gustaría para que no se requiere la activó la verificación al iniciar sesión el sitio Web de las ubicaciones.

<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 directo sin un servidor proxy

Puede proteger AD FS cuando no se usa el proxy AD FS. Instalar al servidor de autenticación multifactor de Azure en el servidor de AD FS y configurar el servidor por los siguientes pasos:

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de **Autenticación de IIS** en el menú de la izquierda.
2. Haga clic en la pestaña **HTTP** .
3. Haga clic en el **Agregar...** botón.
4. En el cuadro de diálogo Agregar Base URL, escriba la dirección URL para el sitio Web ADFS donde se realiza la autenticación de HTTP (por ejemplo, https://sso.domain.com/adfs/ls/auth/integrated) en el campo dirección URL Base. A continuación, escriba un nombre de la aplicación (opcional). El nombre de la aplicación aparece en los informes de autenticación multifactor de Azure y se mostrará dentro de los mensajes de autenticación de SMS o aplicación móvil.
5. Si lo desea, ajuste el tiempo de espera inactivo y el máximo tiempos de sesión.
6. Active la casilla **Requerir autenticación multifactor de Azure usuario coincide con** si todos los usuarios han sido o se importarán en el servidor y están sujetos a verificación en dos pasos. Si un gran número de usuarios no se han importado en el servidor o estará exento de verificación en dos pasos, deje la casilla desactivada. Para obtener información adicional acerca de esta característica, consulte el archivo de ayuda.
7. Active la casilla de la caché de cookies si lo desea.
<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Haga clic en el botón **Aceptar** .
9. Haga clic en la ficha **Módulo nativo** y seleccione el servidor, el sitio Web (como "sitio Web predeterminado") o la aplicación de ADFS (por ejemplo, "ls" en "adfs") para habilitar el complemento de IIS en el nivel que desee.
10. Haga clic en el cuadro **autenticación habilitar IIS** en la parte superior de la pantalla. La autenticación multifactor Azure ahora es proteger ADFS.

Asegúrese de que los usuarios se han importado desde Active Directory en el servidor. Vea la sección de IP fiables si las direcciones IP internas blanca que le gustaría para que no se requiere la activó la verificación al iniciar sesión el sitio Web de las ubicaciones.


## <a name="trusted-ips"></a>Direcciones IP de confianza
Direcciones IP de confianza permitir a los usuarios omitir la autenticación multifactor de Azure para las solicitudes de sitio Web que provienen de direcciones IP o subredes específicos. Por ejemplo, es aconsejable usuarios excluidos de verificación en dos pasos cuando iniciar sesión de la oficina. Para ello, debe especificar la subred office como una entrada de IP fiables.

### <a name="to-configure-trusted-ips"></a>Para configurar las direcciones IP de confianza


1. En la sección autenticación de IIS, haga clic en la ficha **IP fiables** .
1. Haga clic en el **Agregar...** botón.
1. Cuando aparezca el cuadro de diálogo Agregar direcciones IP de confianza, seleccione uno de los botones de opción **IP única**, el **intervalo IP**o **subred** .
1. Escriba la dirección IP, intervalo de direcciones IP o subred que debería estar en la lista blanca. Si introduce una subred, seleccione la máscara de red correspondiente y haga clic en el botón **Aceptar** . Se ha agregado la IP de confianza.


<center>![Programa de instalación](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
