<properties
   pageTitle="Introducción a la integración de registro de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo instalar el servicio de integración de registro de Azure e integrar registros de almacenamiento de Azure, registros de auditoría de Azure y alertas del centro de seguridad de Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Introducción a la integración de Azure log (vista preliminar)

Integración de registro Azure le permite integrar registros sin formato de los recursos de Azure en los sistemas de gestión de eventos (SIEM) e información de seguridad local. Esta integración proporciona un panel unificado de todos los activos, local o en la nube para que puedan agregar, relacionar, analizar y alertas para los eventos de seguridad asociados con las aplicaciones.

Este tutorial describe cómo instalar la integración de Azure registro e integrar registros de almacenamiento de Azure, registros de auditoría de Azure y alertas del centro de seguridad de Azure. Tiempo estimado para realizar este tutorial es una hora.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe tener el siguiente:

- Un equipo (local o en la nube) para instalar el servicio de integración de registro de Azure. Este equipo debe ejecutar un sistema operativo Windows de 64 bits con .net 4.5.1 instalado. Este equipo se denomina la **Integración de Azlog**.
- Suscripción de Azure. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
- Diagnóstico de Azure habilitado para sus máquinas virtuales Azure (VM). Para habilitar diagnósticos de servicios en la nube, consulte [Habilitar diagnósticos de Azure en servicios de nube de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para habilitar diagnósticos para una máquina virtual de Azure con Windows, vea [Usar PowerShell para habilitar diagnóstico de Azure en Máquina Virtual ejecutando Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Conectividad de la integración de Azlog con el almacenamiento de Azure y para autenticar y autorizar a Azure suscripción.
- Para los registros de máquina virtual de Azure, debe estar instalado el agente SIEM (por ejemplo, Splunk Universal reenviador, agente de selector de eventos de Windows de ArcSight HP o IBM QRadar WinCollect) en la integración de Azlog.

## <a name="deployment-considerations"></a>Consideraciones de implementación

Puede ejecutar varias instancias de la integración de Azlog si es alto volumen de eventos. Equilibrio de carga de cuentas de almacenamiento de Azure diagnósticos para Windows *(TORUNDA)* y el número de suscripciones para proporcionar a las instancias debe basarse en su capacidad.

En un equipo de 8 procesadores (principal), una instancia de integración de Azlog puede procesar eventos alrededor de 24 millones por día (~1M/hour).

En un equipo de procesador 4 (principal), una instancia de integración de Azlog puede procesar alrededor de 1,5 millones de eventos por día (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Instalar la integración de registro de Azure

Descargue la [integración de registro de Azure](https://www.microsoft.com/download/details.aspx?id=53324).

El servicio de integración de registro de Azure recopila los datos de telemetría desde el equipo donde está instalado.  Datos de telemetría recopilados son:

- Excepciones que se producen durante la ejecución de integración de registro de Azure
- Métricas sobre el número de consultas y eventos procesados
- Estadísticas sobre qué Azlog.exe se utilizan opciones de línea de comandos

> [AZURE.NOTE] Puede desactivar la recopilación de datos de telemetría, debe desactivar esta opción.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Integrar registros de Azure VM de sus cuentas de almacenamiento de información de diagnóstico de Azure

1. Compruebe los requisitos previos enumerados anteriormente para asegurarse de que su cuenta de almacenamiento TORUNDA es recopilar registros antes de continuar la integración del registro de Azure. No lleve a cabo los siguientes pasos si su cuenta de almacenamiento de TORUNDA no es recopilar registros.

2. Abra el símbolo del sistema y **cd** en **c:\Program Files\Microsoft Azure registro integración**.

3. Ejecute el comando

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Reemplace StorageAccountName con el nombre de la cuenta de almacenamiento de Azure configurado para recibir eventos de diagnósticos de la máquina virtual.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Si desea que el identificador de suscripción para mostrarse en el evento XML, anexar el identificador de suscripción para el nombre descriptivo:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Espere 30 y 60 minutos (podría tardar hasta una hora), y ver los eventos que se extraen de la cuenta de almacenamiento. Para ver, abra **Visor de sucesos > registros de Windows > reenviar eventos** en la integración de Azlog.

5. Asegúrese de que el conector SIEM estándar instalado en el equipo está configurado para elegir eventos de la carpeta de **Eventos reenviados** y les de canalización a su instancia SIEM. Revise la configuración de SIEM específica para configurar y ver los registros de integración.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>¿Qué sucede si datos no se muestran en la carpeta de eventos reenviados?

Si después de una hora datos no se muestran en la carpeta **Eventos reenviados** , a continuación:

1. Compruebe el equipo y confirme que puede tener acceso a Azure. Para probar la conectividad, intente abrir el [portal de Azure](http://portal.azure.com) desde el explorador.

2. Asegúrese de que la cuenta de usuario **azlog** tiene permiso de escritura en la carpeta **users\azlog**.

3. Asegúrese de que la cuenta de almacenamiento agregada en el comando **Agregar origen azlog** aparece cuando se ejecuta el comando **azlog lista de origen**.
4. Vaya a **Visor de sucesos > registros de Windows > aplicación** ver si hay algún error informa de la integración de registro de Azure.

Si todavía no ve los eventos, a continuación:

1. Descargar [Microsoft Azure almacenamiento Explorer](http://storageexplorer.com/).

2. Conectarse a la cuenta de almacenamiento agregada en el comando **Agregar origen de azlog**.

3. En el Explorador de almacenamiento de Microsoft Azure, vaya a **WADWindowsEventLogsTable** para ver si hay datos de tabla. Si no es así, diagnósticos de la máquina virtual no está configurado correctamente.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar registros de auditoría de Azure y alertas del centro de seguridad

1. Abra el símbolo del sistema y **cd** en **c:\Program Files\Microsoft Azure registro integración**.

2. Ejecute el comando

        azlog createazureid

      Este comando solicita el inicio de sesión de Azure. El comando crea un [Principal de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md) en los inquilinos de Azure AD que hospeda las suscripciones de Azure en la que el usuario conectado es un administrador, un administrador de colaboración o un propietario. El comando se producirá un error si el usuario ha iniciado sesión es solo un usuario de invitado en el inquilino AD de Azure. Autenticación de Azure se realiza a través de Azure Active Directory (AD).  Creación de una entidad de seguridad de servicios de integración de Azlog crea la identidad de Azure AD que se concederá acceso de lectura de Azure suscripciones.

3. Ejecute el comando

        azlog authorize <SubscriptionID>

      Esto asigna acceso de lectura en la suscripción al servicio principal que creó en el paso 2. Si no especifica un SubscriptionID, a continuación, intenta asignar el rol de lector principal de servicio para todas las suscripciones a los que tiene acceso.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Es posible que vea advertencias si ejecuta el comando **autorizar** inmediatamente después del comando de **createazureid** . Hay algunos latencia entre cuando se crea la cuenta de Azure AD y cuando la cuenta está disponible para su uso. Si espera unos 10 segundos después de ejecutar el comando **createazureid** para ejecutar el comando **autorizar** , no verá estas advertencias.

4. Compruebe las siguientes carpetas para confirmar que los archivos de JSON de registro de auditoría están presentes:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Compruebe las siguientes carpetas para confirmar que las alertas del centro de seguridad se encuentran en ellos:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Seleccione la carpeta correspondiente en la canalización de los datos a la instancia SIEM SIEM archivo reenviador el conector estándar. Puede que tenga algunas asignaciones de campo en función del producto SIEM que está utilizando.

Si tiene preguntas sobre la integración de registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo instalar la integración de Azure registro e integrar registros de almacenamiento de Azure. Para obtener más información, consulte lo siguiente:

- [Integración con Microsoft Azure registro para los registros de Azure (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) : Centro de descarga para información detallada, requisitos del sistema y las instrucciones de instalación en la integración de registro de Azure.
- [Introducción a la integración de Azure registro](security-azure-log-integration-overview.md) : este documento se presenta integración de registro de Azure, sus capacidades claves y cómo funciona.
- [Pasos de configuración de partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog muestra cómo configurar la integración de Azure registro para trabajar con las soluciones de asociados Splunk, ArcSight HP y IBM QRadar.
- [Registro de azure integración con frecuencia preguntas más frecuentes (P+F)](security-azure-log-integration-faq.md) - este preguntas más frecuentes sobre responde a preguntas sobre la integración de registro de Azure.
- [Alertas del centro de seguridad de la integración con Azure iniciar integración](../security-center/security-center-integrating-alerts-with-log-integration.md) : este documento se muestra cómo sincronizar alertas del centro de seguridad, junto con los eventos de seguridad de máquina virtual recopilados diagnósticos de Azure y Azure registros de auditoría, con la solución SIEM o el análisis de registro.
- [Nuevas características para los registros de auditoría de Azure y diagnóstico de Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : esta entrada de blog presenta a los registros de auditoría de Azure y otras características que ayudan a obtienen recomendaciones sobre las operaciones de los recursos de Azure.
