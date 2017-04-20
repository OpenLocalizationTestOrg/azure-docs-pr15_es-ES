<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con ServiceNow y ServiceNow Express | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ServiceNow y ServiceNow Express."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Tutorial: Integración de Azure Active Directory con ServiceNow y ServiceNow Express.


En este tutorial, aprenderá a integrar ServiceNow y ServiceNow Express con Azure Active Directory (AD Azure).

Integración de ServiceNow y ServiceNow Express con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ServiceNow y ServiceNow Express
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en ServiceNow y ServiceNow Express (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ServiceNow y ServiceNow Express, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Para ServiceNow, una instancia o inquilino de ServiceNow, versión Calgary o superior
- Para ServiceNow Express, una instancia de ServiceNow Express, versión Helsinki o superior
- ServiceNow inquilino debe tener el [Varios proveedor solo inicio de sesión en complemento](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Esto puede hacerse mediante el envío de una solicitud de servicio en <https://hi.service-now.com/> 


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba. El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar ServiceNow de la Galería
2. Configurar y probar Azure AD de sesión único para ServiceNow o ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Agregar ServiceNow de la Galería
Para configurar la integración de ServiceNow o ServiceNow Express en Azure AD, debe agregar ServiceNow desde la galería a la lista de aplicaciones de SaaS administradas. 

**Para agregar ServiceNow de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **ServiceNow**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. En el panel de resultados, seleccione **ServiceNow**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar Azure AD inicio de sesión único con ServiceNow o ServiceNow Express basada en un usuario de prueba denominado a "Britta Simon".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en ServiceNow a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en ServiceNow.
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ServiceNow. Para configurar y probar el inicio de sesión único de Azure AD con ServiceNow, debe completar los siguientes bloques de creación:

1. **[Configurar Azure AD inicio de sesión único para ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** : para habilitar los usuarios usar esta característica.
2. **[Configurar Azure AD inicio de sesión único para ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - para permitir a los usuarios usar esta característica.
3. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un ServiceNow probar usuario](#creating-a-servicenow-test-user)** - a tiene equivalente de Britta Simon en ServiceNow que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
6. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

> [AZURE.NOTE] Si desea configurar ServiceNow omite el paso 2. Del mismo modo, si desea configurar ServiceNow Express omite el paso 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuración de sesión único de Azure AD para ServiceNow

1.  En el portal de Azure AD clásico, en la página de integración de aplicación **ServiceNow** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ServiceNow** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar la dirección URL de la aplicación")

    una. en el cuadro de texto **ServiceNow inicio de sesión en la URL** , escriba la dirección URL de utilizar los usuarios para el inicio de sesión en la aplicación de ServiceNow siguiendo el modelo: `https://<instance-name>.service-now.com`.

    b. En el cuadro de texto **identificador** , escriba la dirección URL que utilizan los usuarios para inicio de sesión en la aplicación de ServiceNow siguiendo el modelo: `https://<instance-name>.service-now.com`.

    c. Haga clic en **siguiente**

4.  Para tener Azure AD ServiceNow se configura automáticamente para la autenticación basada en SAML, escriba su nombre de instancia ServiceNow, nombre de usuario de administrador y la contraseña de administrador en el formulario **configurar automáticamente el inicio de sesión único** y haga clic en *Configurar*. Tenga en cuenta que proporciona el nombre de usuario administrador debe tener el rol de **security_admin** asignado en ServiceNow para que esto funcione. En caso contrario, para configurar manualmente ServiceNow para usar Azure AD como un proveedor de identidades SAML, haga clic en **configurar manualmente la aplicación para el inicio de sesión único**, a continuación, haga clic en **siguiente** y complete los pasos siguientes.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar la dirección URL de la aplicación")



5.  En la página **configurar inicio de sesión único en ServiceNow** , haga clic en **Descargar certificado**, guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar inicio de sesión único")

1. Inicio de sesión en la aplicación ServiceNow como administrador.
2. Activar el complemento de _integración - varias proveedor solo inicio de sesión de instalador_ siguiendo los pasos siguientes:

    una. En el panel de navegación en el lado izquierdo, vaya a la sección de **Definición del sistema** y, a continuación, haga clic en **complementos**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Complemento para activar")
    
    b. Búsqueda de _integración - varias proveedor solo inicio de sesión de instalador_.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Complemento para activar")

    c. Seleccione el complemento. Derecho haga clic en y seleccione **Activar o actualizar**.
    
    d. Haga clic en el botón **Activar** .

2. En el panel de navegación en el lado izquierdo, haga clic en **Propiedades**.  

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurar la dirección URL de la aplicación")


3. En el cuadro de diálogo **Varias propiedades de SSO de proveedor** , siga estos pasos:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar la dirección URL de la aplicación")

    una. Como **Habilitar a varios proveedor SSO**, seleccione **Sí**.

    b. Como **Habilitar registro de depuración obtuvo el proveedor de múltiples integración de SSO**, seleccione **Sí**.

    c. En el cuadro de texto en **el campo en el usuario de la tabla que...** , escriba **nombreDeUsuario**.

    d. Haga clic en **Guardar**.



1. En el panel de navegación en el lado izquierdo, haga clic en **certificados x509**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo **Certificados X.509** , haga clic en **nuevo**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo **Certificados X.509** , realice los pasos siguientes:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar inicio de sesión único")

    una. Haga clic en **nuevo**.

    b. En el cuadro de texto **nombre** , escriba un nombre para la configuración (por ejemplo: **TestSAML2.0**).

    c. Seleccione **activo**.

    d. Como **formato**, seleccione **PEM**.

    e. Como **tipo**, seleccione **Confiar en el certificado de la tienda**.
    
    f. Abra su certificado base 64 codificado en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado PEM** .

    g. Haga clic en **Actualizar**.


1. En el panel de navegación en el lado izquierdo, haga clic en **Proveedores de identidades**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurar inicio de sesión único")

1. En el cuadro de diálogo **Proveedores de identidades** , haga clic en **nuevo**:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo **Proveedores de identidades** , haga clic en **SAML2 Update1?**:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar inicio de sesión único")


1. En el cuadro de diálogo Propiedades de Update1 SAML2, realice los pasos siguientes:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar inicio de sesión único")


    una. en el cuadro de texto **nombre** , escriba un nombre para la configuración (por ejemplo: **SAML 2.0**).

    b. En el cuadro de texto **Campo de usuario** , tipo de **correo electrónico** o **user_id**, dependiendo de qué campo se usa para identificar los usuarios de la implementación de ServiceNow. 
    
    > [AZURE.NOTE] Puede configue Azure AD para emitir el identificador de usuario de Azure AD (nombre principal de usuario) o la dirección de correo electrónico como el identificador único en el token de SAML yendo a la **ServiceNow > atributos > Inicio de sesión único** sección del portal clásico Azure y asignación del campo que desee para el atributo **nameidentifier** . El valor almacenado para el atributo seleccionado en Azure AD (por ejemplo, nombre principal de usuario) debe coincidir con el valor almacenado en ServiceNow para el campo especificado (por ejemplo, user_id)

    c. En el portal de clásico de Azure AD, copie el valor del **Identificador de proveedor de servicios de identidad** y péguelo en el cuadro de texto de la **Dirección URL del proveedor de identidades** .

    d. En el portal de clásico de Azure AD, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **del proveedor de identidades AuthnRequest** .

    e. En el portal de clásico de Azure AD, copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **del proveedor de identidades SingleLogoutRequest** .

    f. En el cuadro de texto de la **Página principal de ServiceNow** , escriba la dirección URL de la página de inicio de la instancia ServiceNow.

    > [AZURE.NOTE] La página de inicio de la instancia ServiceNow es una concatenación de la **dirección URL del inquilino de ServieNow** y **/navpage.do** (por ejemplo:`https://fabrikam.service-now.com/navpage.do`).
 

    g. En la **ID entidad / emisor** cuadro de texto, escriba la dirección URL de su inquilino ServiceNow.

    h. En el cuadro de texto **Dirección URL de la audiencia** , escriba la dirección URL de su inquilino ServiceNow. 

    . En el cuadro de texto de **Protocolo de enlace de SingleLogoutRequest del IDP** , escriba **urn: oasis: nombres: tc: SAML:2.0:bindings:HTTP-redirigir**.

    j. En el cuadro de texto NameID directiva, escriba **urn: oasis: nombres: tc: SAML:1.1:nameid-formato: sin especificar**.

    k. Anule la selección de **crear un AuthnContextClass**.

    l. En el **Método AuthnContextClassRef**, escriba `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Esto solo es necesario si es la única organización de la nube. Si está utilizando local ADFS o AMF para la autenticación no debe configurar este valor. 

    m. En **Reloj contraer** cuadro de texto, escriba **60**.

    n. Como **Inicio de sesión único en Script**, seleccione **MultiSSO_SAML2_Update1**.

    o. Como **x509 certificado**, seleccione el certificado que haya creado en el paso anterior.

    p. Haga clic en **Enviar**. 



6. En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar inicio de sesión único")

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.
 
    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar inicio de sesión único")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuración de sesión único de Azure AD para ServiceNow Express

1.  En el portal de Azure AD clásico, en la página de integración de aplicación **ServiceNow** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ServiceNow** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar la dirección URL de la aplicación")

    una. en el cuadro de texto **ServiceNow inicio de sesión en la URL** , escriba la dirección URL de utilizar los usuarios para el inicio de sesión en la aplicación de ServiceNow siguiendo el modelo: `https://<instance-name>.service-now.com`.

    b. En el cuadro de texto de la **Dirección URL del emisor** , escriba la dirección URL que utilizan los usuarios para inicio de sesión en la aplicación de ServiceNow siguiendo el modelo `https://<instance-name>.service-now.com`.

    c. Haga clic en **siguiente**

4.  Haga clic en **configurar manualmente la aplicación para el inicio de sesión único**, a continuación, haga clic en **siguiente** y complete los pasos siguientes.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar la dirección URL de la aplicación")

5.  En la página **configurar inicio de sesión único en ServiceNow** , haga clic en **Descargar certificado**, guarde el archivo de certificado localmente en el equipo y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar inicio de sesión único")

6. Inicio de sesión en la aplicación de ServiceNow Express como administrador.

7. En el panel de navegación en el lado izquierdo, haga clic en **Inicio de sesión único**.  

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurar la dirección URL de la aplicación")


8. En el cuadro de diálogo **Inicio de sesión único** , haga clic en el icono de configuración en la esquina superior derecha y establezca las siguientes propiedades:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurar la dirección URL de la aplicación")

    una. Activar o desactivar **Habilitar a varios proveedor SSO** a la derecha.

    b. Activar o desactivar **Habilitar registro para la integración de SSO varios proveedor de depuración** a la derecha.

    c. En el cuadro de texto en **el campo en el usuario de la tabla que...** , escriba **nombreDeUsuario**.


9. En el cuadro de diálogo **Inicio de sesión único** , haga clic en **Agregar nuevo certificado**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurar inicio de sesión único")



10. En el cuadro de diálogo **Certificados X.509** , realice los pasos siguientes:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar inicio de sesión único")

    una. En el cuadro de texto **nombre** , escriba un nombre para la configuración (por ejemplo: **TestSAML2.0**).

    b. Seleccione **activo**.

    c. Como **formato**, seleccione **PEM**.

    d. Como **tipo**, seleccione **Confiar en el certificado de la tienda**.

    e. Crear un archivo de base 64 codificado de certificado descargado.
   
    > [AZURE.NOTE] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Abra su certificado base 64 codificado en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado PEM** .

    g. Haga clic en **Actualizar**.


11. En el cuadro de diálogo **Inicio de sesión único** , haga clic en **Agregar nuevo IdP**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurar inicio de sesión único")


12. En el cuadro de diálogo **Agregar nuevo proveedor de identidades** , en **Configurar el proveedor de identidades**, siga estos pasos:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurar inicio de sesión único")


    una. En el cuadro de texto **nombre** , escriba un nombre para la configuración (por ejemplo: **SAML 2.0**).

    b. En el portal de clásico de Azure AD, copie el valor del **Identificador de proveedor de servicios de identidad** y péguelo en el cuadro de texto de la **Dirección URL del proveedor de identidades** .

    c. En el portal de clásico de Azure AD, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **del proveedor de identidades AuthnRequest** .

    d. En el portal de clásico de Azure AD, copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **del proveedor de identidades SingleLogoutRequest** .

    e. **Certificado del proveedor de identidades**, seleccione el certificado que haya creado en el paso anterior.


13. Haga clic en **Configuración avanzada**y en **Propiedades de proveedor de identidades adicionales**, realice los pasos siguientes:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurar inicio de sesión único")

    una. En el cuadro de texto de **Protocolo de enlace de SingleLogoutRequest del IDP** , escriba **urn: oasis: nombres: tc: SAML:2.0:bindings:HTTP-redirigir**.

    b. En el cuadro de texto **NameID directiva** , escriba **urn: oasis: nombres: tc: SAML:1.1:nameid-formato: sin especificar**.    

    c. En el **Método AuthnContextClassRef**, escriba **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
    
    d. Anule la selección de **crear un AuthnContextClass**.

14. En **Propiedades adicionales del proveedor de servicio**, siga estos pasos:

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurar inicio de sesión único")

    una. En el cuadro de texto de la **Página principal de ServiceNow** , escriba la dirección URL de la página de inicio de la instancia ServiceNow.

    > [AZURE.NOTE] La página de inicio de la instancia ServiceNow es una concatenación de la **dirección URL del inquilino de ServieNow** y **/navpage.do** (por ejemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. En la **ID entidad / emisor** cuadro de texto, escriba la dirección URL de su inquilino ServiceNow.

    c. En el cuadro de texto de **URI de audiencia** , escriba la dirección URL de su inquilino ServiceNow. 

    d. En **Reloj contraer** cuadro de texto, escriba **60**.

    e. En el cuadro de texto **Campo de usuario** , tipo de **correo electrónico** o **user_id**, dependiendo de qué campo se usa para identificar los usuarios de la implementación de ServiceNow.
    
    > [AZURE.NOTE] Puede configue Azure AD para emitir el identificador de usuario de Azure AD (nombre principal de usuario) o la dirección de correo electrónico como el identificador único en el token de SAML yendo a la **ServiceNow > atributos > Inicio de sesión único** sección del portal clásico Azure y asignación del campo que desee para el atributo **nameidentifier** . El valor almacenado para el atributo seleccionado en Azure AD (por ejemplo, nombre principal de usuario) debe coincidir con el valor almacenado en ServiceNow para el campo especificado (por ejemplo, user_id)

    f. Haga clic en **Guardar**. 


15. En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar inicio de sesión único")

16. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.
 
    ![Configurar inicio de sesión único] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
El objetivo de esta sección es describen cómo habilitar el usuario de aprovisionamiento de cuentas de usuario de Active Directory para ServiceNow.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1. En el portal de administración de Azure clásico, en la página de integración de aplicación **ServiceNow** , haga clic en **Configurar el aprovisionamiento de usuario**. 

    ![Aprovisionamiento de usuario] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Aprovisionamiento de usuario")


2. En la página **Escriba sus credenciales de ServiceNow para habilitar el aprovisionamiento de usuario automática** , proporcione la siguiente configuración: configurar el aprovisionamiento de usuario 

     una. En el cuadro de texto **Nombre de la instancia de ServiceNow** , escriba el nombre de instancia ServiceNow.

     b. En el cuadro de texto **Nombre de usuario de administración de ServiceNow** , escriba el nombre de la cuenta de administrador de ServiceNow.

     c. En el cuadro de texto **Contraseña de administrador de ServiceNow** , escriba la contraseña para esta cuenta.

     d. Haga clic en **Validar** para comprobar la configuración.

     e. Haga clic en el botón **siguiente** para abrir la página de **pasos siguientes** .

     f. Si desea aprovisionar todos los usuarios de esta aplicación, seleccione "**aprovisionar automáticamente todas las cuentas de usuario en el directorio de esta aplicación**". 

    ![Pasos siguientes] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Pasos siguientes")

     g. En la página **pasos siguientes** , haga clic en **completado** para guardar la configuración.

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   


### <a name="creating-a-servicenow-test-user"></a>Crear un usuario de prueba ServiceNow

En esta sección, creará un usuario llamado a Britta Simon en ServiceNow. En esta sección, creará un usuario llamado a Britta Simon en ServiceNow. Si no sabe cómo agregar un usuario de su cuenta de ServiceNow o ServiceNow Express, póngase en contacto con el equipo de soporte técnico de ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a ServiceNow.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ServiceNow, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ServiceNow**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de todos los usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de ServiceNow en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación ServiceNow.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
