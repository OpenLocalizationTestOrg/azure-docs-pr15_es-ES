<properties
    pageTitle="Azure AD Connect: Puertos | Microsoft Azure"
    description="Esta página es una página de referencia técnica de los puertos que deben estar abiertos para Azure AD Connect"
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
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Híbrido identidad obligatorio puertos y protocolos
El documento siguiente es una referencia técnica para proporcionar información sobre los necesarios puertos y protocolos que son necesarios para implementar una solución de identidad híbrido. Use la siguiente ilustración y consulte la tabla correspondiente.

![¿Qué es Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabla 1: Azure AD conectarse y AD local
Esta tabla describe los puertos y protocolos necesarios para la comunicación entre el servidor de Azure AD Connect y AD local.

Protocolo | Puertos | Descripción
--------- | --------- |---------
DNS|53 (TCP/UDP)| Búsquedas DNS en el bosque de destino.
Kerberos|88 (TCP/UDP)| Autenticación Kerberos del bosque de AD.
RPC DE MS |135 (TCP/UDP)| Se utiliza durante la configuración inicial del Asistente de Azure AD Connect cuando enlaza al bosque de AD.
LDAP|389 (TCP/UDP)| Se usa para la importación de datos de AD. Datos se cifran con inicio de sesión de Kerberos & sello.
LDAP/SSL|636 (TCP/UDP)| Se usa para la importación de datos de AD. La transferencia de datos esté firmada y cifrada. Solo se utiliza si está utilizando SSL.
RPC |49152 a 65535 (aleatorio alto RPC Port)(TCP/UDP)| Se utiliza durante la configuración inicial de Azure AD Connect cuando enlaza a los bosques de AD. Para obtener más información, vea [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)y [KB224196](https://support.microsoft.com/kb/224196) .

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabla 2 - Azure AD conectarse y Azure AD
Esta tabla describen los puertos y protocolos necesarios para la comunicación entre el servidor de Azure AD Connect y Azure AD.

Protocolo |Puertos |Descripción
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Usar para descargar las CRL (listas revocación de certificados) para comprobar los certificados SSL.
HTTPS|443(TCP/UDP)| Se utiliza para sincronizar con Azure AD.

Para obtener una lista de direcciones URL e IP direcciones debe abrir en el firewall, consulte [URL de Office 365 y los intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tabla 3 - Azure AD conectarse y servidores de federación o WAP
Esta tabla describen los puertos y protocolos necesarios para la comunicación entre el servidor de Azure AD Connect y servidores de federación o WAP.  

Protocolo |Puertos |Descripción
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Usar para descargar las CRL (listas revocación de certificados) para comprobar los certificados SSL.
HTTPS|443(TCP/UDP)| Se utiliza para sincronizar con Azure AD.
WinRM|5985| Escucha WinRM

## <a name="table-4---wap-and-federation-servers"></a>Tabla 4 - WAP y los servidores de federación
Esta tabla describen los puertos y protocolos necesarios para la comunicación entre los servidores de federación y WAP.

Protocolo |Puertos |Descripción
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Se usa para la autenticación.

## <a name="table-5---wap-and-users"></a>Tabla 5 - WAP y los usuarios
Esta tabla describen los puertos y protocolos necesarios para la comunicación entre los usuarios y los servidores WAP.

Protocolo |Puertos |Descripción
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Se usa para la autenticación del dispositivo.
TCP|49443 (TCP)| Se usa para la autenticación de certificado.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabla 6a & 6b - agente de estado de conexión de Azure AD para (AD FS/sincronización) y Azure AD
Las tablas siguientes describen los extremos, puertos y protocolos necesarios para la comunicación entre agentes de estado de conexión de AD de Azure y Azure AD

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabla 6a - puertos y protocolos para agente de estado de conexión de Azure AD para (AD FS/sincronización) y Azure AD
Esta tabla describe la siguientes salidos puertos y protocolos que son necesarios para la comunicación entre los agentes de estado de conexión de AD de Azure y Azure AD.  

Protocolo |Puertos  |Descripción
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Saliente
Bus de servicio de Azure|5671 (TCP/UDP)| Saliente

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6b - extremos para agente de estado de conexión de Azure AD para (AD FS/sincronización) y Azure AD
Para obtener una lista de extremos, vea [la sección Requisitos para el agente de mantenimiento de conexión de Azure AD](active-directory-aadconnect-health-agent-install.md#requirements).
