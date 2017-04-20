<properties
   pageTitle="Azure Active Directory elaboración de informes: Introducción | Microsoft Azure"
   description="Enumera los distintos informes disponibles en los informes de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Introducción a informes de Azure Active Directory

## <a name="what-it-is"></a>¿Qué es

Azure Active Directory (AD Azure) incluye seguridad, actividad y los informes de auditoría para el directorio. Aquí tiene una lista de los informes que se incluyen:

### <a name="security-reports"></a>Informes de seguridad

- Inicios de sesión desde orígenes desconocidos
- Complementos de inicio de sesión después de varios errores
- Inicios de sesión de diversas localidades
- Inicios de sesión de las direcciones IP con la actividad sospechosa
- Actividad de inicio de sesión irregular
- Inicios de sesión desde dispositivos posiblemente infectados
- Usuarios con actividades de inicio de sesión irregulares

### <a name="activity-reports"></a>Informes de actividad

- Uso de la aplicación: resumen
- Uso de la aplicación: detallada
- Panel de la aplicación
- Errores de aprovisionamiento de la cuenta
- Dispositivos de usuario individual
- Actividad de usuario individual.
- Informe de actividad de grupos
- Informe de actividad de registro de restablecer contraseña
- Actividad de restablecimiento de contraseña

### <a name="audit-reports"></a>Informes de auditoría

- Informe de auditoría de directorio

> [AZURE.TIP] Para obtener más documentación de elaboración de informes de Azure AD, consulte [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md).



## <a name="how-it-works"></a>Cómo funciona


### <a name="reporting-pipeline"></a>Canalización de generación de informes

La canalización de informes consta de tres pasos principales. Cada vez que un usuario inicia sesión o se realiza una autenticación, ocurre lo siguiente:

- En primer lugar, el usuario se autentica (correctamente o no) y el resultado se almacena en las bases de datos de servicio de Azure Active Directory.
- A intervalos regulares, inicio de sesión reciente todos los complementos se procesan. En este momento, nuestra seguridad y algoritmos actividades irregulares búsqueda signo reciente todos los complementos de la actividad sospechosa.
- Después del procesamiento, los informes se escrito, caché y servidos en el portal de clásico de Azure.

### <a name="report-generation-times"></a>Tiempos de generación de informes

Debido a la gran volumen de autenticaciones y ins procesados por la plataforma de Azure AD de inicio de sesión, los complementos de inicio de sesión más recientes procesados son, Media, un hora anterior. En algunas ocasiones, puede tardar hasta 8 horas para procesar los inicios de sesión más recientes.

Puede encontrar la sesión más reciente procesado examinando el texto de ayuda en la parte superior de cada informe.

![Texto de ayuda en la parte superior de cada informe](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Para obtener más documentación de elaboración de informes de Azure AD, consulte [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md).



## <a name="getting-started"></a>Introducción


### <a name="sign-into-the-azure-classic-portal"></a>Inicie sesión en el portal de clásico de Azure

En primer lugar, deberá iniciar sesión en el [portal clásica Azure](https://manage.windowsazure.com) como global o administrador de cumplimiento. También debe ser un administrador del servicio de suscripción de Azure o un administrador compañeros o estar usando el "acceso a Azure AD" suscripción Azure.

### <a name="navigate-to-reports"></a>Navegue hasta los informes

Para ver los informes, vaya a la pestaña informes en la parte superior del directorio.

Si esta es la primera vez que ver los informes, deberá aceptar un cuadro de diálogo para poder ver los informes. Esto es para asegurarse de que es aceptable para los administradores de su organización para ver los datos, que pueden considerarse información privada en algunos países.

![Cuadro de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Explorar cada informe

Vaya a cada informe para ver los datos recopilados y los complementos de inicio de sesión procesados. Puede encontrar una [lista de todos los informes aquí](active-directory-reporting-guide.md).

![Todos los informes](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Descargar los informes como CSV

Cada informe puede descargarse como archivo CSV (valores separados por comas). Puede usar estos archivos en Excel, PowerBI o los programas más analizar los datos de análisis de terceros.

Para descargar todos los informes como un CSV, desplácese hasta el informe y haga clic en "Descargar" en la parte inferior.

![Botón Descargar](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Para obtener más documentación de elaboración de informes de Azure AD, consulte [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md).





## <a name="next-steps"></a>Pasos siguientes

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personalizar alertas para el inicio de sesión anómala de actividad

Vaya a la ficha "Configurar" del directorio.

Desplácese hasta la sección "Notificaciones".

Habilitar o deshabilitar la sección "Notificaciones por correo electrónico irregulares inicios de sesión".

![La sección notificaciones](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrar con Azure AD API de informes

Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Integrarse con autenticación multifactor en usuarios

Seleccione un usuario en un informe.

Haga clic en el botón "Habilitar AMF" en la parte inferior de la pantalla.

![El botón de autenticación multifactor en la parte inferior de la pantalla](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Para obtener más documentación de elaboración de informes de Azure AD, consulte [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md).




## <a name="learn-more"></a>Aprende más


### <a name="audit-events"></a>Eventos de auditoría

Obtenga más información sobre qué eventos se van a auditar en el directorio de [Azure Active Directory informar de eventos de auditoría](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Integración de la API

Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md) y la [documentación de referencia de la API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Ponerse en contacto

Correo electrónico [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) de comentarios, ayuda o dudas es posible que tenga.

> [AZURE.TIP] Para obtener más documentación de elaboración de informes de Azure AD, consulte [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md).
