<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con QuickHelp | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y QuickHelp."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Tutorial: Integración de Azure Active Directory con QuickHelp

El objetivo de este tutorial es mostrar cómo integrar QuickHelp con Azure Active Directory (AD Azure).  
Integración de QuickHelp con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a QuickHelp 
- Puede habilitar los usuarios automáticamente obtener firmado en QuickHelp (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con QuickHelp, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un QuickHelp inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar ayuda rápida de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-quickhelp-from-the-gallery"></a>Agregar ayuda rápida de la Galería
Para configurar la integración de QuickHelp en Azure AD, debe agregar QuickHelp desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar QuickHelp desde la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **QuickHelp**.

    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **QuickHelp**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Aplicaciones][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con QuickHelp en función de un usuario de prueba denominado a "Bárbara Menéndez".


Para configurar y probar el inicio de sesión único de Azure AD con QuickHelp, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un QuickHelp probar usuario](#creating-a-quickhelp-test-user)** - a tiene equivalente de Britta Simon en QuickHelp que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación QuickHelp.


**Para configurar el inicio de sesión único Azure AD con QuickHelp, realice los pasos siguientes:**

1. En el portal de clásico Azure, en la página de integración de aplicación **QuickHelp** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión QuickHelp** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][7] 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Establecer la configuración de la aplicación][8] 
 
     una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en su sitio QuickHelp (por ejemplo:* https://quickhelp.com/bsiazure/*).

     > [AZURE.NOTE] Póngase en contacto con el soporte técnico de QuickHelp si no conoce el valor de inicio de sesión en dirección URL.

     b. Haga clic en **siguiente**.

 
4. En la página **configurar inicio de sesión único en QuickHelp** , realice los siguientes pasos: haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![¿Qué es Azure AD Connect][9] 



1. Inicio de sesión en el sitio de su empresa QuickHelp como administrador.

2. En el menú de la parte superior, haga clic en **Administrador**.

    ![Configurar el inicio de sesión único][21]


1. En el menú **Administración QuickHelp** , haga clic en **configuración**.

    ![Configurar el inicio de sesión único][22]

1. Haga clic en **configuración de autenticación**.

1. En la página **Configuración de autenticación** , realice los pasos siguientes

    ![Configurar el inicio de sesión único][23]

    una. Como **Tipo de SSO**, seleccione **WSFederation**.

    b. Para cargar el archivo de metadatos de Azure descargado, haga clic en **Examinar**, desplácese hasta el archivo, final, a continuación, haga clic en **Cargar metadatos**.

    c. En el cuadro de texto de **correo electrónico** , escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. En el cuadro de texto **nombre** , **escriba http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. En el cuadro de texto **Apellido** , **escriba http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    f. En la **Barra de acciones**, haga clic en **Guardar**.







6. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![¿Qué es Azure AD Connect][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![¿Qué es Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  
En la lista de usuarios, seleccione **Bárbara Menéndez**.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-a-quickhelp-test-user"></a>Crear un usuario de prueba QuickHelp

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en QuickHelp.
Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en QuickHelp a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Ayuda rápida.

QuickHelp admite en el tiempo de aprovisionamiento. Esto significa que, si es necesario, una cuenta de usuario se crea automáticamente en QuickHelp y la cuenta está vinculada a la cuenta de Azure AD.

No hay ningún elemento de acción para en esta sección.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a QuickHelp.

![Asignar usuario][200] 

**Para asignar a Britta Simon a QuickHelp, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **QuickHelp**.

    ![Asignar usuario][202] 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el icono de Ayuda rápida en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación QuickHelp.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png




