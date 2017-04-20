<properties
   pageTitle="Azure AD Connect: Actualizar desde una versión anterior | Microsoft Azure"
   description="Explica los diferentes métodos para actualizar a la versión más reciente de Azure Active Directory conectarse, incluidos actualización en contexto y migración de recorrido."
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
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Actualización desde una versión anterior a la última
Este tema describe los diferentes métodos que puede usar para actualizar la instalación de Azure AD Connect a la versión más reciente. Le recomendamos que mantenga usted mismo actual con las versiones de Azure AD Connect. Los pasos descritos en [Girar migración](#swing-migration) también se usan cuando realiza una configuración importante cambiar.

Si desea actualizar de sincronización de directorios, consulte la [actualización de la herramienta de sincronización de Azure AD (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Existen algunas estrategias diferentes para actualizar Azure AD Connect.

Método | Descripción
--- | ---
[Actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) | Los clientes con una instalación express, éste es el método más fácil.
[Actualización en contexto](#in-place-upgrade) | Si tiene un solo servidor, actualizar la instalación en lugar de en el mismo servidor.
[Migración de recorrido](#swing-migration) | Con dos servidores, puede preparar uno de los servidores con la configuración o la nueva versión y cambiar active server cuando esté listo.

Para los permisos necesarios, consulte [permisos necesarios para la actualización](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Actualización en contexto
Una actualización en contexto funciona para moverse de sincronización de Azure AD o Azure AD Connect. No funcionará para la sincronización de directorios o para una solución con FIM + conector de Azure AD.

Este método es preferido cuando tiene un único servidor y menos de aproximadamente 100000 objetos. Si hay cambios a las reglas de cuadro de sincronización, una importación completa y sincronización completa se producen después de la actualización. Esto garantiza que la nueva configuración se aplica a todos los objetos existentes en el sistema. Esto puede tardar unas horas en función del número de objetos en el ámbito del motor de sincronización. El programador de sincronización delta normal, de forma predeterminada, cada 30 minutos, se suspende pero continúa la sincronización de contraseñas. Puede para realizar la actualización en contexto durante un fin de semana. Si no hay ningún cambio en la configuración de fábrica con la nueva versión de Azure AD Connect, se iniciará una importación delta normal o sincronización.  
![Actualización en contexto](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si ha realizado cambios en las reglas de sincronización, estas se establecerá volver a la configuración predeterminada en la actualización. Para asegurarse de que se mantiene la configuración entre las actualizaciones, asegúrese de que los cambios realizados como se describe en [los procedimientos recomendados para cambiar la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migración de recorrido
Si tiene una implementación compleja o muy muchos objetos, podría ser práctico hacer una actualización en contexto en el sistema activo. Esto podría para algunos clientes tardar varios días y durante este periodo no procesará cambios delta. Este método también se utiliza cuando va a realizar cambios importantes en la configuración y desea probarlo antes de que estos se envían a la nube.

El método recomendado para estos escenarios es usar una migración de recorrido. Debe (mínimo) dos servidores, un activo y un servidor de ensayo. El servidor activa (líneas continuas azules en la imagen siguiente) es responsable de la carga de producción activa. El servidor de ensayo (líneas discontinuas púrpuras en la siguiente imagen) está preparado con la nueva versión o configuración y cuando totalmente esté listo, este servidor está activo. El servidor de active anterior, ahora con la versión anterior o configuración instalado, se realiza el servidor de ensayo y actualizado.

Los dos servidores pueden usar diferentes versiones. Por ejemplo, el servidor activo que piensa retirar puede usar la sincronización de Azure AD y el servidor de ensayo nuevo puede usar Azure AD Connect. Si usa migración de recorrido para desarrollar una nueva configuración es una buena idea tener las mismas versiones en los dos servidores.  
![Servidor de ensayo](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: Se ha sido en cuenta que algunos clientes prefieren tener tres o cuatro servidores para este escenario. Cuando se actualiza el servidor de ensayo, no tiene un servidor de copia de seguridad en caso de una [recuperación](active-directory-aadconnectsync-operations.md#disaster-recovery). Con tres o cuatro servidores, puede estar preparado un conjunto de servidores primario/en espera con la nueva versión, cómo garantizar que siempre hay un servidor de ensayo preparado para tomar el control.

Estos pasos también funciona para mover de sincronización de Azure AD o una solución con FIM + conector de Azure AD. Estos pasos no funcionan para la sincronización de directorios, pero el mismo método de migración (también denominado implementación paralela) de recorrido con pasos para la sincronización de directorios que puede encontrarse en [Actualización de Azure Active Directory (DirSync) de sincronización](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Pasos de la migración de recorrido

1. Si utilizar Azure AD Connect para los servidores y planear solo hacer un cambio de configuración, asegúrese de que el servidor activo y servidor provisional ambos utilizan la misma versión. Que facilita la compare diferencias más adelante. Si está actualizando desde Azure AD Sync, estos servidores tienen diferentes versiones. Si está actualizando desde una versión anterior de Azure AD Connect, es una buena idea empezar con los dos servidores con la misma versión, pero no es necesario.
2. Si ha realizado una configuración personalizada y su servidor de ensayo no tiene, siga los pasos en [mover configuración personalizada de activo a servidor de ensayo](#move-custom-configuration-from-active-to-staging-server).
3. Si está actualizando desde una versión anterior de Azure AD Connect, actualizar el servidor de ensayo a la versión más reciente. Si se mueve de Azure AD Sync, a continuación, instalar Azure AD Connect en su servidor de ensayo.
4. Permitir que el motor de sincronización Ejecutar importación completa y sincronización completa en su servidor de ensayo.
5. Compruebe que la nueva configuración no produjo cambios inesperados mediante los pasos indicados en **Comprobar** en [comprobar la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si algo no está sincronización e importar como se esperaba, correcta, ejecutar y compruebe hasta que los datos está bien. Estos pasos se pueden encontrar en el tema vinculado.
6. Cambiar el servidor de ensayo para que sea el servidor activo. Este es el paso final **cambiar active server** en [comprobar la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si está actualizando Azure AD Connect, actualizar el servidor ahora en modo de la versión más reciente de ensayo. Para obtener los datos y configuración actualizado, siga los mismos pasos que antes. Si ha actualizado desde la sincronización de AD de Azure, ahora puede desactivar y retirar el antiguo servidor.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Mover configuración personalizada desde active al servidor de ensayo
Si ha realizado cambios en la configuración en el servidor activo, debe asegurarse de que se aplican los mismos cambios en el servidor de ensayo.

Las reglas de sincronización personalizada que ha creado se pueden mover con PowerShell. Otros cambios se deben aplicar la misma manera en ambos sistemas y no se pueden migrar.

Lo que debe asegurarse de que se configura la misma manera en ambos servidores:

- Conexión a la misma bosques.
- Cualquier dominio y OU filtrado.
- Las mismas funcionalidades opcionales, como la sincronización de la contraseña y reescritura de contraseña.

**Mover reglas de sincronización**  
Para mover una regla personalizada de la sincronización, haga lo siguiente:

1. Abra **El Editor de reglas de sincronización** en el servidor activo.
2. Seleccione la regla personalizada. Haga clic en **Exportar**. Esto abre una ventana de Bloc de notas. Guardar el archivo temporal con una extensión PS1. De esta forma un script de PowerShell. Copie el archivo ps1 para el servidor de ensayo.  
![Exportación de reglas de sincronización](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. GUID de conector es diferente en el servidor de ensayo y se debe cambiar. Para obtener el GUID, inicie el **Editor de reglas de sincronización**, seleccione una de las reglas de fuera de la caja que representa el mismo sistema conectado y haga clic en **Exportar**. Reemplazar el GUID en el archivo PS1 con el GUID desde el servidor de ensayo.
4. En un símbolo del sistema de PowerShell, ejecute el archivo PS1. Esto creará la regla personalizada de la sincronización en el servidor de ensayo.
5. Si tiene varias reglas personalizadas, repita para todas las reglas personalizadas.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
