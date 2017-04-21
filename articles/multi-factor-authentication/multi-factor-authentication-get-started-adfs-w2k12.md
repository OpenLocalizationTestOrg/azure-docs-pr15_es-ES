<properties
    pageTitle="Servidor AMF con Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="En este artículo se describe cómo empezar a usar autenticación multifactor de Azure y AD FS en Windows Server 2012 R2."
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


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Proteger los recursos de la nube y locales con el servidor de autenticación multifactor de Azure con AD FS en Windows Server 2012 R2

Si utiliza los servicios de federación de Active Directory (AD FS) y desea nube segura o recursos locales, puede configurar el servidor de autenticación multifactor de Azure para que funcione con AD FS. Esta configuración activa activó la verificación para extremos de gran valor.

En este artículo, trataremos con el servidor de autenticación multifactor de Azure con AD FS en Windows Server 2012 R2. Para obtener más información, lea acerca de cómo [recursos seguros de nube y locales con Azure Server de autenticación multifactor con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Proteger Windows Server 2012 R2 AD FS con el servidor de Azure autenticación multifactor

Cuando instala el servidor de autenticación multifactor de Azure, tiene las siguientes opciones:

- Instalar el servidor de autenticación multifactor de Azure localmente en el mismo servidor de AD FS
- Instalar al adaptador de Azure con autenticación multifactor localmente en el servidor de AD FS y después instalar el servidor de autenticación multifactor en otro equipo

Antes de empezar, tenga en cuenta la siguiente información:

- No se requieren para instalar el servidor de autenticación multifactor de Azure en su servidor de AD FS. Sin embargo, debe instalar al adaptador de autenticación multifactor de AD FS en Windows Server 2012 R2 que se está ejecutando AD FS. Puede instalar al servidor en un equipo distinto si es una versión compatible y volver a instalar al adaptador de AD FS por separado en su servidor de federación de AD FS. Consulte los procedimientos siguientes para aprender a instalar al adaptador por separado.

- Cuando se ha diseñado el adaptador AMF servidor AD FS, que fue previsto que AD FS puede pasar el nombre de usuario de confianza para el adaptador. A continuación, el nombre de fiesta de confianza podría utilizarse como un nombre de aplicación. Sin embargo, Esto resultó para que no sea el caso. Si su organización usa mensajes de texto o métodos de verificación de la aplicación móvil, las cadenas definidas en la configuración de la compañía contienen un marcador de posición, <$*application_name*$>. Este marcador de posición no es reemplazado automáticamente cuando se usa el adaptador de AD FS. Se recomienda que quite el marcador de posición de las cadenas apropiadas cuando seguro AD FS.

- La cuenta que utiliza para iniciar sesión debe tener derechos de usuario para crear grupos de seguridad en el servicio de Active Directory.

- El Asistente para la instalación de autenticación multifactor AD FS adaptador crea un grupo de seguridad llamado PhoneFactor Admins en su instancia de Active Directory. A continuación, se agrega la cuenta de servicio de AD FS de los servicios de federación a este grupo. Le recomendamos que compruebe en el controlador de dominio que ya se ha creado el grupo PhoneFactor Admins y que cuenta de servicio de AD FS es un miembro de este grupo. Si es necesario, agregue manualmente la cuenta de servicio de AD FS al grupo PhoneFactor Admins en el controlador de dominio.

- Para obtener información acerca de cómo instalar el SDK de servicio Web con el portal de usuario, lea acerca de [implementar el portal de usuario para el servidor de autenticación multifactor de Azure.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Instalar el servidor de autenticación multifactor de Azure localmente en el servidor de AD FS

1. Descargue e instale el servidor de autenticación multifactor de Azure en su servidor de AD FS. Para obtener información de instalación, lea acerca de [cómo empezar con el servidor de autenticación multifactor de Azure](multi-factor-authentication-get-started-server.md).
2. En la consola de administración de servidor de autenticación multifactor de Azure, haga clic en el icono de **AD FS** y, a continuación, seleccione las opciones de **Permitir la inscripción de usuario** y **Permitir a los usuarios seleccionar método**.
3. Seleccione cualquier opción adicional que desea especificar para su organización.
4. Haga clic en **instalar adaptador de AD FS**.
<center>![Nube](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si se muestra la ventana de Active Directory, que ofrece dos cosas. Su equipo está unido a un dominio y la configuración de Active Directory para proteger la comunicación entre el adaptador de AD FS y el servicio de autenticación multifactor está incompleta. Haga clic en **siguiente** para finalizar la configuración automáticamente o seleccione la **Omitir la configuración automática de Active Directory y configurar las opciones manualmente** casilla de verificación y, a continuación, haga clic en **siguiente**.
6. Si se muestran las ventanas de grupo Local, eso significa dos cosas. Su equipo no está unido a un dominio y la configuración de grupo local para proteger la comunicación entre el adaptador de AD FS y el servicio de autenticación multifactor está incompleta. Haga clic en **siguiente** para finalizar la configuración automáticamente o seleccione la **Omitir la configuración automática de grupo Local y configurar las opciones manualmente** casilla de verificación y, a continuación, haga clic en **siguiente**.
7. En el Asistente de instalación, haga clic en **siguiente**. Servidor de autenticación multifactor Azure crea el grupo PhoneFactor Admins y agrega la cuenta de servicio de AD FS al grupo PhoneFactor Admins.
<center>![Nube](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. En la página **Iniciar el programa de instalación** , haga clic en **siguiente**.
9. En el programa de instalación de adaptador autenticación multifactor AD FS, haga clic en **siguiente**.
10. Cuando termine la instalación, haga clic en **Cerrar** .
11. Cuando se ha instalado el adaptador, debe registrar con AD FS. Abrir Windows PowerShell y ejecute el siguiente comando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Nube](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Para utilizar su adaptador recién registrado, editar la directiva de autenticación global en AD FS. En la consola de administración de AD FS, vaya hasta el nodo **Directivas de autenticación** . En la sección **Autenticación multifactor** , haga clic en el vínculo **Editar** junto a la sección **Configuración Global** . En la ventana **Editar directiva de autenticación Global** , seleccione la **Autenticación multifactor** como método de autenticación adicionales y, a continuación, haga clic en **Aceptar**. El adaptador está registrado como WindowsAzureMultiFactorAuthentication. Reinicie el servicio de AD FS para el registro surta efecto.

<center>![Nube](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

En este momento, servidor de autenticación multifactor está configurado para que sea un proveedor de servicios de autenticación adicionales para usarlas con AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Instalar una instancia independiente del adaptador de AD FS mediante el SDK de servicio Web
1. Instale el SDK de servicio Web en el servidor que se está ejecutando el servidor de autenticación multifactor.
2. Copie los siguientes archivos de la \Program Files\Multi-directorio de servidor de autenticación de Factor en el servidor en el que planea instalar el adaptador de AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Registrar MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.ps1 anular el registro
  - MultiFactorAuthenticationAdfsAdapter.config
3. Ejecute el archivo de instalación MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. En el programa de instalación de adaptador autenticación multifactor AD FS, haga clic en **siguiente** para iniciar la instalación.
5. Cuando termine la instalación, haga clic en **Cerrar** .

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Editar el archivo MultiFactorAuthenticationAdfsAdapter.config

Siga estos pasos para editar el archivo MultiFactorAuthenticationAdfsAdapter.config:

1. Establecer el nodo **UseWebServiceSdk** en **true**.  
2. Establezca el valor de **WebServiceSdkUrl** a la dirección URL del SDK de servicio de Web de autenticación multifactor. Por ejemplo: *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, donde certificatename es el nombre del certificado.  
3. Editar la secuencia de comandos de registrar MultiFactorAuthenticationAdfsAdapter.ps1 agregando *- ConfigurationFilePath &lt;ruta&gt; * hasta el final de la `Register-AdfsAuthenticationProvider` comando, donde * &lt;ruta&gt; * es la ruta completa al archivo MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurar el SDK de servicio Web con un nombre de usuario y contraseña

Existen dos opciones para configurar el SDK de servicio Web. La primera es con un nombre de usuario y contraseña, el segundo es con un certificado de cliente. Siga estos pasos para la primera opción, o vaya directamente para el segundo.  

1. Establezca el valor de **WebServiceSdkUsername** a una cuenta que sea miembro del grupo de seguridad PhoneFactor Admins. Usar el &lt;dominio&gt;& #92; &lt;nombre de usuario&gt; formato.  
2. Establezca el valor de **WebServiceSdkPassword** a la contraseña de la cuenta correspondiente.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurar el SDK de servicio Web con un certificado de cliente

Si no desea usar un nombre de usuario y contraseña, siga estos pasos para configurar el SDK de servicio Web con un certificado de cliente.

1. Obtener un certificado de cliente de una entidad emisora de certificados para el servidor que se está ejecutando el SDK de servicio Web. Obtenga información sobre cómo [obtener certificados de cliente](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importar el certificado de cliente en el almacén de certificados personales del equipo local en el servidor que se está ejecutando el SDK de servicio Web. Asegúrese de que el certificado de la entidad emisora de certificados pública se encuentra en almacén de certificados de certificados raíz de confianza.  
3. Exporte las claves públicas y privadas del certificado de cliente a un archivo .pfx.  
4. Exportar la clave pública en formato base 64 a un archivo .cer.  
5. En el administrador del servidor, compruebe que la característica de autenticación de asignaciones de certificado de cliente de servidor Web (IIS) \Web Server\Security\IIS está instalada. Si no está instalado, seleccione **Agregar funciones y características** para agregar esta característica.  
6. En el Administrador de IIS, haga doble clic en **Editor de configuración** del sitio Web que contiene el directorio virtual SDK del servicio Web. Es importante hacerlo en el nivel de sitio Web y no en el nivel de directorio virtual.  
7. Vaya a la sección **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Establezca enabled en **true**.  
9. Establecer oneToOneCertificateMappingsEnabled en **true**.  
10. Haga clic en el botón **...** junto a oneToOneMappings y, a continuación, haga clic en **Agregar** vínculo.  
11. Abra el archivo .cer de base 64 que exportó anteriormente. Quitar *---comenzar certificado---*, *---Finalizar certificado (...)*y todos los saltos de línea. Copie la cadena resultante.  
12. Establecer el certificado con la cadena que se copió en el paso anterior.  
13. Establezca enabled en **true**.  
14. Establecer el nombre de usuario a una cuenta que sea miembro del grupo de seguridad PhoneFactor Admins. Usar el &lt;dominio&gt;& #92; &lt;nombre de usuario&gt; formato.  
15. Establecer la contraseña para la contraseña de la cuenta adecuada y, a continuación, cierre el Editor de configuración.  
16. Haga clic en el vínculo de **Aplicar** .  
17. En el directorio virtual SDK del servicio Web, haga doble clic en **autenticación**.  
18. Compruebe que la representación de ASP.NET y la autenticación básica se configuran en **habilitado**y que todos los demás elementos se establecen en **deshabilitado**.  
19. En el directorio virtual SDK del servicio Web, haga doble clic en **Configuración de SSL**.  
20. Establezca los certificados de cliente en **Aceptar**y, a continuación, haga clic en **Aplicar**.  
21. Copie el archivo .pfx que exportó anteriormente en el servidor que ejecuta el adaptador de AD FS.  
22. Importar el archivo .pfx en el almacén de certificados personales del equipo local.  
23. Haga clic en y seleccione **Administrar claves privadas**y, a continuación, conceda acceso de lectura a la cuenta utilizada para iniciar sesión en el servicio de AD FS.  
24. Abra el certificado de cliente y copie la huella digital de la ficha **Detalles** .  
25. En el archivo MultiFactorAuthenticationAdfsAdapter.config, establezca **WebServiceSdkCertificateThumbprint** en la cadena que se copió en el paso anterior.  


Por último, para registrar el adaptador, ejecute el \Program Files\Multi-Factor de autenticación Server\Register MultiFactorAuthenticationAdfsAdapter.ps1 secuencia de comandos de PowerShell. El adaptador está registrado como WindowsAzureMultiFactorAuthentication. Reinicie el servicio de AD FS para el registro surta efecto.

## <a name="related-topics"></a>Temas relacionados

Para la solución de problemas, consulte las [P+f de autenticación multifactor de Azure](multi-factor-authentication-faq.md)
