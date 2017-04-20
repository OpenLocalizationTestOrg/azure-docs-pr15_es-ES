<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con gestión RR. de Ceridian Dayforce | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y gestión RR. de Ceridian Dayforce."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integración de Azure Active Directory con Dayforce de Ceridian gestión RR.

El objetivo de este tutorial es mostrar cómo integrar gestión RR. de Ceridian Dayforce con Azure Active Directory (AD Azure).  
Integración de gestión RR. de Ceridian Dayforce con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Dayforce de Ceridian gestión RR.
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en gestión de RR. Dayforce Ceridian (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure


Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con gestión RR. de Ceridian Dayforce, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Una gestión RR. de Ceridian Dayforce inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Ceridian Dayforce HCM de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Agregar Ceridian Dayforce HCM de la Galería
Para configurar la integración de gestión RR. de Ceridian Dayforce en Azure AD, debe agregar gestión RR. de Ceridian Dayforce de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar gestión RR. de Ceridian Dayforce de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Ceridian Dayforce HCM**.

    ![Aplicaciones](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)

7. En el panel de resultados, seleccione **Gestión RR. de Ceridian Dayforce**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Aplicaciones](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con Ceridian Dayforce HCM en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en gestión RR. de Ceridian Dayforce a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en gestión RR. de Ceridian Dayforce.

Para configurar y probar el inicio de sesión único de Azure AD con gestión RR. de Ceridian Dayforce, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear una gestión RR. de Ceridian Dayforce probar usuario](#creating-a-ceridian-dayforce-hcm-test-user)** - a tiene equivalente de Britta Simon en gestión RR. de Ceridian Dayforce que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de gestión RR. de Ceridian Dayforce.

La aplicación de gestión RR. de Ceridian Dayforce espera las afirmaciones SAML en un formato específico. Trabaje con el equipo de Dayforce HCM para identificar el identificador de usuario correctas. Microsoft recomienda usar el atributo **"nombre"** como identificador de usuario. Puede administrar el valor de este atributo en el cuadro de diálogo **"Atrribute"** . La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) 

**Para configurar el inicio de sesión único Azure AD con gestión RR. de Ceridian Dayforce, siga estos pasos:**




1. En el portal de clásico Azure, en la página de integración de aplicación de **Gestión RR. de Ceridian Dayforce** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png) 


1. En la lista de **atributos de token saml** atributos, seleccione el atributo de nombre y, a continuación, haga clic en **Editar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png) 


1. En el cuadro de diálogo **Modificar el atributo de usuario** , siga estos pasos:
 
    una. En la lista **Valor del atributo** , seleccione el atributo de usuario que desea usar para su implementación.  
    Por ejemplo, si desea usar el IdEmpleado como identificador de usuario único y ha almacenado el valor del atributo en el ExtensionAttribute2, a continuación, seleccione **user.extensionattribute2**. 

    b. Haga clic en **Finalizar**.  
    



1. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  





1. En el portal de clásico Azure, en la página de integración de aplicación de **Gestión RR. de Ceridian Dayforce** , haga clic en **configurar inicio de sesión único**.

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión gestión RR. de Ceridian Dayforce** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , siga estos pasos:.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) 


    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de gestión RR. de Ceridian Dayforce. Para entornos de producción, use el siguiente formato URL:`https://sso.dayforcehcm.com/DayforcehcmNamespace`

    Para aclarar, reemplace DayforcehcmNamespace por el espacio de nombres de su entorno o el identificador de la compañía; Por ejemplo:`https://sso.dayforcehcm.com/contoso`
    
    En entornos de prueba, use el siguiente formato URL:`https://ssotest.dayforcehcm.com/DayforcehcmNamespace` 

    b. En el cuadro de texto **Dirección URL de respuesta** , escriba la dirección URL utilizada por Azure AD para registrar la respuesta.  
    Para entornos de producción, use:`https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2`  
    En entornos de prueba, use:`https://fs-test.dayforcehcm.com/sp/ACS.saml2`  
   

4. En la página **configurar inicio de sesión único en gestión RR. de Ceridian Dayforce** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para obtener el SSO configurado para la aplicación, póngase en contacto con el soporte técnico de gestión RR. de Ceridian Dayforce por correo electrónico y proporcionarles lo siguiente:

    - El archivo de certificado descargados
    - La **dirección URL del emisor**
    - La **dirección URL de SAML SSO** 
    - El **Inicio de sesión único fuera de la dirección URL del servicio** 


6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-ceridian-dayforce-hcm-test-user"></a>Crear un usuario de prueba de Dayforce de Ceridian gestión RR.

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en Ceridian Dayforce HCM. 

Trabaje con el equipo de soporte técnico de gestión RR. de Ceridian Dayforce para obtener los usuarios agregados a su en la aplicación de gestión RR. de Ceridian Dayforce. 





### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a gestión RR. de Ceridian Dayforce.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Ceridian Dayforce HCM, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Gestión RR. de Ceridian Dayforce**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de gestión RR. de Ceridian Dayforce en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de gestión RR. de Ceridian Dayforce.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png
