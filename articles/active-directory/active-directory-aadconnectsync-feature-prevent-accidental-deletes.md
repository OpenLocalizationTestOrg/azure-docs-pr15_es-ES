<properties
   pageTitle="Sincronización de Azure AD Connect: evitar eliminaciones accidentales | Microsoft Azure"
   description="Este tema describe la característica de impedir eliminaciones accidentales (para evitar eliminaciones accidentales) en Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronización de Azure AD Connect: evitar eliminaciones accidentales
Este tema describe la característica de impedir eliminaciones accidentales (para evitar eliminaciones accidentales) en Azure AD Connect.

Cuando la instalación de Azure AD Connect, impide accidentales elimina está habilitado de forma predeterminada y configurado para no permitir una exportación con más de 500 eliminaciones. Esta característica está diseñada para proteger de cambios de configuración accidental y los cambios al directorio local que afecten a muchos usuarios y otros objetos.

Escenarios comunes cuando vea elimina muchos incluyen:

- Cambios en [filtrado](active-directory-aadconnectsync-configure-filtering.md) donde está seleccionada una toda [unidad organizativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) o un [dominio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) .
- Se eliminan todos los objetos de una unidad organizativa.
- Se cambia el nombre de una unidad organizativa para que todos los objetos que considera que están fuera del ámbito de sincronización.

Puede cambiar el valor predeterminado de 500 objetos con PowerShell mediante `Enable-ADSyncExportDeletionThreshold`. Debe configurar este valor para ajustar el tamaño de su organización. Dado que el programador de sincronización ejecuta cada 30 minutos, el valor es el número de eliminaciones visto en 30 minutos.

Si hay demasiados eliminaciones organizados para exportarse a Azure AD, a continuación, se detiene la exportación y recibe un correo electrónico similar a esta:

![Evitar eliminaciones accidentales correo](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hola a todos (contacto técnico). A las (hora) el servicio de sincronización de identidad detectó que el número de eliminaciones había sobrepasado el umbral de eliminación configurado para (nombre de la organización). Un total de (número) de los objetos se ha enviado para su eliminación en esta sincronización identidad ejecutar. Esto cumple o supera el valor de umbral configurado eliminación de objetos (número). Es necesario que proporcione confirmación que deben procesarse estas eliminaciones antes de que se continuará. Vea las eliminaciones accidentales impide para obtener más información sobre el error que aparece en este mensaje de correo electrónico.*

También puede ver el estado `stopped-deletion-threshold-exceeded` cuando busque en el **Administrador de servicios de sincronización** de interfaz de usuario para el perfil de exportación.
![Evitar eliminaciones accidentales UI Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Si se tratase inesperado, investigar y tomar medidas correctivas. Para ver los objetos que se va a eliminar, haga lo siguiente:

1. Iniciar el **servicio de sincronización** en el menú Inicio.
2. Vaya a **conectores**.
3. Seleccione el conector con el tipo de **Azure Active Directory**.
4. En **acciones** a la derecha, seleccione **El espacio del conector de búsqueda**.
5. En el menú emergente en el **ámbito**, seleccione **Desconectado como** y elija una hora en el pasado. Haga clic en **Buscar**. Esta página proporciona una vista de todos los objetos va a eliminar. Haciendo clic en cada elemento, puede obtener información adicional sobre el objeto. También puede hacer clic en **Configuración de la columna** para agregar atributos adicionales para que sea visible en la cuadrícula.

![Espacio de conector de búsqueda](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Si se desean tener todas las eliminaciones, a continuación, haga lo siguiente:

1. Temporalmente deshabilitar esta protección y dejar que las eliminaciones vaya a través de, ejecute el cmdlet de PowerShell: `Disable-ADSyncExportDeletionThreshold`. Proporcionar una cuenta de administrador Global de Azure AD y una contraseña.
![Credenciales](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Con Azure Active Directory Connector todavía seleccionado, seleccione la acción que se **ejecute** y seleccione **Exportar**.
3. Para volver a habilitar la protección, ejecute el cmdlet de PowerShell: `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Pasos siguientes

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
