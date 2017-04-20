<properties
    pageTitle="Lista de compatibilidad de federación de Azure AD"
    description="Esta página tiene proveedores de identidades no son de Microsoft que se pueden usar para implementar el inicio de sesión único."
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
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidad de federación de Azure AD
Azure Active Directory proporciona el inicio de sesión único en y mejorado de seguridad de acceso de la aplicación para Office 365 y otros servicios de Microsoft Online para implementaciones de nube de solo lectura y híbrido sin necesidad de cualquier solución no sean de Microsoft. Office 365, como la mayoría de servicios en línea de Microsoft, se integra con Azure Active Directory para servicios de directorio, la autenticación y la autorización. Azure Active Directory también proporciona un inicio de sesión único a miles de aplicaciones SaaS y local de aplicaciones web. Vea la Galería de la aplicación de Azure Active Directory para las aplicaciones SaaS compatibles.

Para las organizaciones que han invertido en soluciones de federación no sean de Microsoft, este tema contiene instrucciones para configurar un inicio de sesión único para los usuarios de Active Directory de Windows Server con Microsoft Online services utilizando proveedores de identidades no sean de Microsoft de la "lista de compatibilidad de Azure Active Directory federation" debajo. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Grupo de equipos Oxford](http://oxfordcomputergroup.com/), un tercero, en nombre de Microsoft, había probado estas única sesión experiencias de con proveedores de identidades no sean de Microsoft con un conjunto de casos de uso común con Azure Active Directory.

Para obtener información sobre cómo hacer para que su proveedor de identidades de terceros incluido aquí, póngase en contacto con grupo de equipo Oxford en [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Grupo de equipos Oxford había probado solo la funcionalidad de federación de estos escenarios de inicio de sesión único. Grupo de equipos Oxford no se realizó las pruebas de la sincronización, la autenticación de dos factores, componentes etc. de estos escenarios de inicio de sesión único.

>Uso de la sesión por ID UPN alternativo no está probado también en este programa.



- [Azure Active Directory](#azure-active-directory)
- [Servicios de federación de IDM óptimo identidad Virtual Server](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11](#pingfederate-611) 
- [7.2 PingFederate](#pingfederate-72) 
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify) 
- [IBM Tivoli federado 6.2.2 del Administrador de identidades](#ibm-tivoli-federated-identity-manager-622) 
- [IdP SecureAuth 7.2.0](#secureauth-idp-720) 
- [CA SiteMinder 12,52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [Administrador de acceso de NetIQ 4.0.1](#netiq-access-manager-401) 
- [BIG IP con el Administrador de directivas de acceso BIG-IP versión 11.3 x-x 11.6](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [Portal de área de trabajo de VMware versión 2.1](#vmware-workspace-portal-version-21) 
- [Inicie sesión y vaya 5.3](#signampgo-53) 
- [IceWall federación versión 3.0](#icewall-federation-version-30) 
- [Nube segura de entidad emisora de certificados](#ca-secure-cloud) 
- [Dell una identidad nube Access Manager 7.1](#dell-one-identity-cloud-access-manager-v71) 
- [Inicio de sesión único de AuthAnvil 4.5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Puesto que se trata de productos de terceros, Microsoft no proporciona soporte técnico para la implementación, configuración, solución de problemas, mejores prácticas, etc. problemas y preguntas sobre estos proveedores de identidades. Para obtener soporte técnico y preguntas sobre estos proveedores de identidades, póngase en contacto con terceros compatibles directamente.

>Estos proveedores de identidades de terceros se han probado interoperabilidad con los servicios de nube de Microsoft mediante WS-Federation y WS-Trust protocolos solo. Pruebas no incluía mediante el protocolo SAML.

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory puede autenticar a los usuarios por la federación con su Active Directory local o sin un servidor de federación local mediante el uso de la sincronización de la contraseña. 

A continuación se muestra la matriz de soporte de escenario para esta experiencia de inicio de sesión: 


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|
|Aplicaciones modernas utilizando ADAL como Office 2016| Compatibles|Ninguno|

Para obtener más información sobre el uso de Azure Active Directory con AD FS, vea [Servicios de federación de Active Directory (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Para obtener más información sobre el uso de Azure Active Directory con la sincronización de contraseñas, vea [Azure AD Connect](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Servicios de federación de IDM óptimo identidad Virtual Server 
Óptima IDM Virtual servidor de federación de servicios de identidad puede autenticar a los usuarios que se encuentran en Active Directory de los clientes locales.

A continuación se muestra el escenario los esta experiencia de inicio de sesión único de matriz de soporte:


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Para obtener más información acerca del acceso de cliente directivas vea [limitar el acceso a Office 365 servicios según la ubicación del cliente.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 

PingFederate 6.11 implementa el estándar de identidad de federación de WS utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno (versiones anteriores deben actualizar a 6.11|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener instrucciones sobre cómo configurar esto la PingFederate STS para proporcionar la experiencia de inicio de sesión único a los usuarios de Active Directory, descargue el archivo pdf [aquí.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>7.2 PingFederate 
7.2 PingFederate implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Instrucciones de PingFederate sobre cómo configurar este STS proporcionar la experiencia de inicio de sesión único a los usuarios de Active Directory, vea [aquí.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x 
PingFederate 8.x implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Instrucciones de PingFederate sobre cómo configurar este STS proporcionar la experiencia de inicio de sesión único a los usuarios de Active Directory, vea [aquí.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify ayuda a proporciona un federados inicio de sesión único para Office 365 sin necesidad de hospedaje de un servidor de federación local.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |No se admite el Control de acceso de cliente 

Para obtener más información sobre Centrify, consulte [aquí.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli federado 6.2.2 del Administrador de identidades 
IBM Tivoli federado identidad Manager 6.2.2 con el Administrador de acceso para 1.4 de aplicaciones de Microsoft de IBM seguridad implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información acerca de IBM Tivoli federado identidad Manager, vea [acceso de administrador de seguridad de IBM for Microsoft Applications.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>IdP SecureAuth 7.2.0 
SecureAuth IdP 7.2.0 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar una experiencia de inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre SecureAuth, consulte [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>Versión acumulativa CA SiteMinder 12,52 SP1 4
CA SiteMinder federación 12,52 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información acerca de la entidad emisora de certificados SiteMinder, consulte [CA SiteMinder federación.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
Servicios de federación de nube RadiantOne (CFS) 3.0 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre RadiantOne CFS, consulte [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 


| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Autenticación de Windows integrada requiere la configuración de servidor web adicionales y aplicación de Okta.|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre Okta, consulte [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin probado de mayo de 2014 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Autenticación de Windows integrada|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre OneLogin, consulte [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>Administrador de acceso de NetIQ 4.0.1 
Administrador de acceso de NetIQ 4.0.1 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |* Kerberos contratos compatibles|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |No se admite la autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

* NetIQ admiten la autenticación Kerberos a través de la configuración de un contrato de Kerberos.  Para obtener ayuda con esta configuración, póngase en contacto con NetIQ o ver a la Guía de configuración. Para obtener más información sobre el Administrador de acceso de NetIQ, consulte [Administrador de acceso de NetIQ.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG IP con la versión de administrador de directivas de acceso BIG-IP 11,3 x-x 11.6 
BIG-IP con acceso de administrador de directivas, (APM) ver de BIG IP. 11,3 x-x 11.6 implementa el estándar de identidades SAML utilizado para proporcionar una experiencia de inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único. 

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  No compatible |No compatible|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre el Administrador de directivas de acceso de BIG IP, consulte [Administrador de directivas de acceso BIG IP.](https://f5.com/products/modules/access-policy-manager) 

Para las instrucciones del Administrador de directivas de acceso BIG IP en forma de configurar STS para proporcionar la experiencia de inicio de sesión único a los usuarios de Active Directory, descargue el archivo pdf [aquí.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Portal de área de trabajo de VMware versión 2.1 
Portal de área de trabajo de VMware versión 2.1 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |No se admite la autenticación de Windows integrada|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM | Compatibles |No se admite la autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre el Portal de área de trabajo de VMware versión 2.1, descargue el archivo pdf [aquí.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Inicie sesión y vaya 5.3 
Inicie sesión & ir la identidad WS federación WS-Trust utilizado 5.3 implementa estándar para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Contratos de Kerberos compatibles |
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM | Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|


Signo & ir 5.3 es compatible con la autenticación Kerberos a través de la configuración de un contrato de Kerberos.  Para obtener ayuda con esta configuración, póngase en contacto con Ilex o ver la Guía de configuración [aquí.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>IceWall federación versión 3.0 
IceWall federación versión 3.0 implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |No se admite la autenticación de Windows integrada|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM | Compatibles |No se admite la autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información acerca de la federación de IceWall, consulte [aquí](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) y [aquí.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>Nube segura de entidad emisora de certificados 

Entidad emisora de certificados Secure nube implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |No se admite la autenticación de Windows integrada|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM | Compatibles |No se admite la autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información acerca de la nube segura de entidad emisora de certificados, consulte [entidad emisora de certificados Secure Cloud.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell una identidad nube Access Manager 7.1 
Administrador de acceso de Dell una identidad nube implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |Ninguno|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM |  Compatibles |Ninguno|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|

Para obtener más información sobre Dell una identidad nube Access Manager, vea [Dell una identidad nube Access Manager.](http://software.dell.com/products/cloud-access-manager)

 Para que obtener instrucciones sobre cómo configurar este STS proporcionar la experiencia de inicio de sesión único a los usuarios de Office 365, vea [Configurar usuarios de Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>Inicio de sesión único de AuthAnvil 4.5 
Inicio de sesión único en 4.5 AuthAnvil implementa el estándar de identidad WS federación WS-Trust utilizado para proporcionar un inicio de sesión único y marco de atributos de exchange.

La siguiente es la matriz de soporte de escenario para esta experiencia de inicio de sesión único.

| Cliente |Soporte técnico  |Excepciones|
| --------- | --------- |--------- |
| Clientes Web como Web Access de Exchange y SharePoint Online | Compatibles |No se admite la autenticación de Windows integrada|
| Aplicaciones de cliente enriquecido como Lync, la suscripción de Office, CRM | Compatibles |No se admite la autenticación de Windows integrada|
| Clientes de correo electrónico enriquecido como Outlook y ActiveSync |  Compatibles |Ninguno|


Para obtener más información, consulte [AuthAnvil inicio de sesión único.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
