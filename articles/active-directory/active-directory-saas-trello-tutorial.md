<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Trello | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Trello."
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


# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Tutorial: Integración de Azure Active Directory con Trello

En este tutorial, aprenderá a integrar Trello con Azure Active Directory (AD Azure).

Integrar Trello con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Trello
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Trello (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Trello, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un **Trello** inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba. El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Trello de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-trello-from-the-gallery"></a>Agregar Trello de la Galería
Para configurar la integración de Trello en Azure AD, debe agregar Trello desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Trello de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Trello**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)

7. En el panel de resultados, seleccione **Trello**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con Trello en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Trello a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Trello.
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Trello. Para configurar y probar el inicio de sesión único de Azure AD con Trello, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un Trello probar usuario](#creating-a-the-funding-portal-test-user)** - a tiene equivalente de Britta Simon en Trello que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación Trello.

Aplicación de Trello espera las afirmaciones SAML que contenga atributos específicos. Configure los siguientes atributos de esta aplicación. Puede administrar los valores de estos atributos en la pestaña **"Atrributes"** de la aplicación. La siguiente captura de pantalla muestra un ejemplo de este.

![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png) 

**Para configurar el inicio de sesión único Azure AD con Trello, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación **Trello** , en el menú de la parte superior, haga clic en **atributos**.
     
    ![Configurar el inicio de sesión único][5]


2. En el cuadro de diálogo **atributos de token SAML** , para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes:
    

  	| Nombre del atributo | Valor del atributo |
  	| --- | --- |    
  	| User.Email | User.Mail |
  	| User.FirstName | User.givenName |
  	| User.LastName | User.Surname |

    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)
    
    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    
    c. En la lista **Valor del atributo** , seleccione el valor que se muestra de la fila.
    
    d. Haga clic en **Finalizar**. A continuación, **Aplicar cambios** en la parte inferior de la página.

3. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único][6]

4. En el portal clásico, en la página de integración de aplicación **Trello** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][7] 

5. En la página **¿cómo desea que los usuarios para iniciar sesión Trello** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)

6. En la página de diálogo **Configurar opciones de aplicación** , si desea configurar la aplicación en **IDP iniciadas modo**, realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)


    una. En el cuadro de texto dirección URL de respuesta, escriba una dirección URL mediante el siguiente patrón: `https://trello.com/auth/saml/consume/<enterprise>`.

    b. Haga clic en **siguiente**.

> [AZURE.NOTE] Debe obtener la ** \<enterprise\> ** indicaciones de Trello. Si no tiene el valor de indicaciones, póngase en contacto con el equipo de soporte de Trello de <support@trello.com> para obtener las indicaciones enterprise.

6. Si desea configurar la aplicación en **SP iniciados por el modo** en la página de diálogo de **Configuración de aplicación** , haga clic en **"Mostrar avanzadas configuración (opcional)"** y, a continuación, introduzca el **Inicio de sesión en la dirección URL**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba una dirección URL mediante el siguiente patrón:`https://trello.com/auth/saml/consume/<enterprise>`

    b. Haga clic en **siguiente**

7. En la página **configurar inicio de sesión único en Trello** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo en el equipo.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)

6. Para obtener el SSO configurado para la aplicación, vaya a la página de [configuración de SSO de empresa de Trello](https://trello.com/sso-configuration) para enviar Trello equipo el inicio de sesión en dirección URL y adjuntar el certificado descargado.

7. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

8. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-trello-test-user"></a>Crear un usuario de prueba Trello

En esta sección, creará un usuario llamado a Britta Simon en Trello. En esta sección, creará un usuario llamado a Britta Simon en Trello. Trello admite aprovisionamiento en el tiempo y se creará una nueva cuenta de la primera vez que inicie sesión de Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Trello.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Trello, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Trello**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de todos los usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de Trello en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación Trello.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png
