<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con el correo electrónico de Skydesk | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y correo electrónico Skydesk."
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


# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integración de Azure Active Directory con el correo electrónico de Skydesk

El objetivo de este tutorial es mostrar cómo integrar el correo electrónico de Skydesk con Azure Active Directory (AD Azure).

Integración de correo electrónico de Skydesk con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso al correo electrónico Skydesk
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en correo electrónico de Skydesk (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure Active Directory

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con correo electrónico de Skydesk, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un correo electrónico de Skydesk inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Adición de correo electrónico de Skydesk de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-skydesk-email-from-the-gallery"></a>Adición de correo electrónico de Skydesk de la Galería
Para configurar la integración de correo electrónico de Skydesk en Azure AD, debe agregar Skydesk de correo electrónico desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Skydesk de correo electrónico de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Correo electrónico Skydesk**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)

7. En el panel de resultados, seleccione **Correo electrónico de Skydesk**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD Skydesk de correo basadas en un usuario de prueba denominado a "Britta Simon".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente Skydesk por correo electrónico a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado Skydesk por correo electrónico.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** de correo electrónico Skydesk.

Para configurar y probar el inicio de sesión único de Azure AD con el correo electrónico de Skydesk, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un correo electrónico de Skydesk probar usuario](#creating-a-Skydesk-Email-test-user)** - tener un equivalente de Britta Simon en Skydesk de correo electrónico que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de correo electrónico Skydesk.



**Para configurar el inicio de sesión único Azure AD con correo electrónico de Skydesk, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **Correo electrónico de Skydesk** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión correo electrónico de Skydesk** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png) 


3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png) 


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación de correo electrónico Skydesk mediante el siguiente patrón: **"https://mail.skydesk.jp/portal/\<nombre de la compañía\>"**.

    b. Haga clic en **siguiente**.


4. En la página **configurar inicio de sesión único con el correo electrónico de Skydesk** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para habilitar SSO **Skydesk**por correo electrónico, siga estos pasos:
 
    una. Inicio de sesión en su cuenta de correo electrónico de Skydesk como administrador.

    b. En el menú de la parte superior, haga clic en configuración y a continuación, seleccione org. 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)

    c. Haga clic en dominios desde el panel izquierdo.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Haga clic en Agregar dominio.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Escriba su nombre de dominio y, a continuación, compruebe el dominio.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Haga clic en **Autenticación de SAML** en el panel izquierdo

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

6. En la página de diálogo **SAML autenticación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [AZURE.NOTE] Para usar la autenticación de SAML según debería tener **comprobado dominio** o **portal URL** el programa de instalación. Puede configurar el portal de dirección URL con el nombre único.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)


    una. En el portal de Azure AD clásico, copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .

    b. En el portal de Azure AD clásico, copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto de dirección URL de **cierre de sesión** .

    c. **Cambiar contraseña dirección URL** es opcional lo deje en blanco.

    d. Haga clic en **Obtener clave desde archivo** para seleccionar el certificado de correo electrónico Skydesk descargado y, a continuación, haga clic en **Abrir** para cargar el certificado.

    e. **Algoritmo de**, seleccione **RSA**.

    f. Haga clic en **Aceptar** para guardar los cambios.


7. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

8. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
  
    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-skydesk-email-test-user"></a>Crear un usuario de prueba de correo electrónico Skydesk

En esta sección, creará un usuario llamado a Britta Simon Skydesk por correo electrónico.

una. Haga clic en **Acceso de usuario** en el panel izquierdo Skydesk por correo electrónico y, a continuación, escriba el nombre de usuario. 

![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)


[AZURE.NOTE] Si necesita crear usuarios de forma masiva, debe ponerse en contacto con el equipo de soporte técnico de correo electrónico Skydesk.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al correo electrónico Skydesk.

![Asignar usuario][200] 

**Para asignar a Britta Simon al correo electrónico de Skydesk, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
 
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Correo electrónico Skydesk**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el icono de correo electrónico de Skydesk en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de correo electrónico Skydesk.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png
