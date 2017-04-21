<properties 
    pageTitle="Remoto puerta de enlace de escritorio y servidor de autenticación multifactor de Azure con RADIUS"
    description="Esta es la página de autenticación multifactor de Azure que le ayudarán a en la implementación de la puerta de enlace de escritorio remoto (RD) y servidor de autenticación multifactor de Azure con RADIUS."
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

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Remoto puerta de enlace de escritorio y servidor de autenticación multifactor de Azure con RADIUS

En muchos casos, la puerta de enlace de escritorio remoto usa NPS local para autenticar a los usuarios. Este documento describe cómo enrutar solicitud RADIUS fuera desde el Gateway de escritorio remoto (a través de NPS local) al servidor de autenticación multifactor.

El servidor de autenticación multifactor debe instalarse en un servidor distinto, que, a continuación, proxy la solicitud RADIUS NPS en el servidor de puerta de enlace de escritorio remoto. Una vez que NPS valida el nombre de usuario y la contraseña, devolverá una respuesta al servidor de autenticación multifactor que realiza el segundo factor de autenticación antes de devolver un resultado de la puerta de enlace.





## <a name="configure-the-rd-gateway"></a>Configurar la puerta de enlace de escritorio remoto

La puerta de enlace de escritorio remoto debe estar configurado para enviar autenticación RADIUS a un servidor de autenticación multifactor de Azure. Una vez que se ha instalado la puerta de enlace de escritorio remoto, configurado y trabajo, vaya a las propiedades de la puerta de enlace de escritorio remoto. Vaya a la ficha almacén de capital RD y cámbielo a usar un servidor Central que ejecuta NPS en lugar de servidor Local con NPS. Agregar uno o más servidores de autenticación multifactor de Azure como servidores RADIUS y especifique un secreto compartido por cada servidor.





## <a name="configure-nps"></a>Configurar NPS

La puerta de enlace de escritorio remoto usa NPS para enviar la solicitud RADIUS autenticación multifactor de Azure. Tiempo de espera se debe cambiar para evitar que la puerta de enlace de escritorio remoto de tiempo de espera antes de que finalice la autenticación multifactor. Use el procedimiento siguiente para configurar NPS.

1. En NPS, expanda el menú clientes RADIUS y servidor en la columna izquierda y haga clic en grupos de servidores RADIUS remotos. Vaya a las propiedades del grupo de servidores de puerta de enlace de TS. Modificar los servidores de radio de muestra y vaya a la pestaña de equilibrio de carga. Cambiar la "eliminado de la cantidad de segundos sin respuesta antes de solicitud se considera" y "Número de segundos entre solicitudes cuando el servidor se identifica como no disponible" a 30 y 60 segundos. Haga clic en la pestaña autenticación o cuenta y asegurarse de que los puertos RADIUS especificados coinciden con los puertos que el servidor de autenticación multifactor va a escuchar.
2. NPS también debe estar configurado para recibir autenticaciones RADIUS desde el servidor de autenticación multifactor de Azure. En el menú de la izquierda, haga clic en clientes RADIUS. Agregar el servidor de autenticación multifactor de Azure como un cliente RADIUS. Elija un nombre descriptivo y especifique un secreto compartido.
3. Expanda la sección directivas en el panel de navegación izquierdo y haga clic en directivas de solicitud de conexión. Debe contener una directiva de solicitud de conexión denominado directiva de autorización de puerta de enlace de TS que se creó durante la configuración de puerta de enlace de escritorio remoto. Esta directiva reenvíe solicitudes RADIUS al servidor de autenticación multifactor.
4. Copie esta directiva para crear uno nuevo. En la nueva directiva, agregue una condición que coincida con el nombre descriptivo del cliente con el nombre descriptivo establecido en el paso 2 para el cliente de servidor de autenticación de varios factores de Azure RADIUS. Cambiar el proveedor de autenticación en equipo Local. Esta directiva garantiza que cuando se recibe una solicitud RADIUS desde el servidor de autenticación multifactor de Azure, la autenticación se produce localmente en lugar de enviar una solicitud de radio al servidor de autenticación multifactor de Azure que daría como resultado una condición de bucle. Para evitar la condición de bucle, esta nueva directiva debe estar ordenada por encima de la directiva original que reenvíe al servidor de autenticación multifactor.

## <a name="configure-azure-multi-factor-authentication"></a>Configurar la autenticación multifactor Azure


--------------------------------------------------------------------------------



El servidor de autenticación multifactor de Azure está configurado como proxy RADIUS entre la puerta de enlace de escritorio remoto y NPS.  Debe instalarse en un servidor de dominio diferente desde el servidor de puerta de enlace de escritorio remoto. Utilice el procedimiento siguiente para configurar el servidor de autenticación multifactor de Azure.

1. Abra el servidor de autenticación multifactor de Azure y haga clic en el icono de autenticación RADIUS. Active la casilla de verificación de radio de habilitar la autenticación.
2. En la ficha clientes, asegúrese de que los puertos coincide con lo que se configura en NPS y haga clic en Agregar... botón. Agregue la dirección IP del servidor puerta de enlace de escritorio remoto, el nombre de la aplicación (opcional) y un secreto compartido. El secreto compartido deberá ser el mismo en el servidor de autenticación multifactor de Azure y la puerta de enlace de escritorio remoto.
3. Haga clic en la pestaña de destino y elija el botón de radio de servidores RADIUS.
4. Haga clic en Agregar... botón. Escriba la dirección IP, secreto compartido y puertos del servidor NPS. A menos que se usa un NPS central, el cliente RADIUS y el destino de radio será el mismo. El secreto compartido debe coincidir con el programa de instalación en la sección de cliente RADIUS del servidor NPS.

![Autenticación RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
