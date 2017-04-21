<properties 
    pageTitle="Servidor de autenticación multifactor de autenticación de IIS y Azure"
    description="Esta es la página de autenticación multifactor de Azure que le ayudarán a implementar la autenticación de IIS y el servidor de autenticación multifactor de Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>Autenticación de IIS

La sección autenticación de IIS del servidor de autenticación multifactor de Azure le permite habilitar y configurar la autenticación de IIS para la integración con las aplicaciones web de IIS de Microsoft. El servidor de autenticación multifactor de Azure instala un complemento que puede filtrar las solicitudes que se realizan en el servidor web IIS con el fin de agregar la autenticación multifactor de Azure. El complemento de IIS proporciona soporte técnico para la autenticación basada en formularios y autenticación integrada HTTP de Windows. Direcciones IP también se pueden configurar para excluidas direcciones IP internas de autenticación en dos fases de confianza.


![Autenticación de IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Usar autenticación basada en formularios IIS con servidor de Azure autenticación multifactor

Para proteger una aplicación web IIS que utiliza la autenticación basada en formularios, instale al servidor de autenticación multifactor de Azure en el servidor web IIS y configurar el servidor por el procedimiento siguiente.

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de autenticación de IIS en el menú de la izquierda.
2. Haga clic en la pestaña basadas en formularios.
3. Haga clic en Agregar... botón.
4. Para detectar el nombre de usuario, variables de contraseña y el dominio automáticamente, escriba la dirección URL de inicio de sesión (por ejemplo, https://localhost/contoso/auth/login.aspx) en el cuadro de diálogo del sitio Web de Auto-Configure Form-Based y haga clic en Aceptar.
5. Active la casilla de coincidencia de usuario de requerir la autenticación multifactor si todos los usuarios han sido o se importarán en el servidor y sujetos a la autenticación multifactor. Si un gran número de usuarios no se han importado en el servidor o estará exento de la autenticación multifactor, deje la casilla desactivada.
6. Si no pueden detectar automáticamente las variables de la página, haga clic en el especificar manualmente... botón en el cuadro de diálogo del sitio Web de Auto-Configure Form-Based.
7. En el cuadro de diálogo del sitio Web de Add Form-Based, escriba la dirección URL a la página de inicio de sesión en el campo dirección URL de envío y escriba un nombre de la aplicación (opcional). El nombre de la aplicación aparece en los informes de autenticación multifactor de Azure y se mostrará dentro de los mensajes de autenticación de SMS o aplicación móvil. Vea el archivo de ayuda para obtener más información sobre la dirección URL de envío.
8. Seleccione el formato correcto de la solicitud. Esto se establece en "POST o GET" para la mayoría de las aplicaciones web.
9. Escriba la variable de nombre de usuario, la variable de contraseña y la variable de dominio (si aparece en la página de inicio de sesión). Debe ir a la página de inicio de sesión en un explorador web, haga clic en la página y seleccione "Ver código fuente" para encontrar los nombres de los cuadros dentro de la página.
10. La casilla requerir autenticación multifactor de Azure usuario coincidir si todos los usuarios han sido o se importarán en el servidor y sujetos a la autenticación multifactor. Si un gran número de usuarios no se han importado en el servidor o estará exento de la autenticación multifactor, deje la casilla desactivada. Ver el archivo de ayuda para obtener información adicional acerca de esta característica.
11.  Haga clic en Avanzadas... botón para revisar la configuración avanzada, incluida la capacidad para seleccionar un archivo de página personalizado denegación, en caché autenticaciones correctas para el sitio Web durante un período de tiempo con las cookies y seleccionar si se deben autenticar las credenciales principales en el dominio de Windows, un directorio LDAP o un servidor RADIUS. Cuando haya terminado, haga clic en el botón Aceptar para volver al cuadro de diálogo de sitio Web de Add Form-Based. Consulte el archivo de ayuda para obtener más información sobre la configuración avanzada.
12. Haga clic en el botón Aceptar.
13. Una vez que se han detectado o especificado las variables URL y páginas, los datos del sitio Web se mostrarán en el panel basadas en formularios.
14. Vea los IIS habilitar complementos de la sección servidor de autenticación multifactor de Azure directamente a continuación para completar la configuración de autenticación de IIS.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Usar autenticación de Windows integrada con servidor de Azure autenticación multifactor

Para proteger una aplicación web IIS que utiliza la autenticación integrada HTTP de Windows, instale al servidor de autenticación multifactor de Azure en el servidor web IIS y configurar el servidor por el procedimiento siguiente.

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de autenticación de IIS en el menú de la izquierda.
2. Haga clic en la pestaña HTTP. Haga clic en la pestaña basadas en formularios.
3. Haga clic en Agregar... botón.
4. En el cuadro de diálogo Agregar dirección URL de Base, escriba la dirección URL para el sitio Web donde está la autenticación HTTP realiza (por ejemplo, http://localhost/owa) en el campo dirección URL Base y escriba un nombre de la aplicación (opcional). El nombre de la aplicación aparece en los informes de autenticación multifactor de Azure y se mostrará dentro de los mensajes de autenticación de SMS o aplicación móvil.
5. Ajustar el tiempo de espera inactivo y máximo tiempos de sesión si no es suficiente el valor predeterminado.
6. Active la casilla de coincidencia de usuario de requerir la autenticación multifactor si todos los usuarios han sido o se importarán en el servidor y sujetos a la autenticación multifactor. Si un gran número de usuarios no se han importado en el servidor o estará exento de la autenticación multifactor, deje la casilla desactivada. Ver el archivo de ayuda para obtener información adicional acerca de esta característica.
7. Active la casilla de la caché de cookies si lo desea.
8. Haga clic en el botón Aceptar.
9. Vea la sección [habilitar IIS Plug-ins para servidor de autenticación multifactor de Azure](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) directamente siguiente para completar la configuración de autenticación de IIS.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Habilitar IIS Plug-ins para servidor de Azure autenticación multifactor

Una vez que haya configurado basadas en formularios o las direcciones URL HTTP autenticación y configuración, debe seleccionar las ubicaciones que los complementos de IIS de autenticación multifactor de Azure deben se cargan y habilitados en IIS. Utilice el procedimiento siguiente:

1. Si se ejecuta en IIS 6, haga clic en la pestaña ISAPI y seleccione el sitio Web de la aplicación web se está ejecutando en (por ejemplo, sitio Web predeterminado) para habilitar el complemento de filtro de ISAPI de autenticación multifactor de Azure para ese sitio.
2. Si ejecuta IIS 7 o superior, haga clic en la ficha módulo nativo y seleccione el servidor, los sitios Web o aplicaciones para habilitar el complemento de IIS en lo niveles que desee.
3. Haga clic en el cuadro de autenticación habilitar IIS en la parte superior de la pantalla. La autenticación multifactor Azure ahora es proteger la aplicación de IIS seleccionada. Asegúrese de que los usuarios se han importado en el servidor. Vea la sección IP fiables si le gustaría blanca que las direcciones IP internas para que la autenticación de dos factores no es necesaria al iniciar sesión en el sitio Web de las ubicaciones.


## <a name="trusted-ips"></a>Direcciones IP de confianza

Las direcciones IP de confianza permite a los usuarios omitir la autenticación multifactor de Azure para las solicitudes de sitio Web que provienen de determinadas direcciones IP o subredes. Por ejemplo, es aconsejable usuarios excluidos de la autenticación multifactor de Azure durante la sesión de la oficina. Para ello, debe especificar la subred office como una entrada de IP fiables. Para configurar direcciones IP de confianza Utilice el procedimiento siguiente:

1. En la sección autenticación de IIS, haga clic en la ficha IP fiables.
2. Haga clic en Agregar... botón.
3. Cuando aparezca el cuadro de diálogo Agregar direcciones IP de confianza, seleccione el intervalo IP o el botón de radio de subred IP única.
4. ntral la dirección IP, intervalo de direcciones IP o subred que debería estar en la lista blanca. Si introduce una subred, seleccione la máscara de red correspondiente y haga clic en el botón Aceptar. Se ha agregado la lista blanca.
