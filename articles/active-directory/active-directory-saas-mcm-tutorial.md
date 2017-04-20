<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con MCM | Microsoft Azure" 
    description="Aprenda a usar MCM con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Tutorial: Integración de Azure Active Directory con MCM
  
El objetivo de este tutorial es mostrar cómo integrar MCM con Azure Active Directory (AD Azure).

Integración de MCM con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a MCM
- Puede habilitar los usuarios automáticamente obtener firmado en MCM (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con MCM, necesitará los elementos siguientes:

- Una suscripción válida de Azure
- Un MCM inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar MCM desde la Galería
2. Configurar y probar Azure AD de sesión único

## <a name="adding-mcm-from-the-gallery"></a>Agregar MCM desde la Galería
Para configurar la integración de MCM en Azure AD, debe agregar MCM desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar MCM desde la galería, realice los pasos siguientes:**

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **MCM**.

    ![Galería de aplicación] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **MCM**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![MCM] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con MCM en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en MCM a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en MCM.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en MCM establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con MCM, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un MCM probar usuario](#creating-a-mcm-test-user)** - a tiene equivalente de Britta Simon en MCM que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD
  
En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación de MCM.

**Para configurar el inicio de sesión único Azure AD con MCM, realice los pasos siguientes:**

1.  En el portal de clásico Azure, en la página de integración de aplicación **MCM** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión MCM** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Inicio de sesión único de AD en Microsoft Azure] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Inicio de sesión único de AD en Microsoft Azure")

3.  En la página de diálogo Configurar opciones de aplicación, realice los pasos siguientes:

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurar la dirección URL de la aplicación")

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba: `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en MCM** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurar el inicio de sesión único")

5. Para obtener el SSO configurado para la aplicación, póngase en contacto con el soporte técnico MCM. Adjunte el archivo de metadatos descargados y compartirlo con el equipo MCM configurar SSO por su parte.

6.  En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurar el inicio de sesión único")

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurar el inicio de sesión único")


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD

Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

###<a name="creating-a-mcm-test-user"></a>Crear un usuario de prueba MCM
  
En esta sección, creará un usuario llamado a Britta Simon en MCM. Trabaje con el equipo de soporte técnico MCM para agregar los usuarios de la plataforma MCM.

>[AZURE.NOTE]Puede usar cualquier otros MCM usuario cuenta herramientas de creación o API proporcionan por MCM a cuentas de usuario AAD de aprovisionamiento.


###<a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD
  
El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a MCM.
    
![Asignar usuarios] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Asignar usuarios")

**Para asignar a Britta Simon a MCM, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Asignar usuarios] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Asignar usuarios")

2. En la lista de aplicaciones, seleccione **MCM**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. En el menú de la parte superior, haga clic en **usuarios**.
    
    ![Asignar usuarios] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Asignar usuarios")

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.
    
    ![Asignar usuarios] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Asignar usuarios")


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.
 
Al hacer clic en el mosaico MCM en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su aplicación MCM.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)