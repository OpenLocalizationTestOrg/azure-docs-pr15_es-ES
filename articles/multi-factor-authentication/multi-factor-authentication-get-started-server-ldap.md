<properties 
    pageTitle="Servidor de autenticación multifactor de autenticación LDAP y Azure"
    description="Esta es la página de autenticación multifactor de Azure que le ayudarán a implementar autenticación LDAP y servidor de autenticación multifactor de Azure."
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

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticación multifactor de autenticación LDAP y Azure


De forma predeterminada, el servidor de autenticación multifactor de Azure está configurado para importar o sincronizar los usuarios desde Active Directory. Sin embargo, se puede configurar para enlazar a directorios LDAP diferentes, como un directorio de ADAM o controlador de dominio de Active Directory específico. Cuando está configurado para conectarse a un directorio mediante LDAP, el servidor de autenticación multifactor de Azure se puede configurar para que actúe como un proxy LDAP para realizar autenticaciones. También permite el uso de enlace LDAP como un destino de radio de autenticación previa de usuarios con la autenticación de IIS o de autenticación principal en el Portal de usuario de autenticación de varios factores de Azure.

Cuando se utiliza la autenticación multifactor de Azure como un proxy LDAP, el servidor de autenticación multifactor de Azure se inserta entre el cliente LDAP (por ejemplo, el dispositivo VPN, la aplicación) y el servidor de directorio LDAP para agregar la autenticación multifactor. Para la autenticación multifactor de Azure a función, el servidor de autenticación multifactor de Azure debe estar configurado para comunicarse con los servidores de cliente y el directorio LDAP. En esta configuración, el servidor de autenticación multifactor de Azure acepta las solicitudes LDAP de servidores de cliente y aplicaciones y los reenvía al servidor de directorio LDAP de destino para validar las credenciales principales. Si la respuesta del directorio LDAP muestra que principal credenciales son válidos, autenticación multifactor de Azure realiza la autenticación multifactor de segundo y envía una respuesta al cliente LDAP. La autenticación completo se realizará correctamente solo si la autenticación con el servidor LDAP y la autenticación multifactor correctamente.





## <a name="ldap-authentication-configuration"></a>Configuración de autenticación de LDAP


Para configurar la autenticación LDAP, instale al servidor de autenticación multifactor de Azure en un servidor de Windows. Utilice el procedimiento siguiente:

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de autenticación LDAP en el menú de la izquierda.
2. Active la casilla de verificación Habilitar la autenticación de LDAP.![Autenticación LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. En la ficha clientes cambie los puertos TCP y SSL si el servicio LDAP de autenticación multifactor de Azure debe enlazar puertos no estándar para escuchar las solicitudes LDAP de los clientes que se configurarán.
4. Si planea usar LDAPS desde el cliente para el servidor de autenticación multifactor de Azure, debe estar instalado un certificado SSL en el servidor que se está ejecutando el servidor. Haga clic en Buscar... botón junto al cuadro certificado SSL y seleccione el certificado instalado que se usará para la conexión segura.
5. Haga clic en Agregar... botón.
6. En el cuadro de diálogo Agregar cliente de LDAP, escriba la dirección IP del dispositivo, servidor o aplicación que se autentica para el servidor y un nombre de la aplicación (opcional). El nombre de la aplicación aparece en los informes de autenticación multifactor de Azure y se mostrará dentro de los mensajes de autenticación de SMS o aplicación móvil.
7. La casilla requerir autenticación multifactor de Azure usuario coincidir si todos los usuarios han sido o se importarán en el servidor y están sujetos a la autenticación multifactor múltiple. Si un gran número de usuarios no se han importado en el servidor o estará exento de la autenticación multifactor múltiple, deje la casilla desactivada. Ver el archivo de ayuda para obtener información adicional acerca de esta característica.
8. Puede repetir los pasos del 5 al 7 para agregar a clientes LDAP adicionales.
9. Cuando la autenticación multifactor de Azure está configurada para recibir autenticaciones LDAP, debe a proxy esos autenticaciones al directorio LDAP. Por lo tanto, la pestaña de destino sólo muestra una sola, atenuado opción para usar un destino LDAP. Para configurar la conexión de directorio LDAP, haga clic en el icono de integración de directorios.
10. En la pestaña Configuración, seleccione el botón de opción Usar una configuración LDAP específico.
11. Haga clic en la edición... botón.
12. En el cuadro de diálogo Editar configuración de LDAP, rellene los campos con la información necesaria para conectar con el directorio LDAP. Descripciones de los campos se incluyen en la tabla siguiente. Nota: Esta información también se incluye en el archivo de Ayuda de servidor de autenticación multifactor de Azure.![Integración de directorios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Probar la conexión LDAP haciendo clic en el botón de prueba.
14. Si la prueba de conexión LDAP se realizó correctamente, haga clic en el botón Aceptar.
15. Haga clic en la pestaña filtros. El servidor no está preconfigurado para cargar contenedores, grupos de seguridad y los usuarios de Active Directory. Si el enlace a un directorio LDAP diferente, es probable que necesite editar los filtros que se muestran. Haga clic en el vínculo Ayuda para obtener más información sobre los filtros.
16. Haga clic en la ficha atributos. El servidor no está preconfigurado para asignar los atributos de Active Directory.
17. Si el enlace a un directorio LDAP diferente, o bien para cambiar las asignaciones de atributo preconfigurada, haga clic en la edición... botón.
18. En el cuadro de diálogo Editar atributos, modifique la asignación de atributos LDAP para el directorio. Pueden escritos o seleccionados haciendo clic en los nombres de atributos la... botón al lado de cada campo.
19. Haga clic en el vínculo Ayuda para obtener más información sobre los atributos.
20. Haga clic en el botón Aceptar.
21. Haga clic en el icono de configuración de la compañía y seleccione la ficha resolución de nombre de usuario.
22. Si va a conectarse a Active Directory desde un servidor de dominio, debería poder dejar los identificadores de seguridad (SID) usar Windows para que coincidan con el botón de opción de nombres de usuario seleccionado. En caso contrario, seleccione el atributo de identificador único de utilizar LDAP coincidente botón de radio de nombres de usuario. Si se selecciona, el servidor de autenticación multifactor de Azure intentará resolver cada nombre de usuario a un identificador único en el directorio LDAP. Se realizará una búsqueda LDAP en el nombre de usuario atributos definidos en la integración de directorios -> ficha atributos. Cuando un usuario se autentica, se resolverá el nombre de usuario para el identificador único en el directorio LDAP y utilizará el identificador único para que coincida con el usuario en el archivo de datos de Azure con autenticación multifactor. Esto permite comparaciones entre mayúsculas y minúsculas, como nombre de usuario también a larga y corta formatos Esto completa la configuración del servidor de autenticación multifactor de Azure. El servidor está escuchando en los puertos configurados para las solicitudes de acceso LDAP de los clientes configurados y establece a proxy las solicitudes en el directorio LDAP para la autenticación.


## <a name="ldap-client-configuration"></a>Configuración de cliente LDAP

Para configurar al cliente LDAP, siga las directrices:

- Configurar el dispositivo, el servidor o la aplicación para autenticar a través de LDAP al servidor de autenticación multifactor de Azure como si fuese el directorio LDAP. Debe usar la misma configuración que normalmente se utilizan para conectar directamente con el directorio LDAP, excepto el nombre del servidor o la dirección IP que será el servidor de autenticación multifactor de Azure.
- Configurar el tiempo de espera LDAP 30 y 60 segundos para que hay tiempo para validar las credenciales del usuario con el directorio LDAP, realizar la autenticación del segundo factor, reciba su respuesta y, a continuación, responder a la solicitud de acceso LDAP.
- Si usa LDAPS, el dispositivo o el servidor realizar las consultas LDAP debe confiar en el certificado SSL instalado en el servidor de autenticación multifactor de Azure.
