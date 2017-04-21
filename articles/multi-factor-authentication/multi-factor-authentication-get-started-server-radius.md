<properties 
    pageTitle="Servidor de autenticación multifactor de autenticación y RADIUS Azure"
    description="Esta es la página de autenticación multifactor de Azure que le ayudarán a implementar autenticación RADIUS y el servidor de autenticación multifactor de Azure."
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
    ms.date="08/15/2016"
    ms.author="kgremban"/>



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticación multifactor de autenticación y RADIUS Azure

La sección autenticación RADIUS permite habilitar y configurar la autenticación RADIUS para el servidor de autenticación multifactor de Azure. RADIUS es un protocolo estándar para aceptar las solicitudes de autenticación y para procesar las solicitudes. El servidor de autenticación multifactor de Azure actúa como un servidor RADIUS y se inserta entre su cliente RADIUS (por ejemplo, el dispositivo VPN) y el destino de autenticación, que podría ser Active Directory (AD), un directorio LDAP o en otro servidor RADIUS, para agregar la autenticación multifactor de Azure. Para la autenticación multifactor de Azure a función, debe configurar el servidor de autenticación multifactor de Azure para que se puede comunicar con los servidores de cliente y el destino de autenticación. El servidor de autenticación multifactor de Azure acepta solicitudes de un cliente RADIUS, valida credenciales con el destino de autenticación, agrega la autenticación multifactor de Azure y envía una respuesta al cliente RADIUS. La autenticación completo se realizará correctamente solo si el éxito la autenticación principal y la autenticación multifactor de Azure.

>[AZURE.NOTE]
>El servidor AMF solo es compatible con PAP (Protocolo de autenticación de contraseña) y MSCHAPv2 (Protocolo de autenticación de desafío mutuo de Microsoft) RADIUS protocolos cuando actúa como un servidor RADIUS.  Otros protocolos como EAP (Protocolo de autenticación extensible) se pueden usar cuando el servidor de AMF actúa como un proxy RADIUS a otro servidor RADIUS que admita ese protocolo como Microsoft NPS.
></br>
>Al utilizar otros protocolos en esta configuración, unidireccionales tokens SMS y JURAMENTO no funcionará ya que el servidor AMF no se puede iniciar una respuesta de desafío RADIUS correcta con ese protocolo.


![Autenticación RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Configuración de autenticación de RADIUS

Para configurar la autenticación RADIUS, instale al servidor de autenticación multifactor de Azure en un servidor de Windows. Si tiene un entorno de Active Directory, el servidor debe combinarse en el dominio dentro de la red. Utilice el procedimiento siguiente para configurar el servidor de autenticación multifactor de Azure:

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de autenticación RADIUS en el menú de la izquierda.
2. Active la casilla de verificación de radio de habilitar la autenticación.
3. En la ficha clientes cambie los puertos de autenticación y puertos de contabilidad si el servicio de radio de autenticación multifactor de Azure debe enlazar puertos no estándar para escuchar las solicitudes RADIUS de los clientes que se configurarán.
4. Haga clic en Agregar... botón.
5. En el cuadro de diálogo Agregar cliente RADIUS, escriba la dirección IP del dispositivo o servidor que autentica el servidor de autenticación multifactor de Azure, un nombre de la aplicación (opcional) y un secreto compartido. El secreto compartido deberá ser el mismo en el servidor de autenticación multifactor de Azure y el servidor del dispositivo. El nombre de la aplicación aparece en los informes de autenticación multifactor de Azure y se mostrará dentro de los mensajes de autenticación de SMS o aplicación móvil.
6. Active la casilla de coincidencia de usuario de requerir la autenticación multifactor si todos los usuarios han sido o se importarán en el servidor y sujetos a la autenticación multifactor. Si un gran número de usuarios no se han importado en el servidor o estará exento de la autenticación multifactor, deje la casilla desactivada. Ver el archivo de ayuda para obtener información adicional acerca de esta característica.
7. Active la casilla Habilitar la reserva JURAMENTO token si los usuarios utilizarán la autenticación de la aplicación móvil de autenticación multifactor de Azure y desea usar códigos de acceso JURAMENTO como una autenticación de reserva a la notificación de inserción, SMS o llamada de teléfono de fuera de banda.
8. Haga clic en el botón Aceptar.
9. Puede repetir los pasos 4 a 8 para agregar a clientes RADIUS adicionales.
10. Haga clic en la pestaña de destino.
11. Si el servidor de autenticación multifactor de Azure está instalado en un servidor de dominio en un entorno de Active Directory, seleccione el dominio de Windows.
12. Si los usuarios deben ser autenticados con un directorio LDAP, seleccione enlace LDAP. Cuando se utiliza enlace LDAP, debe haga clic en el icono de integración de directorios y modificar la configuración de LDAP en la pestaña Configuración para que el servidor puede enlazar al directorio. Instrucciones para configurar LDAP pueden encontrarse en la Guía de configuración de Proxy LDAP.
13. Si los usuarios se deben autenticar con otro servidor RADIUS, seleccione servidores RADIUS.
14. Configurar el servidor que el servidor le a proxy las solicitudes RADIUS a haciendo clic en Agregar... botón.
15. En el cuadro de diálogo Agregar servidor RADIUS, escriba la dirección IP del servidor RADIUS y un secreto compartido. El secreto compartido deberá ser el mismo en el servidor de autenticación multifactor de Azure y el radio de servidor. Si el servidor RADIUS utiliza puertos diferentes, cambie los puertos de autenticación y contabilidad.
16. Haga clic en el botón Aceptar.
17. Debe agregar el servidor de autenticación multifactor de Azure como un cliente RADIUS en el otro servidor RADIUS para que procesará las solicitudes de acceso que se envía a él desde el servidor de autenticación multifactor de Azure. Debe utilizar el mismo secreto compartido configurado en el servidor de autenticación multifactor de Azure.
18. Puede repetir este paso para agregar servidores RADIUS adicionales y configurar el orden en que el servidor debe llamada con los botones Subir y Bajar. Esto completa la configuración del servidor de autenticación multifactor de Azure. El servidor está escuchando en los puertos configurados para las solicitudes de acceso RADIUS de los clientes configurados.   


## <a name="radius-client-configuration"></a>Configuración del cliente RADIUS

Para configurar al cliente RADIUS, siga las directrices:

- Configurar el dispositivo o el servidor para autenticar a través de RADIUS a dirección IP de Azure multifactor autenticación del servidor, que actúe como el servidor RADIUS.
- Usar el mismo secreto compartido que configuró anteriormente.
- Configurar el tiempo de espera de radio de 30 y 60 segundos para que hay tiempo para validar las credenciales del usuario, realizar la autenticación multifactor, reciba su respuesta y, a continuación, responder a la solicitud de acceso RADIUS.
