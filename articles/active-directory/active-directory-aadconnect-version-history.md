<properties
   pageTitle="Azure AD Connect: Historial de versiones de versión | Microsoft Azure"
   description="Este tema enumeran todas las versiones de Azure AD Connect y sincronización de Azure AD"
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
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historial de versiones de versión

El equipo de Azure Active Directory actualiza periódicamente Azure AD Connect con nuevas características y funcionalidades. No todas las adiciones son aplicables a todas las audiencias.

Este artículo está diseñado para ayudarle a realizar un seguimiento de las versiones que se han publicado y comprender si necesita actualizar a la versión más reciente o no.

Se trata de una lista de temas relacionados:

Tema |  
--------- | --------- |
Pasos para la actualización de Azure AD Connect | Entre diferentes métodos para la [actualización de una versión anterior a la más reciente](active-directory-aadconnect-upgrade-previous-version.md) de Azure AD Connect versión.
Permisos necesarios | Para los permisos necesarios para aplicar una actualización, vea [cuentas y permisos](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Descargar| [Descargar Azure AD conectar](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Publicado: agosto de 2016

**Problemas fijos:**

- Sincronizar intervalo no realizar cambios hasta después de que finalice el siguiente ciclo de sincronización.
- Asistente de Azure AD Connect no acepta la cuenta de Azure AD cuyo nombre de usuario empieza con un carácter de subrayado (\_).
- Se produce un error en el Asistente de Azure AD Connect autenticar cuentas de Azure AD proporcionado si la contraseña de la cuenta contiene demasiados caracteres especiales. Mensaje de error "no se pudo validar las credenciales. Ha ocurrido un error inesperado." se devuelve.
- Desinstalación de servidor provisional deshabilita la sincronización de contraseña en el inquilino de Azure AD y hace que la sincronización de contraseñas no se realice correctamente con active server.
- Sincronización de contraseñas falla en casos comunes cuando no hay ningún hash de contraseña almacenado en el usuario.
- Cuando se habilita el servidor de Azure AD Connect para el modo de ensayo, reescritura de contraseña está deshabilitado temporalmente.
- Asistente de Azure AD Connect no muestra la sincronización de contraseñas real y la configuración de la reescritura de contraseña cuando el servidor está en modo de ensayo. Siempre muestra como deshabilitado.
- Cambios de configuración de sincronización de contraseñas y reescritura de contraseña no se conservan por el Asistente de Azure AD Connect cuando el servidor está en modo de ensayo.

**Mejoras:**

- Cmdlet del inicio ADSyncSyncCycle actualizado para indicar si se puede iniciar correctamente un nuevo ciclo de sincronización o no.
- Cmdlet Stop ADSyncSyncCycle agregada para terminar el ciclo de sincronización y operación que están actualmente en curso.
- Cmdlet Stop ADSyncScheduler actualizado para terminar el ciclo de sincronización y operación que están actualmente en curso.
- Al configurar [Las extensiones del directorio de](active-directory-aadconnectsync-feature-directory-extensions.md) Azure AD Connect asistente, ahora se puede seleccionar el atributo de Active Directory de tipo "Teletex string".

## <a name="111890"></a>1.1.189.0
Publicado: junio de 2016

**Problemas corregidos y mejoras:**

- Azure AD Connect ahora puede instalarse en un servidor de compatible con FIPS.
    - Para la sincronización de contraseñas, consulte [sincronización de la contraseña y FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Corregir un problema donde un nombre NetBIOS no se pudo resolver al FQDN en el conector de Active Directory.

## <a name="111800"></a>1.1.180.0
Publicado: 2016 mayo

**Nuevas características:**

- Se advierte y le ayuda a comprobar dominios si no lo hace antes de ejecutar Azure AD Connect.
- Compatibilidad agregada con [Alemania de nube de Microsoft](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Compatibilidad agregada con la infraestructura de [nube de Microsoft Azure gobierno](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) más reciente con los requisitos de dirección URL nuevas.

**Problemas corregidos y mejoras:**

- Agregar filtrado para el Editor de reglas de sincronización para facilitar la búsqueda de reglas de sincronización.
- Mejorar el rendimiento al eliminar un espacio de conector.
- Corregido un problema al mismo objeto se ha eliminado tanto agregado en la misma ejecutar (llamado eliminar o agregar).
- Una regla de sincronización deshabilitada dejará de volver a habilitar incluyen objetos y atributos en el esquema de directorio o actualización actualización.

## <a name="111300"></a>1.1.130.0
Publicado: 2016 abril

**Nuevas características:**

- Compatibilidad agregada con los atributos de valores múltiples extensiones de [Directorio](active-directory-aadconnectsync-feature-directory-extensions.md).
- Compatibilidad agregada con más variaciones de configuración para la [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) se considera elegible para la actualización.
- Agrega algunos cmdlets programador [personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Publicado: marzo de 2016

**Problemas fijos:**

- Realizado seguro instalar Express no se pueden usar en Windows Server 2008 (pre-R2) desde la sincronización de la contraseña no se admite en este sistema operativo.
- Actualización de sincronización de directorios con una configuración de filtro personalizado no funciona como se esperaba.
- Al actualizar a una versión más reciente y no hay ningún cambio en la configuración, no se debe programar una importación/sincronización completa.

## <a name="111100"></a>1.1.110.0
Publicado: febrero de 2016

**Problemas fijos:**

- Actualización de versiones anteriores no funciona si la instalación no está en la carpeta **C:\Program Files** de forma predeterminada.
- Si instala y anular la selección de **iniciar el proceso de sincronización...** al final del Asistente para la instalación, vuelva a ejecutar al Asistente de instalación no se permitirá al programador.
- El programador no funcionará como se esperaba en servidores donde el formato de fecha y hora no está en Estados Unidos. También se bloqueará `Get-ADSyncScheduler` para devolver horas correctas.
- Si instala una versión anterior de Azure AD Connect con ADFS como la opción de inicio de sesión y la actualización, no puede ejecutar al Asistente para instalación de nuevo.

## <a name="111050"></a>1.1.105.0
Publicado: febrero de 2016

**Nuevas características:**

- Característica de [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) para los clientes de configuración de Express.
- Compatibilidad con el administrador global con AMF y PIM en el Asistente para instalación.
    - Debe permitir el servidor proxy permitir también el tráfico a https://secure.aadcdn.microsoftonline-p.com si usa AMF.
    - Debe agregar https://secure.aadcdn.microsoftonline-p.com a la lista de sitios de confianza para AMF funcione correctamente.
- Permite cambiar el método de inicio de sesión del usuario después de la instalación inicial.
- Permitir [el filtrado de dominio y unidad organizativa](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) en el Asistente para instalación. Esto también permite conectar con bosques donde no todos los dominios están disponibles.
- [Programador](active-directory-aadconnectsync-feature-scheduler.md) está integrada en el motor de sincronización.

**Características de vista previa de promoverse a GA:**

- [Reescritura de dispositivo](active-directory-aadconnect-feature-device-writeback.md).
- [Extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nuevas características de vista previa:**

- El nuevo valor predeterminado sincronizar ciclo es de 30 minutos. Solía ser 3 horas para todas las versiones anteriores. Agrega compatibilidad para cambiar el comportamiento del [Programador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas fijos:**

- La página de dominios DNS comprobar siempre no ha reconocido los dominios.
- Solicita las credenciales de administrador de dominio al configurar ADFS.
- Local en el Asistente para instalación no reconoce cuentas de AD si se encuentra en un dominio con un árbol DNS que el dominio raíz.

## <a name="1091310"></a>1.0.9131.0
Publicado: diciembre de 2015

**Problemas fijos:**

- Sincronización de la contraseña no funcionen cuando cambien las contraseñas en AD DS, pero funciona al establecer contraseña.
- Si tiene un servidor proxy, puede producir un error de autenticación de Azure AD durante la instalación o anule la actualización en la página de configuración.
- Actualizar desde una versión anterior de Azure AD Connect con una completa SQL Server se producirá un error si no SA en SQL.
- Actualizar desde una versión anterior de Azure AD Connect con un equipo remoto SQL Server mostrará el error "No se puede tener acceso a la base de datos de SQL ADSync".

## <a name="1091250"></a>1.0.9125.0
Publicado: noviembre de 2015

**Nuevas características:**

- Puede volver a configurar la confianza de Azure AD ADFS.
- Puede actualizar el esquema de Active Directory y volver a crear las reglas de sincronización.
- Puede desactivar una regla de sincronización.
- Puede definir "AuthoritativeNull" como un nuevo literal en una regla de sincronización.

**Nuevas características de vista previa:**

- [Azure AD conectar estado de sincronización](active-directory-aadconnect-health-sync.md).
- Compatibilidad con la sincronización de contraseñas de [Servicios de dominio de Active Directory de Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nuevo escenario compatible:**

- Admite varias organizaciones de Exchange local. Para obtener más información, vea [implementaciones híbridas con varios bosques de Active Directory](https://technet.microsoft.com/library/jj873754.aspx) .

**Problemas fijos:**

- Problemas de sincronización de contraseña:
    - Un objeto movido desde fuera de ámbito en el ámbito no tendrá su contraseña sincronizada. Este incluye OU y filtrado de atributo.
    - Seleccionar una nueva OU para incluir sincronizados no requiere una sincronización completa mediante contraseña.
    - Cuando se habilita un usuario deshabilitado no sincronizar la contraseña.
    - La cola de intentos de contraseña es infinita y se ha quitado el límite anterior de 5.000 objetos que desea eliminar.
    - [Solución de problemas mejorada](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- No se puede conectar con Active Directory con el nivel de bosque funcional de Windows Server 2016.
- No se puede cambiar el grupo utilizado para el filtrado de grupo después de la instalación inicial.
- Ya no se creará un nuevo perfil de usuario en el servidor de Azure AD Connect por cada usuario que realice un cambio de contraseña con reescritura de contraseña habilitada.
- No puede usar los valores de entero largo sincronizados ámbitos de reglas.
- La casilla de verificación "dispositivo reescritura" permanecerá deshabilitada si hay controladores de dominio no accesible.

## <a name="1086670"></a>1.0.8667.0
Publicado: agosto de 2015

**Nuevas características:**

- El Asistente para instalación de Azure AD Connect ahora se adapta a todos los idiomas de Windows Server.
- Compatibilidad agregada con cuenta desbloquear cuando se usa la administración de contraseñas de Azure AD.

**Problemas fijos:**

- Asistente para instalación de Azure AD Connect se bloquea si otro usuario continúa la instalación en lugar de la persona que inicia la instalación por primera vez.
- Si una desinstalación anterior de Azure AD Connect correctamente desinstalar sin problemas de sincronización de Azure AD Connect, no es posible volver a instalar.
- No puede instalar Azure AD Connect con instalación Express si el usuario no está en el dominio raíz del bosque o si se usa una versión no inglesa de Active Directory.
- Si no se puede resolver el FQDN de la cuenta de usuario de Active Directory, se muestra un mensaje de error "No se pudo confirmar el esquema" confuso.
- Si se cambia la cuenta utilizada en el conector de Active Directory fuera del asistente, se producirá un error en el Asistente en ejecuciones subsiguientes.
- Azure AD Connect a veces no se puede instalar en un controlador de dominio.
- No se puede habilitar y deshabilitar "Modo de ensayo" si se han agregado atributos de extensión.
- Reescritura de contraseña en una configuración se produce un error debido a una contraseña incorrecta en el conector de Active Directory.
- No se puede actualizar la sincronización de directorios si dn se utiliza en el atributo filtrado.
- Uso de CPU excesivo cuando se usa el restablecimiento de contraseña.

**Características de vista previa quitado:**

- La característica Vista previa [que reescritura de usuario](active-directory-aadconnect-feature-preview.md#user-writeback) se ha quitado temporalmente basado en comentarios de nuestros clientes de vista previa. Se vuelve a agregará más adelante cuando nos hemos tratado los comentarios proporcionados.

## <a name="1086410"></a>1.0.8641.0
Publicado: junio de 2015

**Versión inicial de Azure AD Connect.**

Ha cambiado el nombre de la sincronización de Azure AD a Azure AD Connect.

**Nuevas características:**

- Instalación [Express configuración](./connect/active-directory-aadconnect-get-started-express.md)
- Puede [configurar ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Puede [actualizar de sincronización de directorios](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Presentó [el modo de ensayo](active-directory-aadconnectsync-operations.md#staging-mode)

**Nuevas características de vista previa:**

- [Reescritura de usuario](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Reescritura de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Reescritura de dispositivo](active-directory-aadconnect-feature-device-writeback.md)
- [Extensiones de directorio](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
Publicado: mayo de 2015

**Requisito de nuevo:**

- Sincronización de Azure AD ahora requiere .net framework versión 4.5.1 para instalarse.

**Problemas fijos:**

- Reescritura de contraseña de Azure AD falla con un error de conectividad de servicebus.

## <a name="104910413"></a>1.0.491.0413
Publicado: abril de 2015

**Problemas corregidos y mejoras:**

- El conector de Active Directory no procesar eliminaciones correctamente si se habilita la Papelera de reciclaje y hay varios dominios del bosque.
- Se ha mejorado el rendimiento de las operaciones de importación para el conector de Azure Active Directory.
- Cuando un grupo ha superado el límite de pertenencia (de forma predeterminada, el límite se establece en 50k objetos), el grupo se eliminó en Azure Active Directory. El nuevo comportamiento es que el grupo permanecerán, se produce un error y no se exportarán cambios de pertenencia a un nuevo.
- No se puede proporcionar un objeto nuevo si delete preconfigurada con el mismo DN ya se encuentra en el espacio del conector.
- Algunos objetos se marcan para que se sincronizan durante una sincronización de delta, aunque no hay ningún cambio por fases en el objeto.
- Forzar una sincronización de contraseña, también quita la lista de DC preferida.
- CSExportAnalyzer tiene problemas con algunos Estados de objetos.

**Nuevas características:**

- Una combinación ahora puede conectarse a tipo de objeto "ANY" en la MV.

## <a name="104850222"></a>1.0.485.0222
Publicado: febrero de 2015

**Mejoras:**

- Rendimiento de importación mejorado.

**Problemas fijos:**

- El atributo cloudFiltered utilizado filtrado de atributo respeta la sincronización de la contraseña. Objetos filtrados ya no estará en el ámbito de sincronización de contraseñas.
- En ocasiones donde la topología tiene muchas controladores de dominio, la sincronización de contraseña no funciona.
- Se ha habilitado en Azure AD/Intune "Detenido servidor" al importar desde el conector de Azure AD después de administración de dispositivos.
- Unirse a principales de seguridad externa (FSP) desde varios dominios en el mismo bosque, produce un error ambiguas.

## <a name="104751202"></a>1.0.475.1202
Publicado: diciembre de 2014

**Nuevas características:**

- Ahora es compatible para realizar la sincronización de contraseñas con atributo en función de filtrado. Para obtener más detalles, consulte [sincronización de contraseñas con el filtro](active-directory-aadconnectsync-configure-filtering.md).
- El atributo msDS-ExternalDirectoryObjectID se vuelve a escribir AD. Agrega la compatibilidad con aplicaciones de Office 365 con OAuth2 para tener acceso a ambas, en línea y buzones de correo en una implementación híbrida de Exchange local.

**Problemas de actualización fijos:**

- Una versión más reciente del Asistente para inicio de sesión está disponible en el servidor.
- Ruta de instalación personalizada se utilizó para instalar la sincronización de Azure AD.
- Un criterio de combinación personalizada no válida, bloquea la actualización.

**Otras correcciones:**

- Corregir las plantillas para Office Pro Plus.
- Problemas de instalación fija causados por los nombres de usuario que comienzan con un guión.
- Fijo perder la configuración de sourceAnchor cuando se ejecuta al Asistente para instalación una segunda vez.
- Seguimiento de ETW fijo para la sincronización de contraseña

## <a name="104701023"></a>1.0.470.1023
Publicado: octubre de 2014

**Nuevas características:**

- Sincronización de contraseñas de varios locales AD a Azure AD.
- IU de instalación adaptado a todos los idiomas de Windows Server.

**Actualizar desde AADSync 1.0 GA**

Si ya tiene instalado Azure AD Sync, hay un paso adicional que debe realizar en caso de que han cambiado cualquiera de las reglas de cuadro de sincronización. Después de haber actualizado a la 1.0.470.1023 suelte, la sincronización se duplican las reglas que se ha modificado. Para cada regla de sincronización modificada, haga lo siguiente:

- Busque la regla de sincronización ha modificado y anote los cambios.
- Eliminar la regla de sincronización.
- Busque la nueva regla de sincronización creados por la sincronización de Azure AD y volver a aplicar los cambios.

**Permisos para la cuenta de AD**

La cuenta de AD debe tener permisos adicionales puedan leer los valores de hash de contraseña de AD. Los permisos para conceder se denominan "Replicar cambios de directorio" y "Replicar directorio cambios todo". Ambos permisos son necesarios para que pueda leer los valores de hash de contraseña

## <a name="104190911"></a>1.0.419.0911
Publicado: septiembre de 2014

**Versión inicial de Azure AD Sync.**

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
