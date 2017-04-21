<properties
    pageTitle="Acceder a clave depósito detrás del firewall | Microsoft Azure"
    description="Obtenga información sobre cómo acceder a clave depósito desde una aplicación detrás de un firewall"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>Acceso a la clave depósito detrás del firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>P: mi aplicación de cliente de clave depósito debe estar detrás de un firewall, ¿qué puertos y hosts/direcciones IP debo abrir para habilitar el acceso a depósito clave?

Para obtener acceso a la aplicación de cliente de clave depósito necesita tener acceso a varios extremos para varias funcionalidades un depósito de clave.

- Autenticación (a través de Azure Active Directory)
- Administración de depósito de clave (que incluye crear, leer, actualizar o eliminar y también la configuración de directivas de acceso) a través del Administrador de recursos de Azure
- Acceso y administración de objetos (teclas e información confidencial) almacenados en depósito clave propio, pasa por la cámara clave extremo específico (por ejemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Dependiendo de su entorno y configuración, hay algunas variaciones.   

## <a name="ports"></a>Puertos

Todo el tráfico a un depósito clave para todas las 3 funciones (autenticación, administración y datos plano de access) trata sobre HTTPS: puerto 443. Sin embargo CRL, habrá ocasionalmente tráfico HTTP (puerto 80). Los clientes que admiten OCSP no deberían llegar CRL, pero en ocasiones pueden llegar a [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticación

Aplicación de cliente de clave depósito tendrán que tener acceso a los extremos de Azure Active Directory para la autenticación. Extremo utilizado depende de la configuración del inquilino AAD y el tipo de capital--principal de usuario, servicio principal y el tipo de cuenta, por ejemplo, Microsoft Account o identificador de organización.  

| Tipo de capital | Extremo: puerto |
|----------------|---------------|
| Usuario con Account de Microsoft<br> (p. ej.user@hotmail.com) | **Global:**<br> Login.microsoftonline.com:443<br><br> **China Azure:**<br> Login.chinacloudapi.CN:443<br><br>**Azure gobierno:**<br> inicio de sesión us.microsoftonline.com:443<br><br>**Alemania Azure:**<br> Login.microsoftonline.de:443<br><br> y <br>Login.Live.com:443   |
| Entidad de seguridad de usuario o servicio mediante ID Org con AAD (p. ej.user@contoso.com) | **Global:**<br> Login.microsoftonline.com:443<br><br> **China Azure:**<br> Login.chinacloudapi.CN:443<br><br>**Azure gobierno:**<br> inicio de sesión us.microsoftonline.com:443<br><br>**Alemania Azure:**<br> Login.microsoftonline.de:443 |
| Entidad de seguridad de usuario o servicio mediante organización ID + ADFS u otro extremo federado (por ejemplouser@contoso.com) | Todos los extremos anteriores para ID Org plus ADFS u otros extremos federados |

Hay otras posibles escenarios complejos. Consulte [Flujo de Azure Active Directory autenticación](/documentation/articles/active-directory-authentication-scenarios/), [Aplicaciones de integración con Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) y [Protocolos de autenticación de Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para obtener información adicional.  

## <a name="key-vault-management"></a>Administración de claves de cámara

Para la administración de depósito clave (CRUD y establecer la directiva de acceso), la aplicación cliente de cámara clave se necesita tener acceso a extremo del Administrador de recursos de Azure.  

| Tipo de operación | Extremo: puerto |
|----------------|---------------|
| Operaciones de plano de Control del depósito de clave<br> mediante el Administrador de recursos Azure | **Global:**<br> Management.Azure.com:443<br><br> **China Azure:**<br> Management.chinacloudapi.CN:443<br><br> **Azure gobierno:**<br> Management.usgovcloudapi.NET:443<br><br> **Alemania Azure:**<br> Management.microsoftazure.de:443 |
| Gráfico de Azure Active Directory API | **Global:**<br> Graph.Windows.NET:443<br><br> **China Azure:**<br> Graph.chinacloudapi.CN:443<br><br> **Azure gobierno:**<br> Graph.Windows.NET:443<br><br> **Alemania Azure:**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operaciones de depósito clave

Para toda la administración de claves depósito objeto (claves e información confidencial) y las operaciones de cifrado, clave depósito cliente debe acceder el punto final de depósito clave. Según la ubicación de la cámara de clave, el sufijo DNS de extremo es diferente. El punto final de clave depósito tiene el formato: < nombre de depósito >. < región--sufijo dns específico-> tal como se describe en la tabla siguiente.  

| Tipo de operación | Extremo: puerto |
|----------------|---------------|
| Operaciones de clave depósito como las operaciones de cifrado de claves, claves de creación, lectura, actualización y eliminación e información confidencial, set o get etiquetas y otros atributos de objetos de depósito clave (teclas/información confidencial)     | **Global:**<br> &lt;nombre de depósito&gt;. vault.azure.net:443<br><br> **China Azure:**<br> &lt;nombre de depósito&gt;. vault.azure.cn:443<br><br> **Azure gobierno:**<br> &lt;nombre de depósito&gt;. vault.usgovcloudapi.net:443<br><br> **Alemania Azure:**<br> &lt;nombre de depósito&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalos de direcciones IP

Servicio de depósito de clave por separado usa otros recursos Azure como infraestructura de PaaS, por lo tanto, no es posible proporcionar un rango específico de direcciones IP que tendrán los extremos de servicio de depósito de clave en cualquier momento. Sin embargo si el firewall solo es compatible con la dirección IP rangos, a continuación, consulte el documento de [Intervalos de IP de centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) .   Para la autenticación y de identidad (Azure Active Directory), la aplicación debe poder conectarse a los extremos que se describe en la [autenticación y las direcciones de la identidad](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Pasos siguientes

- Si tiene preguntas sobre depósito clave, visite los [Foros de depósito de clave de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
