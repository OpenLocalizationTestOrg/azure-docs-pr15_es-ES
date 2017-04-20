<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con @Task| Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y @Task."
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


# <a name="tutorial-azure-active-directory-integration-with-task"></a>Tutorial: Integración de Azure Active Directory con@Task

El objetivo de este tutorial es mostrar cómo integrar @Task con Azure Active Directory (AD Azure).  
Integración de @Task con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso al@Task
- Puede habilitar los usuarios obtener automáticamente firmado en @Task (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el Portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Configurar la integración de Azure AD con @Task, tiene los siguientes elementos:

- Una suscripción de Azure AD
- Un @Task el inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres pilares básicos:

1. Agregar @Task de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-task-from-the-gallery"></a>Agregar @Task de la Galería
Para configurar la integración de @Task en Azure AD, debe agregar @Task desde la Galería de la lista de aplicaciones de SaaS administradas.

**Agregar @Task desde la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1] 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2] 

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3] 

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4] 

6. En el cuadro Buscar, escriba **@Task**.

    ![Aplicaciones][5] 

7. En el panel de resultados, seleccione **@Task**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Aplicaciones][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único

El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con @Task en función de un usuario de prueba denominado "Bárbara Menéndez".

Para que el inicio de sesión único trabajar, necesita saber lo que el usuario equivalente en Azure AD @Task a un usuario en Azure AD es. En otras palabras, una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en @Task debe establecerse.   
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en @Task.
 
Configurar y probar el inicio de sesión único de Azure AD con @Task, tiene que completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un @Tasktest usuario](#creating-a-halogen-software-test-user) ** - tener un equivalente de Britta Simon en @Taskthat está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es para habilitar el inicio de sesión único de Azure AD en el portal de clásico Azure y configurar inicio de sesión único en su @Task aplicación.

**Para configurar el inicio de sesión único de Azure AD con @Task, realice los pasos siguientes:**

1. En el portal de Azure clásico, en el **@Task** integración de las aplicaciones de página, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la **cómo le gustaría usuarios a iniciar sesión en @Task ** de página, seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][7] 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Establecer la configuración de la aplicación][8] 
 
     una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en su @Task aplicación (por ejemplo:*https://<Tenant name>.attask ondemand.com*).

     b. Haga clic en **siguiente**.

4. En la **configurar inicio de sesión único en @Task ** de página, haga clic en **descargar metadatos**, guarde el archivo de metadatos localmente en el equipo y, a continuación, haga clic en **siguiente**.

    ![¿Qué es Azure AD Connect][9] 



1. Inicio de sesión en su @Task sitio de la empresa como administrador.

2. Vaya a **configuración de inicio de sesión único**.


1. En el cuadro de diálogo **Inicio de sesión único** , realice los pasos siguientes

    ![Configurar el inicio de sesión único][23]

    una. Como **tipo**, seleccione **SAML 2.0**.

    b. Seleccione el **ID de proveedor de servicio**.

    c. En el portal de clásico Azure, copie la **Dirección URL de inicio de sesión remota**y péguelo en el cuadro de texto de la **Dirección URL del Portal de inicio de sesión** .

    d. En el portal de clásico Azure, copie la **Dirección URL del servicio de Sign-Out único**y péguelo en el cuadro de texto **Dirección URL de Sign-Out** .

    e. En el portal de clásico Azure, copie la **Dirección URL de cambiar contraseña**y, a continuación, péguelo en el cuadro de texto **Cambiar contraseña dirección URL** .

    f. Haga clic en **Guardar**.

6. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![¿Qué es Azure AD Connect][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![¿Qué es Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-an-task-test-user"></a>Crear un @Task usuario de prueba

Es el objetivo de esta sección crear un usuario llamado Britta Simon en @Task.


**Para crear un usuario llamado Britta Simon en @Task, realice los pasos siguientes:**

1. Inicie sesión en su @Task sitio de la empresa como administrador.

2. En el menú de la parte superior, haga clic en **personas**.

3. Haga clic en **nueva persona**. 

4. En el cuadro de diálogo nueva persona, realice los pasos siguientes:

    ![Crear un @Task usuario de prueba][21] 

    una. En el cuadro de texto **nombre** , escriba a "Bárbara".

    b. En el cuadro de texto **Apellido** , escriba a "Simon".

    c. En el cuadro de texto **Dirección de correo electrónico** , escriba la dirección de correo electrónico de Britta Simon de Azure Active Directory.

    d. Haga clic en **Agregar a persona**.




### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al @Task.

![Asignar usuario][200] 

**Para asignar Britta Simon a @Task, realizar los siguientes pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **@Task**.

    ![Asignar usuario][202] 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el @Task de mosaico en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su @Task aplicación.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






