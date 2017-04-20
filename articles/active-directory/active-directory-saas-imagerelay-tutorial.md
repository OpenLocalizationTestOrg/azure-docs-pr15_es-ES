<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con ImageRelay | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ImageRelay."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-imagerelay"></a>Tutorial: Integración de Azure Active Directory con ImageRelay

El objetivo de este tutorial es mostrar cómo integrar ImageRelay con Azure Active Directory (AD Azure).  
Integrar ImageRelay con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ImageRelay
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en ImageRelay (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ImageRelay, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un inicio de sesión único de ImageRelay activado suscripción


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar ImageRelay de la Galería

2. Configurar y probar Azure AD de sesión único


## <a name="adding-imagerelay-from-the-gallery"></a>Agregar ImageRelay de la Galería
Para configurar la integración de ImageRelay en Azure AD, debe agregar ImageRelay desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar ImageRelay de la galería, realice los pasos siguientes:**

1. En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **ImageRelay**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. En el panel de resultados, seleccione **ImageRelay**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con ImageRelay en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único trabajar, Azure AD necesita una cuenta de usuario que representa el usuario relacionado en ImageRelay.  En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en ImageRelay.  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ImageRelay.

Para configurar y probar el inicio de sesión único de Azure AD con ImageRelay, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un ImageRelay probar usuario](#creating-a-userecho-test-user)** - a tiene equivalente de Britta Simon en ImageRelay que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación ImageRelay.


**Para configurar el inicio de sesión único Azure AD con ImageRelay, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación **ImageRelay** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

     ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión ImageRelay** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

     ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación ImageRelay (por ejemplo: *https://fabrikam.ImageRelay.com/*).

    b. Haga clic en **siguiente**.

4. En la página **configurar inicio de sesión único en ImageRelay** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.

5. En otra ventana del explorador, inicie sesión en el sitio de su empresa ImageRelay como administrador.

1. En la barra de herramientas en la parte superior, haga clic en la carga de trabajo de **usuarios y permisos** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Haga clic en **Crear nuevo permiso**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 

1. En la carga de trabajo de **Inicio de sesión único en configuración** , seleccione la casilla de verificación **este grupo puede solo inicio de sesión a través del inicio de sesión único** y, a continuación, haga clic en **Guardar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Vaya a **configuración de la cuenta**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Vaya a la carga de trabajo de **Inicio de sesión único en configuración** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. En el cuadro de diálogo **Configuración de SAML** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

    una. En el portal de clásico Azure, copiar la **URL de servicio de inicio de sesión único**y péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .


    b. En el portal de clásico Azure, copie la **Dirección URL del servicio de Sign-Out único**y péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .

    c. **Formato de nombre Id**, seleccione **urn: oasis: nombres: tc: SAML:1.1:nameid-formato: dirección de correo electrónico**.

    
    d. Como **Enlace opciones para las solicitudes del proveedor de servicios (retransmisión de imagen)**, seleccione **El enlace de entrada**.
   

    e. **X.509 certificado**, haga clic en **El certificado de actualización**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abrir el certificado descargado en el Bloc de notas, copie el contenido y péguelo en el cuadro de texto del certificado x.509.
  
    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. En la sección **Just aprovisionamiento de usuario** , seleccione el **Aprovisionamiento de usuario de Just habilitar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Seleccione el grupo de permisos que se permite iniciar sesión en sólo a través de inicio de sesión único (por ejemplo, **SSO básico**).

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    . Haga clic en **Guardar**.

6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.

    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-imagerelay-test-user"></a>Crear un usuario de prueba ImageRelay

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en ImageRelay.

**Para crear un usuario llamado a Britta Simon en ImageRelay, siga estos pasos:**

1. Inicio de sesión en el sitio de su empresa ImageRelay como administrador.

1. Vaya a **usuarios y permisos** y seleccione **Crear SSO de usuario**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Escriba el **correo electrónico**, **nombre**, **Apellido** y **compañía** del usuario que desee aprovisionar y seleccione el grupo de permisos que es el grupo que se puede iniciar sesión en sólo a través de inicio de sesión único (por ejemplo, SSO básico).

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Haga clic en **crear**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a ImageRelay.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ImageRelay, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ImageRelay**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de ImageRelay en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación ImageRelay.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png
