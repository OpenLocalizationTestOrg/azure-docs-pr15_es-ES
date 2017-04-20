<properties
    pageTitle="Configurar el registro de dispositivo automáticas para dispositivos de pertenencia a un dominio de Windows 8.1 | Microsoft Azure"
    description=" Pasos para configurar la directiva de grupo para Windows 8.1 dominio dispositivos registrar automáticamente con Azure AD. "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Configurar el registro de dispositivo automáticas para dispositivos de pertenencia a un dominio de Windows 8.1

Puede utilizar una directiva de grupo de Active Directory para configurar los dispositivos de pertenencia a un dominio de Windows 8.1 para registrar automáticamente con Azure AD. Para configurar la directiva de grupo, debe tener al menos un dominio combinados Windows Server 2012 R2 o Windows 8.1 equipo con la característica de administración de directivas de grupo instalada. Una vez habilitada esta directiva de grupo para su dominio, cualquier usuario del dominio que inicia sesión en la máquina estará automáticamente registrado con un objeto de dispositivo en Azure AD. Habrá un objeto de dispositivo en Azure AD para cada usuario registrado del dispositivo físico. Asegúrese de leer y complete los requisitos previos desde el registro del dispositivo automática con dispositivos Azure Active Directory for Windows Domain-Joined.

>[AZURE.NOTE]
 Para ver instrucciones más recientes acerca de cómo configurar el registro de dispositivo automática, [cómo configurar el registro automático del dominio de Windows unido dispositivos con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Configurar la directiva de grupo para los dispositivos de la pertenencia a un dominio de Windows 8.1

1. Abra el administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2. En administración de directivas de grupo, desplácese hasta el nodo del dominio que corresponde al dominio en el que desea habilitar **Unirse a automático de área de trabajo**.
3. Haga clic en **Los objetos de directiva de grupo** y seleccione **nuevo**. Asigne un nombre, por ejemplo, **Unirse a automático de área de trabajo**a su objeto de directiva de grupo. Haga clic en **Aceptar**.
4. Haga clic en el nuevo objeto de directiva de grupo y, a continuación, seleccione **Editar**.
5. Vaya a **Configuración del equipo** > **directivas** > **plantillas administrativas** > **componentes de Windows** > **unirse al área de trabajo**.
6. Haga automáticamente los equipos de cliente de combinación de lugar de trabajo y, a continuación, seleccione **Editar**.
7. Seleccione la imagen del botón de radio y, a continuación, haga clic en aplicar. Haga clic en **Aceptar**.
8. Ahora puede vincular el objeto de directiva de grupo a una ubicación de su elección. Para habilitar esta directiva para todos los dispositivos de Windows 8.1 pertenencia a un dominio en su organización, vincular la directiva de grupo para el dominio.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Anular el registro de su dominio de Windows 8.1 unido dispositivos

Puede elegir Anular el registro de sus dispositivos de Windows 8.1 dominio unido haciendo lo siguiente: modificar la configuración de directiva de grupo de trabajo unirse creada en la sección anterior. Establecer la automáticamente trabajo unirse equipos directiva de cliente en deshabilitado. Esto impedirá que nuevos dispositivos unirse automáticamente el área de trabajo.

Anular el registro de los equipos de Windows 8.1 dominio unido existentes siguiendo uno de las dos opciones siguientes:

* Opción 1: **anular una Windows 8.1 dominio unido dispositivo con la configuración de PC**
  1. En el dispositivo Windows 8.1, vaya a **Configuración de PC** > **red** > **lugar de trabajo**
  2. Seleccione **dejar**.
Este proceso se debe repetir para cada usuario del dominio que ha iniciado sesión en el equipo y ha sido automáticamente Unido de área de trabajo.

* Opción 2: Anular el registro de un dispositivo combinado de dominio de Windows 8.1 con una secuencia de comandos
    1. Abra un símbolo del sistema en el equipo de Windows 8.1 y ejecute el siguiente comando:` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Este comando se debe ejecutar en el contexto de cada usuario del dominio que ha iniciado sesión en el equipo.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Visor de eventos y errores para Windows 8.1 dominio unido dispositivos

El registro de eventos de Windows en un equipo Windows 8.1 muestra mensajes relacionados con el registro de dispositivo. Encontrará los mensajes para los eventos de éxito y. 

El registro de eventos puede encontrarse en el evento Visor en aplicaciones y servicios **registros** > **Microsoft** > **Windows > área de trabajo unirse a**.

##<a name="additional-details"></a>Obtener más información

La directiva de grupo permite una tarea programada en el sistema que se ejecuta en el contexto del usuario y se activa en el inicio de sesión de usuario. La tarea registrará silenciosamente el dispositivo y el usuario con Azure AD después de iniciar sesión completa. Puede encontrar la tarea programada en dispositivos de Windows 8.1 en la biblioteca del programador de tareas en **Microsoft** > **Windows** > **Unirse al área de trabajo**. La tarea se ejecutará y registrar todos los usuarios de Active Directory en ese inicio de sesión. 

## <a name="additional-topics"></a>Temas adicionales
- [Información general de registro de Active Directory dispositivo Azure](active-directory-conditional-access-device-registration-overview.md)
- [Registro de dispositivo automática con dispositivos de dominio de Azure Active Directory para Windows 10](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar el registro del dispositivo automática para dispositivos de pertenencia a un dominio de Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

