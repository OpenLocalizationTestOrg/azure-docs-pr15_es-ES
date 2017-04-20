<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con PerformanceCentre | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y PerformanceCentre."
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
    ms.date="09/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integración de Azure Active Directory con PerformanceCentre

El objetivo de este tutorial es mostrar cómo integrar PerformanceCentre con Azure Active Directory (AD Azure).  
Integrar PerformanceCentre con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a PerformanceCentre 
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en PerformanceCentre (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure Active Directory

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con PerformanceCentre, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un PerformanceCentre inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres pilares básicos:

1. Agregar PerformanceCentre de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-performancecentre-from-the-gallery"></a>Agregar PerformanceCentre de la Galería
Para configurar la integración de PerformanceCentre en Azure AD, debe agregar PerformanceCentre desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar PerformanceCentre de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **PerformanceCentre**.
 
    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **PerformanceCentre**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Aplicaciones][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con PerformanceCentre en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en PerformanceCentre a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en PerformanceCentre.  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en PerformanceCentre.
 
Para configurar y probar el inicio de sesión único de Azure AD con PerformanceCentre, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un PerformanceCentre probar usuario](#creating-a-halogen-software-test-user)** - a tiene equivalente de Britta Simon en PerformanceCentre que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación PerformanceCentre.

**Para configurar el inicio de sesión único Azure AD con PerformanceCentre, siga estos pasos:**

1. En el portal de Azure AD clásico, en la página de integración de aplicación **PerformanceCentre** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión PerformanceCentre** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][7] 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![En el inicio de sesión único de Azure AD][8] 
 
     una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en su sitio de PerformanceCentre (por ejemplo: *http://companyname.performancecentre.com/saml/SSO*).
 
     b. Haga clic en **siguiente**.
 
4. En la página **configurar inicio de sesión único en PerformanceCentre** , realice los pasos siguientes:

    ![En el inicio de sesión único de Azure AD][9] 

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.



1. Inicio de sesión en el sitio de su empresa **PerformanceCentre** como administrador.

2. En la pestaña en el lado izquierdo, haga clic en **Configurar**.

    ![En el inicio de sesión único de Azure AD][10]

2. En la pestaña en el lado izquierdo, haga clic en **varios**y, a continuación, haga clic en **Inicio de sesión único**.

    ![En el inicio de sesión único de Azure AD][11]

2. Como **protocolo**, seleccione **SAML**.

    ![En el inicio de sesión único de Azure AD][12]

2. Abra el archivo de metadatos descargados en el Bloc de notas, copie el contenido, pegarlo en el cuadro de texto de **Metadatos del proveedor de identidades** y, a continuación, haga clic en **Guardar**.

    ![En el inicio de sesión único de Azure AD][13]

2. Compruebe que los valores de la **Dirección URL de Base de entidad** y **entidad ID** están correctos.

    ![En el inicio de sesión único de Azure AD][14]


6. En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![En el inicio de sesión único de Azure AD][15]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][16]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-a-performancecentre-test-user"></a>Crear un usuario de prueba PerformanceCentre

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en PerformanceCentre.

**Para crear un usuario llamado a Britta Simon en PerformanceCentre, siga estos pasos:**

1. Inicie sesión como administrador el sitio de su empresa PerformanceCentre.

2. En el menú de la izquierda, haga clic en **Interrelate**y, a continuación, haga clic en **Crear participante**.

    ![Crear usuario][400]

4. En el cuadro de diálogo **Interrelate - crear participante** , realice los pasos siguientes:

    ![Crear usuario][401]

    una. Escriba los atributos necesarios para Britta Simon en cuadros de texto relacionados.
    
    > [AZURE.IMPORTANT] Atributo de nombre de usuario de Bárbara en PerformanceCentre debe ser el mismo que el nombre de usuario en Azure AD.


    b. Seleccione **Administrador de cliente** como **Elegir rol**. 

    c. Haga clic en **Guardar**.   


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a PerformanceCentre.

![Asignar usuario][200] 

**Para asignar a Britta Simon a PerformanceCentre, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **PerformanceCentre**.

    ![Asignar usuario][202]

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de PerformanceCentre en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación PerformanceCentre.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png



