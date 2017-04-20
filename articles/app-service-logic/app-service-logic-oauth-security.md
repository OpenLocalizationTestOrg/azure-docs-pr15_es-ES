<properties
    pageTitle="Seguridad OAUTH de conectores de SaaS y aplicaciones de la API | Azure"
    description="Obtenga información sobre la seguridad OAUTH en los conectores y las API de aplicaciones de servicio de la aplicación de Azure; arquitectura de microservices; SaaS"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>Obtenga más información sobre seguridad de OAUTH SaaS conectores

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica.

Muchas del Software como un conectores de servicio (SaaS) como Facebook, Twitter, DropBox y así sucesivamente requieran que los usuarios autenticar mediante el protocolo OAUTH.  Cuando usa estos conectores SaaS de lógica de aplicaciones, le proporcionamos una experiencia de usuario simplificada donde hacer clic en "Autorizar" en el Diseñador de aplicaciones de lógica. Cuando usted **autorizar**, se le pregunte para iniciar sesión en (si no ya) y proporcione su consentimiento para conectar con el servicio de SaaS en su nombre. Después de proporcionar consentimiento y autorizar, las aplicaciones de lógica, a continuación, puede acceder a estos servicios SaaS.

## <a name="create-your-own-saas-app"></a>Crear su propia aplicación SaaS
Esta experiencia simplificada es posible porque previamente creado y registra la aplicación en estos servicios SaaS.  En algunos casos, es aconsejable registrar y utilizar su propia aplicación.  Esto es necesario, por ejemplo, cuando desee utilizar estos conectores SaaS en las aplicaciones personalizadas. Este ejemplo usa el conector de DropBox, pero el proceso es el mismo para todos los conectores que se basan en OAUTH.

Incluso en el contexto de lógica de aplicaciones, puede usar su propia aplicación en lugar de usar la aplicación predeterminada que proporcionamos. Si el botón "Autorizar" no puede conectarse, intente crear su propia aplicación. A continuación enumeran estos pasos para el conector de Twitter:

1. Abra el conector de Twitter en el portal de vista previa de Azure. Vaya a **Examinar** > **API aplicaciones**. Seleccione el conector de Twitter:  
    ![][1]

2. Seleccione **configuración** > **autenticación**:  
    ![][2]

3. Copie el valor de **Redirección URI** :  
    ![][3]

4. Vaya a [Twitter](http://apps.twitter.com) y **crear una nueva aplicación**. En la propiedad de la **Dirección URL de devolución de llamada** , pegue el valor de **Redirección URI de** copia desde el conector de Twitter: ![][4]  
5. Cuando se crea la aplicación de Twitter, seleccione **clave y Tokens de acceso**. Copie estos valores.
6. En la configuración de autenticación de conector de Twitter, pegue estos valores en las propiedades del **Identificador de cliente** y el **Secreto de cliente** :   
    ![][5]  
7. Guardar la configuración del conector.  

Ahora, podrá usar el conector de aplicaciones de lógica. Cuando utiliza este conector de lógica de aplicaciones, se usa la aplicación en lugar de la aplicación predeterminada.  

> [AZURE.NOTE] Si previamente ha autorizado una aplicación, debe autorizar la aplicación.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png
