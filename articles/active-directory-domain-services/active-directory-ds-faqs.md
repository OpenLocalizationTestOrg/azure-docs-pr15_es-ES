<properties
    pageTitle="Preguntas más frecuentes - servicios de dominio de Azure Active Directory | Microsoft Azure"
    description="Preguntas más frecuentes sobre los servicios de dominio de Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Servicios de dominio de Azure Active Directory: Preguntas más frecuentes (P+f)

Esta página responde a preguntas más frecuentes sobre los servicios de dominio de Azure Active Directory. Mantener comprobación de actualizaciones de.

### <a name="troubleshooting-guide"></a>Guía de solución de problemas
Consulte nuestra [Guía de solución de problemas](active-directory-ds-troubleshooting.md) para obtener soluciones para problemas comunes al configurar o administración de servicios de dominio de Active Directory de Azure.


### <a name="configuration"></a>Configuración

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>¿Puedo crear varios dominios para un único directorio de Azure AD?
No. Solo puede crear un único dominio atendido por los servicios de dominio de Active Directory de Azure para un único directorio de Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>¿Habilitar servicios de dominio de Active Directory de Azure en una red virtual del Administrador de recursos de Azure?
No. Servicios de dominio de Active Directory Azure sólo se puede habilitar en una red virtual Azure clásica. La red virtual clásica puede conectarse a una red virtual del Administrador de recursos con una red virtual interconexión para usar su dominio administrado en una red virtual del Administrador de recursos.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>¿Puedo realizar servicios de dominio de Active Directory de Azure disponible en varias redes virtuales dentro de mi suscripción?
El servicio no admite directamente este escenario. Servicios de dominio de Active Directory Azure está disponible en solo una red virtual a la vez. Sin embargo, puede configurar la conectividad entre varias redes virtuales para exponer los servicios de dominio de Active Directory de Azure a otras redes virtuales. Este artículo describe cómo puede [conectar redes virtuales en Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>¿Habilitar servicios de dominio de Active Directory de Azure con PowerShell?
Implementación de PowerShell/automatizada de servicios de dominio de Active Directory de Azure no está disponible actualmente.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>¿Está disponible en el nuevo portal de Azure Servicios de dominio de Active Directory de Azure?
No. Se pueden configurar servicios de dominio de Active Directory Azure solo en el [portal de clásico de Azure](https://manage.windowsazure.com). Esperamos ampliar la compatibilidad para el [portal de Azure](https://portal.azure.com) en el futuro.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>¿Puedo agregar controladores de dominio a un dominio administrado de servicios de dominio de Active Directory de Azure?
No. El dominio de servicios de dominio de Active Directory de Azure es un dominio administrado. No es necesario aprovisionar, configurar o administrar los controladores de dominio para este dominio - estas actividades de administración se proporcionan como un servicio de Microsoft. Por lo tanto, no puede agregar controladores de dominio adicionales para el dominio administrado (lectura y escritura o de sólo lectura).

### <a name="administration-and-operations"></a>Administración y operaciones

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>¿Puedo conectar con el controlador de dominio para mi dominio administrado mediante Escritorio remoto?
No. No tiene permisos para conectarse a los controladores de dominio para el dominio administrado a través de escritorio remoto. Los miembros del grupo 'AAD DC administradores' pueden administrar el dominio administrado usando las herramientas de administración de AD como el centro de administración de Active Directory (ADAC) o AD PowerShell. Estas herramientas se instalan mediante la característica 'Herramientas de administración de servidor remoto' en un servidor de Windows unido al dominio administrado.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>He habilitado Servicios de dominio de Active Directory de Azure. ¿Qué cuenta de usuario se usa para equipos de combinación de dominio para este dominio?
Los usuarios que ha agregado al grupo administrativo (por ejemplo, ' AAD DC administradores') pueden máquinas de unión al dominio. Además, los usuarios de este grupo se le ha concedido acceso de escritorio remoto en equipos que se han unido al dominio.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>¿Puedo tener un privilegios de administrador de dominio para el dominio de servicios de dominio de Active Directory de Azure?
No. No se conceden privilegios de administrador en el dominio administrado. Privilegios de administrador del dominio y Administrador de la empresa no están disponibles para su uso dentro del dominio. Administrador de dominio existente o los grupos de administrador de empresa en el directorio de Azure AD también no se conceden privilegios de administrador de empresa o dominio en el dominio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>¿Puedo modificar pertenencias a grupos mediante LDAP u otras herramientas administrativas de AD en dominios de los servicios de dominio de Active Directory de Azure?
No. No se puede modificar la pertenencia a grupos en dominios atendidas por los servicios de dominio de Active Directory de Azure. Lo mismo sucede con los atributos de usuario. Sin embargo, puede cambiar las pertenencias a grupos o atributos de usuario en Azure AD o en su dominio local. Dichos cambios se sincronizan automáticamente a los servicios de dominio de Active Directory de Azure.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>¿Ampliar el esquema de dominio proporcionado servicios de dominio de Active Directory de Azure?
No. Microsoft administra el esquema para el dominio administrado. Extensiones de esquema no son compatibles con servicios de dominio de Active Directory de Azure.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>¿Se pueden modificar o agregar registros DNS en mi dominio administrado?
Sí. Los usuarios que pertenecen al grupo 'AAD DC administradores' se conceden privilegios de ' Administrador de DNS', para modificar los registros DNS del dominio administrado. Estos usuarios pueden utilizar la consola del Administrador de DNS en un equipo que ejecuta Windows Server unido al dominio administrado, para administrar el DNS. Para usar la consola del Administrador de DNS, instale 'Herramientas del servidor DNS', que forma parte de la característica opcional 'Herramientas de administración de servidor remoto' en el servidor. Más información acerca de [utilidades para administrar, supervisar y solucionar problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) está disponible en TechNet.


### <a name="billing-and-availability"></a>Facturación y disponibilidad

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>¿Es que un servicio de pago de servicios de dominio de Active Directory de Azure?
Sí. Para obtener más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>¿Hay una versión de prueba gratuita para el servicio?
Este servicio se incluye en la versión de prueba gratuita de Azure. Puede suscribirse a una [versión de prueba gratuita de un mes de Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>¿Puedo obtener los servicios de dominio de Active Directory de Azure como parte del conjunto de aplicaciones de movilidad de empresa (EMS)?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>¿Debo Azure AD Premium para usar los servicios de dominio de Active Directory de Azure?
No. Servicios de dominio de Active Directory Azure es un pago por uso servicio Azure y no forma parte de EMS. Servicios de dominio de Azure AD están disponibles para todas las ediciones de Azure AD (libre, básico y, Premium) y se cargarán cada hora, dependiendo de uso.

#### <a name="what-azure-regions-is-the-service-available-in"></a>¿Qué regiones Azure es el servicio disponible en?
Consulte la página de [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para ver una lista de las regiones de Azure en servicios de dominio de Active Directory de Azure está disponible.
