<properties
    pageTitle="Informes de Azure autenticación multifactor"
    description="Describe cómo usar la característica de autenticación multifactor de Azure - informes."
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
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Informes en Azure autenticación multifactor

La autenticación multifactor Azure proporciona varios informes que pueden ser usados por usted y su organización. Estos informes se pueden acceder a través del Portal de administración de autenticación multifactor, que debe tener una licencia de Azure AMF proveedor, AMF Azure, Azure AD Premium o un conjunto de aplicaciones de movilidad de empresa. A continuación se muestra una lista de los informes disponibles.

Puede acceder a informes a través del portal de administración de Azure.

Nombre| Descripción
:------------- | :------------- |
Uso | Los informes de uso muestran información sobre el uso general, usuario resumen y detalles del usuario.
Estado del servidor|Este informe muestra el estado de los servidores de autenticación multifactor asociada a su cuenta.
Historial de usuario bloqueados|Estos informes muestran el historial de solicitudes para bloquear o desbloquear usuarios.
Historial de usuario omitida|Muestra el historial de solicitudes para omitir la autenticación multifactor para el número de teléfono de un usuario.
Alerta de fraude|Muestra un historial de las alertas de fraude enviada durante el intervalo de fechas especificada.
En la cola|Listas de informes en cola para el procesamiento y su estado. Cuando haya terminado el informe, se proporciona un vínculo para descargar o ver el informe.

## <a name="to-view-reports"></a>Ver informes

1.  Inicie sesión en http://azure.microsoft.com
2.  En la parte izquierda, seleccione Active Directory.
3.  Seleccione una de las siguientes opciones:
    - **Opción 1**: haga clic en la ficha proveedores de autenticación multifactor. Seleccione su proveedor de AMF y haga clic en el botón administrar en la parte inferior.
    - **Opción 2**: seleccione el directorio y haga clic en la ficha configurar. En la sección autenticación multifactor, seleccione Administrar la configuración de servicio. En la parte inferior de la página Configuración del servicio MFA, haga clic en el vínculo Ir al portal.
4.  En el Portal de administración de autenticación de varios factores de Azure, verá la vista de la sección de un informe en el panel de navegación izquierdo. Desde aquí puede seleccionar los informes que se indica más arriba.

<center>![Nube](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionales**

* [Para los usuarios](./end-user/multi-factor-authentication-end-user.md)
* [Azure autenticación multifactor en MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
