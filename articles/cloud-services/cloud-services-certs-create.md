<properties 
    pageTitle="Certificados de administración de servicios de nube | Microsoft Azure" 
    description="Obtenga información sobre cómo crear y usar certificados con Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Introducción a los certificados para servicios de nube de Azure
Los certificados se utilizan en Azure para los servicios de nube ([certificados de servicio](#what-are-service-certificates)) y para autenticar con la API ([certificados de administración](#what-are-management-certificates) cuando se usa el portal clásica Azure y no ARM) de administración. Este tema ofrece una descripción general de ambos tipos de certificado, cómo [crear](#create) e [implementar](#deploy) a Azure.

Certificados que se usan en Azure x.509 v3 certificados y otro certificado de confianza se pueden firmar o pueden ser autofirmados. Un certificado autofirmado está firmado por su creador y, por ello, no son de confianza de forma predeterminada. La mayoría de los exploradores pueden pasar por alto esta. Certificados de firma automática sólo se deben utilizar su propio para desarrollar y probar los servicios de nube. 

Certificados utilizados por Azure pueden contener una privada o una clave pública. Los certificados tienen una huella digital que permite identificarlos de forma ambigua. Esta huella digital se utiliza en el [archivo de configuración](cloud-services-configure-ssl-certificate.md) de Azure para identificar el certificado que se debe usar un servicio de nube. 

## <a name="what-are-service-certificates"></a>¿Qué son los certificados de servicio?
Certificados de servicio están vinculados a los servicios de nube y habilitar la comunicación segura a y desde el servicio. Por ejemplo, si implementa una función de web, que desee proporcionar un certificado que se puede autenticar un extremo HTTPS expuesto. Certificados de servicio, en la definición de servicio, se implementan automáticamente en la máquina virtual que se está ejecutando una instancia de su rol. 

Puede cargar certificados del servicio de Azure portal clásico mediante el portal de clásico Azure o mediante la API de administración de servicio. Certificados del servicio de están asociados a un servicio de nube específica y asignados a una implementación en el archivo de definición de servicio.

Certificados de servicio pueden administrarse por separado de los servicios y pueden ser administrados por diferentes personas. Por ejemplo, un desarrollador puede cargar un paquete de servicio que hace referencia a un certificado que un administrador de TI ha cargado previamente en Azure. Un administrador de TI puede administrar y cambiar la configuración del servicio sin necesidad de cargar un paquete de servicio nuevo certificado de renovación. Esto es posible porque el nombre lógico para el certificado y la ubicación y nombre de la tienda se especifican en el archivo de definición de servicio, mientras que la huella digital del certificado que se especifica en el archivo de configuración de servicio. Para actualizar el certificado, solo es necesario cargar un nuevo certificado y cambie el valor de huella digital en el archivo de configuración de servicio.

## <a name="what-are-management-certificates"></a>¿Qué son los certificados de administración?
Certificados de administración le permiten autenticar con la API de administración de servicio proporcionadas por Azure clásico. Muchos programas y herramientas (como Visual Studio o el SDK de Azure) usará estos certificados para automatizar la configuración e implementación de los diversos servicios de Azure. Estos no están realmente relacionadas con servicios de nube. 

>[AZURE.WARNING] ¡Ten cuidado! Estos tipos de certificados permitir que cualquier persona que autentica con ellos para administrar la suscripción que están asociados. 

### <a name="limitations"></a>Limitaciones
Hay un límite de certificados de administración de 100 por suscripción. También hay un límite de 100 certificados de administración para todas las suscripciones en el identificador de usuario. del Administrador de un servicio específico Si ya se ha usado el identificador de usuario para el Administrador de la cuenta para agregar 100 certificados de administración y es necesario para los certificados más, puede agregar un administrador de colaboración para agregar los certificados adicionales. 

Antes de agregar más de 100 certificados, vea si puede volver a usar un certificado existente. Usar coadministradores agrega complejidad potencialmente innecesaria para el proceso de administración de certificados.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Crear un nuevo certificado autofirmado
Puede usar cualquier herramienta para crear un certificado autofirmado siempre que sigan estos ajustes:

* Un certificado X.509.
* Contiene una clave privada.
* Creado para el intercambio de claves (archivo .pfx).
* Nombre de asunto debe coincidir con el dominio que se usa para obtener acceso al servicio de nube. 
    > No se puede adquirir un dominio de certificado para la cloudapp.net (o para cualquier relacionados de Azure) SSL; nombre de asunto del certificado debe coincidir con el nombre de dominio personalizado utilizado para tener acceso a la aplicación. Por ejemplo, **contoso.net**, no **contoso.cloudapp.net**.
* Mínimo de cifrado de 2048 bits.
* **Solo certificado de servicio**: certificado de cliente debe estar en el almacén de certificados *personales* .

Existen dos métodos sencillos para crear un certificado en Windows, con la `makecert.exe` utilidad o IIS.

### <a name="makecertexe"></a>Makecert.exe

Esta utilidad está desusada y ya no se documenta aquí. Consulte [este artículo MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968) para obtener más información.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Si desea usar el certificado con una dirección IP en lugar de un dominio, use la dirección IP en el parámetro - DnsName.


Si desea usar este [certificado con el portal de administración](../azure-api-management-certs.md), exportar a un archivo **.cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Hay muchas páginas en internet que explican cómo hacerlo con IIS. [Aquí](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) es una excelente encontrar creo que sí se explica también. 

### <a name="java"></a>Java
Puede usar Java para [crear un certificado](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[En este](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) artículo se describe cómo crear certificados con SSH.

## <a name="next-steps"></a>Pasos siguientes

[Cargar el certificado de servicio en el portal de clásico de Azure](cloud-services-configure-ssl-certificate.md) (o el [portal de Azure](cloud-services-configure-ssl-certificate-portal.md)).

Cargar un [certificado de la API de administración](../azure-api-management-certs.md) en el portal clásico Azure.

>[AZURE.NOTE] El portal de Azure no utiliza certificados de administración para tener acceso a la API sino que utiliza cuentas de usuario.
