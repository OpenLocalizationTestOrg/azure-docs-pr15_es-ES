<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Proofpoint a petición | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Proofpoint a petición."
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
    ms.date="10/05/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Tutorial: Integración de Azure Active Directory con Proofpoint a petición

En este tutorial, aprenderá a integrar Proofpoint a petición con Azure Active Directory (AD Azure).

Integración de Proofpoint a petición con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Proofpoint a petición.
- Puede habilitar los usuarios obtener sesión automáticamente Proofpoint a petición (inicio de sesión único o SSO) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el portal clásica Azure.

Si desea saber más detalles sobre la integración de la aplicación de SaaS con Azure AD, consulte [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Proofpoint a petición, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un Proofpoint de suscripción de inicio de sesión único de petición


Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede [obtener una versión de prueba de un mes](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Proofpoint a petición de la galería.
2. Configurar y probar el inicio de sesión único Azure AD.


## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Agregar Proofpoint a petición de la Galería
Para configurar la integración de Proofpoint a petición en Azure AD, debe agregar Proofpoint a petición de la galería a la lista de aplicaciones de SaaS administradas.

1. En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Icono de Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú en la parte superior.

    ![Elemento de menú de aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Botón Agregar][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Opción de agregar una aplicación de la Galería][4]

6. En el cuadro Buscar, escriba **Proofpoint a petición**.

    ![Cuadro donde se escribe "Proofpoint petición"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)

7. En el panel de resultados, seleccione **Proofpoint a petición**y, a continuación, haga clic en **completado** para agregar la aplicación.



##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar y probar el inicio de sesión único Azure AD
En esta sección, configurar y probar el inicio de sesión único de Azure AD con Proofpoint a petición en función de un usuario de prueba denominado a Britta Simon.

Para que el inicio de sesión único trabajar, debe saber qué es el usuario equivalente en Proofpoint a petición a un usuario en Azure AD Azure AD. En otras palabras, debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Proofpoint a petición.

Establecer esta relación de vínculo asignando el valor de **nombre de usuario** en Azure AD como el valor de **nombre de usuario** en Proofpoint a petición.

Para configurar y probar el inicio de sesión único de Azure AD con Proofpoint a petición, complete los siguientes procedimientos:

1. [Configurar Azure AD inicio de sesión único](#configuring-azure-ad-single-sign-on), para permitir a los usuarios usar esta característica.
2. [Crear un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user), para probar Azure AD inicio de sesión único con Britta Simon.
3. [Crear un Proofpoint de usuario de prueba de petición](#creating-a-proofpoint-ondemand-test-user), tener un equivalente de Britta Simon en Proofpoint a petición que está vinculado a la representación de Azure AD de ella.
4. [Asignar al usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user), habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. [Inicio de sesión único de prueba](#testing-single-sign-on), para comprobar que funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar el inicio de sesión único Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en su Proofpoint en aplicación de la demanda.

1. En el portal clásico, en la página de integración de aplicación **Proofpoint a petición** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Botón "Configurar el inicio de sesión único"][6]

2. En la página **¿cómo desea que los usuarios para iniciar sesión Proofpoint a petición** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Botón de opción "Microsoft Azure AD de sesión único"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)

3. En la página **Configurar opciones de aplicación** , realice los pasos siguientes:

    !["Configurar la configuración de la aplicación" página con cuadros rellenado](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)

    una. En el cuadro **Dirección URL de inicio de sesión en** , escriba la dirección URL donde los usuarios iniciar sesión su Proofpoint en aplicación de la demanda. Usar el siguiente patrón: **https://\<hostname\>.pphosted.com/ppssamlsp_hostname**

    b. En el cuadro **identificador** , escriba la dirección URL utilizando el modelo siguiente: **https://\<hostname / >.pphosted.com/ppssamlsp**

    c. En el cuadro **Dirección URL de respuesta** , escriba la dirección URL utilizando el modelo siguiente: **https://\<hostname / >.pphosted.com:portnumber/v1/samlauth/samlconsumer**

    d. Haga clic en **siguiente**.

4. En la página **configurar inicio de sesión único en Proofpoint a petición** , realice los pasos siguientes:

    !["Configurar el inicio de sesión único en Proofpoint a petición" página con el botón "Descargar certificado"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.

5. Para obtener SSO configurado para la aplicación, póngase en contacto con el Proofpoint en el equipo de soporte técnico petición y proporcionarles lo siguiente:

    • El certificado descargado

    • El identificador de la entidad

    • La dirección URL de SAML SSO

6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![Casilla de verificación que confirma que ha configurado el inicio de sesión único][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![Página de confirmación][11]


### <a name="create-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba denominado a Britta Simon en el portal de clásico.


![El usuario de prueba en la lista de usuarios][20]

1. En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Icono de Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú en la parte superior, haga clic en **usuarios**.

    ![Elemento de menú de usuarios](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Botón Agregar usuario](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)

5. En la página **Díganos sobre este usuario** , siga estos pasos:  !["Comuníquese con nosotros este usuario" página con cuadros rellenado](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)

    una. En el cuadro **Tipo de usuario** , seleccione **nuevo usuario en su organización**.

    b. En el cuadro **Nombre de usuario** , escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de **perfil de usuario** , siga estos pasos: ![la página "perfil de usuario" con cuadros rellenado](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)

    una. En el cuadro **nombre** , escriba **Bárbara**.  

    b. En el cuadro **Apellidos** , escriba **Simon**.

    c. En el cuadro **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página **obtener una contraseña temporal** , haga clic en **crear**.

    ![Botón para crear una contraseña temporal](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)

8. En la página **Obtenga una contraseña temporal** , realice los pasos siguientes:

    !["Obtener una contraseña temporal" página con información de contraseña](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)

    una. Anote el valor en el cuadro **Contraseña nueva** .

    b. Haga clic en **Finalizar**.   



### <a name="create-a-proofpoint-on-demand-test-user"></a>Crear un Proofpoint de usuario de prueba de petición

En esta sección, creará un usuario llamado a Britta Simon en Proofpoint a petición. Trabaje con Proofpoint en el equipo de soporte técnico petición para agregar usuarios de la Proofpoint en plataforma de petición.


### <a name="assign-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Proofpoint a petición.

![Información de usuario, que muestra access habilitado a través del método directo][200]

1. En el portal clásico, en la vista de directorio, haga clic en **aplicaciones** en el menú en la parte superior para abrir la vista de aplicaciones.

    ![Elemento de menú de aplicaciones][201]

2. En la lista de aplicaciones, seleccione **Proofpoint a petición**.

    ![Lista de aplicaciones con Proofpoint a petición seleccionado](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)

3. En el menú en la parte superior, haga clic en **usuarios**.

    ![Elemento de menú de usuarios][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar botón][205]


### <a name="test-single-sign-on"></a>Probar el inicio de sesión único

En esta sección, pruebe la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el mosaico **Proofpoint a petición** en el Panel de acceso, se debe ser sesión automáticamente su Proofpoint en aplicación de la demanda.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png
