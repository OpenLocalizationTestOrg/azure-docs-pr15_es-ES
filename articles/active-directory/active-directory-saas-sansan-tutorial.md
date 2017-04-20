<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con SanSan | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y SanSan."
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


# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Tutorial: Integración de Azure Active Directory con SanSan

En este tutorial, aprenderá a integrar SanSan con Azure Active Directory (AD Azure).

Integrar SanSan con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SanSan
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en SanSan (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SanSan, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un SanSan inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, pruebe Microsoft Microsoft Azure AD inicio de sesión único en un entorno de prueba. El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar SanSan de la Galería
2. Configurar y probar el inicio de sesión único de Microsoft Azure AD


## <a name="adding-sansan-from-the-gallery"></a>Agregar SanSan de la Galería
Para configurar la integración de SanSan en Azure AD, debe agregar SanSan desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar SanSan de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **SanSan**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_01.png)

7. En el panel de resultados, seleccione **SanSan**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_06.png)

##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurar y probar el inicio de sesión único de Microsoft Azure AD
En esta sección, configurar y probar Microsoft Azure AD inicio de sesión único con SanSan en función de un usuario de prueba denominado a "Britta Simon".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en SanSan a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en SanSan.
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en SanSan.

Para configurar y probar el inicio de sesión único de Microsoft Azure AD con SanSan, debe completar los siguientes bloques de creación:

1. **[Configurar Microsoft Azure AD inicio de sesión único](#configuring-azure-ad-single-single-sign-on)** : para habilitar los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - para probar Microsoft Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un SanSan probar usuario](#creating-an-sansan-test-user)** - a tiene equivalente de Britta Simon en SanSan que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Microsoft Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de AD en Microsoft Azure

En esta sección, habilitar inicio de sesión único de Microsoft Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación SanSan.


**Para configurar el inicio de sesión único de Microsoft Azure AD con SanSan, siga estos pasos:**


1. En el portal de Azure clásico, en la página de integración de aplicación **SanSan** , haga clic en configurar inicio de sesión único para abrir el cuadro de diálogo Configurar inicio de sesión único.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png) 

2. En la página **¿cómo desea que los usuarios para iniciar sesión SanSan** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL en el modelo siguiente:

  	| Entorno             | DIRECCIÓN URL |
  	| :--                     | :-- |
  	| Web PC                  | `https://ap.sansan.com/v/saml2/<company name>/acs`|
  	| Aplicación móvil nativa       | `https://internal.api.sansan.com/saml2/<company name>/acs` |
  	| Configuración del explorador móvil | `https://ap.sansan.com/s/saml2/<company name>/acs` |


    b. En el cuadro de texto **identificador** , escriba la dirección URL en el modelo siguiente: 

  	| Entorno             | DIRECCIÓN URL |
  	| :--                     | :-- |
  	| Web PC                  | `https://ap.sansan.com/v/saml2/<company name>`|
  	| Aplicación móvil nativa       | `https://internal.api.sansan.com/saml2/<company name>` |
  	| Configuración del explorador móvil | `https://ap.sansan.com/s/saml2/<company name>` |


    c. Haga clic en **siguiente**.

4. En la página **configurar inicio de sesión único en SanSan** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5.  Para obtener el SSO configurado para la aplicación, SanSan de soporte técnico de contacto y le ayudará a configurar SSO. Proporcionarles con la siguiente información:

    • Descargado **certificado**

    • El **ID de proveedor de identidades**

    • La **dirección URL de SAML SSO**

    • El **Inicio de sesión único fuera de la dirección URL del servicio**

> [AZURE.NOTE] Configuración del explorador de PC también funciona para una aplicación móvil y explorador móvil junto con web PC. 


6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD

En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.
En la lista de usuarios, seleccione **Bárbara Menéndez**.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-sansan-test-user"></a>Crear un usuario de prueba SanSan

En esta sección, creará un usuario llamado a Britta Simon en SanSan. Aplicación SanSan tenga el usuario estén configurados en la aplicación antes de SSO. 


> [AZURE.NOTE] Si necesita crear un usuario de forma manual o por lotes de usuarios, debe ponerse en contacto con el equipo de soporte técnico de SanSan.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a SanSan.

![Asignar usuario][200] 

**Para asignar a Britta Simon a SanSan, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SanSan**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, probar la Microsoft Azure AD inicio de sesión único configuración mediante el Panel de acceso.
Al hacer clic en el mosaico de SanSan en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación SanSan.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_205.png
