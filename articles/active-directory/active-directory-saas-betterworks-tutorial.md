<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con BetterWorks | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y BetterWorks."
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


# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Tutorial: Integración de Azure Active Directory con BetterWorks

El objetivo de este tutorial es mostrar cómo integrar BetterWorks con Azure Active Directory (AD Azure).

Integrar BetterWorks con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a BetterWorks
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en BetterWorks (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con BetterWorks, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un BetterWorks inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar BetterWorks de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-betterworks-from-the-gallery"></a>Agregar BetterWorks de la Galería
Para configurar la integración de BetterWorks en Azure AD, debe agregar BetterWorks desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar BetterWorks de la galería, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
    
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **BetterWorks**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)

7. En el panel de resultados, seleccione **BetterWorks**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Selección de la aplicación en la Galería](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con BetterWorks en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en BetterWorks a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en BetterWorks.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en BetterWorks.

Para configurar y probar el inicio de sesión único de Azure AD con BetterWorks, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un BetterWorks probar usuario](#creating-a-betterworks-test-user)** - a tiene equivalente de Britta Simon en BetterWorks que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación BetterWorks.

Aplicación de BetterWorks espera las afirmaciones SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la pestaña "**Atrribute**" de la aplicación. La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Para configurar el inicio de sesión único Azure AD con BetterWorks, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación **BetterWorks** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)

2. En el cuadro de diálogo **atributos de token SAML** , para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes:
    

  	| Nombre del atributo | Valor del atributo |
  	| --- | --- |    
  	| saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)
    
    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    
    c. En la lista **Valor del atributo** , escriba el identificador de token de saml se muestra de la fila.
    
    d. Haga clic en **completada**

3. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 

4. En la página **¿cómo desea que los usuarios para iniciar sesión BetterWorks** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)

5. En la página de diálogo **Configurar opciones de aplicación** , si desea configurar la aplicación en **IDP iniciadas modo**, realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)


    una. En el cuadro de texto **identificador** , escriba la dirección URL en el modelo siguiente:`https://app.betterworks.com/saml2/metadata/`


    b. En el cuadro de texto **Dirección URL de respuesta** , escriba la dirección URL en el modelo siguiente:`https://app.betterworks.com/saml2/acs/`


    c. Haga clic en **siguiente**

6. En la página de diálogo **Configurar opciones de aplicación** , si desea configurar la aplicación en **SP iniciadas modo**, lleve a cabo la en los siguientes pasos:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)

    una.  Seleccione **Mostrar avanzadas configuración (opcional)**.



    b. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de BetterWorks utilizando el modelo siguiente:`https://app.betterworks.com`

    b. Haga clic en **siguiente**.

7. En la página **configurar inicio de sesión único en BetterWorks** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.

8. Para obtener SSO configurado para la aplicación, póngase en contacto con el soporte técnico de BetterWorks mediante <mailto:support@betterworks.com>. Adjunte el archivo de metadatos descargados y compartirlo con el equipo de BetterWorks para configurar el SSO por su parte.

9. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-betterworks-test-user"></a>Crear un usuario de prueba BetterWorks

En esta sección, creará un usuario llamado a Britta Simon en BetterWorks. 

Trabaje con el equipo de soporte técnico de BetterWorks a través de <mailto:support@betterworks.com> para agregar los usuarios de la plataforma de BetterWorks.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a BetterWorks.
    
   ![Asignar usuario][200]

**Para asignar a Britta Simon a BetterWorks, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **BetterWorks**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)

1. En el menú de la parte superior, haga clic en **usuarios**.
    
    ![Asignar usuario][203]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.
    
    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.
 
Al hacer clic en el mosaico de BetterWorks en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación BetterWorks.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png
