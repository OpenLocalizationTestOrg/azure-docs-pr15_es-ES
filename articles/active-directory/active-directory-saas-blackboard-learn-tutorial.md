<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con más de Pizarra | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y obtener información de la Pizarra."
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


# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Tutorial: Integración de Azure Active Directory con más de Pizarra

En este tutorial, aprenderá a integrar obtener Pizarra con Azure Active Directory (AD Azure).

Integración de obtener Pizarra con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a más de Pizarra
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en más de Pizarra (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con más de la Pizarra, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Una plataforma de nube de pizarra obtener inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar más de Pizarra de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-blackboard-learn-from-the-gallery"></a>Agregar más de Pizarra de la Galería
Para configurar la integración de obtener la Pizarra en Azure AD, debe agregar más Pizarra desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar más Pizarra desde la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Más Pizarra**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_01.png)

7. En el panel de resultados, seleccione **Obtener la Pizarra**y, a continuación, haga clic en **completado** para agregar la aplicación.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_06.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con más de pizarra en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en pizarra aprender a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en más de la Pizarra.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en pizarra obtener establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con más de la Pizarra, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear una pizarra obtener probar usuario](#creating-a-blackboard-learn-test-user)** - a tiene equivalente de Britta Simon en pizarra saber que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación obtener Pizarra.

Aplicación de aprendizaje de pizarra espera las afirmaciones SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la pestaña **"Atrribute"** de la aplicación. La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_51.png) 

**Para configurar el inicio de sesión único Azure AD con más de Pizarra, siga estos pasos:**


1. En el portal de clásico Azure, en la página integración **Más Pizarra** de aplicación en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_80.png) 


1. En el cuadro de diálogo **atributos de token SAML** , para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes: nos hemos asignar Userprincipalname como el atributo de usuario único aquí, pero puede asignar el valor apropiado que distinguir de forma exclusiva el usuario de la organización y que se asigna a la Pizarra información sobre el campo de nombre de usuario.

  	| Nombre del atributo | Valor del atributo |
  	| --- | --- |    
  	| urn:oid:1.3.6.1.4.1.5923.1.1.1.6  | User.userPrincipalName |
   
 
    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_81.png) 


    b. En el cuadro de texto **Nombre del atributo isErrorPage** , escriba el nombre del atributo que se muestra de la fila.

    c. En la lista **Valor del atributo** , selsect el valor del atributo se muestra de la fila.

    d. Haga clic en **Finalizar**.  

2. En el portal clásico, en la página de integración de aplicación de **Más de la Pizarra** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

3. En la página **¿cómo le gustaría usuarios a iniciar sesión más de la Pizarra** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_03.png) 

4. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación obtener Pizarra utilizando el modelo siguiente: **https://\<precios de nombre de compañía\>.blackboard.com/**
    
    b. Haga clic en **siguiente**
 
5. En la página **configurar inicio de sesión único en más de la Pizarra** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_05.png)

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


6. Para obtener SSO configurado para la aplicación, póngase en contacto con pizarra obtener soporte técnico y proporcionarles lo siguiente:

    • Los metadatos descargados


7. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

8. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.


![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , siga estos pasos:  ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , siga estos pasos: ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-blackboard-learn-test-user"></a>Crear un usuario de prueba más Pizarra

En esta sección, cree un usuario llamado a Britta Simon en más de la Pizarra. 

Aplicación de aprendizaje de pizarra admite solo en el aprovisionamiento de usuario de tiempo. Asegúrese de que ha configurado las notificaciones, como se describe en la sección ** [Configurar Azure AD inicio de sesión único](#configuring-azure-ad-single-sign-on)**

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a más de Pizarra.

![Asignar usuario][200] 

**Para asignar a Britta Simon para obtener información de la Pizarra, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Obtener Pizarra**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Pizarra más soporte de aplicaciones al hacer clic en el icono más Pizarra en el Panel de acceso, debe obtener firmado en automáticamente a la aplicación obtener Pizarra.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_205.png
