<properties
    pageTitle="Azure AD Connect: Los pasos siguientes y cómo administrar Azure AD Connect | Microsoft Azure"
    description="Obtenga información sobre cómo ampliar la configuración predeterminada y tareas operativas para Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Pasos siguientes y cómo administrar Azure AD Connect
Los siguientes son avanzados operativas temas que le permiten personalizar Azure Active Directory conectar para satisfacer las necesidades y los requisitos de su organización.  

## <a name="add-additional-sync-administrators"></a>Agregar administradores de sincronización adicional
De forma predeterminada, solo para el usuario que hizo la instalación y administradores locales podrán administrar el motor de sincronización instalada. Para otras personas puedan acceder y administrar el motor de sincronización, localice el grupo denominado ADSyncAdmins en el servidor local y agregan a este grupo.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Asignar licencias a los usuarios de Azure AD Premium y movilidad de la empresa

Ahora que ya se han sincronizado los usuarios a la nube, debe asignar una licencia por lo que pueden empezar a trabajar con aplicaciones de la nube, como Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para asignar un Azure AD Premium o licencia de conjunto de movilidad de empresa
--------------------------------------------------------------------------------
1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página de Active Directory, haga doble clic en el directorio con los usuarios que desea habilitar.
4. En la parte superior de la página directorio de, seleccione **licencias**.
5. En la página licencias, seleccione Premium de Active Directory o conjunto de movilidad de empresa y, a continuación, haga clic en **asignar**.
6. En el cuadro de diálogo, seleccione los usuarios que desea asignar licencias a y, a continuación, haga clic en el icono de marca de verificación para guardar los cambios.


## <a name="verifying-the-scheduled-synchronization-task"></a>Comprobar la tarea de sincronización programada
Si desea comprobar el estado de una sincronización puede hacerlo activando la casilla de verificación en el portal de Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para comprobar la tarea de sincronización programada
--------------------------------------------------------------------------------
1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página de Active Directory, haga doble clic en el directorio con los usuarios que desea habilitar.
4. En la parte superior de la página de directorio, seleccione la **Integración de directorios**.
5. En la integración con nota de active directory local, la hora de la última sincronización.

<center>![Nube](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Iniciar una tarea de sincronización programada
Si necesita para ejecutar una tarea de sincronización puede hacerlo si vuelve a ejecutar el Asistente de Azure AD Connect.  Debe proporcionar sus credenciales de Azure AD.  En el asistente, seleccione la tarea de **Personalizar las opciones de sincronización** y haga clic en siguiente con el asistente. Al final, compruebe que esté activada la casilla **iniciar el proceso de sincronización en cuanto se completa la configuración inicial** .

<center>![Nube](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para obtener más información sobre la sincronización de Azure AD Connect: programador, vea [Programador conectar de Azure AD](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tareas adicionales disponibles en Azure AD Connect
Después de la instalación inicial de Azure AD Connect, puede siempre iniciar al Asistente para nuevo desde el inicio de Azure AD Connect página o escritorio directo.  Observe que pasar por el Asistente para nuevo proporciona algunas opciones nuevas en el formulario de tareas adicionales.  

La tabla siguiente proporciona un resumen de estas tareas y una breve descripción de cada uno de ellos.

![Unirse a la regla](./media/active-directory-aadconnect-whats-next/addtasks.png)


Tareas adicionales | Descripción
------------- | ------------- |
Ver el escenario seleccionado  |Le permite ver su solución de Azure AD Connect actual.  Esto incluye la configuración general, directorios sincronizados, configuración de sincronización, etcetera.
Personalizar las opciones de sincronización | Le permite cambiar la configuración actual, incluida la adición de bosques de Active Directory adicionales a la configuración o habilitar opciones de sincronización, como un usuario, grupo, dispositivo o contraseña de escritura.
Habilitar el modo de ensayo |  Esto le permite la información de fase que más adelante se sincronizará pero nada se exportarán Azure AD o Active Directory.  Esto le permite obtener una vista previa de las sincronizaciones antes de que se producen.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
