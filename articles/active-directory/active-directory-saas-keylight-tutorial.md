<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con luz clave | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y luz clave."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Tutorial: Integración de Azure Active Directory con luz clave

En este tutorial, aprenderá a integrar luz clave con Azure Active Directory (AD Azure).

Integración de luz clave con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a luz clave
- Puede habilitar los usuarios automáticamente obtener firmado en luz clave (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con luz clave, necesitará los elementos siguientes:

- Una suscripción de Azure
- Una luz clave inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Adición de luz clave desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-keylight-from-the-gallery"></a>Adición de luz clave desde la Galería
Para configurar la integración de luz clave en Azure AD, debe agregar luz clave de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar luz clave de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **luz clave**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. En el panel de resultados, seleccione **luz clave**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con luz clave en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para configurar y probar el inicio de sesión único de Azure AD con luz clave, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear una luz clave probar usuario](#creating-a-keylight-test-user)** - tener un equivalente de Britta Simon de luz clave que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de luz clave.


**Para configurar el inicio de sesión único Azure AD con luz clave, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **luz clave** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 


2. En la página **¿cómo desea que los usuarios para iniciar sesión luz clave** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de luz clave mediante el siguiente patrón: **"https://\<nombre de la compañía\>.keylightgrc.com/Login.aspx?saml=1"**.


4. En la página **configurar inicio de sesión único de luz clave** , realice los pasos siguientes:
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para habilitar SSO de luz clave, siga estos pasos:
 
    una. Inicio de sesión en su cuenta de luz clave como administrador.

    b. En el menú de la parte superior, haga clic en **persona**y seleccione **Configuración de luz clave**.
       
    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. En la vista de árbol de la izquierda, haga clic en **SAML**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. En el cuadro de diálogo **Configuración de SAML** , haga clic en **Editar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/404.png) 
  

5. En la página de diálogo **Editar configuración de SAML** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/405.png) 

    una. Establecer la **autenticación de SAML** **activa**.


    b. En el portal de Azure AD clásico, copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades** .

    c. En el portal de Azure AD clásico, copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades** .

    d. Haga clic en **Elegir archivo** para seleccionar el certificado de luz clave descargado y, a continuación, haga clic en **Abrir** para cargar el certificado.


    e. Establecer **ubicación de identificador de usuario de SAML** **NameIdentifier**elemento de la instrucción de asunto.
   
    f. Proporcionar la **proveedor de servicios de luz clave mediante el siguiente patrón: **https://&lt;nombre de la compañía&gt;. keylightgrc.com**.

    g. Establecer **Auto aprovisionamiento de usuarios** **activos**.

    h. Establecer **tipo de cuenta de aprovisionamiento automático** al **Usuario completa**.

    . **Rol de seguridad de aprovisionamiento automático**, seleccione **Usuario estándar con SAML**.
   
    j. Como la **configuración de seguridad de aprovisionamiento automático**, seleccione **Configuración de usuario estándar**.
   
    k. En el cuadro de texto de atributo de correo electrónico, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. En el cuadro de texto de **atributo de nombre** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. En el cuadro de texto del **último atributo de nombre** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Haga clic en **Guardar**.
   
  
   
  
6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

En la lista de usuarios, seleccione **Bárbara Menéndez**.

![Crear usuario de Azure AD][20]



**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 


2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 


4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-keylight-test-user"></a>Crear un usuario de prueba de luz clave

En esta sección, creará un usuario denominado a Britta Simon de luz clave. Luz clave admite en el tiempo de aprovisionamiento, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para en esta sección. Se crea un nuevo usuario al acceder a luz clave si todavía no existe el usuario. 

> [AZURE.NOTE] Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico de luz clave.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a luz clave.

![Asignar usuario][200] 

**Para asignar a Britta Simon a luz clave, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **luz clave**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el icono de luz clave en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de luz clave.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png
