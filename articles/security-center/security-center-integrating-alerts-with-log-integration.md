<properties
   pageTitle="Integración de alertas del centro de seguridad de Azure con integración de Azure log (vista preliminar) | Microsoft Azure"
   description="En este artículo le ayuda a empezar a trabajar con integración de alertas del centro de seguridad con integración de registro de Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Integración de alertas del centro de seguridad con integración de Azure log (vista preliminar)

Muchas operaciones de seguridad y equipos de respuesta a incidencias confían en una solución de administración de eventos (SIEM) e información de seguridad como punto inicial para la clasificación e investigar las alertas de seguridad. Con la integración de Azure registro, los clientes pueden sincronizar alertas del centro de seguridad de Azure, junto con los eventos de seguridad de máquina virtual recopilados diagnósticos de Azure y los registros de auditoría de Azure, con su análisis de registro o solución SIEM en casi en tiempo real.

Integración de Azure registro funciona con HP ArcSight, Splunk, IBM QRadar y otros usuarios.

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se debe integrar?

Azure genera extensiva de registro por cada servicio. Estos registros se clasifican como:

- **Control y administración de registros** que da visibilidad a las operaciones de administrador de recursos de Azure crear, actualizar y eliminar.
- **Registros de datos plano** ofrecer visibilidad en los eventos generados cuando se utiliza un recurso de Azure. Un ejemplo es el registro de eventos de Windows: seguridad y registros de la aplicación en una máquina virtual.

Integración de Azure registro admite actualmente la integración de:

- Registros de Azure VM
- Registros de auditoría de Azure
- Alertas del centro de seguridad de Azure

## <a name="install-azure-log-integration"></a>Instalar la integración de registro de Azure

Descargue la [integración de registro de Azure](https://www.microsoft.com/download/details.aspx?id=53324).

El servicio de integración de registro de Azure recopila los datos de telemetría desde el equipo donde está instalado.  Datos de telemetría recopilados son:

- Excepciones que se producen durante la ejecución de integración de registro de Azure
- Métricas sobre el número de consultas y eventos procesados
- Estadísticas sobre qué Azlog.exe se utilizan opciones de línea de comandos

> [AZURE.NOTE] Puede desactivar la recopilación de datos de telemetría, debe desactivar esta opción.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar alertas del centro de seguridad y registros de auditoría de Azure

1. Abra el símbolo del sistema y **cd** en **c:\Program Files\Microsoft Azure registro integración**.

2. Ejecute el comando **azlog createazureid** para crear un [Principal de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md) en los inquilinos de Azure Active Directory (AD) que hospeda las suscripciones de Azure.

    Se le pedirá para el inicio de sesión de Azure.

    > [AZURE.NOTE] Debe ser el propietario de la suscripción o un compañero administrador de la suscripción.

    Autenticación de Azure se realiza a través de Azure AD.  Creación de una entidad de seguridad de servicio de integración de Azure registro creará la identidad de Azure AD que se concederá acceso de lectura de Azure suscripciones.

3. Ejecutar la **autorizar azlog <SubscriptionID> ** comando asignar acceso de lectura en la suscripción a la entidad de seguridad de servicio que creó en el paso 2. Si no especifica un **SubscriptionID**, a continuación, el capital de servicio se asignará la función Lector para todas las suscripciones a las que tiene acceso.

    > [AZURE.NOTE] Es posible que vea advertencias si ejecuta el comando **autorizar** inmediatamente después del comando de **createazureid** porque hay algunas latencia entre cuando se crea la cuenta de Azure AD y cuando la cuenta está disponible para su uso. Si espera unos 10 segundos después de ejecutar el comando **createazureid** para ejecutar el comando **autorizar** , no verá estas advertencias.

4. Compruebe las siguientes carpetas para confirmar que los archivos de JSON de registro de auditoría están presentes:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Compruebe las siguientes carpetas para confirmar que las alertas del centro de seguridad se encuentran en ellos:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Seleccione la carpeta correspondiente en la canalización de los datos a la instancia SIEM SIEM archivo reenviador el conector estándar. Consulte [las configuraciones de SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) en la configuración de SIEM.

Si tiene preguntas sobre la integración de registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los registros de auditoría de Azure y definiciones de propiedades, vea:

- [Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)
- [Enumere los eventos de administración en una suscripción](https://msdn.microsoft.com/library/azure/dn931934.aspx) - para recuperar los eventos de registro de auditoría.

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.
