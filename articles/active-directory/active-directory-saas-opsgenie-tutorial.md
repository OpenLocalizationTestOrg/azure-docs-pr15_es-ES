<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con OpsGenie | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y OpsGenie."
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


# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integración de Azure Active Directory con OpsGenie

El objetivo de este tutorial es mostrar cómo integrar OpsGenie con Azure Active Directory (AD Azure).

Integrar OpsGenie con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a OpsGenie
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en OpsGenie (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con OpsGenie, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un inicio de sesión único de OpsGenie activado suscripción


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar OpsGenie de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-opsgenie-from-the-gallery"></a>Agregar OpsGenie de la Galería
Para configurar la integración de OpsGenie en Azure AD, debe agregar OpsGenie desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar OpsGenie de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **OpsGenie**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. En el panel de resultados, seleccione **OpsGenie**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con OpsGenie en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único trabajar, necesita saber el usuario equivalente en OpsGenie a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en OpsGenie.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en OpsGenie.

Para configurar y probar el inicio de sesión único de Azure AD con OpsGenie, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un OpsGenie probar usuario](#creating-a-opsgenie-test-user)** - a tiene equivalente de Britta Simon en OpsGenie que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de Azure clásica y configurar el inicio de sesión único en la aplicación OpsGenie.



**Para configurar el inicio de sesión único Azure AD con OpsGenie, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación **OpsGenie** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión OpsGenie** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de OpsGenie mediante el siguiente patrón: **"https://app.opsgenie.com/auth/login"**.

    > [AZURE.NOTE] Póngase en contacto con su [equipo de soporte técnico OpsGenie](mailto:support@opsgenie.com) si necesita su inicio de sesión en la URL.

    b. Haga clic en **siguiente**.


4. En la página **configurar inicio de sesión único en OpsGenie** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo. Debemos este certificado y direcciones URL de metadatos (Id. de entidad, SSO de inicio de sesión en la URL y dirección URL de fuera de inicio de sesión) configurar SSO OpsGenie lado.

    b. Haga clic en **siguiente**.


5. Abrir otra instancia del explorador y, a continuación, inicie sesión en OpsGenie como administrador.

6. Haga clic en **configuración**y, a continuación, haga clic en la ficha **Inicio de sesión único** .
 
    ![En el inicio de sesión único OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 

7. Para habilitar SSO, seleccione **activado**.

    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
   
8. En la sección **proveedor** , haga clic en la pestaña de **Azure Active Directory** .

    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
    
9. En la página de diálogo Azure Active Directory, realice los pasos siguientes:
 
    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 

    una. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en OpsGenie** , copie el valor de **Inicio de sesión único en URL del servicio** y, a continuación, péguelo en el cuadro de texto de **Extremo de SAML 2.0** .

    b. Crear un archivo de base 64 codificado de certificado descargado.      
    
    > [AZURE.NOTE] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Abrir el certificado codificado de base 64 en el Bloc de notas, copie el contenido del mismo en su Portapapeles y péguelo en el cuadro de texto de **Certificado X.500** .

    d. Haga clic en **Guardar cambios**.


6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.



![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-opsgenie-test-user"></a>Crear un usuario de prueba OpsGenie

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en OpsGenie. 

1.  En una ventana de explorador web, inicie sesión en su inquilino OpsGenie como administrador.

2.  Haciendo clic en el **usuario** en el panel izquierdo, vaya a la lista de usuarios.
   
    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3.  Haga clic en "**Agregar usuario**".

3.  En el cuadro de diálogo **Agregar usuario** , siga estos pasos:

    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 

    una. En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de Bárbara en Azure Active Directory.

    b. En el cuadro de texto **Nombre completo** , escriba **Bárbara Menéndez**.

    c. Haga clic en **Guardar**. 

Bárbara recibirá un correo electrónico con las instrucciones para configurar su perfil.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a OpsGenie.

![Asignar usuario][200] 

**Para asignar a Britta Simon a OpsGenie, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
 
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **OpsGenie**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de OpsGenie en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación OpsGenie.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png
