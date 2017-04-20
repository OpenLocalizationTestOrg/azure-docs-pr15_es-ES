<properties 
    pageTitle="Cómo crear un ASE ILB uso de plantillas de administrador de recursos de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo crear un ASE del equilibrador de carga interno con plantillas de administrador de recursos de Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Cómo crear un ASE ILB uso de plantillas de administrador de recursos de Azure

## <a name="overview"></a>Información general ##
Entornos de servicio de aplicaciones pueden crearse con una dirección de una red virtual interno en lugar de pública VIP.  Esta dirección interna proporciona un componente de Azure denominado equilibrador de carga interno (ILB).  Un ASE ILB pueden crearse con el portal de Azure.  También pueden crearse con la automatización mediante el Administrador de recursos de Azure plantillas.  En este artículo se recorre los pasos y sintaxis necesaria para crear un ASE ILB con las plantillas de administrador de recursos de Azure.

Existen tres pasos en automatizar la creación de un ASE ILB:
1. La base ASE se crea por primera vez en una red virtual mediante una dirección del equilibrador de carga interno en lugar de pública VIP.  Como parte de este paso, se asigna un nombre de dominio raíz a ASE de ILB.
2. Una vez creada la ASE ILB, se carga un certificado SSL.  
3. El certificado SSL cargado explícitamente se asigna a la ASE ILB como su certificado SSL de "predeterminado".  Se usará este certificado SSL para el tráfico SSL a aplicaciones en la ASE ILB cuando se tratan las aplicaciones con el dominio raíz comunes asignado a la ASE (por ejemplo, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Creación de la Base ILB ASE ##
Un ejemplo de la plantilla de administrador de recursos de Azure y su archivo de parámetros asociados, están disponibles en GitHub [aquí][quickstartilbasecreate].

La mayoría de los parámetros en el archivo *azuredeploy.parameters.json* es comunes con la creación de ASEs ILB, así como ASEs enlazados a una pública VIP.  La lista de llamadas de parámetros de nota especial, o que son únicas, al crear un ASE ILB:


- *interalLoadBalancingMode*: en la mayoría de los casos establecer esta opción para 3, lo que significa el tráfico HTTP/HTTPS en puertos 80 y 443 tanto los datos de control de puertos de canal de escucha por el servicio FTP en la ASE se enlazarán a un ILB asigna la dirección de una red virtual interna.  Si en su lugar, esta propiedad está establecida en 2, el servicio de FTP relacionado con puertos (canales de control y datos) se enlazarán a una dirección ILB, mientras que el tráfico HTTP/HTTPS permanecerá en la dirección VIP pública.
-  *dnsSuffix*: este parámetro define el dominio raíz predeterminado que va a asignar a la ASE.  En la variación pública del servicio de aplicación de Azure, el dominio raíz de forma predeterminada para todas las aplicaciones web es *azurewebsites.net*.  Sin embargo como un ASE ILB es interno a la red virtual de un cliente, no tiene sentido usar el dominio raíz del valor predeterminado del servicio público.  En su lugar, un ASE ILB debería tener un dominio raíz predeterminado que tenga sentido para su uso dentro de una red virtual interno de la empresa.  Por ejemplo, una empresa de Contoso hipotética puede usar el dominio raíz predeterminado *interno contoso.com* para las aplicaciones que se van a utilizar solo puede resolver y accesible en una red virtual de Contoso. 
-  *ipSslAddressCount*: este parámetro automáticamente valor predeterminado es un valor de 0 en el archivo *azuredeploy.json* porque ILB ASEs solo tiene una dirección ILB.  No existen direcciones IP SSL explícitos para un ASE ILB, por lo tanto, el grupo de direcciones IP SSL para un ASE ILB debe estar establecido en cero, en caso contrario, se producirá un error de aprovisionamiento. 

Una vez que se ha rellenado el archivo *azuredeploy.parameters.json* para un ASE ILB, a continuación, pueden crearse ASE de ILB con el siguiente fragmento de código de Powershell.  Cambiar el archivo rutas de acceso para que coincida con donde se encuentran los archivos de plantilla de administrador de recursos de Azure en su equipo.  Recuerde proporcionar sus propios valores para el nombre de la implementación de administrador de recursos de Azure y el nombre del grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

El Administrador de recursos después de Azure plantilla se enviará tardará unas cuantas horas ASE de ILB que debe crearse.  Una vez completada la creación, la ASE ILB se muestran en el portal UX en la lista de entornos de aplicación de servicio de la suscripción que activó la implementación.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Cargar y configurar el certificado SSL de "Predeterminado" ##

Una vez creada la ASE ILB, un certificado SSL debe ser asociado con la ASE certificado SSL "predeterminado" se usa para establecer las conexiones de SSL para las aplicaciones.  Continuar con el ejemplo de Contoso Corporation hipotético, si la ASE predeterminado DNS sufijo es *interno contoso.com*, a continuación, una conexión a *https://some-random-app.internal-contoso.com* requiere un certificado SSL válido para **.internal contoso.com*. 

Existen diversas maneras de obtener un certificado SSL válido incluido entidades emisoras internas, adquirir un certificado de un emisor externo y a continuación, usando un certificado autofirmado.  Independientemente del origen del certificado SSL, deben configurarse correctamente los siguientes atributos de certificado:

- *Asunto*: este atributo debe estar establecido en **here.com-su-raíz de dominio*
- *Nombre de asunto alternativo*: este atributo debe incluir ambas * *su-raíz-dominio-here.com*, y * *.scm.your-raíz-dominio-here.com*.  El motivo de la segunda entrada es que conexiones de SSL en el sitio SCM/Kudu asociada a cada aplicación se realizará mediante una dirección de la forma de *your-app-name.scm.your-root-domain-here.com*.

Con un certificado SSL en mano, se necesitan dos pasos de preparación adicionales.  El certificado SSL debe estar convertir/guardado como un archivo .pfx.  Recuerde que el archivo .pfx debe incluir todas las intermedias y certificados de raíz y también debe proteger con contraseña.

A continuación, el archivo .pfx resultante debe convertirse en una cadena de base 64 porque el certificado SSL se cargarán mediante una plantilla de administrador de recursos de Azure.  Como administrador de recursos de Azure plantillas son archivos de texto, el archivo .pfx debe convertirse en una cadena de base 64 para que pueda incluirse como un parámetro de la plantilla.

El fragmento de código de Powershell a continuación muestra un ejemplo de generar un certificado autofirmado, exporte el certificado como un archivo .pfx, convertir el archivo .pfx en una base de 64 cadena codificada y, a continuación, guardar la base de 64 codificado cadena en un archivo independiente.  El código de Powershell de codificación base 64 es una adaptación del [Blog de secuencias de comandos de Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Una vez que se ha generado y se convierten en una base de 64 codificado correctamente el certificado SSL de cadena, la plantilla de administrador de recursos de Azure de ejemplo en GitHub para [Configurar el certificado SSL predeterminado] [ configuringDefaultSSLCertificate] puede usarse.

A continuación se muestran los parámetros del archivo *azuredeploy.parameters.json* :

- *appServiceEnvironmentName*: el nombre de la ASE ILB está configurando.
- *existingAseLocation*: cadena de texto que contiene la región de Azure donde se ha implementado la ASE ILB.  Por ejemplo: "Sur Central de EE".
- *pfxBlobString*: el based64 codificado representación de cadena del archivo .pfx.  El fragmento de código mostrado anteriormente desea copiar la cadena contenida en "exportedcert.pfx.b64" y pegarla como el valor del atributo *pfxBlobString* .
- *contraseña*: la contraseña que utiliza para proteger el archivo .pfx.
- *certificateThumbprint*: huella digital del certificado.  Si recupera este valor de Powershell (por ejemplo, *$certificate. Huella digital* desde el fragmento de código anterior), puede usar el valor como-es.  Sin embargo, si copia el valor en el cuadro de diálogo del certificado de Windows, recuerde eliminar los espacios iniciales.  La *certificateThumbprint* debe ser similar: AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: un identificador de cadena descriptivo de su elección que usa para la identidad del certificado.  Como parte del identificador único administrador de recursos de Azure, se utiliza el nombre de la entidad *Microsoft.Web/certificates* que representa el certificado SSL.  Final **debe** nombre con el sufijo siguiente: \_yourASENameHere_InternalLoadBalancingASE.  Este sufijo se usa en el portal como un indicador de que el certificado se utiliza para proteger un ASE habilitado ILB.


A continuación se muestra un ejemplo de *azuredeploy.parameters.json* abreviado:


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Una vez que se ha rellenado el archivo *azuredeploy.parameters.json* , el certificado SSL de forma predeterminada se puede configurar mediante el siguiente fragmento de código de Powershell.  Cambiar el archivo rutas de acceso para que coincida con donde se encuentran los archivos de plantilla de administrador de recursos de Azure en su equipo.  Recuerde proporcionar sus propios valores para el nombre de la implementación de administrador de recursos de Azure y el nombre del grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Después de enviar la plantilla de administrador de recursos de Azure tardará unos minutos 40 minutos por ASE front-end para aplicar el cambio.  Por ejemplo, con un ASE predeterminado tamaño con dos servidores, la plantilla tardará aproximadamente una hora y veinte minutos para completar.  Mientras se está ejecutando la plantilla la ASE no podrá escalar.  

Una vez completada la plantilla, aplicaciones en la ASE ILB pueden tener acceso a través de HTTPS y las conexiones se protegen mediante el certificado SSL de forma predeterminada.  Cuando se tratan aplicaciones en la ASE ILB mediante una combinación de nombre de la aplicación más el nombre de host de forma predeterminada, se usará el certificado SSL de forma predeterminada.  Por ejemplo *https://mycustomapp.internal-contoso.com* utilizar el certificado SSL de forma predeterminada para **.internal contoso.com*.

Sin embargo, igual que las aplicaciones que se ejecuta en el servicio público de varios inquilino, los desarrolladores pueden también configurar nombres de host personalizados para las aplicaciones individuales y configure enlaces de certificado SSL SNI únicos para aplicaciones individuales.  


## <a name="getting-started"></a>Introducción

Para empezar con entornos de servicio de aplicación, vea [Introducción a entorno de aplicación de servicio](app-service-app-service-environment-intro.md)

Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
