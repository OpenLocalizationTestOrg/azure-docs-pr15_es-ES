<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño: definir la estrategia de protección de datos | Microsoft Azure"
    description="Deberá definir la estrategia de protección de datos para la solución de identidad híbrido cumplir con los requisitos empresariales que haya definido."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir la estrategia de protección de datos para la solución de identidad híbrido

En esta tarea, deberá definir la estrategia de protección de datos para la solución de identidad híbrido cumplir con los requisitos empresariales que haya definido en:

- [Determinar los requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Determinar los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Determinar los requisitos de control de acceso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Determinar los requisitos de respuesta a incidencias](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir las opciones de protección de datos
Como se explica en [determinar requisitos de sincronización de directorio](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD puede sincronizar con los servicios de dominio de Active Directory (AD DS) encuentra local. Esta integración permite a las organizaciones aprovechar Azure AD para comprobar las credenciales de usuario cuando intenta obtener acceso a recursos de la empresa. Esto puede hacerse en ambos casos: datos en el resto de local y en la nube.  Acceso a los datos de Azure AD requiere autenticación de usuario a través de un servicio de token de seguridad (STS).

Una vez autenticada, el nombre principal de usuario (UPN) se lee desde el símbolo de autenticación y la partición replicada y se determina el contenedor correspondiente para el dominio del usuario. Obtener información sobre la existencia del usuario, estado habilitado y función se usa el sistema de autorización para determinar si está autorizado el acceso solicitado para el inquilino de destino para este usuario en esta sesión. Algunas acciones autorizados (específicamente, crear usuarios, restablecer contraseña) crear un rastro de auditoría que puede ser usado por un administrador de inquilinos para administrar los esfuerzos de cumplimiento o investigaciones.

Mover los datos desde el centro de datos local en el almacenamiento de Azure sobre una conexión a Internet no siempre posible debido a volúmenes de datos, la disponibilidad de ancho de banda u otras consideraciones. El [Servicio de importación o exportación de almacenamiento de Azure](../storage/storage-import-export-service.md) proporciona una opción basada en hardware para colocar o recuperar grandes volúmenes de datos en el almacenamiento de blobs. Permite enviar las unidades de disco duro de [cifrado BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) directamente a un centro de datos de Azure donde operadores de nube cargará el contenido a su cuenta de almacenamiento o a las unidades para volver a la pueden descargar los datos de Azure. Solo los discos cifrados se aceptan para este proceso (con una clave de BitLocker generada por el servicio durante la instalación de trabajo). La clave de BitLocker sirve para Azure por separado, lo que proporciona fuera de banda clave de uso compartido.

Puesto que los datos al transmitirlos tenga lugar en escenarios diferentes, también es importante conocer que Microsoft Azure utiliza [una red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para aislar el tráfico de inquilinos de otro, que emplea medidas como servidores de seguridad de nivel de host y de invitado, filtrado de paquetes IP, el bloqueo de puertos y extremos HTTPS. Sin embargo, la mayoría de las comunicaciones internas de Azure, incluidos infraestructura de infraestructura y la infraestructura de cliente (local), también se cifra. Otro escenario importante es las comunicaciones de los centros de datos de Azure; Microsoft administra redes para garantizar que ninguna VM puede representar o interceptar la dirección IP del otro. Al acceder a bases de datos de SQL o el almacenamiento de Azure o al conectarse a servicios en la nube, se utiliza SSL/TLS. En este caso, el administrador del cliente es responsable de obtener un certificado SSL/TLS e implementar a su infraestructura de inquilinos. Mover de tráfico de datos entre máquinas virtuales de Windows en la misma implementación o entre inquilinos en una sola implementación a través de la red Virtual de Microsoft Azure se puede proteger mediante protocolos de comunicación cifrada como HTTPS, SSL/TLS u otras personas.

Dependiendo de cómo respondió a las preguntas de [determinar requisitos de protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), debe determinar cómo desea proteger los datos y cómo la solución de identidad híbrida le ayudará a en el. La tabla muestra las opciones admitidas por Azure que están disponibles para cada escenario de protección de datos.


| Opciones de protección de datos         | En el resto de la nube | En el resto de local | En tránsito |
|---------------------------------|----------------------|---------------------|------------|
| Cifrado de unidad BitLocker      | X                    | X                   |            |
| SQL Server para cifrar las bases de datos | X                    | X                   |            |
| Cifrado de VM a VM             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Lea [cumplimiento característica](https://azure.microsoft.com/support/trust-center/services/) en el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) para obtener más información acerca de los certificados que sea compatible con cada servicio de Azure.
Dado que las opciones de protección de datos utilizan un enfoque multinivel, comparación entre estas opciones no son aplicables para esta tarea. Asegúrese de que están aprovechando todas las opciones disponibles para cada estado que serán los datos.

## <a name="define-content-management-options"></a>Definir las opciones de administración de contenido
Una ventaja de usar Azure AD para administrar una infraestructura de identidad híbrido es que el proceso es completamente transparente desde la perspectiva del usuario final. El usuario intenta obtener acceso a un recurso compartido, el recurso requiere autenticación, el usuario tendrá que enviar una solicitud de autenticación a Azure AD para poder obtener el token y tener acceso al recurso. Todo este proceso sucede en segundo plano, sin intervención del usuario. También es posible conceder permiso a un [grupo](active-directory-manage-groups.md#getting-started-with-access-management) de usuarios para permitirles realizar determinadas acciones comunes.

Las organizaciones que suelen preocupaciones sobre la privacidad de datos requieren la clasificación de datos para la solución. Si su infraestructura local actual ya está usando la clasificación de datos, es posible aprovechar Azure AD como repositorio principal para la identidad del usuario. Una herramienta común que es utilizado en local para la clasificación de datos se denomina [Kit de herramientas de clasificación de datos](https://msdn.microsoft.com/library/Hh204743.aspx) para Windows Server 2012 R2. Esta herramienta puede ayudar a identificar, clasificar y proteger los datos en servidores de archivos en la nube privada. También es posible aprovechar la [Clasificación automática de archivos](https://technet.microsoft.com/library/hh831672.aspx) en Windows Server 2012 para conseguirlo.

Si su organización no tiene la clasificación de datos de pero necesita proteger archivos confidenciales sin agregar nuevos servidores locales, pueden usar Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS usa cifrado, identidad y directivas de autorización para ayudar a proteger su correo electrónico y los archivos y funciona en varios dispositivos, teléfonos, tabletas y equipos. Dado que Azure RMS es un servicio de nube, no es necesario configurar explícitamente confianzas con otras organizaciones para poder compartir contenido protegido con ellos. Si ya tienen un Office 365 o un directorio de Azure AD, la colaboración entre organizaciones automáticamente es compatible. También puede sincronizar solo los atributos de directorio que necesita Azure RMS para admitir una identidad común para las cuentas de Active Directory local, mediante servicios de sincronización de directorio de Azure Active (AAD Sync) o Azure AD Connect.

Es una parte esencial de administración de contenido conocer quién tiene acceso a los recursos, por lo tanto, una capacidad de registro enriquecido es importante para la solución de administración de identidades. Azure AD proporciona registro de más de 30 días incluidos:

- Cambios en la pertenencia a una función (ex: usuario agregado a la función de administrador Global)
- Actualizaciones de credenciales (ex: cambios de contraseña)
- Administración de dominios (ex: comprobar un dominio personalizado, quitar un dominio)
- Agregar o eliminar aplicaciones
- Administración de usuarios (ex: agregar, eliminar, actualizar un usuario)
- Agregar o quitar licencias

>[AZURE.NOTE]
Lea [Microsoft Azure seguridad y administración de registro de auditoría](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber más sobre las capacidades de registro en Azure.
Dependiendo de cómo respondió a las preguntas de [determinar los requisitos de administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), debe determinar cómo desea que el contenido que se administra en la solución de identidad híbrido. Aunque todas las opciones que se exponen en la tabla 6 son capaces de integración con Azure AD, es importante definir que sea más adecuado para sus necesidades empresariales.

| Opciones de administración de contenido                                                               | Ventajas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Desventajas                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralizada local (servidor de administración de derechos de Active Directory)                      | Control total sobre la infraestructura de servidor responsable de clasificar los datos <br> Función incorporada en Windows Server, sin necesidad de licencia adicional o suscripción <br> Puede integrar con Azure AD en un escenario híbrido <br> Es compatible con las capacidades de información rights management (IRM) en Microsoft Online services como Exchange Online y SharePoint Online, así como Office 365 <br> Es compatible con productos de servidor de Microsoft en local, como Exchange Server, SharePoint Server y los servidores de archivos que ejecutan Windows Server y la infraestructura de clasificación de archivo (FCI). | Superior, mantenimiento (conservar el con las actualizaciones, configuración y actualizaciones potenciales), desde TI es el propietario del servidor <br> Requerir una infraestructura de servidor local<br> Doesn'tleverage capacidades de Azure forma nativa                                     |
| Centralizada en la nube (Azure RMS)                                                     | Más fácil de administrar en comparación con la solución local <br> Puede integrar con AD DS en un escenario híbrido <br>  Completamente integrado con Azure AD <br> No requiere un servidor local para implementar el servicio <br> Es compatible con productos de servidor de Microsoft, como Exchange Server, SharePoint, servidor y servidores de archivos que ejecutan Windows Server y clasificación de archivos, infraestructura (FCI) local <br> TI, puede tener un control completo sobre la clave de su inquilino con capacidad de BYOK.                                                                                    | Su organización debe tener una suscripción de nube compatible con RMS <br> Su organización debe tener un directorio de Azure AD para admitir autenticación de usuario de RMS                                                                                  |
| Híbrido (integrado con Azure RMS, servidor de administración de derechos de Active Directory de local) | Este escenario acumula las ventajas de ambos, local centralizada y en la nube.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Su organización debe tener una suscripción de nube compatible con RMS <br> Su organización debe tener un directorio de Azure AD para admitir autenticación de usuario de RMS, <br> Requiere una conexión entre el servicio de nube de Azure e infraestructura local |

## <a name="define-access-control-options"></a>Definir las opciones de control de acceso
Al aprovechar la autenticación, autorización y control de acceso capacidades disponibles en Azure AD poder permitir a su empresa usar un repositorio central identidad permitiendo que los usuarios y socios para usar el inicio de sesión único (SSO) como se muestra en la figura siguiente:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Administración centralizada y totalmente la integración con otros directorios

Azure Active Directory proporciona un inicio de sesión único a miles de aplicaciones SaaS y local de aplicaciones web. Lea la [lista de compatibilidad de federación de Active Directory de Azure: proveedores de identidades de terceros que se pueden usar para implementar el inicio de sesión único](https://msdn.microsoft.com/library/azure/jj679342.aspx) artículo para obtener más detalles sobre el SSO de terceros que se han probado por Microsoft. Esta capacidad permite organización implementar una gran variedad de escenarios B2B y mantener el control de la administración de identidades y acceso. Sin embargo, durante la B2B diseño proceso es importante conocer el método de autenticación que usará el partner y valida si este método es compatible con Azure. Actualmente, estos son compatibles con Azure AD de métodos:

- Lenguaje de marcado de aserción de seguridad (SAML)
- OAuth
- Kerberos
- Tokens
- Certificados


>[AZURE.NOTE] Lea los [Protocolos de autenticación de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para conocer más detalles sobre cada protocolo y sus capacidades en Azure.

Aplicaciones empresariales móviles mediante la compatibilidad de Azure AD, pueden usar la misma experiencia de autenticación de servicios móviles fácil para permitir que los empleados iniciar sesión en sus aplicaciones móviles con sus credenciales de Active Directory corporativos. Con esta característica es compatible Azure AD como un proveedor de identidades de servicios móviles junto con los otros proveedores de identidades admitimos ya (que incluyen Accounts de Microsoft, Facebook ID, identificador de Google y Twitter ID). Si las aplicaciones locales usa las credenciales del usuario que se encuentra en AD DS la compañía, el acceso de usuarios procedentes de la nube y de socios debería transparente. Puede administrar el control de acceso condicional del usuario a aplicaciones web (basada en nube), web API, servicios de nube de Microsoft, 3er aplicaciones SaaS de terceros y aplicaciones cliente (móvil) nativa y tiene las ventajas de seguridad, auditoría, informes desde un único lugar. Sin embargo, se recomienda para validar esto en un entorno de producción no o con una cantidad limitada de usuarios.


>[AZURE.TIP] es importante mencionar que Azure AD carece de directiva de grupo que tenga AD DS. Para aplicar directivas para dispositivos necesitará una solución de administración de dispositivos móviles como [Intune de Microsoft](https://technet.microsoft.com/library/jj676587.aspx).

Una vez que el usuario se autentica con Azure AD, es importante evaluar el nivel de acceso que el usuario tendrá. El nivel de acceso que el usuario tendrá sobre un recurso puede variar, aunque Azure AD puede agregar un nivel de seguridad adicional al controlar el acceso a algunos recursos, también debe tener en cuenta que el propio recurso también puede tener su propia lista de control de acceso por separado, como el control de acceso para los archivos ubicados en un servidor de archivos. La siguiente ilustración resume los niveles de control de acceso que se puede encontrar en un escenario híbrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Cada interacción en el diagrama mostrado en la figura X representa un escenario de control de acceso que se puede abarcar Azure AD. A continuación tiene una descripción de cada escenario:

1 condicional acceso a las aplicaciones que están alojados en local: puede usar dispositivos registrados con las directivas de acceso para las aplicaciones que se han configurado para usar AD FS con Windows Server 2012 R2. Para obtener más información sobre cómo configurar el acceso condicional para local, consulte [Configurar acceso condicional locales con Azure Active Directory dispositivo de registro](active-directory-conditional-access-on-premises-setup.md).
2.Haga Control de acceso para el Portal de administración de Azure: Azure también tiene la capacidad de controlar el acceso al Portal de administración utilizando RBAC (función de Control de acceso). Este método permite la empresa restringir la cantidad de operaciones que una persona puede realizar una vez que se tiene acceso al Portal de administración de Azure. Mediante el uso de RBAC para controlar el acceso al portal, entidad emisora de certificados de los administradores de TI delegar el acceso mediante el uso de los siguientes enfoques de administración de acceso:

 - Asignación de roles basado en grupo: puede asignar acceso a grupos de Azure AD pueden se sincronizan desde Active Directory local. Esto le permite aprovechar las inversiones existentes que su organización ha hecho en herramientas y procesos de administración de grupos. También puede usar la característica de administración de grupo delegado de Azure AD Premium.
 - Aproveche integrado en roles en Azure: puede utilizar tres roles: propietario, Colaborador y lector, para asegurarse de que los usuarios y grupos tienen permiso para realizar sólo las tareas que necesitan para realizar su trabajo.
 - Granular acceso a los recursos: puede asignar roles a usuarios y grupos para una determinada suscripción, grupo de recursos o un recurso individual Azure como un sitio Web o una base de datos. De esta forma, puede asegurarse de que los usuarios tengan acceso a todos los recursos que necesitan y sin acceso a los recursos que no se necesitan para administrar.

>[AZURE.NOTE] Lea [control de acceso basado en roles en Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) para conocer más detalles acerca de esta funcionalidad. Para los desarrolladores que crean aplicaciones y desea personalizar el control de acceso de ellos, también es posible usar funciones de aplicación de Azure AD para autorización. Revise este [ejemplo RoleClaims de aplicación Web de DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) acerca de cómo crear su aplicación para usar esta función.

3.en condicional acceso a las aplicaciones de Office 365 con Microsoft Intune: los administradores de TI pueden aprovisionar directivas de dispositivo de acceso condicional para proteger recursos corporativos, mientras que al mismo tiempo que permite que los trabajadores de la información en dispositivos compatibles con acceso a los servicios. Para obtener más información, vea [Directivas de dispositivo de acceso condicional para servicios de Office 365](active-directory-conditional-access-device-policies.md).

4 acceso condicional de para las aplicaciones Saas: [esta característica](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) le permite configurar reglas de acceso de cada aplicación con autenticación multifactor y la capacidad de bloquear el acceso a los usuarios no se encuentran en una red de confianza. Puede aplicar las reglas de autenticación multifactor a todos los usuarios que están asignados a la aplicación, o solo para los usuarios dentro de los grupos de seguridad especificado. Los usuarios se pueden excluir desde el requisito de autenticación multifactor si tienen acceso a la aplicación desde una dirección IP que en dentro de la organización de red.

Dado que las opciones para controlar el acceso usan un enfoque multinivel, comparación entre estas opciones no son aplicables para esta tarea. Asegúrese de que están aprovechando todas las opciones disponibles para cada escenario que requiere que controlar el acceso a los recursos.

## <a name="define-incident-response-options"></a>Definir las opciones de respuesta a incidencias
Azure AD puede ayudar a TI a posible riesgos de seguridad en el entorno por supervisar la actividad de usuario, que puede aprovechan Azure AD acceso y capacidad para obtener visibilidad de la integridad y la seguridad del directorio de su organización de los informes de uso de la identidad. Con esta información, un administrador de TI puede determinar mejor donde puede encontrarse posibles riesgos de seguridad para que puedan planear adecuadamente a mitigar estos riesgos.  [Suscripción de Azure AD Premium](active-directory-get-started-premium.md) tiene un conjunto de informes de seguridad puede habilitar TI para obtener esta información. [Informes de Azure AD](active-directory-view-access-usage-reports.md) se clasifican como se muestra a continuación:

- **Informes de anomalías**: contienen eventos que hemos encontrado a ser incorrectos de inicio de sesión. Nuestro objetivo es que tenga en cuenta este tipo de actividad y le permiten podrá determinar si un evento es sospechoso.
- **Informe de aplicación integrada**: proporciona información sobre cómo se utilizan aplicaciones de nube de su organización. Azure Active Directory ofrece integración con miles de aplicaciones de la nube.
- **Informes de errores**: indicar errores que pueden producirse al hacer el aprovisionamiento de cuentas de aplicaciones externas.
- **Los informes específicos del usuario**: mostrar dispositivo/inicio de sesión de los datos de la actividad de un usuario específico.
- **Registros de actividad**: contienen un registro de todos los eventos de auditoría dentro de las últimas 24 horas últimos 7 días o últimos 30 días, así como cambios de la actividad de grupo y actividad de registro y restablecer contraseña.

>[AZURE.TIP]
Otro informe que también puede ayudar al equipo de respuesta de incidente trabajando en un caso es el [usuario con credenciales perdidas](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Este informe surge a las coincidencias entre estas lista credenciales perdida y su inquilino.

Otros importantes integrada en los informes de Azure AD que puede utilizarse durante una investigación de respuesta a incidencias y son:

- **Actividad de restablecimiento de contraseña**: proporcione el Administrador con recomendaciones sobre cómo restablecer contraseña es que se usen en la organización.
- **Actividad de registro de restablecimiento de contraseña**: proporciona información en la que los usuarios que haya registrado sus métodos para restablecer contraseña, y los métodos que ha seleccionado.
- **Actividad de grupo**: proporciona un historial de cambios al grupo (ex: los usuarios agregar o quitar) que se inicia en el Panel de acceso.

Además de la capacidad de reporting core disponible en Premium de AD de Azure también se puede aprovechar durante un proceso de investigación de respuesta de incidente, que puede aprovechar informe de auditoría para obtener información como:

- Cambios en la pertenencia a una función (ex: usuario agregado a la función de administrador Global)
- Actualizaciones de credenciales (ex: cambios de contraseña)
- Administración de dominios (ex: comprobar un dominio personalizado, quitar un dominio)
- Agregar o eliminar aplicaciones
- Administración de usuarios (ex: agregar, eliminar, actualizar un usuario)
- Agregar o quitar licencias

Dado que las opciones de respuesta a incidencias usa un enfoque multinivel, comparación entre estas opciones no son aplicables para esta tarea. Asegúrese de que están aprovechando todas las opciones disponibles para cada escenario que requiere que utilice la capacidad de generación de informes de Azure AD como parte del proceso de respuesta a incidencias de su empresa.

## <a name="next-steps"></a>Pasos siguientes
[Determinar las tareas de administración de identidades híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)
