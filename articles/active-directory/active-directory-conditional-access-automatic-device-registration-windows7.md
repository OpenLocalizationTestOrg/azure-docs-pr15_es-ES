<properties
    pageTitle="# Configurar el registro del dispositivo automática para dispositivos de pertenencia a un dominio de Windows 7 | Microsoft Azure"
    description="Pasos para configurar un dominio de Windows 7 unido dispositivos para registrar automáticamente con Azure AD. y pasos para implementar el paquete de software de registro de dispositivo en su dominio de Windows 7 unido dispositivos con un sistema de distribución de software como el Administrador de configuración de System Center."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Configurar el registro del dispositivo automática para dispositivos de pertenencia a un dominio de Windows 7

Como administrador de TI, puede configurar los dispositivos de pertenencia a un dominio de Windows 7 para registrar automáticamente con Azure AD. Para ello, debe implementar el paquete de software de registro de dispositivo en su dominio de Windows 7 unido dispositivos con un sistema de distribución de software como el Administrador de configuración de System Center. Asegúrese de leer y complete los requisitos previos enumerados en el registro de dispositivo automático con dispositivos Unido de Azure Active Directory para Windows dominio.

>[AZURE.NOTE]
 Para ver instrucciones más recientes acerca de cómo configurar el registro de dispositivo automática, [cómo configurar el registro automático del dominio de Windows unido dispositivos con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Instalar el paquete de software de registro de dispositivo en Windows 7 dominio unido dispositivos

Registro de dispositivo de Windows 7 está disponible como un [paquete MSI descargable](https://connect.microsoft.com/site1164). El paquete debe estar instalado en equipos de Windows 7 que se unen a un dominio de Active Directory. Debe implementar el paquete mediante un sistema de distribución de software como el Administrador de configuración de System Center. El paquete MSI es compatible con las opciones de instalación silencioso estándar mediante /quiet parámetro.
El paquete de software está disponible para su descarga en el [sitio Web de Microsoft Connect](https://connect.microsoft.com/site1164). Aquí puede seleccionar y, a continuación, descargar trabajo unirse a para Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Combinación de área de trabajo con Azure Active Directory
Registro de dispositivo para dispositivos de Windows 7 dominio unido no requiere o no incluyen una interfaz de usuario. Una vez instalado en el equipo, cualquier usuario del dominio que inicia sesión en la máquina estará automáticamente registrado con un objeto de dispositivo en Azure AD. Habrá un objeto de dispositivo en Azure AD para cada usuario registrado del dispositivo físico.

El instalador crea una tarea programada en el sistema que se ejecuta en el contexto del usuario y se activa en el inicio de sesión de usuario. La tarea silenciosamente registra el usuario y dispositivo con Azure AD después de signos en el usuario se completa.
La tarea programada se puede encontrar en la biblioteca del programador de tareas en **Microsoft** > **Unirse al área de trabajo**.
La tarea se ejecutará y registrar todos los usuarios de Active Directory que inicie sesión en el equipo.
La siguiente ilustración muestra el proceso paso a paso para el registro de dispositivo automática.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un usuario (trabajador de la información) inicia sesión en un equipo cliente con Windows 7 con las credenciales de dominio de Active Directory.
1. Se ejecuta la tarea programada unirse al área de trabajo.
1. El usuario se autentica silenciosamente con AD FS mediante la autenticación de Windows integrada.
1. El equipo con Windows 7 está registrado para el usuario en Azure AD.
1. Se crea un objeto de dispositivo y un certificado en Azure AD. El objeto representa el user@device.
1. El certificado de unirse al área de trabajo se almacena en el equipo.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Anular el registro de su dominio de Windows 7 unido dispositivos

Puede elegir Anular el registro de sus dispositivos de Windows 7 dominio unido haciendo lo siguiente: desinstalar el lugar de trabajo unirse al paquete de software desde su dominio de Windows 7 unido dispositivos con un sistema de distribución de software como el Administrador de configuración de System Center.

A continuación, abra un símbolo del sistema en el equipo de Windows 7 y ejecute el comando siguiente para eliminar el registro del dispositivo:

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Este comando se debe ejecutar en el contexto de cada usuario del dominio que ha iniciado sesión en el equipo.
Dispositivos de dominio unido Visor de eventos y errores de Windows 7.

El registro de eventos de Windows en el equipo de Windows 7 mostrará mensajes relacionados a unirse al área de trabajo. Puede encontrar mensajes de eventos correctos e incorrectos unirse al área de trabajo. Puede encontrar el registro de eventos visor en registros de aplicaciones y servicios de en el evento > unirse al área de trabajo de Microsoft.

## <a name="additional-topics"></a>Temas adicionales

- [Información general de registro de Active Directory dispositivo Azure](active-directory-conditional-access-device-registration-overview.md)
- [Registro de dispositivo automática con dispositivos Azure Active Directory for Windows Domain-Joined](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar el registro de dispositivo automáticas para dispositivos de pertenencia a un dominio de Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registro de dispositivo automática con dispositivos de dominio de Azure Active Directory para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
