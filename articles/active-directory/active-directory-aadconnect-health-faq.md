<properties
    pageTitle="Azure AD Connect Health preguntas más frecuentes"
    description="Este artículo responde a preguntas sobre el estado de conexión de Azure AD. Estas preguntas más frecuentes cubre preguntas acerca del uso del servicio, incluyendo el modelo de facturación, capacidades, limitaciones y soporte técnico."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect mantenimiento Preguntas más frecuentes (P+F)

Este artículo responde a preguntas sobre el estado de conexión de Azure AD. Estas preguntas más frecuentes cubre preguntas acerca del uso del servicio, incluyendo el modelo de facturación, capacidades, limitaciones y soporte técnico.

## <a name="general-questions"></a>Preguntas generales



**P: ¿puedo administrar varios directorios de Azure AD. ¿Cómo cambiar a la con Azure Active Directory Premium?**

Puede cambiar entre distintos inquilinos de Azure AD seleccionando actualmente iniciada en nombre de usuario en la esquina superior derecha y elegir la cuenta adecuada. Si la cuenta no se ha incluido aquí, seleccione Cerrar sesión y, a continuación, use las credenciales de administrador global del directorio que tiene Azure Active Premium de directorio habilitados para iniciar sesión.

## <a name="installation-questions"></a>Preguntas sobre la instalación



**P: ¿cuál es el impacto de la instalación de agente de mantenimiento de AD conectar Azure en servidores individuales?**

El efecto de instalar la Microsoft Azure AD conectar estado agentes ADFS, servidores Proxy de la aplicación Web, servidores de Azure AD Connect (sycn), controladores de dominio es mínimo con respecto a la CPU, ancho de banda de red de consumo de memoria y almacenamiento.

Los números siguientes son una aproximación.

- Consumo de CPU: aumento % ~ 1
- Consumo de memoria: hasta un 10% de la memoria total del sistema

>[AZURE.NOTE] En caso de que el agente no puede comunicarse en Azure, el agente almacena los datos localmente, hasta un límite máximo definido. El agente sobrescribe los datos "en caché" de forma "atendidas menos recientemente".

- Almacenamiento de búfer local para conectar agentes salud de Azure AD: ~ 20 MB
- Para servidores de AD FS, se recomienda que incluya un espacio en disco de 1024 MB (1 GB) para el canal de auditoría de AD FS para conectar Health agentes de Azure AD procesar todos los datos de auditoría antes de que se sobrescriba.

**P: ¿tendré que reiniciar los servidores durante la instalación de agentes de mantenimiento de AD conectar Azure?**

No. La instalación de los agentes no requieren reiniciar el servidor. Sin embargo, la instalación de algunos de los pasos previos puede requerir un reinicio del servidor.

Por ejemplo, en Windows Server 2008 R2 la instalación de .net Framework 4.5 requiere reiniciar el servidor.


**P: ¿es Azure AD conectar estado Services funcionan a través de un proxy de paso a través de http?**

Sí.  Para en marcha operaciones, puede configurar el agente de mantenimiento para reenviar solicitudes de http de salida con un servidor HTTP Proxy. Para obtener más información, vea [Configurar Azure AD conectar estado agentes usar HTTP Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Si necesita configurar a un servidor proxy durante el registro de agente, debe modificar la configuración de Proxy de Internet Explorer previamente.
1. Abrir Internet Explorer -> Configuración -> Internet Opciones -> conexiones -> configuración de LAN.
2. Seleccione Usar un servidor Proxy para la LAN.
3. Si tiene puertos diferentes proxy HTTP y HTTPS/Secure, seleccione Opciones avanzadas.

**P: ¿servicios de salud de Azure AD conectar admite autenticación básica al conectarse a los servidores proxy Http?**

No. Un mecanismo para especificar arbitrario de nombre de usuario y contraseña para la autenticación básica no es compatible actualmente.


**P: ¿qué versión de AD DS son compatibles con estado de conexión de AD Azure AD DS?**

Supervisión de AD DS se admite mientras instalado en las siguientes versiones de sistema operativo:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Preguntas de operaciones



**P: ¿debo habilitar la auditoría en los servidores Proxy de AD FS aplicación o los servidores Proxy mi aplicación Web?**

No, no necesita auditoría habilitarse en los servidores Proxy de aplicaciones Web (WAP). Habilitarlo en los servidores de AD FS.


**P: ¿cómo resuelto Azure AD conectar alertas de estado?**

Alertas de estado de conexión de AD Azure resuelto en una condición de éxito. Azure AD conectar agentes de mantenimiento detectar e informar de las condiciones de éxito al servicio de forma periódica. Para las alertas algunas, la supresión está basado en tiempo. En otras palabras, si la misma condición de error no se observa dentro de 72 horas de generación de alertas, la alerta se resuelve automáticamente.




**P: ¿qué puertos del firewall es necesario abrir para que la Azure AD conectar agente de mantenimiento de trabajar?**

Debe tener los puertos TCP/UDP 443 y 5671 abierto para el Azure AD conectar agente de mantenimiento puedan comunicarse con los extremos de servicio de estado de Azure AD.


**P: ¿por qué veo dos servidores con el mismo nombre en el Portal de salud conectar de AD Azure?**

Al quitar un agente de un servidor, el servidor no se eliminan automáticamente desde el Portal de Azure AD conectarse.  Si quita a un agente de un servidor o quita el propio servidor manualmente, debe eliminar manualmente la entrada del servidor desde el portal de estado de conexión de Azure AD. Para obtener más información, vaya a [Eliminar una instancia del servidor o servicio.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Si vuelve a un servidor o creado un servidor de nuevo los detalles de la misma (por ejemplo, el nombre del equipo) y no eliminó el servidor ya registrado desde el portal de estado de conexión de Azure AD, instalado el agente en el servidor nuevo, es posible que vea dos entradas con el mismo nombre.  
En este caso, debe eliminar la entrada que pertenecen al servidor antiguo manualmente. Los datos de este servidor deben estar obsoletos.

## <a name="related-links"></a>Vínculos relacionados

* [Azure AD Connect estado](active-directory-aadconnect-health.md)
* [Azure AD Connect instalación de agente de estado](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de estado de conexión de Azure AD](active-directory-aadconnect-health-operations.md)
* [Conecte mediante Azure AD Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando el estado de conexión de Azure AD para sincronizar](active-directory-aadconnect-health-sync.md)
* [Conecte mediante Azure AD estado con AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect historial de versiones de estado](active-directory-aadconnect-health-version-history.md)
