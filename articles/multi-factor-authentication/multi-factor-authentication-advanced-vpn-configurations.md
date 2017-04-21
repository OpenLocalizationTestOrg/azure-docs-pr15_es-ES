<properties
    pageTitle="Escenarios avanzados con Azure autenticación multifactor y 3er VPN de terceros"
    description="Esta página proporciona información sobre la configuración de configuración paso a paso para Azure AMF con 3ª prodcuts de terceros."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban" 
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Escenarios avanzados con Azure autenticación multifactor y 3er VPN de terceros
La autenticación multifactor Azure puede usarse para conectar con una amplia variedad de soluciones VPN parte 3ª.  Esto incluye dispositivo Cisco® ASA VPN, dispositivo Citrix NetScaler SSL VPN y el equipo de redes seguro o Pulse el acceso seguro conectar Secure VPN de Juniper SSL.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Dispositivo de ASA VPN Cisco y autenticación multifactor de Azure
La autenticación multifactor Azure se integra sin problemas con su dispositivo Cisco® ASA VPN para proporcionar seguridad adicional para inicios de sesión de Cisco AnyConnect® VPN y acceso al portal.  Esto puede hacerse mediante el protocolo LDAP o de radio.  Seleccione una de las siguientes acciones para descargar las guías de configuración paso a paso detallado.

Guía de configuración  | Descripción
------------- | ------------- |
[Cisco ASA con Anyconnect VPN y Azure configuración AMF para LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integre el dispositivo Cisco ASA VPN con AMF Azure mediante LDAP|
[Cisco ASA con VPN Anyconnect Azure AMF la configuración y RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integre el dispositivo Cisco ASA VPN con AMF Azure con RADIUS

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN y Azure autenticación multifactor
La autenticación multifactor Azure se integra sin problemas con su dispositivo Citrix NetScaler SSL VPN para proporcionar seguridad adicional para inicios de sesión de Citrix NetScaler SSL VPN y acceso al portal.  Esto puede hacerse mediante el protocolo LDAP o de radio.  Seleccione una de las siguientes acciones para descargar las guías de configuración paso a paso detallado.

Guía de configuración  | Descripción
------------- | ------------- |
[Configuración de AMF Citrix NetScaler SSL VPN y Azure para LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integre su Citrix NetScaler SSL VPN con el dispositivo de Azure AMF mediante LDAP|
[Configuración de AMF Citrix NetScaler SSL VPN y Azure para radio](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integre el dispositivo Citrix NetScaler SSL VPN con AMF Azure con RADIUS

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Dispositivo Juniper o Pulse seguro SSL VPN y autenticación multifactor de Azure
La autenticación multifactor Azure se integra sin problemas con su dispositivo Juniper o Pulse seguro SSL VPN para proporcionar seguridad adicional para inicios de sesión de Juniper o Pulse seguro SSL VPN y acceso al portal.  Esto puede hacerse mediante el protocolo LDAP o de radio.  Seleccione una de las siguientes acciones para descargar las guías de configuración paso a paso detallado.

Guía de configuración  | Descripción
------------- | ------------- |
[Configuración de AMF Juniper o Pulse seguro SSL VPN y Azure para LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Integre sus Juniper o Pulse seguro SSL VPN con dispositivo de Azure AMF mediante LDAP|
[VPN seguro de SSL de Juniper o Pulse Azure AMF la configuración y RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integre el dispositivo Juniper o Pulse seguro SSL VPN con AMF Azure con RADIUS
