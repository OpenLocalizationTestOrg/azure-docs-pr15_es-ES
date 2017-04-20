<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Pluralsight | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Pluralsight."
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


# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Tutorial: Integración de Azure Active Directory con Pluralsight

El objetivo de este tutorial es mostrar cómo integrar Pluralsight con Azure Active Directory (AD Azure).

Integración de Pluralsight con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Pluralsight
- Puede habilitar los usuarios automáticamente obtener firmado en Pluralsight (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure


Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Pluralsight, necesitará los elementos siguientes:

- Una suscripción de Azure
- Un Pluralsight inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Pluralsight desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-pluralsight-from-the-gallery"></a>Agregar Pluralsight desde la Galería
Para configurar la integración de Pluralsight en Azure AD, debe agregar Pluralsight desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Pluralsight desde la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
 
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Pluralsight**.
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)

7. En el panel de resultados, seleccione **Pluralsight**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con Pluralsight en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para configurar y probar el inicio de sesión único de Azure AD con Pluralsight, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un Pluralsight probar usuario](#creating-a-pluralsight-test-user)** - a tiene equivalente de Britta Simon en Pluralsight que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de Pluralsight.

La aplicación de Pluralsight espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de atributos token de SAML. La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

También puede agregar el atributo **"identificador exclusivo"** con el valor correspondiente como IdEmpleado o algo más que le convenga para su organización. Observe también que no es el atributo obligatorio; Sin embargo, puede agregarla para identificar el usuario único. 

**Para configurar el inicio de sesión único Azure AD con Pluralsight, realice los pasos siguientes:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **Pluralsight** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 



1. Para quitar los **atributos de token SAML**de redundantes, realice los pasos siguientes: 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/2829.png) 


    una. Para cada atributo de usuario en el cuadro rojo de la tabla anterior, desplace el puntero sobre el atributo y, a continuación, haga clic en eliminar. 




1. Para agregar los necesarios **atributos de token SAML**, para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes:

  	| Nombre del atributo | Valor del atributo |
  	| --- | --- |    
  	| Nombre| User.givenName |
  	| Apellido  | User.Surname |
  	| Correo electrónico | User.Mail |

    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png) 


    b. En el cuadro de texto **Nombre del atributo isErrorPage** , escriba el nombre del atributo que se muestra de la fila.

    c. En la lista **Valor del atributo** , selsect el valor del atributo se muestra de la fila.

    d. Haga clic en **Finalizar**.  
    


1. Haga clic en **Aplicar cambios**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/3232.png)  



1. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  









1. En el portal de clásico Azure, en la página de integración de aplicación de **Pluralsight** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión Pluralsight** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , siga estos pasos:.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de Pluralsight utilizando el modelo siguiente:`https://<instance name>.pluralsight.com/sso/<comapny name>`

    b. Haga clic en **siguiente**.


4. En la página **configurar inicio de sesión único en Pluralsight** , realice los pasos siguientes:  ![configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png) 

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para obtener SSO configurado para la aplicación, póngase en contacto con el equipo de [Professional Services](mailTo:professionalservices@pluralsight.com) de Pluralsight y proporcionar el archivo de metadatos descargado.


6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
  
    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

En la lista de usuarios, seleccione **Bárbara Menéndez**.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-pluralsight-test-user"></a>Crear un usuario de prueba de Pluralsight

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en Pluralsight. Trabaje con el equipo de soporte técnico de Pluralsight para agregar los usuarios en la cuenta de Pluralsight. 


> [AZURE.NOTE]Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico de Pluralsight.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Pluralsight.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Pluralsight, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Pluralsight**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de Pluralsight en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de Pluralsight.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png
