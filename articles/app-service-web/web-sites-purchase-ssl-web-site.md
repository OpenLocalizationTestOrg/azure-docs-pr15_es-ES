<properties
    pageTitle="Comprar y configurar un certificado SSL para el servicio de aplicación de Azure"
    description="Obtenga información sobre cómo comprar y configurar un certificado SSL para el servicio de aplicación de Azure."
    services="app-service"
    documentationCenter=".net"
    authors="apurvajo"
    manager="stefsch"
    editor="cephalin"
    tags="buy-ssl-certificates"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="apurvajo"/>

#<a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Comprar y configurar un certificado SSL para el servicio de aplicación de Azure

> [AZURE.SELECTOR]
- [Comprar el certificado SSL en Azure](web-sites-purchase-ssl-web-site.md)
- [Usar el certificado SSL desde cualquier lugar](web-sites-configure-ssl-certificate.md)

De forma predeterminada, **[Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** ya permite HTTPS para la aplicación web con un certificado de caracteres comodín para la *. azurewebsites.net dominio. Si no va a configurar un dominio personalizado, puede beneficiarse del certificado HTTPS predeterminado. Sin embargo, al igual que todos * [dominios comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), no es tan seguro como utilizar un dominio personalizado con su propio certificado. Azure de aplicación de servicio ahora proporciona una manera simplificada realmente para comprar y administrar un certificado SSL directamente desde el Portal de Azure sin tener que salir del portal.  
En este artículo se explica cómo comprar y configurar un certificado SSL para el **[Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** en 3 pasos sencillos. 

> [AZURE.NOTE]
> No se pueden usar certificados SSL para nombres de dominio personalizado con Free y Shared web app. Debe configurar la aplicación web de modo básico, estándar o Premium, que puede cambiar la cantidad de facturación de la suscripción. Para obtener más información, consulte **[Detalles de precios de aplicaciones Web](https://azure.microsoft.com/pricing/details/web-sites/)** .



##<a name="bkmk_Overview"></a>Información general
> [AZURE.NOTE]
> No intente adquirir un certificado SSL con una suscripción que no tiene una tarjeta de crédito activa asociada con él. Esto podría provocar la suscripción está deshabilitada. 

##<a name="a-purchase-store-and-assign-an-ssl-certificate-for-your-custom-domain-a"></a><a>Comprar, almacenar y asignar un certificado SSL para el dominio personalizado</a>
Para habilitar HTTPS para un dominio personalizado, como por ejemplo contoso.com, primero debe ** [configurar un nombre de dominio personalizado en la aplicación de servicio de Azure.](web-sites-custom-domain-name.md)**

Antes de solicitar un certificado SSL, primero debe determinar qué nombres de dominio que se va a proteger el certificado. Esto determinará qué tipo de certificado debe obtener. Si desea proteger un único nombre de dominio, como contoso.com o www.contoso.com estándar (básica) certificado es suficiente. Si es necesario proteger varios nombres de dominio, como contoso.com, www.contoso.com y mail.contoso.com, puede obtener un [certificado comodín](http://en.wikipedia.org/wiki/Wildcard_certificate) de ****

##<a name="bkmk_purchasecert"></a>Paso 0: Colocar un pedido de certificado SSL

En este paso, aprenderá a realizar un pedido de un certificado SSL de su elección.

1.  En el **[Portal de Azure](https://portal.azure.com/)**, haga clic en Examinar y escriba "Certificados de servicio de aplicación" en la barra de búsqueda y seleccione "Certificados de servicio de aplicación" en el resultado y haga clic en Agregar. 

    ![Insertar imagen de crear usando examinar](./media/app-service-web-purchase-ssl-web-site/browse.jpg)

    ![Insertar imagen de crear usando examinar](./media/app-service-web-purchase-ssl-web-site/add.jpg)

2.  Escriba **un nombre descriptivo** para el certificado SSL.

3.  Escriba el **nombre de Host**
> [AZURE.NOTE]
    Esta es una de las partes más importantes del proceso de compra. Asegúrese de que escriba el nombre de host correcto (dominio personalizado) que desea proteger con este certificado. **No** anexar el nombre de Host con WWW. Por ejemplo, si su nombre de dominio personalizado es www.contoso.com, a continuación, simplemente escriba contoso.com en el campo nombre de Host, el certificado en cuestión protege dominios www y raíz. 
    
4.  Seleccione la **suscripción**. 

    Si tiene varias suscripciones, asegúrese de crear un certificado SSL en la misma suscripción que ha utilizado para su dominio personalizado o la aplicación Web en cuestión.
       
5.  Seleccione o cree un **grupo de recursos**.

    Grupos de recursos le permiten administrar recursos relacionados de Azure como una unidad y son útiles para establecer reglas de acceso basado en roles (RBAC) control para las aplicaciones. Para obtener más información, vea Administrar los recursos de Azure.
     
6.  Seleccione el **certificado SKU** 

    Por último, seleccione el certificado SKU que se adapte a sus necesidades y haga clic en crear. En la actualidad, servicio de aplicación de Azure le permite adquirir dos diferentes SKU • S1 – certificado estándar con 1 año validez y renovación automática  
           • B1: certificado comodín con renovación automática y validez de 1 año      
    Para obtener más información, consulte **[Detalles de precios de aplicaciones Web](https://azure.microsoft.com/pricing/details/web-sites/)** .

![Insertar imagen de certificado SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [AZURE.NOTE]
> Creación de certificado SSL puede durar entre 1 y 10 minutos. Este proceso realiza varios pasos en segundo plano que resulten muy difícil de llevar a cabo manualmente.  

##<a name="bkmk_StoreKeyVault"></a>Paso 1: Almacenar el certificado en depósito de clave de Azure

En este paso, aprenderá cómo colocar un almacén de un certificado SSL que compró en Azure depósito de clave de su elección.

1.  Una vez completada la compra de certificado SSL debe abrir manualmente la hoja de recursos de la **Aplicación de servicio de certificados** desplazándose hasta él nuevamente (consulte el paso 1 anterior)   

    ![Insertar imagen de listo para almacenar en KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)

    Observe que el estado de certificado es **"emisión pendiente"** que han algunos pasos más que se debe completar antes de empezar a usar este certificados.
 
2. Haga clic en **"configuración de certificados"** dentro de la hoja de propiedades del certificado y haga clic en **"paso 1: almacenar"** almacenar este certificado en depósito de clave de Azure.

3.  En placa **"clave depósito estado"** haga clic en **"clave depósito repositorio"** para elegir un depósito de clave existente para almacenar este certificado **o "Crear nuevo depósito clave"** crear nuevo depósito clave dentro del mismo grupo de recursos y de suscripción.
 
    ![Insertar imagen de crear nueva KV](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
 
    > [AZURE.NOTE]
    Depósito de clave Azure tiene gastos mínimas para almacenar este certificado. Para obtener más información, consulte **[Detalles de precios de Azure clave depósito](https://azure.microsoft.com/pricing/details/key-vault/)** .

4. Una vez que haya seleccionado el repositorio de depósito de clave para almacenar en este certificado, continuar y almacenar haciendo clic en el botón **"Store"** en la parte superior del módulo de **"clave depósito estado"** .  

    Se debe completar el paso para almacenar el certificado que compró con Azure depósito de clave de su elección. Al actualizar el módulo, debería ver verde Active marcar con este paso también.
    
##<a name="bkmk_VerifyOwnership"></a>Paso 2: Comprobar la propiedad del dominio

En este paso, aprenderá cómo realizar la comprobación de la propiedad del dominio para un certificado SSL que acaba de colocar un pedido. 

1.  Haga clic en **"paso 2: comprobar"** paso desde el módulo **"Configuración de certificados"** . Hay 4 tipos de dominio compatible con la aplicación de servicio de certificados de verificación.

    * **Comprobación del servicio de aplicación** 
    
        * Este es el proceso más adecuado si ya tiene **su dominio personalizado asignado a las aplicaciones de servicio de la aplicación.** Este método mostrará una lista a todas las aplicaciones de servicio de aplicación que cumplan estos criterios. 
           En este caso, por ejemplo, **contosocertdemo.com** es un dominio personalizado asignado a la aplicación de servicio de aplicación denominado **"ContosoCertDemo"** y, por tanto, que es la aplicación de servicio de aplicación solo aparece aquí. Si hay múltiples región implementación, lo haría lista todo en todas las regiones.
        
           El método de verificación solo está disponible para compras estándar certificado (básico). Para los certificados comodín, omita y mover a la opción B, C o d. a continuación.
        * Haga clic en el botón **"Comprobar"** para completar este paso.
        * Haga clic en **"Actualizar"** para actualizar el estado de certificado una vez completada la comprobación. Puede llevar algunos minutos para la verificación completar.
        
        ![Insertar imagen de comprobación de servicios de aplicación](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)     

    * **Comprobación de dominio** 

        * Este es el proceso más adecuado **solo si** tiene **[compró su dominio personalizado de servicio de aplicaciones de Azure.](custom-dns-web-site-buydomains-web-app.md)**
        
        * Haga clic en el botón **"Comprobar"** para completar este paso.
        
        * Haga clic en **"Actualizar"** para actualizar el estado de certificado una vez completada la comprobación. Puede llevar algunos minutos para la verificación completar.

    * **Comprobación de correo**
        
        * Correo electrónico de verificación ya se han enviado a las direcciones de correo electrónico asociada a este dominio personalizado.
         
        * Abra el correo electrónico y haga clic en el vínculo de comprobación para completar el paso de comprobación de correo electrónico. 
        
        * Si necesita volver a enviar el correo electrónico de verificación, haga clic en el botón **"reenviar correo electrónico"** .
         
    * **Comprobación manual**    
                 
        1. **Comprobación de la página Web HTML**
        
            * Crear un archivo HTML denominado .html **{Token de comprobación de dominio}**(puede copiar el token desde el módulo de estado de comprobación de dominio)
            
            * El contenido de este archivo debe ser exactamente el mismo nombre de **Dominio verificación Token**.
            
            * Cargar este archivo en la raíz del servidor web que aloja el dominio.
            
            * Haga clic en **"Actualizar"** para actualizar el estado de certificado una vez completada la comprobación. Puede llevar algunos minutos para la verificación completar.
            
            Por ejemplo, si va a comprar un certificado estándar para contosocertdemo.com con el Token de comprobación de dominio **'cAGgQrKc'** , a continuación, debe devolver una solicitud web a **'http://contosocertdemo.com/cAGgQrKc.html'** **cAGgQrKc.**
        2. **Comprobación de registro DNS TXT**

            * Mediante el Administrador de DNS, cree un registro TXT en el subdominio **'DZC'** con valor igual a la **Token de comprobación del dominio.**
            
            * Haga clic en **"Actualizar"** para actualizar el estado de certificado una vez completada la comprobación. Puede llevar algunos minutos para la verificación completar.
                              
            Por ejemplo, para realizar la validación de un certificado comodín con hostname ** \*. contosocertdemo.com** o ** \*. subdomain.contosocertdemo.com** y **cAGgQrKc**de Token de comprobación del dominio, debe crear un registro TXT en dzc.contosocertdemo.com con valor **cAGgQrKc.**     


##<a name="bkmk_AssignCertificate"></a>Paso 3: Asignar certificado a la aplicación de servicio de aplicaciones

En este paso, aprenderá a asignar esta recién comprados certificado a sus aplicaciones de servicio de aplicación. 

> [AZURE.NOTE]
> Antes de realizar los pasos de esta sección, debe tener asociado un nombre de dominio personalizado con la aplicación. Para obtener más información, vea ** [configurar un nombre de dominio personalizado para un web app.](web-sites-custom-domain-name.md)**

1.  En el explorador, abra el ** [Portal Azure.](https://portal.azure.com/)**
2.  Haga clic en la opción de **Servicio de aplicaciones** en el lado izquierdo de la página.
3.  Haga clic en el nombre de la aplicación a la que desea asignar este certificado. 
4.  En la **configuración**, haga clic en **certificados SSL**
5.  Haga clic en **Importar certificado de servicio de aplicación** y seleccione el certificado que acaba de comprar

    ![Insertar imagen de certificado de importación](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

6. En los **enlaces ssl** de la sección, haga clic en **Agregar enlaces**
7. En el módulo **Agregar enlace SSL** utilice las listas desplegables para seleccionar el nombre de dominio para proteger con SSL y el certificado para usar. También puede seleccionar si va a usar **[Indicación de nombre de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o SSL basada en IP.

    ![Insertar imagen de enlaces SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
       
7. Haga clic en **Agregar enlace** para guardar los cambios y habilitar SSL.



Si seleccionó **SSL basada en IP** y el dominio personalizado está configurado con un registro, debe realizar los siguientes pasos adicionales:

* Después de haber configurado una dirección IP según enlace SSL, se asigna una dirección IP a la aplicación. Puede encontrar esta dirección IP en la página del **dominio personalizado** en configuración de la aplicación, justo encima de la sección de **nombres de host** . Se mostrarán como **Dirección IP externa**
    
    ![Insertar imagen de IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

    Tenga en cuenta que esta dirección IP será diferente de la dirección IP virtual utilizada previamente para configurar el registro para su dominio. Si ha configurado para usar SNI según SSL o no está configurado para utilizar SSL, no se mostrarán ninguna dirección para esta entrada.
    
2. Con las herramientas proporcionadas por el registrador de nombres de dominio, modifique el registro para su nombre de dominio personalizado para que apunten a la dirección IP del paso anterior.
En este momento, debería poder visitar la aplicación utilizando HTTPS:// en lugar de HTTP:// para comprobar que el certificado se ha configurado correctamente.


##<a name="bkmk_Rekey"></a>Cambiar la clave y sincronizar el certificado

1. Por motivos de seguridad, si desea cambiar la clave de certificado, a continuación, simplemente seleccione opción **"cambiar la clave y sincronizar"** de **"propiedades de certificado"** módulo. 

2. Haga clic en el botón **"cambiar la clave"** para iniciar el proceso. Este proceso puede tardar 1-10 minutos en completarse. 

    ![Insertar imagen de cambiar la clave de SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

3. Certificado de claves de registro, se cambiará el certificado con un nuevo certificado emitido de la entidad emisora de certificados.
4. No se cobrará para el cambio de claves para la duración del certificado. 
5. Certificado de claves de registro pasarán por estado de emisión pendiente. 
6. Una vez que el certificado esté listo, asegúrese de que sincronizar los recursos utilizando este certificado para evitar la interrupción del servicio.
7. Opción Sincronizar no está disponible para los certificados que no se ha asignado a la aplicación Web. 

## <a name="more-resources"></a>Más recursos ##
- [Habilitar HTTPS para una aplicación de servicio de aplicaciones de Azure](web-sites-configure-ssl-certificate.md)
- [Comprar y configurar un nombre de dominio personalizado en la aplicación de servicio de Azure](custom-dns-web-site-buydomains-web-app.md)
- [Centro de confianza de Microsoft Azure](/support/trust-center/security/)
- [Opciones de configuración desbloqueadas en los sitios Web de Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Portal de administración de Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.


