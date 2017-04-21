<properties
   pageTitle="Información general sobre la seguridad en el almacén de datos de lago | Microsoft Azure"
   description="Entender cómo lago almacén de datos de Azure es un almacén de datos grande sea más seguro"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Seguridad en el almacén de lago de datos de Azure

Muchas empresas están aprovechando de análisis de datos grande de información de la empresa para ayudarle a tomar decisiones inteligentes. Una organización puede tener un entorno complejo y regulado, con un gran número de usuarios diversos. Es fundamental para la empresa para asegurarse de que los datos críticos del negocio se almacenaron de forma más segura, con el nivel de acceso de usuarios individuales correcto. Almacén de lago de datos de Azure está diseñada para satisfacer estos requisitos de seguridad. En este artículo, obtenga más información sobre las capacidades de seguridad lago de almacén de datos, incluidos:

* Autenticación
* Autorización
* Aislamiento de red
* Protección de datos
* Auditoría

## <a name="authentication-and-identity-management"></a>Administración de autenticación e identidad

La autenticación es el proceso por el que se comprueba una identidad de usuario cuando el usuario interactúa con el almacén de datos de lago o con cualquier servicio que se conecta al almacén de datos de lago. Para la administración de identidades y la autenticación, almacenamiento de datos lago usa [Azure Active Directory](../active-directory/active-directory-whatis.md), una identidad completa y solución de nube de administración de access que simplifica la administración de usuarios y grupos.

Cada suscripción Azure se puede asociar con una instancia de Azure Active Directory. Sólo los usuarios y las identidades de servicio que se definen en el servicio de Azure Active Directory pueden acceder a su cuenta de almacén de datos lago, mediante el portal de Azure, herramientas de línea de comandos, o a través de aplicaciones cliente de su organización se crea mediante el SDK de almacén de lago de datos de Azure. Principales ventajas del uso de Azure Active Directory como mecanismo de control de acceso centralizado son:

* Administración del ciclo de vida de identidad simplificada. La identidad de un usuario o un servicio (una identidad principal del servicio) pueden crear rápidamente y revocar rápidamente simplemente eliminando o deshabilitación de la cuenta en el directorio.

* Autenticación multifactor. [Autenticación multifactor](../multi-factor-authentication/multi-factor-authentication.md) proporciona una capa adicional de seguridad para inicios de sesión de usuario y las transacciones.

* Autenticación desde cualquier cliente mediante un protocolo open estándar, como OAuth o OpenID.

* Federación con los servicios de directorio de empresa y proveedores de identidades de la nube.

## <a name="authorization-and-access-control"></a>Autorización y control de acceso

Después de Azure Active Directory autentica a un usuario para que el usuario puede tener acceso a la tienda de lago de datos de Azure, controles de autorización de permisos de acceso a datos de la tienda lago. Almacén de datos de lago separa autorización para actividades relacionadas con la cuenta y datos de la siguiente manera:

* [Control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) (RBAC) proporcionados por Azure para administración de cuentas
* POSIX ACL para tener acceso a datos de la tienda


### <a name="rbac-for-account-management"></a>RBAC para la administración de cuenta

Cuatro funciones básicas definidas para el almacén de datos de lago predeterminada. Las funciones permiten operaciones diferentes en una cuenta de almacén de datos lago a través del portal de Azure, cmdlets de PowerShell y las API de REST. Los roles de colaborador y propietario pueden realizar una gran variedad de funciones de administración de la cuenta. Puede asignar la función de lector a los usuarios que solo interactúan con los datos.

![Funciones RBAC] (./media/data-lake-store-security-overview/rbac-roles.png "Funciones RBAC")

Tenga en cuenta que, aunque se asignan funciones para la administración de cuentas, algunas de las funciones afecta al acceso a datos. Debe usar ACL para controlar el acceso a las operaciones que puede realizar un usuario en el sistema de archivos. La siguiente tabla muestra un resumen de derechos de administración y derechos de acceso de datos para las funciones predeterminadas.

| Roles                    | Derechos de administración               | Derechos de acceso a datos | Explicación |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Sin rol asignado         | Ninguno                            | Regulados por ACL    | El usuario no puede usar el portal de Azure o los cmdlets de PowerShell de Azure para examinar el almacén de datos de lago. El usuario puede usar sólo las herramientas de línea de comandos.
| Propietario  | Todos los  | Todos los  | El rol de propietario es un superusuario. Esta función puede administrar todo el contenido y tiene acceso completo a los datos.
| Lector   | Sólo lectura  | Regulados por ACL    | La función lector puede ver todo sobre la administración de cuentas, como los que el usuario está asignado a qué rol. La función lector no realice los cambios.   |
| Colaborador              | Todo excepto funciones agregar y quitar | Regulados por ACL    | El rol de colaborador puede administrar algunos aspectos de una cuenta, como implementaciones y crear y administrar las alertas. El rol de colaborador no puede agregar o quitar roles.
| Administrador de acceso de usuario | Agregar y quitar roles            | Regulados por ACL    | El rol de administrador de acceso de usuario puede administrar el acceso de usuario a cuentas. |

Para obtener instrucciones, vea [asignar usuarios o grupos de seguridad para cuentas de almacén de datos de lago](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Uso de ACL para operaciones en los sistemas de archivo

Almacén de datos de lago es un sistema de archivos jerárquico como sistema de archivo de distribuido de Hadoop (HDFS) y admite [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controles de lectura (r), escritura (w) y ejecución (x) permisos a los recursos del rol de propietario, para el grupo de propietarios y para otros usuarios y grupos. En el lago almacén público vista previa de datos (la versión actual), ACL están habilitadas en la carpeta raíz, en subcarpetas y archivos individuales. Las ACL que se aplican a la carpeta raíz también se aplicarán a todos los archivos y carpetas secundarias.

Se recomienda definir ACL para varios usuarios mediante [grupos de seguridad](../active-directory/active-directory-accessmanagement-manage-groups.md). Agregar usuarios a un grupo de seguridad y, a continuación, asignar las ACL de un archivo o carpeta a ese grupo de seguridad. Esto es útil cuando desea proporcionar acceso personalizados, porque está limitado a la adición de un máximo de nueve entradas de acceso personalizados. Para obtener más información acerca de cómo asegurar mejor datos almacenados en el almacén de datos de lago mediante grupos de seguridad de Azure Active Directory, vea [asignar usuarios o grupos de seguridad como ACL al sistema de archivos de almacenamiento de lago de datos de Azure](data-lake-store-secure-data.md#filepermissions).

![Lista estándar y acceso personalizado] (./media/data-lake-store-security-overview/adl.acl.2.png "Lista estándar y acceso personalizado")

## <a name="network-isolation"></a>Aislamiento de red

Usar el almacén de lago de datos para controlar el acceso a su almacén de datos en el nivel de red. Puede establecer los firewalls y definir un intervalo de direcciones IP para los clientes de confianza. Con un intervalo de direcciones IP, solo los clientes que tengan una dirección IP dentro del intervalo definido pueden conectarse al almacén de datos lago.

![Configuración del firewall y acceso IP] (./media/data-lake-store-security-overview/firewall-ip-access.png "Configuración del firewall y la dirección IP")

## <a name="data-protection"></a>Protección de datos

Las organizaciones desean garantizar que sus datos empresariales están protegidos a lo largo de su ciclo de vida. Datos de tránsito, almacén de datos de lago usa el protocolo de seguridad de capa de transporte (TLS) estándar para proteger los datos que se mueve entre un cliente y el almacén de datos de lago.

Protección de datos para los datos al resto estará disponible en futuras versiones.

## <a name="auditing-and-diagnostic-logs"></a>Registros de auditoría y diagnósticos

Puede usar el registro de auditoría o diagnóstico, dependiendo de si está buscando registros de actividades relacionadas con la administración o actividades relacionadas con los datos.

*  Las actividades relacionadas con la administración de usan las API de administrador de recursos de Azure y aparecen en el portal de Azure a través de los registros de auditoría.
*  Las actividades relacionadas con los datos usan las API de REST de WebHDFS y aparecen en el portal de Azure a través de los registros de diagnóstico.

### <a name="auditing-logs"></a>Registros de auditoría

Para cumplir con regulaciones, una organización podría ser necesario auditorías adecuado si es necesario profundizar en incidencias específicas. Almacén de datos de lago tiene supervisión integrada y auditoría y registra todas las actividades de administración de cuenta.

Para las auditorías de administración de cuenta, vea y seleccione las columnas que desea iniciar. También puede exportar los registros de auditoría para el almacenamiento de Azure.

![Registros de auditoría] (./media/data-lake-store-security-overview/audit-logs.png "Registros de auditoría")

### <a name="diagnostic-logs"></a>Registros de diagnóstico

Puede establecer auditorías de acceso a datos en el portal de Azure (en configuración de diagnóstico) y cree una cuenta de almacenamiento de blobs de Windows Azure donde se almacenan los registros.

![Registros de diagnóstico] (./media/data-lake-store-security-overview/diagnostic-logs.png "Registros de diagnóstico")

Después de configurar opciones de diagnóstico, puede ver los registros en la pestaña de **Registros de diagnóstico** .

Para obtener más información sobre cómo trabajar con registros de diagnóstico con lago almacén de datos de Azure, consulte [registros de diagnóstico de acceso para el almacén de datos de lago](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumen

Clientes de empresa requieren una plataforma de nube de análisis de datos es segura y fácil de usar. Almacén de lago de datos de Azure está diseñado para ayudar a dirección que estos requisitos a través de la administración de identidades y autenticación a través de la integración de Azure Active Directory, autorización de ACL, aislamiento de red, cifrado de datos en tránsito y en coloque (en el futuro) y auditoría.

Si desea ver las nuevas características de almacén de datos de lago, envíenos sus comentarios en el [foro de UserVoice de almacén de datos lago](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Vea también

- [Introducción a Azure lago de almacén de datos](data-lake-store-overview.md)
- [Empezar a trabajar con el almacén de datos de lago](data-lake-store-get-started-portal.md)
- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
