<properties
   pageTitle="Eventos de informe de auditoría de Azure Active Directory | Microsoft Azure"
   description="Auditar eventos que están disponibles para verlos y descargando de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Eventos de informe de auditoría de Azure Active Directory

*Esta documentación es parte de la [Azure Active Directory Reporting guía] (activo-directorio-informes-guide.md).*

El informe de auditoría de Azure Active Directory ayuda a los clientes a identificar acciones con privilegios que ocurrieron en su Azure Active Directory. Acciones con privilegios incluyen cambios de elevación (por ejemplo, creación de roles o restablecimiento de contraseñas), cambiar las configuraciones de directiva (por ejemplo directivas de contraseña) o cambios de configuración del directorio (por ejemplo, los cambios a la configuración de la federación de dominio). Los informes proporcionan el registro de auditoría para el nombre de evento, el actor que realizó la acción, el recurso de destino que se ven afectado por el cambio y la fecha y hora (en UTC). Los clientes pueden recuperar la lista de eventos de auditoría para su Azure Active Directory a través del [Portal de Azure](https://portal.azure.com/), como se describe en [Ver los registros de auditoría](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Lista de eventos de informe de auditoría
<!--- audit event descriptions should be in the past tense --->

Eventos                               | Descripción del evento
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Eventos de usuario**                      |
Agregar usuario                             | Agregar un usuario en el directorio.
Eliminar usuario                          | Eliminar un usuario desde el directorio.
Establecer propiedades de licencia               | Establecer las propiedades de la licencia para un usuario en el directorio.
Restablecer la contraseña de usuario                  | Restablecer la contraseña de un usuario en el directorio.
Cambiar la contraseña de usuario                 | Cambiar la contraseña de un usuario en el directorio.
Licencia de usuario de cambio                  | Cambiar la licencia asignada a un usuario en el directorio. Para ver qué licencias se han actualizado, examine las propiedades de [usuario de actualización](#update-user-attributes) siguiente
Actualizar usuario                          | Actualizar un usuario en el directorio. [Siga leyendo](#update-user-attributes) para los atributos que se pueden actualizar.
Establecer forzar cambiar contraseña de usuario       | Establezca la propiedad que fuerza al usuario cambiar su contraseña de inicio de sesión.
Actualizar credenciales de usuario        | Usuario ha cambiado la contraseña  
**Eventos de grupo**                     |
Agregar grupo                            | Crear un grupo en el directorio.
Actualizar grupo                         | Actualizar un grupo en el directorio. Para ver qué propiedades del grupo se han actualizado, consulte [Auditoría de propiedades de grupo](#update-group-attributes) en la sección siguiente
Eliminar grupo                         | Eliminar un grupo desde el directorio.
Agregar a miembros al grupo            | Agregar a un miembro a un grupo en el directorio.
Quitar a miembro de grupo         | Quitar a un miembro de un grupo en el directorio.
CreateGroupSettings              | Configuración de grupo creado
UpdateGroupSettings          | Actualizar la configuración del grupo. Para ver qué configuración del grupo se ha actualizado, consulte [Auditoría de propiedades de grupo](#update-group-attributes) en la sección siguiente
DeleteGroupSettings            | Configuración de grupo eliminado
SetGroupLicense                  | Establecer la licencia de grupo.
SetGroupManagedBy              | Establecer grupo administrarse por usuario
AddGroupMember               | Miembro agregado al grupo
RemoveGroupMember            | Quitar a miembro de grupo
AddGroupOwner                | Propietario agregado al grupo
RemoveGroupOwner                 | Quiten propietario del grupo
**Eventos de aplicación**               |
Agregar a principal del servicio                | Agrega una entidad de seguridad de servicio en el directorio.
Quitar a principal del servicio             | Quitar una entidad de seguridad de servicio desde el directorio.
Agregar credenciales de servicio principal    | Credenciales agregadas a una entidad de seguridad del servicio.
Quitar las credenciales de servicio principal | Credenciales quitadas de un servicio principal.
Agregar entrada de delegación                 | Crea un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) en el directorio.
Entrada de conjunto de delegación                 | Actualizar una [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) en el directorio.
Quitar entrada de delegación              | Eliminar un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) en el directorio.
**Eventos de roles**                      |
Agregar a miembro de la función a función              | Agregar un usuario a una función de directorio.
Quitar a miembro del rol de función         | Quita un usuario de una función de directorio.
Establecer la información de contacto de la empresa      | Establecer preferencias de contacto de nivel de empresa. Esto incluye direcciones de correo electrónico para las notificaciones de marketing, así como técnicas acerca de Microsoft Online Services.
Agregar entrada de delegación                 | Crea un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio.
Entrada de conjunto de delegación                 | Actualizar una [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio.
Quitar entrada de delegación              | Eliminar un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio.
AddSevicePrincipalOwner          | Propietario agregado a principal del servicio.
RemoveSevicePrincipalOwner   | Quita el propietario de servicio principal.
AddApplication  | Agregar la aplicación.
UpdateApplication | Actualizar la aplicación. Para ver qué configuración de la aplicación se ha actualizado, consulte [Auditoría de propiedades de la aplicación](#update-application-attributes) en la sección siguiente
DeleteApplication | Eliminar la aplicación.
RestoreApplication|Restaurar aplicación.
AddApplicationOwner   |     Agregar propietario a la aplicación.
RemoveApplicationOwner    |     Quitar el propietario de la aplicación.
**Eventos de roles**                         |
Agregar a miembro de la función a función                 | Agregar un usuario a una función de directorio.
Quitar a miembro del rol de función            | Quita un usuario de una función de directorio.
AddRoleDefinition               | Definición de la función agregado.
UpdateRoleDefinition                | Actualizar la definición de función. Para ver la configuración de rol se ha actualizado, consulte [Auditoría de propiedades de definición de función](#update-role-definition-attributes) en la sección siguiente
DeleteRoleDefinition                | Definición de la función eliminada.
AddRoleAssignmentToRoleDefinition | Asignación de roles agregados a la definición de función.
RemoveRoleAssignmentFromRoleDefinition | Asignación de roles quiten de definición de función.
AddRoleFromTemplate     | Función de agregado desde plantilla.
UpdateRole  | Función actualizado.
AddRoleScopeMemberToRole    | Miembro de ámbito agregado al rol.
RemoveRoleScopedMemberFromRole  | Quitar a miembro de ámbito de función.
**Eventos del dispositivo (todos los eventos)**                    |
AddDevice               | Dispositivo agregado.
UpdateDevice            | Dispositivo actualizado. Para ver qué propiedades de dispositivos se han actualizado, se refieren a las [Propiedades de dispositivos auditada](#update-device-attributes) en la sección siguiente
DeleteDevice            | Dispositivo eliminado.
AddDeviceConfiguration      | Configuración de dispositivo agregado.
UpdateDeviceConfiguration   | Configuración de dispositivo actualizado. Para ver qué propiedades de configuración de dispositivo se han actualizado, se refieren a las [Propiedades de configuración de dispositivo auditada](#update-device-configuration-attributes) en la sección siguiente
DeleteDeviceConfiguration   | Configuración de dispositivo eliminado.
AddRegisteredOwner     | Propietario registrado agregado al dispositivo.
AddRegisteredUsers     | Agregar usuarios registrados al dispositivo.
RemoveRegisteredOwner    | Quitar propietario registrado desde el dispositivo.
RemoveRegisteredUsers    | Quitar usuarios registrados desde el dispositivo.
RemoveDeviceCredentials  | Quitar las credenciales del dispositivo.
**Eventos de B2B**                       |
Invitaciones a lote cargan.              | Un administrador ha cargado un archivo que contiene las invitaciones que se envíen a los usuarios de partner.
Invitaciones de lote procesados.             | Se ha procesado un archivo que contiene las invitaciones a usuarios de partner.
Invitar a usuarios externos.                | Un usuario externo ha sido invitado en el directorio.
Canjear invitar a usuarios externos.         | Un usuario externo ha canjear su invitación para el directorio.
Agregar usuarios externos al grupo.          | Un usuario externo se ha asignado a pertenencia a un grupo en el directorio.
Asignar a usuario externo a la aplicación. | Un usuario externo se ha asignado el acceso directo a la aplicación.
Creación de virus inquilino.               | Se ha creado un nuevo inquilino en Azure AD por la amortización de invitación.
Creación de virus de usuario.                 | Ha creado un usuario en un inquilino existente en Azure AD por la amortización de invitación.
**Unidades administrativas (todos los eventos)**             |
AddAdministrativeUnit   |   Agregar unidad administrativa.
UpdateAdministrativeUnit|   Actualizar unidad administrativa. Para ver qué propiedades de unidad administrativa actualizadas, consulte [auditoría administrativo propiedades de la unidad](#update-administrative-unit-attributes) en la sección siguiente
DeleteAdministrativeUnit|   Eliminar unidad administrativa.
AddMemberToAdministrativeUnit|  Agregar a miembros a unidad administrativa.
RemoveMemberFromAdministrativeUnit|     Quitar a miembro de unidad administrativa.
**Eventos de directorio**                 |
Agregar a asociado a la compañía               | Agregar a un partner en el directorio.
Eliminar el Partner de compañía          | Quitar a un socio desde el directorio.
DemotePartner   |   Disminuir el nivel de partner.
Agregar dominio para la empresa                | Agregar un dominio en el directorio.
Quitar el dominio de compañía           | Quitar un dominio desde el directorio.
Actualizar dominio                        | Actualizar un dominio en el directorio. Para ver qué propiedades de dominio se han actualizado, consulte [auditoría de propiedades del dominio](#update-domain-attributes) en la sección siguiente
Configurar la autenticación de dominio            | Cambiar la configuración del dominio predeterminado para la empresa.
Establecer la información de contacto de la empresa      | Establecer preferencias de contacto de nivel de empresa. Esto incluye direcciones de correo electrónico para las notificaciones de marketing, así como técnicas acerca de Microsoft Online Services.
Establecer configuración de federación de dominio    | Actualizar la configuración de federación para un dominio.
Comprobar el dominio                        | Comprobar un dominio en el directorio.
Comprobar el dominio comprobado de correo electrónico         | Comprobar un dominio en el directorio mediante la comprobación de correo electrónico.
Establecer marca de DirSyncEnabled de empresa   | Establezca la propiedad que permite a un directorio de Azure AD Sync.
Establecer la directiva de contraseñas                  | Establecer restricciones de longitud y caracteres de las contraseñas de usuario.
Información de la compañía del conjunto              | Actualizar la información de nivel de empresa. Vea el cmdlet de PowerShell [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) para obtener más detalles.
SetCompanyAllowedDataLocation  |    Establecer compañía permitida la ubicación de los datos.
SetCompanyDirSyncEnabled    |   Establecer marca de DirSyncEnabled.
SetCompanyDirSyncFeature |  Configurar la característica de sincronización de directorios.
SetCompanyInformation |   Información de la compañía del conjunto.
SetCompanyMultiNationalEnabled    |     Establecer la característica multinacionales empresa habilitada.
SetDirectoryFeatureOnTenant   |     Configurar la característica de directorio de inquilinos.
SetTenantLicenseProperties  |   Establecer las propiedades de la licencia de inquilinos.
CreateCompanySettings   |   Crear la configuración de la empresa
UpdateCompanySettings   |   Actualizar la configuración de la compañía. Para ver qué propiedades de empresa se han actualizado, consulte [auditoría de propiedades de la compañía](#update-company-attributes) en la sección siguiente
DeleteCompanySettings   |   Eliminar la configuración de la compañía
SetAccidentalDeletionThreshold   |  Establezca el umbral de borrado accidental.
SetRightsManagementProperties   |   Establecer las propiedades de administración de derechos.
PurgeRightsManagementProperties     |   Purgar propiedades de administración de derechos.
UpdateExternalSecrets   |   Actualizar información confidencial externos
**Eventos de directiva (todos los eventos)**           |
AddPolicy   |   Agregar directiva.
UpdatePolicy    |   Actualizar la directiva.
DeletePolicy    |   Eliminar la directiva.
AddDefaultPolicyApplication     |   Agregar directiva a la aplicación.
AddDefaultPolicyServicePrincipal    |   Agregar directiva a principal del servicio.
RemoveDefaultPolicyApplication  |   Quitar la directiva de aplicación.
RemoveDefaultPolicyServicePrincipal     |   Quitar la directiva de principales de servicio.
RemovePolicyCredentials     |   Quitar las credenciales de la directiva.

## <a name="audit-report-retention"></a>Retención de informes de auditoría
Eventos en el informe de auditoría de Azure AD se conservan durante 180 días. Para obtener más información acerca de la retención en los informes, vea [Directivas de retención de Azure Active Directory informe](active-directory-reporting-retention.md).

Los clientes que desea almacenar los eventos de auditoría para períodos de retención más, la API de informes de puede usarse para extraer periódicamente los eventos de auditoría en un almacén de datos independiente. Para obtener información detallada, vea [Introducción a la API de informes](active-directory-reporting-api-getting-started.md) .

## <a name="properties-included-with-each-audit-event"></a>Propiedades incluidas con cada evento de auditoría

(Propiedad)      | Descripción
------------- | --------------------------------------------------------------
Fecha y hora | La fecha y hora en que se produjo el evento de auditoría
Actor         | El usuario o principal del servicio que realizó la acción
Acción        | La acción que se ha realizado
Destino        | El usuario o principal del servicio que se realizó la acción


## <a name="update-user-attributes"></a>"Actualizar usuario" atributos
El evento de auditoría de "Usuario de actualización" incluye información adicional sobre qué atributos de usuario se han actualizado. Para cada atributo, se incluye el valor anterior y el nuevo valor.

Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | Los usuarios pueden iniciar sesión.
AssignedLicense                     | Todas las licencias que se han asignado al usuario.
AssignedPlan                        | Planes de servicio es el resultado de las licencias asignadas al usuario.
LicenseAssignmentDetail             | Detalles sobre licencias asignadas al usuario. Por ejemplo, si se ha implicado licencias de grupo, esto incluiría el grupo que le ha concedido la licencia.
Móvil                              | Teléfono móvil del usuario.
OtherMail                           | Dirección de correo electrónico alternativa del usuario.
OtherMobile                         | Teléfono móvil alternativa del usuario.
StrongAuthenticationMethod          | Una lista de métodos de verificación configurada por el usuario para la autenticación multifactor, como llamadas de voz, SMS o comprobación de código de una aplicación móvil.
StrongAuthenticationRequirement     | Si se aplica la autenticación multifactor, habilitado o deshabilitado para este usuario.
StrongAuthenticationUserDetails     | Número de teléfono del usuario, alternativo teléfono número y el correo electrónico que utiliza para la verificación de restablecimiento de autenticación multifactor y la contraseña.
StrongAuthenticationPhoneAppDetail  | Detalle forof phone aplicaciones registradas para llevar a cabo el inicio de sesión de 2FA
TelephoneNumber                     | Número de teléfono del usuario.
AlternativeSecurityId               | Un identificador de seguridad alternativo para el objeto.
CreationType                        |Método de creación del usuario (ya sea invitación o virus).
InviteTicket                        |Lista de vales de invitación para el usuario.
InviteReplyUrl                      |Lista de direcciones URL para responder tras la aceptación de la invitación.
InviteResources                     |Lista de recursos que se invitó al usuario.
LastDirSyncTime                     |Última vez que el objeto se actualizó debido a la sincronización de la autoridad directorio (local del cliente).
MSExchRemoteRecipientType           |Se asigna a los tipos de destinatarios de DCE. Consulte https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx [tipos de destinatarios DCE] para tipos de destinatarios
PreferredDataLocation               |La ubicación preferida del usuario, del grupo, del contacto, carpetas públicas, o los datos del dispositivo.
ProxyAddresses                      |La dirección en la que se reconoce un objeto de destinatario de Exchange Server en un sistema de correo externo.
StsRefreshTokensValidFrom           |Lleva actualiza información de revocación token: cualquier tokens de actualización de STS emitidos antes de que se consideran esta vez expirado.
UserPrincipalName                   |El UPN que es un nombre de inicio de sesión de estilo de Internet para un usuario.
UserState                           |Estado de usuario pendiente de aprobación o PendingAcceptance/aceptado/PendingVerification.
UserStateChangedOn                  |Marca de hora de última modificación a UserState. Se usa para desencadenar flujos de trabajo de ciclo de vida.
UserType                            |Tipo de usuario. Miembro (0), invitado (1), Viral(2).

## <a name="update-group-attributes"></a>Atributos de "Actualizar grupo"
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Clasificación            | La clasificación de un grupo unificado (Repercusión, MBI, etcetera).
Descripción               | Frases descriptivos legible sobre el objeto.  
DisplayName               |El nombre para mostrar de un objeto
DirSyncEnabled            |Indica si se produce la sincronización de una relevantes directorio (local del cliente).
GroupLicenseAssignment    |Asignación de licencias de un grupo
GroupType                 |Tipo de grupo, unificada (0)
IsMembershipRuleLocked    |Indica que la propiedad MembershipRule se establece en el servicio de administración de autoservicio de grupo y no se pueden modificar los usuarios. Aplicar solo a grupos donde GroupType incluye GroupType.DynamicMembership
IsPublic                  |Marcar para indicar si el grupo es público o privado.
LastDirSyncTime           |Última vez que se actualiza el objeto como resultado de la sincronización de la relevantes directorio (local del cliente).
Correo                      |Dirección de correo electrónico principal
MailEnabled               |Indica si este grupo tiene capacidad de correo electrónico.
MailNickname              |Moniker para un objeto de la libreta de direcciones, normalmente en la parte de su nombre de correo electrónico anterior la "@" símbolo.   
MembershipRule            |Cadena expresar los criterios utilizados por el servicio de administración de grupo de autoservicio para determinar qué miembros deben pertenecer a este grupo. Vea también IsMembershipRuleLocked. Se aplica solo a los grupos en los que GroupType incluye GroupType.DynamicMembership.
MembershipRuleProcessingState| Un valor de enumeración definido por el servicio de administración de grupo de autoservicio define el estado de pertenencia procesamiento para este grupo. Se aplica solo a los grupos en los que GroupType incluye GroupType.DynamicMembership.
ProxyAddresses            |La dirección en la que se reconoce un objeto de destinatario de Exchange Server en un sistema de correo externo.
RenewedDateTime           |Registro de la marca de tiempo de cuando un grupo renovado más recientemente.   
SecurityEnabled           |Indica si la pertenencia en el grupo puede influir en las decisiones de autorización.
WellKnownObject           |Los nombres de un objeto de directorio, designar como parte de un conjunto predefinido.

## <a name="update-device-attributes"></a>"Actualizar dispositivo" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indica si se puede autenticar una entidad de seguridad.
CloudAccountEnabled | Indica si se puede autenticar una entidad de seguridad. Escribir InTune cuando el dispositivo está domine local.
CloudDeviceOSType | Tipo de dispositivo había basado en el sistema operativo, por ejemplo, Windows RT, iOS. Cuando se establece un servicio de nube (por ejemplo, Intune), este atributo se convierte en relevantes en el directorio DeviceOSType.
CloudDeviceOSVersion | Versión del sistema operativo. Cuando se establece un servicio de nube (por ejemplo, Intune), este atributo se convierte en relevantes en el directorio DeviceOSVersion.
CloudDisplayName | Valor del atributo displayName LDAP. Cuando se establece un servicio de nube (por ejemplo, Intune), este atributo se convierte en relevantes en el directorio de displayName.
CloudCreated |Indica si el objeto se creó mediante servicios en la nube.
CompliantUntil | Hasta el momento de qué dispositivo se considera compatible.
DeviceMetadata |Metadatos personalizados para el dispositivo
DeviceObjectVersion | Este atributo se usa para identificar la versión del esquema del dispositivo.
DeviceOSType |Tipo de dispositivo había basado en el sistema operativo, por ejemplo, Windows RT, iOS. Escribe el servicio de registro y diseñada para actualizar el MDM STS o servicio de administración claro servicio de administración.
DeviceOSVersion |Versión del sistema operativo. Escribe el servicio de registro y diseñada para actualizar el MDM STS o servicio de administración claro servicio de administración.
DevicePhysicalIds |Atributo multivalor está pensado para almacenar identificadores del dispositivo físico. Esto puede incluir identificadores de BIOS, huellas digitales TPM, hardware ID específicos, etcetera.
DirSyncEnabled | Indica si se produce la sincronización de un relevantes (cliente, local) directorio.
DisplayName |El nombre para mostrar de un objeto
IsCompliant | Este atributo se usa para administrar el estado de la administración de dispositivos móviles del dispositivo.
IsManaged |Este atributo se usa para indicar que el dispositivo está gestionado por una nube MDM.
LastDirSyncTime |Última vez que el objeto se actualizó debido a la sincronización de la autoridad directorio (local del cliente).

## <a name="update-device-configuration-attributes"></a>"Actualizar la configuración de dispositivo" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |El número máximo de días que un dispositivo puede estar inactivo antes de que se considera para su eliminación.
RegistrationQuota   |Directiva utilizada para limitar el número de registros de dispositivo permitidos para un solo usuario.

## <a name="update-service-principal-configuration-attributes"></a>"Actualizar la configuración del servicio principal" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indica si se puede autenticar una entidad de seguridad.
AppPrincipalId | Identidad externo, definido por la aplicación de una entidad de seguridad.
DisplayName |El nombre para mostrar de un objeto
Principal de servicio    | Un nombre principal de servicio, que contiene "nombre/autoridad" donde nombre especifica un valor de clase de la aplicación y autoridad contiene al menos hostname [: puerto] o "nombre" que especifica un identificador de la entidad de seguridad del servicio.

## <a name="update-app-attributes"></a>Atributos de "Actualizar la aplicación"
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |El conjunto de direcciones (redirigir direcciones URL) que están asignados a una entidad de seguridad del servicio.
Id                               |Identificador de la aplicación de la aplicación   
AppIdentifierUri | URI de aplicación, que identifica la aplicación.  Normalmente, es la dirección URL de acceso de la aplicación.
AppLogoUrl |La dirección url de la imagen del logotipo de aplicación almacenada en una CDN.
AvailableToOtherTenants | True la aplicación es la aplicación de múltiples arrendatario (es decir, puede ser usada por otros inquilinos).
DisplayName | El nombre para mostrar de un nombre de aplicación
Derecho |Lista de los derechos de aplicación.
ExternalUserAccountDelegationsAllowed |Marcador que indica si la aplicación de recursos es una confianza y puede crear entradas de delegación de cuentas de usuarios externos.
GroupMembershipClaims |La directiva de reclamaciones de pertenencia de grupo.
PublicClient | True si el cliente no puede mantener secreto (es decir, el cliente no confidencial en OAuth2.0)
RecordConsentConditions | Tipos de condiciones de consentimiento, definida por las condiciones del contrato: ninguno (0), SilentConsentForPartnerManagedApp(1). Este valor se mostrará en el esquema de la API de gráfico y sólo se puede establecer o cambiar administradores de inquilinos.
RequiredResourceAccess |Contenido XML de un valor de la propiedad RequiredResourceAccess.
Aplicación Web |Si es true, indica que esta aplicación es una aplicación web.
WwwHomepage |La página Web principal.

## <a name="update-role-attributes"></a>"Actualizar el rol" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |El conjunto de direcciones (redirigir direcciones URL) que están asignados a una entidad de seguridad del servicio.
BelongsToFirstLoginObjectSet |Si es true, indica que este objeto pertenece al conjunto de objetos que necesita habilitar inicio de sesión de la primera administración de un nuevo inquilino.
Integrados |Indica si el sistema posee la duración de un objeto.
Descripción | Frases descriptivos legible sobre el objeto.  
DisplayName |El nombre para mostrar de un objeto
MailNickname | Moniker para un objeto de la libreta de direcciones, normalmente en la parte de su nombre de correo electrónico anterior la "@" símbolo.
RoleDisabled |Indica si se debe omitir el rol de efectos de controles de access.
RoleTemplateId | Identidad de la plantilla de rol.
ServiceInfo |Información aprovisionamiento específica del servicio que puede consumir MOAC y otras instancias de servicio (mismo o de otro tipo de servicio).
TaskSetScopeReference |Identifica una tarea establecer y un conjunto de ámbitos asociados a una función o RoleTemplate.
ValidationError |Información publicada por un servicio federado que describe un error no transitorios y específicos del servicio respecto a las propiedades o vínculo de una acción de administrador de objeto para resolver.
WellKnownObject |Los nombres de un objeto de directorio, designar como parte de un conjunto predefinido.

## <a name="update-role-definition-attributes"></a>"Actualizar la definición de la función" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Colección de ámbitos de autorización que se puede hacer referencia al asignar esta RoleDefinition a una entidad de seguridad.
DisplayName     |El nombre para mostrar de un objeto
GrantedPermissions  |Permisos por este RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>"Actualizar unidad administrativa" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Descripción |   Esta propiedad se actualiza cuando se cambia la descripción de una unidad administrativa.
DisplayName |   Esta propiedad se actualiza cuando se cambia el nombre de una unidad administrativa.

## <a name="update-company-attributes"></a>Atributos de "Actualización empresa"
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | Una ubicación en la que los usuarios de la empresa pueden aprovisionar.
AuthorizedServiceInstance   | Nombres de las instancias de servicio a la que se pueden implementar un plan.
DirSyncEnabled |Indica si se produce la sincronización de un relevantes (cliente, local) directorio.
DirSyncStatus |Indica si se produce la sincronización de objetos de la libreta de direcciones en el contexto de inquilinos de un relevantes (cliente, local) directorio; expansión de la propiedad DirSyncEnabled en los objetos de la empresa.
DirSyncFeatures |Indicador de bits para realizar un seguimiento de conjunto de características de sincronización de directorios habilitado y deshabilitado del inquilino.
DirectoryFeatures |Características de directorio habilitado o deshabilitado.
DirSyncConfiguration |Contiene toda la configuración de sincronización de directorios específicos al inquilino actual.
DisplayName |El nombre para mostrar de un objeto
IsMnc |Un indicador booleano se establece en "true" iff que la empresa está habilitada para la característica de empresa multinacionales.
ObjectSettings | Una colección de configuración aplicable al ámbito del objeto.
PartnerCommerceUrl |Dirección URL de sitio de comercio de su compañero.
PartnerHelpUrl |Dirección URL del sitio de la Ayuda de su compañero.
PartnerSupportEmail | Dirección URL de correo electrónico de soporte técnico de su compañero.
PartnerSupportTelephone |Dirección URL de teléfono de soporte técnico de su compañero.
PartnerSupportUrl |Dirección URL de sitio de soporte técnico de su compañero.
StrongAuthenticationDetails |Detalles de StrongAuthentication.
StrongAuthenticationPolicy |Directiva de autenticación segura para la empresa.
TechnicalNotificationMail |Dirección de correo electrónico para notificar a los problemas técnicos correspondientes a una empresa.
TelephoneNumber |Números de teléfono que cumplen con la recomendación E.123 de ITU.
TenantType |El tipo de un inquilino. Si no se especifica este valor, el inquilino es una compañía. En caso contrario, los valores posibles son: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Un conjunto de nombres de dominio DNS enlazado a una compañía.

## <a name="update-domain-attributes"></a>"Actualizar dominio" atributos
Atributo                           | Descripción
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Capacidades de    |Bit indicadores que describen las capacidades del dominio, si existe.
Predeterminado |Indica si el dominio es el valor predeterminado; Por ejemplo, el sufijo predeterminado de UserPrincipalName cuando un administrador crea un nuevo usuario en MOAC.
Inicial |Indica si el dominio es el dominio inicial de la empresa, como las asigna OCP. El dominio inicial es un subdominio único de un dominio de Microsoft Online; e.g.contoso3.microsoftonline.com.
LiveType    |Tipo de Windows Live espacio de nombres correspondiente, si la hay.
Nombre    | Identificador del extremo.
PasswordNotificationWindowDays  |El número de días antes de que el usuario caduca de una contraseña es una notificación.
PasswordValidityPeriodDays  | El número de días que una contraseña es una buena idea para antes de que se debe cambiar.

Registros de auditoría son un control necesario para muchas reglamentaciones. Los clientes con el informe de auditoría del directorio de Azure Active para satisfacer sus reglamentaciones, se recomienda que el cliente enviar una copia de este tema de ayuda con la copia del informe de auditoría exportado del cliente para ayudar a explicar los detalles del informe. Si el auditor le gustaría comprender las normas de cumplimiento Azure actualmente cumple, dirigir al auditor a la [página de cumplimiento](https://azure.microsoft.com/support/trust-center/compliance/) de Microsoft Azure Trust Center.
