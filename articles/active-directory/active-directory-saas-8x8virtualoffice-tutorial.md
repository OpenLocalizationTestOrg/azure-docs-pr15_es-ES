<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con 8 x 8 Virtual Office | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y de 8 x 8 Virtual Office."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integración de Azure Active Directory con Office Virtual de 8 x 8

El objetivo de este tutorial es mostrar cómo integrar Office Virtual de 8 x 8 con Azure Active Directory (AD Azure).

Integración de 8 x 8 Office Virtual con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Office Virtual de 8 x 8
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Office Virtual de 8 x 8 (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Office Virtual de 8 x 8, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un 8 x 8 Office Virtual inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Microsoft Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Adición de 8 x 8 Virtual Office desde la Galería
2. Configurar y probar el inicio de sesión único de Microsoft Azure AD


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adición de 8 x 8 Virtual Office desde la Galería
Para configurar la integración de Office de 8 x 8 Virtual en Azure AD, debe agregar 8 x 8 Virtual Office desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar 8 x 8 Virtual Office de la galería, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **8 x 8 Virtual Office**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. En el panel de resultados, seleccione **Office virtuales de 8 x 8**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Selección de la aplicación en la Galería](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurar y probar el inicio de sesión único de Microsoft Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Microsoft Azure AD con 8 x 8 que Office Virtual en función de un usuario de prueba denominado a "Britta Simon".

Para que el inicio de sesión único trabajar, Azure AD necesita saber lo que el usuario equivalente en Office Virtual para un usuario en Azure AD es de 8 x 8. En otras palabras, una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de 8 x 8 Office Virtual debe establecerse.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Office virtuales de 8 x 8.

Para configurar y probar el inicio de sesión único de Microsoft Azure AD con Office Virtual de 8 x 8, debe completar los siguientes bloques de creación:

1. **[Configurar Microsoft Azure AD inicio de sesión único](#configuring-azure-ad-single-single-sign-on)** : para habilitar los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - para probar Microsoft Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un usuario de prueba de Office Virtual de 8 x 8](#creating-a-8x8-virtual-office-test-user)** - tener un equivalente de Britta Simon en Office Virtual de 8 x 8 que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Microsoft Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de AD en Microsoft Azure

En esta sección, habilitar inicio de sesión único de Microsoft Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación de Office Virtual de 8 x 8.

**Para configurar el inicio de sesión único de Microsoft Azure AD con Office Virtual de 8 x 8, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicación de **Office Virtual de 8 x 8** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión en Office Virtual de 8 x 8** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    una. En el cuadro de texto **Dirección URL de respuesta** , escriba:`https://sso.8x8.com/saml2`

    b. Haga clic en **siguiente**

4. En la página **configurar inicio de sesión único en Office Virtual de 8 x 8** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.

5. Inicio de sesión en su inquilino de Office Virtual de 8 x 8 como administrador.
6. Seleccione el **jefe de la cuenta de Office Virtual** en el Panel de la aplicación.

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Seleccione la cuenta de **empresa** para administrar y haga clic en el botón **Iniciar sesión** .

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. Haga clic en la pestaña **cuentas** en la lista del menú.

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. En la lista de cuentas, haga clic en **Inicio de sesión único** .

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. Seleccione **Signle de sesión** en el método de autenticación y haga clic en **SAML**.

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Copiar **SAML SSO dirección URL**, **solo se destaque fuera de la dirección URL del servicio** y **emisor** de Azure AD a la **dirección URL de inicio de sesión**, **Cerrar sesión URL** y **dirección URL del emisor** en Office Virtual de 8 x 8. 

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![Configurar en el lado de la aplicación](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Haga clic en el **botón para cargar el certificado que ha descargado desde Azure AD** .

13. Haga clic en el botón **Guardar** .

14. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

15. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.
    
![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-8x8-virtual-office-test-user"></a>Crear un usuario de prueba de Office Virtual de 8 x 8

Es el objetivo de esta sección crear un usuario denominado a Britta Simon de 8 x 8 Virtual Office. 8 x 8 Virtual Office admite en el tiempo de aprovisionamiento, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para en esta sección. Al intentar obtener acceso a Office virtuales de 8 x 8 si todavía no existe, se creará un nuevo usuario. 

> [AZURE.NOTE] Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico de Office Virtual de 8 x 8.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Office Virtual de 8 x 8.
    
![Asignar usuario][200]

**Para asignar a Britta Simon 8 x 8 virtuales Office, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Office Virtual de 8 x 8**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. En el menú de la parte superior, haga clic en **usuarios**.
    
    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Microsoft Azure AD inicio de sesión único configuración mediante el Panel de acceso.

Al hacer clic en el icono de Office Virtual de 8 x 8 en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de Office Virtual de 8 x 8.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png
