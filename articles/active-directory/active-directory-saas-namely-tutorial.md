<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con concretamente | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y concretamente."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="prasannas"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Tutorial: Integración de Azure Active Directory con:

El objetivo de este tutorial es mostrar cómo integrar concretamente con Azure Active Directory (AD Azure).

Es decir, la integración con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a, es decir 
- Puede habilitar los usuarios obtener automáticamente firmado en concreto, (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar Azure AD integración con es decir, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una es decir, inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Es decir, agregar desde la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-namely-from-the-gallery"></a>Es decir, agregar desde la Galería
Para configurar la integración de concretamente en Azure AD, debe agregar es decir, de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar es decir, de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **concretamente**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)

7. En el panel de resultados, **es decir,**seleccione y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con es decir, en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en concreto, para un usuario en Azure AD Azure AD. En otras palabras, es decir, una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en debe establecerse.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en concreto.
 
Para configurar y probar Azure AD inicio de sesión único contigo es decir, tendrá que realizar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un probar es decir, el usuario](#creating-a-namely-test-user)** - tener un equivalente de Britta Simon en concreto, que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en su aplicación es decir. 




**Para configurar inicio de sesión único de Azure AD con, es decir, realice los pasos siguientes:**

1. En el portal de clásico Azure, en la **concretamente** integración página aplicación, haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión en concreto** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , siga estos pasos:.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión su aplicación es decir, (por ejemplo: *https://fabrikam.Namely.com/*).

    b. Haga clic en **siguiente**.
 
 
4. En la página **configurar inicio de sesión único en concreto** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


1. En otra ventana del explorador, inicie sesión en su sitio de empresa es decir, como administrador.

1. En la barra de herramientas en la parte superior, haga clic en **la empresa**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

1. Haga clic en la pestaña **configuración** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 


1. Haga clic en **SAML**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 


1. En la página **Configuración de SAML** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png) 

    una. Haga clic en **Habilitar SAML**. 

    b. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en concreto** , copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto **dirección url DDO del proveedor de identidades** . 

    c. Abra su certificado descargado en el Bloc de notas, copiar el contenido y péguelo en el cuadro de texto de **certificado del proveedor de identidades** .    

    d. Haga clic en **Guardar**.


6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

![Crear usuario de Azure AD][20]


**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png)  

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png)  

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-a-namely-test-user"></a>Crear una prueba concretamente usuario

Es el objetivo de esta sección crear un usuario denominado a Britta Simon en concreto.

**Para crear un usuario llamado a Britta Simon concreto, siga estos pasos:**

1. Inicio de sesión en su sitio de empresa es decir, como administrador.

1. En la barra de herramientas en la parte superior, haga clic en **personas**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

1. Haga clic en la ficha **directorio** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

1. Haga clic en **Agregar a nueva persona**.



1. En el cuadro de diálogo **Agregar nueva persona** , realice los pasos siguientes:

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.

    b. En el cuadro de texto **apellido** , escriba **Simon**.

    c. En el cuadro de texto de **correo electrónico** , escriba dirección de correo electrónico de Bárbara en el portal de clásico de Azure.

    d. Haga clic en **Guardar**.





### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al concretamente.

![Asignar usuario][200] 

**Para asignar Britta Simon es decir, realizar los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **concretamente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico es decir, en el Panel de acceso, debería obtener automáticamente ha iniciado sesión en su aplicación es decir.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png






