<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con la página de estado | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y página de estado."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integración de Azure Active Directory con la página de estado

El objetivo de este tutorial es mostrar cómo integrar la página de estado con Azure Active Directory (AD Azure).

Página de estado de la integración con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a la página de estado 
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en la página de estado (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con la página de estado, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Una página de estado inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar página de estado de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-statuspage-from-the-gallery"></a>Agregar página de estado de la Galería
Para configurar la integración de la página de estado en Azure AD, debe agregar la página de estado de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar la página de estado de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
 
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **página de estado**.
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)

7. En el panel de resultados, seleccione la **página de estado**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con la página de estado en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en la página de estado para un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en la página de estado.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en la página de estado.
 
Para configurar y probar el inicio de sesión único de Azure AD con la página de estado, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear una página de estado probar usuario](#creating-a-statuspage-test-user)** - tener un equivalente de Britta Simon en la página de estado que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de la página de estado. 



**Para configurar el inicio de sesión único Azure AD con la página de estado, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **página de estado** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para suscribirse a la página de estado** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 


3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 

    > [AZURE.NOTE] Póngase en contacto con el equipo de soporte técnico de la página de estado en [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)para solicitar metadatos necesarios para configurar el inicio de sesión único.


    una. Desde los metadatos, copie el valor del emisor y péguelo en el cuadro de texto de **identificador** .

    b. Desde los metadatos, copie la dirección URL de respuesta y, a continuación, péguelo en el cuadro de texto **Dirección URL de respuesta** .

    c. Haga clic en **siguiente**.
 
 
4. En la página **configurar inicio de sesión único en la página de estado** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


1. En otra ventana del explorador, inicie sesión el sitio de su empresa de página de estado como administrador.

1. En la barra de herramientas principal, haga clic en **Administrar cuenta**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 


1. Haga clic en la ficha **Inicio de sesión único** . 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 


1. En la página Configuración de SSO, realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 

    una. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en la página de estado** , copie el valor de **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto **Dirección URL de destino de SSO** . 

    b. Abra su certificado descargado en el Bloc de notas, copiar el contenido y péguelo en el cuadro de texto de **certificado** . 

    c. Haga clic en **Guardar**.


6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
  
    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.



![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png)  

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-a-statuspage-test-user"></a>Crear un usuario de prueba de la página de estado

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en la página de estado.
Página de estado admite en el tiempo de aprovisionamiento. Ya ha habilitado en [El inicio de sesión único configurar Azure AD](#configuring-azure-ad-single-single-sign-on).


**Para crear un usuario llamado a Britta Simon en la página de estado, siga estos pasos:**

1. Inicio de sesión en el sitio de su empresa de página de estado como administrador.

1. En el menú de la parte superior, haga clic en **Administrar cuenta**.

1. Haga clic en la pestaña de los miembros del equipo. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 

1. Haga clic en **Agregar a miembro del equipo**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 

1. Escriba la **Dirección de correo electrónico**, **nombre** y **Apellidos** de un usuario válido que desee aprovisionar en los cuadros de texto relacionados. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 

1. Como **rol**, elija **Administrador de cliente**.

1. Haga clic en **crear cuenta**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar Azure inicio de sesión único para conceder acceso a la página de estado.

![Asignar usuario][200] 

**Para asignar a Britta Simon a la página de estado, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione la **página de estado**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de la página de estado en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de la página de estado.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png






