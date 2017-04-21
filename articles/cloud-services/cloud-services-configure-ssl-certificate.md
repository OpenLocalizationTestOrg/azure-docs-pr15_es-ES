<properties 
    pageTitle="Configurar SSL para un servicio de nube (clásico) | Microsoft Azure" 
    description="Obtenga información sobre cómo especificar un extremo HTTPS para un rol web y cómo cargar un certificado SSL para proteger la aplicación." 
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
    ms.date="10/04/2016"
    ms.author="adegeo"/>




# <a name="configuring-ssl-for-an-application-in-azure"></a>Configuración de SSL para una aplicación en Azure

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-configure-ssl-certificate-portal.md)
- [Portal de clásico de Azure](cloud-services-configure-ssl-certificate.md)

Secure Socket Layer (SSL) cifrado es el método más común de proteger los datos enviados a través de internet. Esta tarea común explica cómo especificar un extremo HTTPS para un rol web y cómo cargar un certificado SSL para proteger la aplicación.

> [AZURE.NOTE] Los procedimientos descritos en esta tarea se aplican a servicios de nube de Azure. para los servicios de aplicación, consulte [este](../app-service-web/web-sites-configure-ssl-certificate.md) artículo.

Esta tarea utiliza una implementación de producción. Al final de este tema se proporciona información sobre el uso de una implementación de ensayo.

Lea [este](cloud-services-how-to-create-deploy.md) artículo en primer lugar, si no ha creado un servicio de nube.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]


## <a name="step-1-get-an-ssl-certificate"></a>Paso 1: Obtener un certificado SSL

Para configurar SSL para una aplicación, debe obtener un certificado SSL firmado por un certificado de entidad emisora (CA), un tercero de confianza que emite certificados para ello. Si ya tiene una, debe obtener uno de una compañía que vende certificados SSL.

El certificado debe cumplir los siguientes requisitos para los certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves, puede exportar a un archivo de intercambio de información Personal (.pfx).
-   Nombre de asunto del certificado debe coincidir con el dominio que se usa para obtener acceso al servicio de nube. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio cloudapp.net. Debe adquirir un nombre de dominio personalizado para utilizarlos al tener acceso al servicio. Cuando se solicita un certificado de una entidad emisora de certificados, el nombre del certificado asunto debe coincidir con el nombre de dominio personalizado utilizado para tener acceso a la aplicación. Por ejemplo, si el nombre de dominio personalizado es **contoso.com** podría solicitar un certificado de la entidad emisora de certificados para * **. contoso.com** o * *www.contoso.com**.
-   El certificado debe usar un mínimo de cifrado de 2048 bits.

Con fines de prueba, puede [crear](cloud-services-certs-create.md) y usar un certificado autofirmado. Un certificado autofirmado no se autentica a través de una entidad emisora de certificados y puede usar el dominio cloudapp.net como la dirección URL del sitio Web. Por ejemplo, la tarea siguiente utiliza un certificado autofirmado en los que el nombre común (CN) utilizado en el certificado es **sslexample.cloudapp.net**.

A continuación, debe incluir información sobre el certificado en la definición del servicio y los archivos de configuración de servicio.

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Paso 2: Modificar los archivos de definición y configuración de servicio

La aplicación debe estar configurada para utilizar el certificado y debe agregarse un extremo HTTPS. Como resultado, la definición de servicio y los archivos de configuración de servicio necesitan actualizarse.

1.  En su entorno de desarrollo, abra el archivo de definición de servicio (CSDEF), agregue una sección de **certificados** dentro de la sección **WebRole** e incluir la información siguiente sobre el certificado (y certificados intermedios):

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="My"
                             permissionLevel="limitedOrElevated" />
                <!-- IMPORTANT! Unless your certificate is either
                self-signed or signed directly by the CA root, you
                must include all the intermediate certificates
                here. You must list them here, even if they are
                not bound to any endpoints. Failing to list any of
                the intermediate certificates may cause hard-to-reproduce
                interoperability problems on some clients.-->
                <Certificate name="CAForSampleCertificate"
                             storeLocation="LocalMachine"
                             storeName="CA"
                             permissionLevel="limitedOrElevated" />
            </Certificates>
        ...
        </WebRole>

    La sección **certificados** define el nombre de nuestro certificado, su ubicación y el nombre de la tienda donde se encuentra.
    
    Permisos (`permisionLevel` atributo) se pueden establecer para uno de los siguientes valores:

  	| Valor de permiso  | Descripción |
  	| ----------------  | ----------- |
  	| limitedOrElevated | **(Predeterminado)** Todos los procesos de rol pueden tener acceso a la clave privada. |
  	| elevados          | Solo los procesos elevados pueden tener acceso a la clave privada.|

2.  En el archivo de definición de servicio, agregar un elemento de **InputEndpoint** dentro de la sección de **extremos** para habilitar HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  En el archivo de definición de servicio, agregue un elemento **de enlace** dentro de la sección de **sitios** . En esta sección se agrega un enlace HTTPS para asignar el extremo a su sitio:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Se han completado todos los cambios necesarios para el archivo de definición de servicio, pero todavía debe agregar la información del certificado para el archivo de configuración de servicio.

4.  En el archivo de configuración de servicio (CSCFG), ServiceConfiguration.Cloud.cscfg, agregue una sección de **certificados** dentro de la sección de la **función** , reemplazar el valor de la huella digital de ejemplo que se muestra a continuación con del certificado:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
                <Certificate name="CAForSampleCertificate"
                    thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(El ejemplo anterior utiliza **sha1** para el algoritmo de huella digital. Especifique el valor adecuado para el algoritmo de huella digital del certificado).

Ahora que se han actualizado la definición de servicio y los archivos de configuración de servicio, empaquete la implementación para cargar en Azure. Si está utilizando **cspack**, no use la marca **/generateConfigurationFile** , mientras sobrescribe la información del certificado que ha insertado.

## <a name="step-3-upload-a-certificate"></a>Paso 3: Cargar el certificado

Se ha actualizado el paquete de implementación para utilizar el certificado y se ha agregado un extremo HTTPS. Ahora puede cargar el paquete y el certificado en Azure con el portal de clásico de Azure.

1. Inicie sesión en el [portal de clásico de Azure][]. 
2. En el panel de navegación de la izquierda, haga clic en **Servicios en la nube** .
3. Haga clic en el servicio de nube deseada.
4. Haga clic en la ficha **certificados** .

    ![Haga clic en la ficha certificados](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Haga clic en el botón **cargar** .

    ![Cargar](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Proporcione el **archivo**, **contraseña**, y haga clic en **completado** (la marca de verificación).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Paso 4: Conectarse a la instancia de rol mediante HTTPS

Ahora que la implementación está en funcionamiento en Azure, puede conectarse a través de HTTPS.

1.  En el portal de Azure clásico, seleccione la implementación, haga clic en el vínculo en la **Dirección URL del sitio**.

    ![Determinar la dirección URL del sitio][2]

2.  En el explorador web, modifique el vínculo para que use **https** en lugar de **http**y, a continuación, visite la página.

    >[AZURE.NOTE] Si está utilizando un certificado autofirmado, cuando examine un extremo HTTPS que está asociada con el certificado autofirmado que es posible que vea un error de certificado en el explorador. Uso de un certificado firmado por una entidad de certificación elimina este problema. mientras tanto, puede pasar por alto el error. (Otra opción es agregar el certificado autofirmado al almacén de certificados de entidad emisora de certificados de confianza del usuario).

    ![Sitio web de ejemplo SSL][3]

Si desea utilizar SSL para una implementación provisional en lugar de una implementación de producción, primero debe determinar la dirección URL que se utiliza para la implementación de ensayo. Implementar el servicio de nube en el entorno de ensayo sin incluir un certificado o cualquier información del certificado. Una vez implementado, puede determinar la dirección URL basado en GUID, que se muestra en el campo de **Dirección URL del sitio** del portal clásica Azure. Crear un certificado con el nombre común (CN) igual a la dirección URL de basado en GUID (por ejemplo, **32818777 6e77 4ced a8fc 57609d404462.cloudapp.net**). Usar el portal de clásico Azure para agregar el certificado en el servicio de nube preconfigurada. A continuación, agregue la información del certificado para los archivos CSDEF y CSCFG, empaquetar su aplicación y actualizar su implementación preconfigurada para usar el nuevo paquete.

## <a name="next-steps"></a>Pasos siguientes

* [Configuración general de su servicio de nube](cloud-services-how-to-configure.md).
* Obtenga información sobre cómo [implementar un servicio de nube](cloud-services-how-to-create-deploy.md).
* Configurar un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administrar el servicio de nube](cloud-services-how-to-manage.md).


  [Portal de clásico de Azure]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  
