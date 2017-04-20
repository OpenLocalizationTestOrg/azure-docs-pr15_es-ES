
<properties
    pageTitle="Conecte mediante Azure AD estado con AD FS | Microsoft Azure"
    description="Esta es la página de estado de conexión de Azure AD cómo supervisar su local infraestructura de AD FS."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>Conecte mediante Azure AD estado con AD FS
La siguiente documentación es específica para supervisar la infraestructura de AD FS con su estado de conexión de Azure AD. Para obtener información sobre la supervisión de Azure AD Connect (sincronizar) con su estado de conexión de Azure AD, vea [Usar Azure AD conectar estado de sincronización](active-directory-aadconnect-health-sync.md). Además, para obtener información sobre la supervisión de servicios de dominio de Active Directory con su estado de conexión de Azure AD, consulte [Uso de Azure AD conectar salud con AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertas de AD FS
La sección de alertas de estado de conexión de AD Azure proporciona la lista de alertas activas. Cada alerta incluye información relevante, pasos de solución y vínculos a la documentación relacionada.

Hacer doble clic en una alerta activa o resuelta, para abrir un nuevo módulo con información adicional, los pasos que puede realizar para resolver la alerta y vínculos a documentación pertinente. También puede ver datos históricos sobre las alertas que se han resuelto en el pasado.

![Azure AD Connect estado Portal](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>Análisis de uso de AD FS
Azure AD conectar estado del análisis de uso analiza el tráfico de autenticación de los servidores de federación. Hacer doble clic en el cuadro de análisis de uso, para abrir el módulo de análisis de uso, que muestra varias métricas y agrupaciones.

>[AZURE.NOTE] Para usar el análisis de uso con AD FS, debe asegurarse de que está habilitada la auditoría de AD FS. Para obtener más información, vea [Habilitar auditoría de AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Azure AD Connect estado Portal](./media/active-directory-aadconnect-health/report1.png)

Para seleccionar métricas adicionales, especifique un intervalo de tiempo, o para cambiar la agrupación, haga clic en el gráfico de análisis de uso y seleccione Editar gráfico. A continuación, especifique el intervalo de tiempo, seleccione una métrica diferente y cambiar la agrupación. Puede ver la distribución del tráfico de autenticación basada en diferentes "métrica" y agrupar cada métrica con parámetros relevantes "Agrupar por" se describe en la tabla siguiente:

| Métrica | Agrupar por | La agrupación de ¿qué significa y por qué es útil? |
| ------ | -------- | -------------------------------------------- |
| Solicitudes de total: El número total de solicitudes de procesado por el servicio de federación | Todos los | Muestra el recuento del número total de solicitudes sin agrupar. |
|  | Aplicación | Los grupos del total de solicitudes basada en el usuario de confianza de destino. Esta agrupación es muy útil para comprender qué aplicación está recibiendo cuánto porcentaje del tráfico total. |
|  | Servidor | Los grupos del total de solicitudes basada en el servidor que procesa la solicitud. Esta agrupación es útil para comprender la distribución de carga del tráfico total. |
|  | Combinación de área de trabajo | Los grupos del total de solicitudes en función de si están procedentes de dispositivos de lugar de trabajo Unido (conocidos). Esta agrupación es muy útil para comprender si los recursos se accede con dispositivos que se conoce la infraestructura de identidades. |
|  | Método de autenticación | Los grupos del total de solicitudes según el método de autenticación que se utiliza para la autenticación. Esta agrupación es útil conocer el método de autenticación común que se usa para la autenticación. Siguientes son los métodos de autenticación posibles <ol> <li>Autenticación integrada de Windows (Windows)</li> <li>(Formularios) de la autenticación basada en formularios</li> <li>SSO (solo inicio de sesión)</li> <li>X509 (certificado) de autenticación de certificado</li> <br>Si los servidores de federación reciben la solicitud con una Cookie SSO, dicha solicitud se cuenta como SSO (inicio de sesión único). En estos casos, si la cookie es válida, el usuario no se le pedirá que proporcione las credenciales y obtiene acceso transparente a la aplicación. Este comportamiento es frecuente si tiene varios usuarios de confianza protegidos por los servidores de federación. |
|  | Ubicación de red | Los grupos del total de solicitudes según la ubicación de red del usuario. Puede ser cualquier intranet o extranet. Esta agrupación es útil saber qué porcentaje del tráfico procede de la intranet y extranet. |
| Total de las solicitudes de error: El número total error solicitudes procesadas por el servicio de federación. <br> (Esta métrica solo está disponible en AD FS para Windows Server 2012 R2)| Tipo de error. | Muestra el número de errores en función de los tipos de error predefinida. Esta agrupación es muy útil para comprender los tipos de errores comunes. <ul><li>Nombre de usuario o la contraseña incorrecta: errores debido a la contraseña o el nombre de usuario incorrecta.</li> <li>"Bloqueo extranet": errores debido a las solicitudes recibidas de un usuario que se bloqueó de extranet </li><li> "Caducado contraseña": errores de debido a los usuarios iniciar sesión con una contraseña caducada.</li><li>"Cuenta deshabilitada": errores de debido a los usuarios que inicien con una cuenta deshabilitada.</li><li>"Autenticación de dispositivo": errores de debido a los usuarios que no permite autenticar mediante la autenticación del dispositivo.</li><li>"Autenticación de certificado de usuario": errores de debido a los usuarios que no permite autenticar debido a un certificado no válido.</li><li>"AMF": errores debido a que no permite autenticar mediante la autenticación multifactor del usuario.</li><li>"Otras credenciales": "Autorización de emisión": errores debido a errores de autorización.</li><li>"Delegación de emisión": errores debido a errores de delegación de emisión.</li><li>"La aceptación de token": errores debido a ADFS rechazar el token de un proveedor de identidades de terceros.</li><li>"Protocolo": error debido a errores de protocolo.</li><li>"Desconocido": comodín. Otros errores en la que no se ajusten a las categorías definidas.</li> |
|  | Servidor | Grupos de los errores que se basan en el servidor. Esta agrupación es útil para comprender la distribución de error entre servidores. Distribución desigual podría ser un indicador de un servidor en un estado defectuoso. |
|  | Ubicación de red | Agrupa los errores según la ubicación de red de las solicitudes (intranet vs extranets). Esta agrupación es útil para comprender el tipo de solicitudes que se producen errores. |
|  | Aplicación | Agrupa los errores en función de la aplicación destina (usuario de confianza). Esta agrupación es muy útil para comprender qué aplicación destino está viendo el número de la mayoría de errores. |
| Número de usuarios: promedio de usuarios únicos activos en el sistema | Todos los | Esta métrica proporciona un recuento de promedio de los usuarios mediante el servicio de federación en el intervalo de tiempo seleccionado. Los usuarios no están agrupados. <br>El promedio depende el intervalo de tiempo seleccionado. |
|  | Aplicación | Agrupa el promedio de los usuarios en función de la aplicación destina (usuario de confianza). Esta agrupación es muy útil para comprender cómo muchos usuarios utilizan la aplicación. |


## <a name="performance-monitoring-for-ad-fs"></a>Supervisión de AD FS de rendimiento
Azure AD conectar rendimiento supervisión de estado proporciona información de supervisión métrica. Seleccionar la casilla de supervisión, se abrirá un nuevo módulo con información detallada sobre las mediciones.


![Azure AD Connect estado Portal](./media/active-directory-aadconnect-health/perf1.png)


Seleccionando la opción de filtro en la parte superior del módulo, puede filtrar por el servidor para ver las mediciones de un servidor individual. Para cambiar las mediciones, haga clic en el gráfico de supervisión en el módulo de supervisión y seleccione Editar gráfico. A continuación, en el nuevo módulo que abre, puede seleccionar métricas adicionales en la lista desplegable y especificar un intervalo de tiempo para ver los datos de rendimiento.

## <a name="reports-for-ad-fs"></a>Informes de AD FS
Estado de conexión de Azure AD proporciona informes sobre la actividad y el rendimiento de AD FS. Estos informes ayudan a los administradores Obtenga información sobre las actividades en sus servidores de AD FS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Principales 50 usuarios con inicios de sesión erróneos de nombre de usuario y contraseña

Una de las causas comunes de una solicitud de error de autenticación en un servidor de AD FS es una solicitud de credenciales no válidas, es decir, un nombre de usuario incorrecta o la contraseña. Normalmente se pasa a los usuarios debido a contraseñas complejas, contraseñas olvidadas o errores tipográficos.

Pero hay otras razones por las que pueden dar lugar a un número inesperado de solicitudes controlando los servidores de AD FS, como por ejemplo: una aplicación que expiren en caché las credenciales de usuario y las credenciales o un usuario malintencionado intenta iniciar sesión en una cuenta con una serie de contraseñas conocidas. Estos dos ejemplos son motivos válidos que podrían provocar un aumento en las solicitudes.

Azure AD conectar estado para ADFS proporciona un informe sobre los principales 50 usuarios con los intentos de inicio de sesión debido a la contraseña o el nombre de usuario no válido. Este informe se logra mediante el procesamiento de los eventos de auditoría generados por todos los servidores de AD FS de los conjuntos de servidores

![Azure AD Connect estado Portal](./media/active-directory-aadconnect-health-adfs/report1a.png)

En este informe tiene fácil acceso a las partes de la información siguientes:

- Número total de solicitudes de errores con el nombre de usuario y la contraseña incorrecta en los últimos 30 días
- Nº promedio de usuarios que no se pudo con un inicio de sesión de nombre de usuario y la contraseña incorrecta al día.


Haga clic en este elemento le lleva al módulo de informe principal que proporciona información adicional. Este módulo incluye un gráfico con información de tendencia para ayudar a establecer una línea base acerca de las solicitudes con un nombre de usuario o contraseña. Además, proporciona la lista de los usuarios principales 50 con el número de intentos.

El gráfico proporciona la información siguiente:

- El número total de inicios de sesión erróneos debido a una nombre de usuario y la contraseña incorrecta en una base por día.
- El número total de usuarios únicos que no se pudo inicios de sesión en una base por día.
- Dirección IP del cliente de para última solicitud

![Azure AD Connect estado Portal](./media/active-directory-aadconnect-health-adfs/report3a.png)

El informe proporciona la información siguiente:

| Elemento de informe | Descripción
| ------ | -------- |
|Identificador de usuario| Muestra el identificador de usuario que se ha usado. Este valor es lo que escribió el usuario, que en algunos casos es el usuario incorrecto identificador que se usa.|
|Intentos| Muestra el número total de intentos de ese Id. La tabla se ordena con el mayor número de intentos en orden descendente.|
|Último error| Muestra la marca de tiempo cuando se produjo el último error.
|Último error IP | Muestra la dirección IP del cliente desde la última solicitud incorrecta.|



>[AZURE.NOTE] Este informe se actualiza automáticamente después de cada dos horas con la nueva información recopilada en ese momento. Por tanto, los intentos de inicio de sesión en las últimas dos horas no pueden incluir en el informe.



## <a name="related-links"></a>Vínculos relacionados

* [Azure AD Connect estado](active-directory-aadconnect-health.md)
* [Azure AD Connect instalación de agente de estado](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operaciones de mantenimiento](active-directory-aadconnect-health-operations.md)
* [Usando el estado de conexión de Azure AD para sincronizar](active-directory-aadconnect-health-sync.md)
* [Conecte mediante Azure AD estado con AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect mantenimiento Preguntas más frecuentes](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historial de versiones de estado](active-directory-aadconnect-health-version-history.md)
