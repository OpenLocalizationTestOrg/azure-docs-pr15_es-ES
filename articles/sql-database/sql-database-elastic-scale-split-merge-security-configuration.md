<properties 
    pageTitle="Configuración de seguridad de la combinación de división | Microsoft Azure" 
    description="Configurar x409 certificados de cifrado" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Configuración de seguridad de la combinación de división  

Para usar el servicio de combinación o división, debe configurar correctamente la seguridad. El servicio es parte de la característica de escala elástico de base de datos de SQL de Microsoft Azure. Para obtener más información, vea [elástico división de escala y combinar Tutorial de servicio](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configuración de certificados

Se configuran los certificados de dos maneras. 

1. [Para configurar el certificado SSL](#To-Configure-the-SSL#Certificate)
2. [Para configurar los certificados de cliente](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Para obtener certificados

Pueden obtener certificados desde público entidades emisoras de certificados (CA) o desde el [Servicio de certificados de Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Estos son los métodos preferidos para obtener certificados.

Si estas opciones no están disponibles, puede generar **certificados firmados**.
 
## <a name="tools-to-generate-certificates"></a>Herramientas para generar certificados

* [Makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2Pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Para ejecutar las herramientas

* Desde el programador símbolo para Visual Studio, vea el [símbolo del sistema de Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Si instaló, vaya al:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obtener el WDK de [Windows 8.1: Descargar Kit y herramientas](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Para configurar el certificado SSL
Se requiere un certificado SSL para cifrar la comunicación y autenticar el servidor. Elija los siguientes tres escenarios más apropiado y ejecutar todos los pasos:

### <a name="create-a-new-self-signed-certificate"></a>Crear un nuevo certificado autofirmado

1.    [Crear un certificado autofirmado](#Create-a-Self-Signed-Certificate)
2.    [Crear archivo PFX de certificado de SSL autofirmado](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Cargar certificado SSL para el servicio de nube](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Actualizar el certificado SSL en el archivo de configuración de servicio](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importar entidad emisora de certificados SSL](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Usar un certificado existente desde el almacén de certificados
1. [Exportar certificado SSL de almacén de certificados](#Export-SSL-Certificate-From-Certificate-Store)
2. [Cargar certificado SSL para el servicio de nube](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Actualizar el certificado SSL en el archivo de configuración de servicio](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Usar un certificado existente en un archivo PFX

1. [Cargar certificado SSL para el servicio de nube](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Actualizar el certificado SSL en el archivo de configuración de servicio](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Para configurar los certificados de cliente
Los certificados de cliente son necesarios para autenticar solicitudes al servicio. Elija los siguientes tres escenarios más apropiado y ejecutar todos los pasos:

### <a name="turn-off-client-certificates"></a>Desactivar los certificados de cliente
1.    [Desactivar la autenticación basada en el certificado de cliente](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Certificados de cliente autofirmado nueva
1.    [Crear una entidad de certificación autofirmado](#Create-a-Self-Signed-Certification-Authority)
2.    [Cargar el certificado de entidad emisora de certificados para servicios de nube](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Certificado de entidad emisora de certificados de actualización en el archivo de configuración de servicio](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Certificados de cliente](#Issue-Client-Certificates)
5.    [Crear archivos PFX certificados de cliente](#Create-PFX-files-for-Client-Certificates)
6.    [Certificado de cliente de importación](#Import-Client-Certificate)
7.    [Copiar certificados de huella digital de cliente](#Copy-Client-Certificate-Thumbprints)
8.    [Configurar clientes permitidos en el archivo de configuración de servicio](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Utilizar los certificados de cliente existente
1.    [Buscar la clave pública de entidad emisora de certificados](#Find-CA-Public Key)
2.    [Cargar el certificado de entidad emisora de certificados para servicios de nube](#Upload-CA-certificate-to-cloud-service)
3.    [Certificado de entidad emisora de certificados de actualización en el archivo de configuración de servicio](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copiar certificados de huella digital de cliente](#Copy-Client-Certificate-Thumbprints)
5.    [Configurar clientes permitidos en el archivo de configuración de servicio](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configurar la comprobación de revocación de certificado de cliente](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Direcciones IP permitidas

Acceso a los extremos del servicio se puede restringir a determinados intervalos de direcciones IP.

## <a name="to-configure-encryption-for-the-store"></a>Para configurar el cifrado de la tienda

Se requiere un certificado para cifrar las credenciales almacenadas en el almacén de metadatos. Elija los siguientes tres escenarios más apropiado y ejecutar todos los pasos:

### <a name="use-a-new-self-signed-certificate"></a>Use un nuevo certificado autofirmado

1.     [Crear un certificado autofirmado](#Create-a-Self-Signed-Certificate)
2.     [Crear archivo PFX autofirmado certificado de cifrado](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Cargar el certificado de cifrado para el servicio de nube](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Actualizar el certificado de cifrado en el archivo de configuración de servicio](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utilizar un certificado existente desde el almacén de certificados

1.     [Exportar certificado de cifrado del almacén de certificados](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Cargar el certificado de cifrado para el servicio de nube](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Actualizar el certificado de cifrado en el archivo de configuración de servicio](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utilizar un certificado existente en un archivo PFX

1.     [Cargar el certificado de cifrado para el servicio de nube](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Actualizar el certificado de cifrado en el archivo de configuración de servicio](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>La configuración predeterminada

La configuración predeterminada impide que todo el acceso al extremo HTTP. Esta es la configuración recomendada, dado que las solicitudes de estos extremos pueden llevar a información confidencial, como las credenciales de la base de datos.
La configuración predeterminada permite el acceso al extremo HTTPS. Esta configuración puede restringirse aún más.

### <a name="changing-the-configuration"></a>Cambiar la configuración

El grupo de reglas de control de acceso que se aplican a y extremo configurados en el **<EndpointAcls>** sección en el **archivo de configuración de servicio**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Las reglas de un grupo de control de acceso se configuran en un <AccessControl name=""> sección del archivo de configuración de servicio. 

El formato se explica en la documentación de listas de Control de acceso de red.
Por ejemplo, para permitir que solo direcciones IP en el intervalo 100.100.0.0 a 100.100.255.255 para tener acceso al extremo HTTPS, las reglas tendría un aspecto similar a este:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Denegación de prevención de servicio

Hay dos mecanismos diferentes compatibles para detectar y evitar ataques de rechazo de servicio:

*    Restringir el número de solicitudes simultáneas por host remoto (desactivada de forma predeterminada)
*    Restringir la tasa de acceso por host remoto (de forma predeterminada)

Se basan en las características más documentadas en seguridad de IP dinámica en IIS. Al cambiar esta configuración tenga cuidado de los siguientes factores:

* El comportamiento de los servidores proxy y dispositivos de traducción de direcciones de red sobre la información de host remoto
* Se considera cada solicitud a cualquier recurso en el rol web (por ejemplo, cargar las secuencias de comandos, imágenes, etcetera)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringir el número de accesos simultáneos

Los valores que configurar este comportamiento son:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Cambiar DynamicIpRestrictionDenyByConcurrentRequests en true para habilitar esta protección.

## <a name="restricting-rate-of-access"></a>Restringir la tasa de access

Los valores que configurar este comportamiento son:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configuración de la respuesta a una solicitud denegada

La siguiente configuración configura la respuesta a una solicitud denegada:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consulte la documentación de seguridad de IP dinámica en IIS para otros valores admitidos.

## <a name="operations-for-configuring-service-certificates"></a>Operaciones de configuración de certificados de servicio
Este tema es sólo de referencia. Siga los pasos de configuración de:

* Configurar el certificado SSL
* Configurar los certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Crear un certificado autofirmado
Ejecutar:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

*    -n con la dirección URL del servicio. Caracteres comodín ("CN = * .cloudapp .net") y son compatibles con nombres alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net").
*    -e con la fecha de caducidad de certificado crear una contraseña segura y especificarlo cuando se le solicite.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Crear archivo PFX de certificado SSL autofirmado

Ejecutar:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:
* Exportar la clave privada
* Exportar todas las propiedades extendidas

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportar certificado SSL de almacén de certificados

* Buscar certificado
* Haga clic en Acciones -> todas las tareas -> Exportar...
* Exportar certificado en una. Archivo PFX con estas opciones:
    * Exportar la clave privada
    * Incluir todos los certificados en la ruta de certificación si es posible * exportar todas las propiedades ampliadas

## <a name="upload-ssl-certificate-to-cloud-service"></a>Cargar certificado SSL para el servicio de nube

Cargar certificado con las existentes o generado. Archivo PFX con el par de claves de SSL:

* Escriba la contraseña para proteger la información de clave privada

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Actualizar el certificado SSL en el archivo de configuración de servicio

Actualizar el valor de la huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado en el servicio de nube:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importar entidad emisora de certificados SSL

Siga estos pasos en todas las cuentas u otro equipo que se comunicará con el servicio:

* Haga doble clic en el. Archivo CER en el Explorador de Windows
* En el cuadro de diálogo certificado, haga clic en Instalar certificado...
* Importar el certificado en el almacén de entidades emisoras de certificados raíz de confianza

## <a name="turn-off-client-certificate-based-authentication"></a>Desactivar la autenticación basada en el certificado de cliente

Se admiten solo cliente autenticación basada en formularios y deshabilitarlo permitirá acceso del público a los extremos del servicio, a menos que otros mecanismos están en su lugar (por ejemplo, Microsoft Azure Virtual Network).

Cambiar esta configuración en false en el archivo de configuración de servicio para desactivar la característica:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

A continuación, copie la huella digital del misma como el certificado SSL en la configuración de certificados de entidad emisora de certificados:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Crear una entidad de certificación autofirmado
Ejecute los siguientes pasos para crear un certificado autofirmado para que actúe como una entidad de certificación:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizarla

*    -e con la fecha de expiración de certificados


## <a name="find-ca-public-key"></a>Buscar la clave pública de entidad emisora de certificados

Todos los certificados de cliente se deben haber emitido por una entidad emisora de confianza para el servicio. Buscar la clave pública que la entidad emisora de certificados que emitió al cliente de certificados que se van a usar para la autenticación para cargar el servicio de nube.

Si el archivo con la clave pública no está disponible, la exportación del almacén de certificados:

* Buscar certificado
    * Buscar un certificado de cliente emitido por la misma entidad emisora de certificados
* Haga doble clic en el certificado.
* Seleccione la ficha ruta de certificación en el cuadro de diálogo certificado.
* Haga doble clic en la entrada de la entidad emisora de certificados en la ruta de acceso.
* Tomar notas de las propiedades del certificado.
* Cierre el cuadro de diálogo **certificado** .
* Buscar certificado
    * Búsqueda de la entidad emisora de certificados se indicó anteriormente.
* Haga clic en Acciones -> todas las tareas -> Exportar...
* Exportar certificado en una. CER con estas opciones:
    * **No, no exportar la clave privada**
    * Incluir todos los certificados en la ruta de certificación si es posible.
    * Exportar todas las propiedades extendidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Cargar el certificado de entidad emisora de certificados en el servicio de nube

Cargar certificado con las existentes o generado. CER junto con la clave pública de la entidad emisora de certificados.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certificado de entidad emisora de certificados de actualización en el archivo de configuración de servicio

Actualizar el valor de la huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado en el servicio de nube:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Actualizar el valor de la siguiente configuración con la misma huella:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Certificados de cliente

Cada autorizada para tener acceso al servicio debe tener un certificado de cliente emitido para his/hers exclusivo use y debe elegir que posee una contraseña segura para proteger su clave privada. 

Los pasos siguientes deben realizarse en el mismo equipo donde se genera y se almacena el certificado autofirmado de entidad emisora de certificados:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizar:

* -n con un identificador para que el cliente que se puede autenticar con este certificado
* -e con la fecha de caducidad de certificado
* MyID.pvk y MyID.cer con nombres únicos para este certificado de cliente

Este comando solicitará una contraseña crear y, a continuación, usar una vez. Usar una contraseña segura.

## <a name="create-pfx-files-for-client-certificates"></a>Crear archivos PFX cliente certificados

Para cada certificado de cliente generada, ejecute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizar:

    MyID.pvk and MyID.cer with the filename for the client certificate

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:

* Exportar la clave privada
* Exportar todas las propiedades extendidas
* La persona a la que se publica este certificado debe elegir la contraseña de exportación

## <a name="import-client-certificate"></a>Certificado de cliente de importación

Cada persona para la que se emitió un certificado de cliente debe importar el par de claves en los equipos que usará para comunicarse con el servicio:

* Haga doble clic en el. Archivo PFX en el Explorador de Windows
* Importar el certificado en el Personal almacenar con al menos esta opción:
    * Incluir todas las propiedades extendidas activadas

## <a name="copy-client-certificate-thumbprints"></a>Copiar certificados de huella digital de cliente
Cada persona para la que se emitió un certificado de cliente debe seguir estos pasos para obtener la huella digital del his/hers certificado que se agregará al archivo de configuración de servicio:
* Ejecutar certmgr.exe
* Seleccione la pestaña Personal
* Haga doble clic en el certificado de cliente que se usará para la autenticación
* En el cuadro de diálogo certificado que se abre, seleccione la ficha Detalles
* Asegúrese de que mostrar está mostrando todos
* Seleccione el campo denominado huella digital en la lista
* Copie el valor de la huella digital **eliminar caracteres Unicode no visibles delante al primer dígito** eliminar todos los espacios

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar a clientes de permitidos en el archivo de configuración de servicio

Actualizar el valor de la siguiente configuración en el archivo de configuración de servicio con una lista de valores separados por comas de las huellas digitales los certificados de cliente permitidos el acceso al servicio:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurar la comprobación de revocación de certificado de cliente

No revisar la configuración predeterminada con la entidad emisora de certificados para el estado de revocación del certificado de cliente. Para activar las comprobaciones, si la entidad emisora de certificados que el cliente de certificados es compatible con estos controles, cambie la opción siguiente con uno de los valores definidos en la enumeración X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Crear archivo PFX certificados de cifrado autofirmado

Para obtener un certificado de cifrado, ejecute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizar:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:
*    Exportar la clave privada
*    Exportar todas las propiedades extendidas
*    Necesitará la contraseña al cargar el certificado en el servicio de nube.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportar certificado de cifrado del almacén de certificados

*    Buscar certificado
*    Haga clic en Acciones -> todas las tareas -> Exportar...
*    Exportar certificado en una. Archivo PFX con estas opciones: 
  *    Exportar la clave privada
  *    Incluir todos los certificados en la ruta de certificación si es posible 
*    Exportar todas las propiedades extendidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Cargar el certificado de cifrado al servicio de nube

Cargar certificado con las existentes o generado. Archivo PFX con el par de claves de cifrado:

* Escriba la contraseña para proteger la información de clave privada

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Actualizar el certificado de cifrado en el archivo de configuración de servicio

Actualizar el valor de la huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado en el servicio de nube:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Operaciones de certificado comunes

* Configurar el certificado SSL
* Configurar los certificados de cliente

## <a name="find-certificate"></a>Buscar certificado

Siga estos pasos:

1. Ejecute mmc.exe.
2. Archivo -> Agregar o quitar complemento...
3. Seleccione **certificados**.
4. Haga clic en **Agregar**.
5. Elija la ubicación del almacén de certificados.
6. Haga clic en **Finalizar**.
7. Haga clic en **Aceptar**.
8. Expanda **certificados**.
9. Expanda el nodo del almacén de certificados.
10. Expanda el nodo secundario de certificado.
11. Seleccione un certificado en la lista.

## <a name="export-certificate"></a>Exportar certificado
En el **Asistente para exportación de certificados**:

1. Haga clic en **siguiente**.
2. Seleccione **Sí**, **exportar la clave privada**.
3. Haga clic en **siguiente**.
4. Seleccione el formato de archivo de salida deseado.
5. Active las opciones que desee.
6. Compruebe la **contraseña**.
7. Escriba una contraseña segura y confírmela.
8. Haga clic en **siguiente**.
9. Escriba o busque dónde desea almacenar el certificado de un nombre de archivo (usar una. Extensión PFX).
10. Haga clic en **siguiente**.
11. Haga clic en **Finalizar**.
12. Haga clic en **Aceptar**.

## <a name="import-certificate"></a>Importar certificado

En el Asistente para importación de certificado:

1. Seleccione la ubicación del almacén.

    * Seleccione el **Usuario actual** si sólo procesos que se ejecutan en el usuario actual tendrá acceso al servicio
    * Seleccione el **Equipo Local** si otros procesos en este equipo tendrá acceso al servicio
2. Haga clic en **siguiente**.
3. Si importa desde un archivo, confirme la ruta de acceso del archivo.
4. Si se importa una. Archivo PFX:
    1.     Escriba la contraseña para proteger la clave privada
    2.     Seleccionar opciones de importación
5.     Seleccione los certificados de "Colocar" en el siguiente almacén
6.     Haga clic en **Examinar**.
7.     Seleccione el almacén que desee.
8.     Haga clic en **Finalizar**.
       
    * Si se ha elegido el almacén de entidad emisora de certificados raíz de confianza, haga clic en **Sí**.
9.     Haga clic en **Aceptar** en todas las ventanas de diálogo.

## <a name="upload-certificate"></a>Cargar el certificado

En el [Portal de Azure](https://portal.azure.com/)

1. Seleccione **servicios en la nube**.
2. Seleccione el servicio de nube.
3. En el menú superior, haga clic en **certificados**.
4. En la barra de la parte inferior, haga clic en **cargar**.
5. Seleccione el archivo de certificado.
6. Si es un. PFX archivo, escriba la contraseña de la clave privada.
7. Una vez completada, copie la huella digital de certificado de la nueva entrada en la lista.

## <a name="other-security-considerations"></a>Otras consideraciones de seguridad
 
La configuración de SSL que se describe en este documento cifra la comunicación entre el servicio y sus clientes cuando se usa el extremo HTTPS. Esto es importante desde credenciales para el acceso de la base de datos y posiblemente otra información confidencial se incluyen en la comunicación. Sin embargo, tenga en cuenta que el servicio continúa estado interno, incluidas las credenciales de sus tablas internas de la base de datos de SQL de Microsoft Azure que ha proporcionado para el almacenamiento de metadatos en su suscripción de Microsoft Azure. Base de datos se definió como parte de la siguiente configuración en el archivo de configuración de servicio (. Archivo CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Las credenciales almacenadas en esta base de datos están cifradas. Sin embargo, como práctica recomendada, asegúrese de que los roles de web y trabajador de las instalaciones de servicio están actualizados y segura que tienen acceso a la base de datos de metadatos y el certificado utilizado para el cifrado y descifrado de credenciales almacenadas. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
