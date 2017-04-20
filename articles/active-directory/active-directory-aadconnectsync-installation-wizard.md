<properties
    pageTitle="Sincronización de Azure AD Connect: ejecutar el Asistente para instalación una segunda vez | Microsoft Azure"
    description="Se explica cómo el Asistente para instalación funciona la segunda vez que lo ejecute."
    keywords="El Asistente para instalación de Azure AD Connect le permite configurar la configuración de mantenimiento de la segunda vez que se ejecute"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronización de Azure AD Connect: ejecutar el Asistente para instalación una segunda vez
La primera vez que ejecute al Asistente para instalación de Azure AD Connect, le guiará a través de cómo configurar la instalación. Si ejecuta al Asistente para instalación de nuevo, ofrece opciones para realizar el mantenimiento.

Puede encontrar al Asistente para instalación en el menú Inicio con el nombre de **Azure AD Connect**.

![Menú Inicio](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Cuando se inicia el Asistente para instalación, verá una página con estas opciones:

![Página con una lista de tareas adicionales](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Si ha instalado ADFS con Azure AD Connect, tiene más opciones. Las opciones adicionales que tiene para ADFS figuran en la [administración de ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Seleccione una de las tareas y haga clic en **siguiente** para continuar.

> [AZURE.IMPORTANT] Mientras tiene abierto el Asistente para instalación, se suspenden todas las operaciones en el motor de sincronización. Asegúrese de que cerrar al Asistente para instalación tan pronto como se haya completado los cambios de configuración.

## <a name="view-current-configuration"></a>Configuración de la vista actual
Esta opción ofrece una vista rápida de las opciones configuradas actualmente.

![Página con una lista de todas las opciones y su estado](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Haga clic en **anterior** para volver atrás. Si selecciona **Salir**, cierre al Asistente para instalación.

## <a name="customize-synchronization-options"></a>Personalizar las opciones de sincronización
Esta opción se utiliza para realizar cambios en la configuración de sincronización. Ver un subconjunto de opciones de la ruta de instalación de configuración personalizada. Consulte esta opción incluso si usa instalación express inicialmente.

- [Agregar más directorios](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Para quitar un directorio, consulte [Eliminar un conector](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Filtrado de OU y cambiar dominio](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Eliminar el filtrado de grupo.
- [Características opcionales de cambio](active-directory-aadconnect-get-started-custom.md#optional-features).

Las demás opciones de la instalación inicial no se puede cambiar y no están disponibles. Estas opciones son:

- Cambie el atributo para userPrincipalName y sourceAnchor.
- Cambiar el método de unión para objetos de bosque diferente.
- Habilitar el filtrado basado en grupo.

## <a name="refresh-directory-schema"></a>Actualizar esquema de directorio
Se usa esta opción si ha cambiado el esquema de una de sus instalaciones bosques de AD DS. Por ejemplo, que tiene instalado Exchange o actualizado a un esquema de Windows Server 2012 con objetos de dispositivo. En este caso, debe indicar a Azure AD Connect para volver a leer el esquema de AD DS y actualizar la memoria caché. Esta acción también vuelve a crear las reglas de sincronización. Si agrega el esquema de Exchange, por ejemplo, las reglas de sincronización para Exchange se agregan a la configuración.

Al seleccionar esta opción, se muestran todos los directorios de la configuración. Puede mantener la configuración predeterminada y actualizar todos los bosques o cancelar la selección de algunos de ellos.

![Página con una lista de todos los directorios en el entorno](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar el modo de ensayo
Esta opción permite habilitar y deshabilitar el modo provisional en el servidor. Encontrará más información acerca de ensayo modo y cómo se utiliza en [las operaciones](active-directory-aadconnectsync-operations.md#staging-mode).

La opción muestra si ensayo está habilitado o deshabilitado:  
![Opción que también se muestra el estado actual del modo provisional](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Para cambiar el estado, seleccione esta opción y seleccione o desactive la casilla de verificación.  
![Opción que también se muestra el estado actual del modo provisional](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Cambiar inicio de sesión de usuario
Esta opción permite cambiar de sincronización de la contraseña para la federación o al revés. No puede cambiar **no se configura**.

Para obtener más información sobre esta opción, consulte [Inicio de sesión de usuario](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el modelo de configuración usado por la sincronización de Azure AD Connect en [Descripción descriptiva aprovisionamiento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
