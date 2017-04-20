<properties
    pageTitle="Proteger el dominio personalizado de la aplicación con HTTPS | Microsoft Azure"
    description="Obtenga información sobre cómo proteger el nombre de dominio personalizado para la aplicación de servicio de la aplicación de Azure configurando un enlace de certificado SSL. También aprenderá a obtener un certificado SSL de varias herramientas."
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cephalin"/>

# <a name="secure-your-apps-custom-domain-with-https"></a>Proteger el dominio personalizado de la aplicación con HTTPS


> [AZURE.SELECTOR]
- [Comprar el certificado SSL en Azure](web-sites-purchase-ssl-web-site.md)
- [Usar el certificado SSL desde cualquier lugar](web-sites-configure-ssl-certificate.md)


Este artículo le muestra cómo habilitar HTTPS para una aplicación web, un aplicación móvil de back-end o una aplicación de API de [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md) que usa un nombre de dominio personalizado. Cubra la autenticación solo servidor. Si necesita autenticación mutua (incluida la autenticación del cliente), consulte [Cómo configurar TLS mutuo autenticación para la aplicación de servicio](app-service-web-configure-tls-mutual-auth.md).

Para proteger con HTTPS una aplicación que tiene un nombre de dominio personalizado, agregue un certificado para ese nombre de dominio. De forma predeterminada, Azure protege el ** \*. azurewebsites.net** dominio comodín con un certificado SSL única, para los clientes ya pueden tener acceso a la aplicación en * *https://*&lt;nombre de aplicación >*. azurewebsites.net**. Pero si desea usar un dominio personalizado, como **www.contoso.com contoso.com**, ****, y ** \*. contoso.com**, el certificado predeterminado no seguro que. Además, como todos los [certificados con caracteres comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), el certificado predeterminado no es tan seguro como utilizar un dominio personalizado y un certificado de dominio personalizado.   

>[AZURE.NOTE] Puede obtener ayuda de expertos de Azure en cualquier momento en los [foros de Azure](https://azure.microsoft.com/support/forums/). Para obtener soporte más personalizado, vaya a [Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

<a name="bkmk_domainname"></a>
## <a name="what-you-need"></a>Lo que necesita
Para proteger su nombre de dominio personalizado con HTTPS, enlaza un certificado SSL personalizado a ese dominio personalizado en Azure. Antes de enlace de un certificado personalizado, debe hacer lo siguiente:

- **Configurar el dominio personalizado** - aplicación de servicio solo permite agregar un certificado de un nombre de dominio que ya está configurado en la aplicación. Para obtener instrucciones, consulte [asignar un nombre de dominio personalizado para una aplicación de Azure](web-sites-custom-domain-name.md). 
- **Escalar a nivel básico o superior** Planes de servicio de aplicaciones en los niveles de precios más bajos no son compatibles con los certificados SSL personalizados. Para obtener instrucciones, consulte [escalar una aplicación en Azure](web-sites-scale.md). 
- **Obtener un certificado SSL** - si ya no tiene uno, debe obtener uno de una [entidad emisora de certificados](http://en.wikipedia.org/wiki/Certificate_authority) (CA) confianza. El certificado debe cumplir los siguientes requisitos:

    - Está firmado por una CA de confianza (sin servidores CA privadas).
    - Contiene una clave privada.
    - Se crea para intercambio de claves y se exporta a una. Archivo PFX.
    - Utiliza un mínimo de cifrado de 2048 bits.
    - Su nombre de asunto coincide con el dominio personalizado que necesita proteger. Para proteger varios dominios con un certificado, debe usar un nombre de comodín (por ejemplo, ** \*. contoso.com**) o especifique valores subjectAltName.
    - Se combina con todos los **[certificados intermedios](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** usado por la entidad emisora de certificados. En caso contrario, pueden surgir problemas de interoperabilidad irreproducibles en algunos clientes.

        >[AZURE.NOTE] La manera más sencilla de obtener un certificado SSL que cumpla todos los requisitos es comprar         [uno en el portal de Azure directamente](web-sites-purchase-ssl-web-site.md). Este artículo le muestra cómo hacerlo manualmente y después asociarlo a su dominio personalizado en la aplicación de servicio.
        >   
        > **Certificados de cifrado de curva elíptica (ECC)** puede trabajar con la aplicación de servicio, pero fuera del ámbito de este artículo. Trabajar con su entidad emisora de certificados en los pasos exactos para crear los certificados ECC.

<a name="bkmk_getcert"></a>
## <a name="step-1-get-an-ssl-certificate"></a>Paso 1. Obtener un certificado SSL

Puesto que entidades emisoras de certificados proporcionan los distintos tipos de certificado SSL a diferentes precios, debe empezar por decidir qué tipo de certificado SSL para comprar. Para proteger un único nombre de dominio (**www.contoso.com**), solo es necesario un certificado básico. Para proteger varios nombres de dominio (**contoso.com** *y* **www.contoso.com** 
*y* **mail.contoso.com**), necesita un [certificado comodín](http://en.wikipedia.org/wiki/Wildcard_certificate) o un certificado con [Nombre alternativo de asunto](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Una vez que sepa qué certificado SSL para comprar, enviar un certificado de firma solicitar (CSR) a una entidad emisora de certificados. Al volver certificado solicitado de la entidad emisora de certificados, a continuación, se genera un archivo .pfx desde el certificado. Puede realizar estos pasos con la herramienta de su elección. Estas son las instrucciones para las herramientas comunes:

- [Pasos de Certreq.exe](#bkmk_certreq) - la utilidad de Windows para la creación de solicitudes de certificados. Ha sido parte de Windows desde Windows XP o Windows Server 2000.
- [El Administrador de IIS pasos](#bkmk_iismgr) - la herramienta de elección si ya está familiarizado con él.
- [Pasos de OpenSSL](#bkmk_openssl) - una [herramienta de código abierto, entre plataformas](https://www.openssl.org). Utilizar para ayudarle a obtener un certificado SSL de cualquier plataforma.
- pasos de los [pasos de subjectAltName mediante OpenSSL](#bkmk_subjectaltname) - para obtener `subjectAltName` certificados.

Si desea probar la configuración de servicio de la aplicación antes de comprar un certificado, puede generar un [certificado autofirmado](https://en.wikipedia.org/wiki/Self-signed_certificate). Este tutorial proporciona dos formas de genere:

- [Certificado autofirmado, Certreq.exe pasos](#bkmk_sscertreq)
- [Certificado autofirmado, OpenSSL pasos](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### <a name="get-a-certificate-using-certreqexe"></a>Obtener un certificado con Certreq.exe

1. Crear un archivo (por ejemplo, **myrequest.txt**), cópielo en el texto siguiente y guárdelo en un directorio de trabajo. Reemplazar el `<your-domain>` marcador de posición con el nombre de dominio personalizado de la aplicación.

        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

    Para obtener más información sobre las opciones en el CSR y otras opciones disponibles, consulte la [documentación de referencia de Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. En el símbolo del sistema, `CD` en el directorio de trabajo y ejecute el siguiente comando para crear el CSR:

        certreq -new myrequest.txt myrequest.csr

    **myRequest.CSR** ahora se crea en el directorio de trabajo actual.

5. Enviar **myrequest.csr** a una entidad emisora de certificados para obtener un certificado SSL. , Cargue el archivo o copie el contenido de un editor de texto en un formulario web.

    Para obtener una lista de entidades emisoras de certificados de confianza de Microsoft, consulte [programa de certificados raíz de confianza de Microsoft: participantes][cas].

6. Una vez que la entidad emisora de certificados ha respondido a usted con un certificado (. CER), guardar archivos en el directorio de trabajo. A continuación, ejecute el comando siguiente para completar el CSR pendiente.

        certreq -accept -user <certificate-name>.cer

    Este comando almacena el certificado terminado en el almacén de certificados de Windows.

6. Si la entidad emisora de certificados utiliza certificados intermedios, instálelas antes de continuar. Normalmente llegan como una descarga independiente desde la entidad emisora de certificados y, en varios formatos para tipos de servidor web diferentes. Seleccione la versión de IIS de Microsoft.

    Una vez que haya descargado los certificados, haga clic en cada uno de ellos en el Explorador de Windows y seleccione  **Instalar certificado**. Usar los valores predeterminados en el **Asistente para importación de certificado**y continuar con la selección **siguiente** hasta que haya completado la importación.

7. Para exportar el certificado SSL del almacén de certificados, presione `Win` + `R` y ejecutar **certmgr.msc** para iniciar el Administrador de certificados. Seleccione **Personal** > **certificados**. En la columna **Emitido para** , debería ver una entrada con su nombre de dominio personalizado y a continuación, la entidad emisora de certificados que usa para generar el certificado en la columna **Emitido por** .

    ![Insertar imagen de administrador del certificado][certmgr]

9. Haga clic en el certificado y seleccione **Todas las tareas** > **Exportar**. En el **Asistente para exportación de certificados**, haga clic en **siguiente**, seleccione **exportar la clave privada**y, a continuación, haga clic en **siguiente** .

    ![Exportar la clave privada][certwiz1]

10. Seleccione el **Intercambio de información Personal: PKCS #12**, **incluir todos los certificados en la ruta de certificación si es posible**y **Exportar todas las propiedades extendidas**. A continuación, haga clic en **siguiente**.

    ![incluir todos los certificados y propiedades extendidas][certwiz2]

11. Seleccione **contraseña**y, a continuación, introduzca y confirme la contraseña. Haga clic en **siguiente**.

    ![especificar una contraseña][certwiz3]

12. Proporcione una ruta de acceso y nombre de archivo para el certificado exportado, con la extensión **.pfx**. Haga clic en **siguiente** para finalizar.

    ![Proporcione una ruta de acceso de archivo][certwiz4]

Ya está listo para cargar el archivo PFX exportado a la aplicación de servicio. Consulte [paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>
### <a name="get-a-certificate-using-the-iis-manager"></a>Obtener un certificado mediante el Administrador de IIS

1. Generar un CSR con el Administrador de IIS para enviar a la entidad emisora de certificados. Para obtener más información acerca de cómo generar un CSR, consulte [Solicitar un certificado de servidor de Internet (IIS 7)][iiscsr].

3. Envíe la CSR a una entidad emisora de certificados para obtener un certificado SSL. Para obtener una lista de entidades emisoras de certificados de confianza de Microsoft, consulte [programa de certificados raíz de confianza de Microsoft: participantes][cas].


3. Completar el CSR con el certificado de la entidad emisora de certificados que se envía a usted. Para obtener más información sobre cómo completar el CSR, consulte [instalar un certificado de servidor de Internet (IIS 7)][installcertiis].

4. Si la entidad emisora de certificados utiliza certificados intermedios, instálelas antes de continuar. Normalmente llegan como una descarga independiente desde la entidad emisora de certificados y, en varios formatos para tipos de servidor web diferentes. Seleccione la versión de IIS de Microsoft.

    Una vez que haya descargado los certificados, haga clic en cada uno de ellos en el Explorador de Windows y seleccione **Instalar certificado**. 
    Usar los valores predeterminados en el **Asistente para importación de certificado**y continuar con la selección **siguiente** hasta que haya completado la importación.

4. Exporte el certificado SSL desde el Administrador de IIS. Para obtener más información sobre cómo exportar el certificado, vea [exportar un certificado de servidor (IIS 7)][exportcertiis]. 

    >[AZURE.IMPORTANT] En el **Asistente para exportación de certificados**, asegúrese de que se seleccione **Sí, exportar la clave privada**  
    >
    >![Exportar la clave privada][certwiz1]  
    >
    > y también seleccionar **Intercambio de información Personal: PKCS #12**, **incluir todos los certificados en la ruta de certificación si es posible**y     **Exportar todas las propiedades extendidas**.
    >
    >![incluir todos los certificados y propiedades extendidas][certwiz2]

Ya está listo para cargar el archivo PFX exportado a la aplicación de servicio. Consulte [paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_openssl"></a>
### <a name="get-a-certificate-using-openssl"></a>Obtener un certificado mediante OpenSSL

1. En una línea de comandos terminal, `CD` en un directorio de trabajo generar una clave privada y CSR ejecutando el siguiente comando:

        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Cuando se le pida, escriba la información adecuada. Por ejemplo:

        Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

        Please enter the following 'extra' attributes to be sent with your certificate request

        A challenge password []:

    Cuando haya terminado, debería tener dos archivos en el directorio de trabajo: **myserver.key** y **server.csr**. 
    El **server.csr** contiene el CSR y necesita **myserver.key** más adelante.

3. Envíe la CSR a una entidad emisora de certificados para obtener un certificado SSL. Para obtener una lista de entidades emisoras de certificados de confianza de Microsoft, consulte [programa de certificados raíz de confianza de Microsoft: participantes][cas].


4. Una vez que la entidad de certificación envía el certificado solicitado, guardarlo en un archivo llamado **myserver.crt** en el directorio de trabajo. Si la entidad emisora de certificados proporciona en un formato de texto, simplemente copie el contenido en **myserver.crt** en un editor de texto y guárdelo. El archivo debe ser similar al siguiente:

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

5. En la línea de comandos terminal, ejecute el comando siguiente para exportar **myserver.pfx** de **myserver.key** y **myserver.crt**:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Cuando se le solicite, definir una contraseña para proteger el archivo .pfx.

    > [AZURE.NOTE] Si la entidad emisora de certificados utiliza certificados intermedios, debe incluir con la `-certfile` parámetro. Normalmente llegan como una descarga independiente desde la entidad emisora de certificados y, en varios formatos para tipos de servidor web diferentes. Seleccione la versión con la `.pem` extensión.
    >
    > Su `openssl -export` comando debe tener un aspecto similar al ejemplo siguiente, que crea un archivo .pfx que incluye los certificados intermedios del archivo **intermedio cets.pem** :
    >  
    > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Ya está listo para cargar el archivo PFX exportado a la aplicación de servicio. Consulte [paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>
### <a name="get-a-subjectaltname-certificate-using-openssl"></a>Obtener un certificado de SubjectAltName mediante OpenSSL

1. Crear un archivo llamado **sancert.cnf**, copie el siguiente texto en él y guardarlo en un directorio de trabajo:

        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----

    En la línea que comienza con `subjectAltName`, reemplace el valor por todos los nombres de dominio que desea proteger (además  `commonName`). Por ejemplo:

        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

    No es necesario cambiar cualquier otro campo, incluidos `commonName`. Se le pedirá que especificarlas en los pasos siguientes.

1. En una línea de comandos terminal, `CD` en el directorio de trabajo y ejecute el siguiente comando:

        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Cuando se le pida, escriba la información adecuada. Por ejemplo:

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

    Una vez que haya terminado, debería tener dos archivos en el directorio de trabajo: **myserver.key** y **server.csr**. 
    El **server.csr** contiene el CSR y necesita **myserver.key** más adelante.

3. Envíe la CSR a una entidad emisora de certificados para obtener un certificado SSL. Para obtener una lista de entidades emisoras de certificados de confianza de Microsoft, consulte [programa de certificados raíz de confianza de Microsoft: participantes][cas].


4. Una vez que la entidad de certificación envía el certificado solicitado, guardarlo en un archivo llamado **myserver.crt**. Si la entidad emisora de certificados proporciona en un formato de texto, simplemente copie el contenido en **myserver.crt** en un editor de texto y guárdelo. El archivo debe tener el siguiente aspecto:

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

5. En la línea de comandos terminal, ejecute el comando siguiente para exportar **myserver.pfx** de **myserver.key** y **myserver.crt**:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Cuando se le solicite, definir una contraseña para proteger el archivo .pfx.

    > [AZURE.NOTE] Si la entidad emisora de certificados utiliza certificados intermedios, debe incluir con la `-certfile` parámetro. Normalmente llegan como una descarga independiente desde la entidad emisora de certificados y, en varios formatos para tipos de servidor web diferentes. Seleccione la versión con la `.pem` extensión).
    >
    > Su `openssl -export` comando debe tener un aspecto similar al ejemplo siguiente, que crea un archivo .pfx que incluye los certificados intermedios del archivo **intermedio cets.pem** :
    >  
    > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Ya está listo para cargar el archivo PFX exportado a la aplicación de servicio. Consulte [paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>
### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Generar un certificado autofirmado con Certreq.exe ###

>[AZURE.IMPORTANT] Certificados autofirmados son solo con fines de prueba. La mayoría de exploradores devuelven errores al visitar un sitio Web que está protegido por un certificado autofirmado. Algunos exploradores aún podrán denegar vaya al sitio. 

1. Crear un archivo de texto (por ejemplo, **mycert.txt**), cópielo en el texto siguiente y guarde el archivo en un directorio de trabajo. Reemplazar el `<your-domain>` marcador de posición con el nombre de dominio personalizado de la aplicación.

        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

    El parámetro importante es `RequestType = Cert`, que especifica un certificado autofirmado. 
    Para obtener más información sobre las opciones en el CSR y otras opciones disponibles, consulte la [documentación de referencia de Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. En el símbolo del sistema, `CD` a su directorio de trabajo y ejecute el siguiente comando:

        certreq -new mycert.txt mycert.crt
    
    El nuevo certificado autofirmado ahora está instalado en el almacén de certificados.

7. Para exportar el certificado del almacén de certificados, presione `Win` + `R` y ejecutar **certmgr.msc** para iniciar el Administrador de certificados. Seleccione **Personal** > **certificados**. En la columna **Emitido para** , debería ver una entrada con su nombre de dominio personalizado y a continuación, la entidad emisora de certificados que usa para generar el certificado en la columna **Emitido por** .

    ![Insertar imagen de administrador del certificado][certmgr]

9. Haga clic en el certificado y seleccione **Todas las tareas** > **Exportar**. En el **Asistente para exportación de certificados**, haga clic en **siguiente**, seleccione **exportar la clave privada**y, a continuación, haga clic en **siguiente** .

    ![Exportar la clave privada][certwiz1]

10. Seleccione el **Intercambio de información Personal: PKCS #12**, **incluir todos los certificados en la ruta de certificación si es posible**y **Exportar todas las propiedades extendidas**. A continuación, haga clic en **siguiente**.

    ![incluir todos los certificados y propiedades extendidas][certwiz2]

11. Seleccione **contraseña**y, a continuación, introduzca y confirme la contraseña. Haga clic en **siguiente**.

    ![especificar una contraseña][certwiz3]

12. Proporcione una ruta de acceso y nombre de archivo para el certificado exportado, con la extensión **.pfx**. Haga clic en **siguiente** para finalizar.

    ![Proporcione una ruta de acceso de archivo][certwiz4]

Ya está listo para cargar el archivo PFX exportado a la aplicación de servicio. Consulte [paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>
###<a name="generate-a-self-signed-certificate-using-openssl"></a>Generar un certificado autofirmado mediante OpenSSL ###

>[AZURE.IMPORTANT] Certificados autofirmados son solo con fines de prueba. La mayoría de exploradores devuelven errores al visitar un sitio Web que está protegido por un certificado autofirmado. Algunos exploradores aún podrán denegar vaya al sitio. 

1. Crear un archivo de texto denominado **serverauth.cnf**, a continuación, copie el siguiente contenido en él y, a continuación, guárdelo en un directorio de trabajo:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your app's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. En una línea de comandos terminal, `CD` en el directorio de trabajo y ejecute el siguiente comando:

        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    Este comando crea dos archivos: **myserver.crt** (el certificado autofirmado) y **myserver.key** (clave privada), según la configuración de **serverauth.cnf**.

3. Exporte el certificado a un archivo .pfx ejecutando el siguiente comando:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Cuando se le solicite, definir una contraseña para proteger el archivo .pfx.

Ya está listo para cargar el archivo PFX exportado a la aplicación de servicio. Consulte [paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>
## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>Paso 2. Cargar y enlazar el certificado SSL personalizado

Antes de pasar, revise la sección [lo que necesita](#bkmk_domainname) y lo compruebe siguiente:

- tiene un dominio personalizado que se asigna a la aplicación de Azure
- la aplicación se está ejecutando en el nivel **básico** o superior, y
- tiene un certificado SSL para el dominio personalizado de una entidad emisora de certificados.


1. En el explorador, abra el ** [Portal Azure.](https://portal.azure.com/)**
2.  Haga clic en la opción de **Servicio de aplicaciones** en el lado izquierdo de la página.
3.  Haga clic en el nombre de la aplicación a la que desea asignar este certificado. 
4.  En la **configuración**, haga clic en **certificados SSL**
5.  Haga clic en **Cargar certificado**
6.  Seleccione el archivo .pfx que exportó en el [paso 1](#bkmk_getcert) y especifique la contraseña que se crea antes. A continuación, haga clic en **cargar** para cargar el certificado. Ahora debe ver el certificado de cargar de nuevo en el módulo de **certificado SSL** .
7. En los **enlaces ssl** de la sección, haga clic en **Agregar enlaces**
8. En el módulo **Agregar enlace SSL** utilice las listas desplegables para seleccionar el nombre de dominio para proteger con SSL y el certificado para usar. También puede seleccionar si va a usar **[Indicación de nombre de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o SSL basada en IP.

    ![Insertar imagen de enlaces SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)

       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
     
9. Haga clic en **Agregar enlace** para guardar los cambios y habilitar SSL.

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>Paso 3. Cambiar la asignación de nombres de dominio (IP según SSL solo)

Si usa **SSL SNI** enlaces solo, omitir esta sección. Varios enlaces **SNI SSL** pueden trabajar juntos en la dirección IP compartida existente asignada a la aplicación. Si crea un enlace **SSL basada en IP** , la aplicación de servicio crea una dirección IP dedicada para el enlace porque el **SSL basada en IP** requiere uno. Solo uno dedicado IP dirección puede crearse, por lo tanto, se pueden agregar solo un enlace **SSL basada en IP** .

Debido a esta dirección IP, debe configurar la aplicación más si:

- [Usa un registro para asignar el dominio personalizado](web-sites-custom-domain-name.md#a) para su aplicación de Azure y acaba de agregar un enlace **SSL basada en IP** . En este escenario, debe volver a asignar las existentes un registro para que apunten a la dirección IP dedicada siguiendo estos pasos:

    1. Después de haber configurado una dirección IP según enlace SSL, se asigna una dirección IP a la aplicación. Puede encontrar esta dirección IP en la página del **dominio personalizado** en configuración de la aplicación, justo encima de la sección de **nombres de host** . Se mostrarán como **Dirección IP externa**
    
        ![Dirección IP virtual](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)

    2. [Reasignar el registro para su nombre de dominio personalizado para esta nueva dirección IP](web-sites-custom-domain-name.md#a).

- Ya tiene uno o más enlaces **SNI SSL** en la aplicación y que acaba de agregar un enlace **SSL basada en IP** . Una vez completado, el enlace su * &lt;nombre de aplicación >*. azurewebsites.net puntos de nombre de dominio a la nueva dirección IP. Por lo tanto, cualquier existente [asignación CNAME del dominio personalizado](web-sites-custom-domain-name.md#cname) a * &lt;nombre de aplicación >*. azurewebsites.net, incluidos los que la **SNI SSL** seguro, también recibe el tráfico en la nueva dirección, que se crea para el **SSL basada en IP** solo. En este escenario, debe enviar el tráfico **SSL SNI** volver a la dirección IP compartida original siguiendo estos pasos:

    1. Identificar todas las [asignaciones CNAME de dominios personalizados](web-sites-custom-domain-name.md#cname) para la aplicación que tiene un enlace **SNI SSL** .

    2. Reasignar cada registro CNAME para **sni.** &lt;nombre de aplicación >. azurewebsites.net en lugar de &lt;nombre de aplicación >. azurewebsites.net.

## <a name="step-4-test-https-for-your-custom-domain"></a>Paso 4. Prueba HTTPS para su dominio personalizado

Todo lo que queda por hacer es asegurarse de que funciona HTTPS de su dominio personalizado. En varios exploradores, vaya a `https://<your.custom.domain>` para ver que sirve a la aplicación.

- Si su aplicación se produce certificados errores de validación, probablemente está usando un certificado autofirmado.

- Si no es el caso, puede queda fuera de los certificados intermedios al exportar certificado .pfx. Vuelva a [lo que necesita](#bkmk_domainname) para comprobar que su CSR cumple todos los requisitos de la aplicación de servicio.

<a name="bkmk_enforce"></a>
## <a name="enforce-https-on-your-app"></a>Exigir HTTPS en la aplicación

Si desea permitir el acceso HTTP a la aplicación, omita este paso. Aplicación de servicio hace *no* exigir HTTPS, para que los visitantes pueden tener acceso a su aplicación utilizando HTTP. Si desea exigir HTTPS para la aplicación, puede definir una regla de reescritura en el `web.config` archivo de la aplicación. Cada aplicación de servicio de aplicación tiene este archivo, independientemente de la estructura del idioma de la aplicación.

> [AZURE.NOTE] No hay redirección específicas del idioma de solicitudes. ASP.NET MVC puede usar el filtro de [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) en lugar de la regla de reescritura en `web.config` (vea [implementar una aplicación de ASP.NET MVC 5 segura a una aplicación web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).

Siga estos pasos:

1. Vaya a la consola de depuración Kudu para la aplicación. Su dirección es `https://<appname>.scm.azurewebsites.net/DebugConsole`.

2. En la consola de depuración CD para `D:\home\site\wwwroot`.

3. Abrir `web.config` haciendo clic en el botón del lápiz.

    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

    Si implementa la aplicación con Visual Studio o Git, servicio de aplicación genera automáticamente el adecuado `web.config` para su aplicación. NET, PHP, Node.js o Python en la raíz de la aplicación. 
    Si `web.config` no existe, ejecute `touch web.config` en el símbolo basados en web a crearlo. O bien, puede crear en su proyecto local y volver a implementar el código.

4. Si tenía que crear un `web.config`, copie el código siguiente en él y guardarlo. Si abre un web.config existente, necesitará copiar toda la `<rule>` de etiqueta en el `web.config`de `configuration/system.webServer/rewrite/rules` elemento.

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <rewrite>
              <rules>
                <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
                <rule name="Force HTTPS" enabled="true">
                  <match url="(.*)" ignoreCase="false" />
                  <conditions>
                    <add input="{HTTPS}" pattern="off" />
                  </conditions>
                  <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
                </rule>
                <!-- END rule TAG FOR HTTPS REDIRECT -->
              </rules>
            </rewrite>
          </system.webServer>
        </configuration>

    Esta regla devuelve un HTTP 301 (redirección permanente) para el protocolo HTTPS siempre que el usuario solicite una página utilizando HTTP. Redirige de http://contoso.com a https://contoso.com.

    >[AZURE.IMPORTANT] Si no hay ya otros `<rule>` etiquetas en su `web.config`, a continuación, coloque el texto copiado `<rule>` etiqueta antes que el otro `<rule>` etiquetas.

4. Guarde el archivo en la consola de depuración Kudu. Debe entrar en vigor inmediatamente redirigir todas las peticiones HTTPS.

Para obtener más información sobre el módulo de reescritura de dirección URL de IIS, consulte la documentación de [Reescritura de URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="more-resources"></a>Más recursos ##
- [Centro de confianza de Microsoft Azure](/support/trust-center/security/)
- [Opciones de configuración desbloqueadas en los sitios Web de Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Habilitar el registro de diagnóstico](web-sites-enable-diagnostic-log.md)
- [Configurar aplicaciones web de servicio de la aplicación de Azure](web-sites-configure.md)
- [Portal de administración de Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación de corta duración starter en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png


