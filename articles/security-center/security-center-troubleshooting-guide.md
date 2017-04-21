<properties
   pageTitle="Guía de solución de problemas de centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a solucionar problemas en el centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Guía de solución de problemas del centro de seguridad de Azure
Esta guía es para profesionales de TI la información, los analistas de seguridad de información y los administradores de nube cuyas organizaciones están usando el centro de seguridad de Azure y necesitan solucionar problemas relacionados con el centro de seguridad.

## <a name="troubleshooting-guide"></a>Guía de solución de problemas
Esta guía explica cómo solucionar problemas relacionados con el centro de seguridad. La mayoría de la solución de problemas de hecho en el centro de seguridad producirá por examina los registros de [Auditoría](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) para el componente error. A través de los registros de auditoría, puede determinar:

- Las operaciones que se han realizado
- ¿Quién inició la operación
- Cuando se ha producido la operación
- El estado de la operación
- Los valores de otras propiedades que pueden ayudarle a investigación la operación

El registro de auditoría contiene todas las operaciones de escritura (SUPERPONER, entrada de eliminación) realizadas en los recursos, pero no incluye las operaciones de lectura (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Solución de problemas de instalación del agente de supervisión de Windows

El agente de supervisión de centro de seguridad se utiliza para realizar la recopilación de datos. Después de habilita la recopilación de datos y el agente está instalado correctamente en el equipo de destino, estos procesos deben estar en ejecución:

- ASMAgentLauncher.exe - agente de supervisión de Azure 
- ASMMonitoringAgent.exe - extensión de supervisión de seguridad de Azure
- ASMSoftwareScanner.exe: el Administrador de análisis de Azure

La extensión de supervisión de seguridad de Azure busca diversos configuración de seguridad relevantes y recopila los registros de seguridad de la máquina virtual. El Administrador de análisis se utilizará como un escáner de revisión.

Si la instalación se realizó correctamente debería ver una entrada similar a la siguiente en los registros de auditoría para la máquina virtual de destino:

![Registros de auditoría](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

También puede leer los registros de agente, que se encuentra en *%systemdrive%\windowsazure\logs* para obtener más información sobre el proceso de instalación (ejemplo: C:\WindowsAzure\Logs).

> [AZURE.NOTE] Si el agente de centro de seguridad de Azure actúe correctamente, debe reiniciar la máquina virtual de destino ya que no hay ningún comando para detener e iniciar al agente.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Solucionar problemas de instalación de agente supervisión en Linux
Cuando la solución de problemas de instalación del agente de máquina virtual de un sistema Linux debe asegurarse de que se ha descargado la extensión a/var/biblioteca/waagent /. Puede ejecutar el comando siguiente para comprobar si lo ha instalado:

`cat /var/log/waagent.log` 

Los otros archivos de registro que puede consultar para solucionar este problema propósito son: 

- /var/log/mdsd.Err
- / var/log/azure /

En un sistema deben aparecer una conexión con el proceso de mdsd en TCP 29130. Este es el registro del sistema comunicarse con el proceso de mdsd. Validar este comportamiento, ejecute el siguiente comando:

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Ponerse en contacto con soporte técnico de Microsoft

Algunos problemas pueden identificarse mediante las directrices siempre en este artículo, otros usuarios que también puede encontrar documentación en el [foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)de centro de seguridad pública. Sin embargo si necesita más de solución de problemas, puede abrir una nueva solicitud de soporte técnico con el Portal de Azure tal como se muestra a continuación: 

![Soporte técnico de Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Vea también

En este documento, ha aprendido cómo configurar directivas de seguridad en el centro de seguridad de Azure. Para obtener más información sobre el centro de seguridad de Azure, consulte lo siguiente:

- [Guía de operaciones y planificación de centro de seguridad de Azure](security-center-planning-and-operations-guide.md) : Obtenga información sobre cómo planificar y comprender las consideraciones de diseño para la adopción de centro de seguridad de Azure.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad
- [Supervisar las soluciones de asociados con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : buscar entradas de blog sobre seguridad de Azure y cumplimiento
