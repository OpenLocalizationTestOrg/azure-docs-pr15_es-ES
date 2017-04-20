<properties
    pageTitle="Enlace de certificados SSL con PowerShell"
    description="Obtenga información sobre cómo enlazar certificados SSL a la aplicación web de PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure enlace de certificado SSL de servicios de aplicación, con PowerShell #

Con el lanzamiento de Microsoft Azure PowerShell versión 1.1.0 se ha agregado un nuevo cmdlet que desea dar al usuario la capacidad de enlazar certificados SSL de nuevo o existentes a una aplicación Web existente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para obtener información sobre el uso de administrador de recursos de Azure según Azure PowerShell cmdlets para administrar las aplicaciones Web comprobar el [Administrador de recursos de Azure en función de comandos de PowerShell para la aplicación Web de Azure](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Cargar y enlazar un nuevo certificado SSL ##

Escenario: El usuario desea enlazar un certificado SSL a uno de sus aplicaciones web.

Conocer el nombre del grupo de recursos que contiene la aplicación web, el nombre de la aplicación web, la ruta del archivo .pfx certificado en el equipo del usuario, la contraseña para el certificado y el nombre de host personalizado, podemos utilizar el siguiente comando PowerShell para crear ese enlace SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Tenga en cuenta que, antes de agregar un enlace de SSL para una aplicación web, debe tener un nombre de host (dominio personalizado) que ya ha configurado. Si el nombre de host no está configurado, se producirá un error 'hostname' no existe mientras se ejecuta AzureRmWebAppSSLBinding de nuevo. Puede agregar un nombre de host directamente desde el portal o el uso de PowerShell de Azure. Puede ser el siguiente fragmento de PowerShell para configurar el nombre de host antes de ejecutar AzureRmWebAppSSLBinding de nuevo.   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
Es importante comprender que el cmdlet Set-AzureRmWebApp sobrescribe los nombres de host para la aplicación web. Por lo tanto, el fragmento de código de PowerShell anterior es anexar a la lista de los nombres de host para la aplicación web existente.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Cargar y enlazar un certificado SSL existente ##

Escenario: El usuario desea enlazar un certificado SSL previamente cargado a uno de sus aplicaciones web.

Que podemos disponer de la lista de certificados ya cargado en un grupo de recursos específicos mediante el comando siguiente

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Tenga en cuenta que los certificados son locales a una ubicación específica y un grupo de recursos, el usuario necesita volver a cargar el certificado si la aplicación web configurado está en una ubicación diferente y grupo otros recursos que del certificado sea necesario 

Conocer el nombre del grupo de recursos que contiene la aplicación web, el nombre de la aplicación web, la huella digital del certificado y el nombre de host personalizado, podemos utilizar el siguiente comando PowerShell para crear ese enlace SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Eliminar un enlace SSL existente  ##

Escenario: El usuario desea eliminar un enlace SSL existente.

Conocer el nombre del grupo de recursos que contiene la aplicación web, el nombre de la aplicación web y el nombre de host personalizado, podemos utilizar el siguiente comando PowerShell para quitar ese enlace SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Tenga en cuenta que, si el enlace SSL eliminado era el último enlace con el certificado que ubicación, de forma predeterminada, el certificado se eliminará, si el usuario desea conservar el certificado que puede usar la opción DeleteCertificate para mantener el certificado

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Referencias ###
- [Administrador de recursos de Azure en función de comandos de PowerShell aplicación Web de Azure](app-service-web-app-azure-resource-manager-powershell.md)
- [Introducción al entorno de servicio de aplicaciones](app-service-app-service-environment-intro.md)
- [Usar PowerShell Azure con el Administrador de recursos Azure](../powershell-azure-resource-manager.md)
