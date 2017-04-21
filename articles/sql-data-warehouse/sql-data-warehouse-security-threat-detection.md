<properties
   pageTitle="Introducción a la detección de amenaza de almacén de datos de SQL"
   description="Cómo empezar a trabajar con detección de amenazas"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Introducción a la detección de amenazas

> [AZURE.SELECTOR]
- [Auditoría](sql-data-warehouse-auditing-overview.md)
- [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Información general

Detección de amenazas detecta actividades irregulares de la base de datos que indica los posibles amenazas de seguridad de la base de datos. Detección de amenazas está en vista previa y se admite el almacenamiento de datos de SQL.

Detección de amenazas proporciona una nueva capa de seguridad, lo que permite a los clientes detectar y responder a las posibles amenazas según se van produciendo proporcionando las alertas de seguridad en actividades irregulares. Los usuarios pueden explorar los eventos sospechosos con [Auditoría de almacén de datos de Azure SQL](sql-data-warehouse-auditing-overview.md) para determinar si son el resultado de un intento de acceso, viola o aprovechar los datos en el almacén de datos.
Detección de amenazas simplifica amenazas dirección al almacén de datos sin necesidad de ser un experto en seguridad o administrar sistemas de supervisión de seguridad avanzada.

Por ejemplo, detección de amenazas detecta ciertas actividades irregulares de la base de datos que indica los posibles intentos de inyección de SQL. Inserción de SQL es uno de los problemas comunes de seguridad de aplicación Web de Internet, usado enfrentarse aplicaciones controlados por datos. Los atacantes aprovechan la vulnerabilidad de la aplicación para insertar instrucciones SQL malintencionadas en campos de entrada de la aplicación, incumplimiento o modificar los datos de la base de datos.


## <a name="set-up-threat-detection-for-your-database"></a>Configurar la detección de amenazas para la base de datos

1. Inicie el Portal de Azure en [https://portal.azure.com](https://portal.azure.com).

2. Vaya a la hoja de configuración del almacén de datos de SQL que desea supervisar. En el módulo de configuración, seleccione **auditoría y detección de amenazas**.

    ![Panel de navegación][1]

3. En el módulo de configuración de **auditoría y detección de amenazas** convertir **en** auditoría, que se mostrará la configuración de detección de amenaza.

    ![Panel de navegación][2]

4. Activar la detección de amenazas **activado** .

5. Configurar la lista de mensajes de correo electrónico que va a recibir alertas de seguridad tras la detección de actividades de almacén de datos irregulares.

6. Haga clic en **Guardar** en el módulo de configuración de **auditoría y amenazas detección** para guardar el nuevo o actualizado de auditoría y directivas de detección de amenazas.

    ![Panel de navegación][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explorar las actividades de almacén de datos irregulares tras la detección de un evento sospechoso

1. Recibirá una notificación de correo electrónico tras la detección de actividades irregulares bases de datos. <br/>
El correo electrónico le proporcionará información sobre el evento de seguridad sospechosos de la naturaleza de las actividades irregulares, el nombre de la base de datos, el nombre del servidor y la hora del evento. Además, le proporcionará información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza de la base de datos.<br/>

    ![Panel de navegación][4]

2. En el correo electrónico, haga clic en el vínculo de **Registro de auditoría de SQL Azure** , que iniciará el Portal de clásico de Azure y mostrar los registros de auditoría relevantes alrededor de la hora del evento sospechoso.

    ![Panel de navegación][5]

3. Haga clic en los registros de auditoría para ver más detalles sobre las actividades de base de datos sospechosos como instrucción SQL, IP de cliente y el motivo de error.

    ![Panel de navegación][6]

4. En el módulo de registros de auditoría, haga clic en **Abrir en Excel** para abrir un entorno plantilla para importar y ejecutar un análisis más profundo del registro de auditoría alrededor de la hora del evento sospechoso de excel.<br/>
**Nota:** En Excel 2010 o posterior, Power Query y la configuración de **Combinación rápida** es necesario

    ![Panel de navegación][7]

5. Para configurar la configuración de la **Combinación rápida** : en la ficha de cinta **POWER QUERY** , seleccione **Opciones** para mostrar el cuadro de diálogo Opciones. Seleccione la sección de privacidad y elige la segunda opción - 'Omitir los niveles de privacidad y mejorar el rendimiento potencialmente':

    ![Panel de navegación][8]

6. Para cargar registros de auditoría SQL, asegúrese de que los parámetros de configuración de la pestaña están configurados correctamente y, a continuación, seleccione la cinta de opciones 'Datos' y haga clic en el botón 'Actualizar todo'.

    ![Panel de navegación][9]

7. Los resultados aparecen en la hoja de **Registros de auditoría de SQL** que le permite ejecutar un análisis más profundo de las actividades irregulares que se detectaron y mitigar el impacto de los eventos de seguridad en la aplicación.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
