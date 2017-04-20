<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Evidence.com | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Evidence.com."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Tutorial: Integración de Azure Active Directory con Evidence.com

El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory (AAD) y Evidence.com. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:
    
* Una suscripción válida a Microsoft Azure
* Una suscripción a Evidence.com con el inicio de sesión único habilitado (correo electrónico earlyaccess@evidence.com si basada en SAML solo inicio de sesión no está habilitado)

Después de completar este tutorial, los usuarios AAD a la que ha asignado acceso Evidence.com podrán al inicio de sesión único en la aplicación utilizando el Panel de acceso AAD.

## <a name="add-evidencecom-to-your-directory"></a>Agregar Evidence.com al directorio

Esta sección describe cómo agregar Evidence.com como una aplicación integrada en Azure Active Directory.

**Para habilitar la integración de las aplicaciones para pruebas:**

1.  En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

4.  Para abrir la Galería de aplicación, haga clic en **Agregar**y, a continuación, haga clic en **Agregar una aplicación de la Galería**.

5.  En el cuadro Buscar, escriba **Evidence.com**.

6.  En el panel de resultados, seleccione **Evidence.com**y, a continuación, haga clic en **completado** para agregar la aplicación.


## <a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

Esta sección describe cómo permitir a los usuarios autenticar Evidence.com con su cuenta de Azure Active Directory, con la federación basada en el protocolo SAML.

**Para configurar el inicio de sesión único, realice los pasos siguientes:**

1.  Después de agregar Evidence.com en el portal Azure clásico, haga clic en **Configurar inicio de sesión único**. 
 
2.  En la siguiente pantalla, seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

3.  En la pantalla Configurar aplicación URL, escriba la dirección URL donde los usuarios se inicie sesión en la dirección URL de inquilinos de Evidence.com (ejemplo: https://yourtenant.evidence.com y, a continuación, haga clic en **siguiente**. 

4.  Haga clic en el vínculo **Descargar certificado** y guardarlo en su unidad local. Este certificado y las direcciones URL de metadatos (Id. de entidad, SSO de inicio de sesión en la URL y dirección URL de fuera de inicio de sesión) se utilizará para configurar el SSO en el sitio Evidence.com. 

5.  En una ventana de explorador web independientes, inicie sesión en su Evidence.com como administrador de inquilinos y vaya a la pestaña **administración**
      
6.  Haga clic en **Agencia solo inicio de sesión**
 
7.  Seleccione **SAML según el inicio de sesión único**
 
8.  Copie la **Dirección URL del emisor**, **Inicio de sesión único** e **Inicio de sesión único fuera** de los valores que se muestran en el portal de clásico Azure y a los campos correspondientes en Evidence.com.

9.  Abrir el certificado descargado en el paso 4 con un editor de texto como Notepad.exe y copiar y pegar el contenido en el cuadro **Certificado de seguridad** . 

10. Guardar la configuración en Evidence.com.
 
11. En el portal de clásico Azure, consulte **confirmar que ha configurado un inicio de sesión único como se describió anteriormente**. Esta comprobación se permitirá el certificado comenzar a trabajar para esta casilla de verificación de la aplicación actual.
 
12. En la página de confirmación de inicio de sesión único, haga clic en **completado**.  


## <a name="creating-an-evidencecom-test-user"></a>Crear un usuario de prueba Evidence.com

Para que usuarios de Azure AD poder iniciar sesión, debe aprovisionar acceso dentro de la aplicación Evidence.com. Esta sección describe cómo crear cuentas de usuario de Azure AD dentro de Evidence.com

**Configurar una cuenta de usuario en Evidence.com:**

1.  En una ventana de explorador web, inicie sesión en el sitio de su empresa Evidence.com como administrador.

2.  Vaya a la pestaña **administración** .

3.  Haga clic en **Agregar usuario**.

4.  Haga clic en el botón **Agregar** .

5.  La **Dirección de correo electrónico** de los usuarios agregados debe coincidir con el nombre de usuario de los usuarios en Azure AD quién desea conceder acceso. Si la dirección de correo electrónico y nombre de usuario no es el mismo valor en su organización, puede usar el **Evidence.com > atributos > Inicio de sesión único** sección del portal clásico Azure para cambiar la nameidenitifer enviado a Evidence.com para ser la dirección de correo electrónico.


## <a name="assigning-users-to-evidencecom"></a>Asignar usuarios a Evidence.com

Aprovisiona AAD los usuarios podrán ver Evidence.com en su Panel de acceso, se debe asignar acceso dentro del portal clásico Azure.

**Para asignar a los usuarios a Evidence.com:**

1.  En la página de inicio rápido para Evidence.com en el portal Azure clásica, haga clic en **asignar usuarios a Evidence.com**.
 
2.  En el menú **Mostrar** , seleccione si desea asignar un usuario o grupo a Evidence.com y haga clic en el botón de marca de verificación.
 
3.  En la lista de **usuarios** , seleccione los usuarios al grupo al que desea asignar Evidence.com.
 
4.  En el pie de página, haga clic en el botón **asignar** .

