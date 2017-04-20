<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Asana | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Asana."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integración de Azure Active Directory con Asana

En este tutorial, aprenderá a integrar Asana con Azure Active Directory (AD Azure).

Integrar Asana con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Asana
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Asana (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Asana, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un **Asana** inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba. El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Asana de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-asana-from-the-gallery"></a>Agregar Asana de la Galería
Para configurar la integración de Asana en Azure AD, debe agregar Asana desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Asana de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Asana**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)

7. En el panel de resultados, seleccione **Asana**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con Asana en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Asana a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Asana.
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Asana.

Para configurar y probar el inicio de sesión único de Azure AD con Asana, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un Asana probar usuario](#creating-an-Asana-test-user)** - a tiene equivalente de Britta Simon en Asana que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de Azure AD de sesión único

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación Asana.

**Para configurar el inicio de sesión único Azure AD con Asana, siga estos pasos:**

1. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único][6]
2. En el portal clásico, en la página de integración de aplicación **Asana** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][7] 

3. En la página **¿cómo desea que los usuarios para iniciar sesión Asana** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)

4. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba una dirección URL mediante el siguiente patrón:`https://app.asana.com`

    c. Haga clic en **siguiente**.

5. En la página **configurar inicio de sesión único en Asana** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo en el equipo. También puede copiar el valor de la dirección URL de SAML SSO.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)

8. Haga clic con el botón secundario en el certificado, a continuación, abra el archivo de certificado con el Bloc de notas o preferida editor de texto. Copiar el contenido entre el inicial y el título del certificado final. Este es el certificado X.509 que se usa en Asana configurar SSO.

6. En una ventana de explorador diferente, inicio de sesión en la aplicación Asana. Para configurar SSO en Asana, obtener acceso a la configuración del área de trabajo haciendo clic en el nombre del área de trabajo en la esquina superior derecha de la pantalla. A continuación, haga clic en ** \<el nombre del área de trabajo\> configuración**. 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

7. En la ventana de **configuración de la organización** , haga clic en **administración**. A continuación, haga clic en **miembros deben iniciar sesión a través de SAML** para habilitar la configuración de SSO. La realizar los siguientes pasos:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)

    una. En el TextBox puede **dirección URL de la página de inicio de sesión** , pegue el inicio de sesión de SAML en la dirección URL de Azure AD.

    b. En el cuadro de texto de **Certificado X.509** , pegue el certificado X.509 que ha copiado de Azure AD.

9. Haga clic en **Guardar**. Vaya a [Asana guía para configurar SSO](https://asana.com/guide/help/premium/authentication#gl-saml) si necesita más ayuda.

7. Vaya a la página **configurar inicio de sesión único en Asana** de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

8. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-asana-test-user"></a>Crear un usuario de prueba Asana

En esta sección, creará un usuario llamado a Britta Simon en Asana.

1. En **Asana**, vaya a la sección de **equipos** en el panel izquierdo. Haga clic en el botón de signo más. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Escriba el correo electrónico britta.simon@contoso.com en el cuadro de texto e, a continuación, seleccione **Invitar**.
3. Haga clic en **Enviar invitación**. El nuevo usuario recibirá un correo electrónico a su cuenta de correo electrónico. Tendrá que crear y validar la cuenta.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Asana.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Asana, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Asana**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de todos los usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar el inicio de sesión único de Azure AD.

Vaya a la página de inicio de sesión de Asana. En el correo electrónico, cuadro de texto dirección insertar la dirección de correo electrónico britta.simon@contoso.com. Deje el cuadro de texto de contraseña en blanco y, a continuación, haga clic en **Log In**. Se le redirigirá a la página de inicio de sesión de Azure AD. Complete sus credenciales de Azure AD. Ahora, sesión Asana.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png
