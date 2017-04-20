<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con adjunto | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y adjunto."
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
    ms.date="09/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: Integración de Azure Active Directory con adjunto

El objetivo de este tutorial es mostrar cómo integrar a adjunto con Azure Active Directory (AD Azure).

Integración de adjunto con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a adjunto
- Puede habilitar los usuarios automáticamente obtener firmado en adjunto (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con adjunto, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un adjunto inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar adjunto desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-deputy-from-the-gallery"></a>Agregar adjunto desde la Galería
Para configurar la integración de adjunto en Azure AD, debe agregar a adjunto desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar a adjunto desde la galería, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
    
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **adjunto**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)

7. En el panel de resultados, seleccione el **adjunto**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Selección de la aplicación en la Galería](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con adjunto en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en adjunto a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en adjunto.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en adjunto.

Para configurar y probar el inicio de sesión único de Azure AD con adjunto, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear a un adjunto probar usuario](#creating-a-deputy-test-user)** - tener un equivalente de Britta Simon en adjunto que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación de adjunto.

**Para configurar el inicio de sesión único Azure AD con adjunto, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicación **adjunto** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión adjunto** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)

3. En la página de diálogo **Configurar opciones de aplicación** , si desea configurar la aplicación en **IDP iniciadas modo**, realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)

    una. En el cuadro de texto **identificador** , escriba una dirección URL mediante el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com`.

    b. En el cuadro de texto **Dirección URL de respuesta** , escriba una dirección URL mediante el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.

    c. Haga clic en **siguiente**.

4. Si desea configurar la aplicación en **SP iniciados por el modo** en la página de diálogo de **Configuración de aplicación** , haga clic en **"Mostrar avanzadas configuración (opcional)"** y, a continuación, introduzca el **Inicio de sesión en la dirección URL** y haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba una dirección URL mediante el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com`.

    b. Haga clic en **siguiente**.

    > [AZURE.NOTE] Sufijo de región de adjunto es opitional o debe utilizar uno de estos: au | NOD | UE | como | la | af | un | ent au | ent NOD | ent UE | ent-como | la ent | Ent af | una empresa

5. En la página **configurar inicio de sesión único en adjunto** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    
6. Vaya a la siguiente URL: https://(your-subdomain).deputy.com/exec/config/system_config. Vaya a **Configuración de seguridad** y haga clic en **Editar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)

7. En el portal de clásico Azure, en la configurar inicio de sesión único en la página de adjunto, copie la dirección URL de SSO de SAML. 

8. En esta página de **Configuración de seguridad** , realice los pasos siguientes.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

    una. Habilitar **Inicio de sesión Social**.

    b. Abra su certificado base 64 codificado en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado OpenSSL** .

    c. En el cuadro de texto dirección URL de SSO SAM, escriba`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. En el cuadro de texto dirección URL de SSO SAM, reemplace `<your subdomain>` con el subdominio.

    e. En el cuadro de texto dirección URL de SSO SAM, reemplace `<saml sso url>` con la dirección URL de SSO de SAML que ha copiado desde el portal de clásico de Azure.

    f. Haga clic en **Guardar configuración**.

9. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-deputy-test-user"></a>Crear un usuario de prueba de adjunto

Para permitir que los usuarios de Azure AD iniciar sesión en adjunto, debe aprovisionar en adjunto. En el caso de adjunto, aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para crear una cuenta de usuario, siga estos pasos:

1.  Inicie sesión en el sitio de su empresa adjunto como administrador.

2.  En el panel de navegación superior, haga clic en **personas**.

    ![Personas] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Personas")

3.  Haga clic en el botón **Agregar contactos** y haga clic en **Agregar una sola persona**.

    ![Agregar personas] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "Agregar personas")

4.  Realice los pasos siguientes y haga clic en **Guardar e invitar**.

    ![Nuevo usuario] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Nuevo usuario")

    una. En el cuadro de texto **nombre** , escriba **Bárbara** y **Simon**.  

    b. En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de una cuenta de Azure AD que desee aprovisionar.

    c. En el cuadro de texto de **trabajo en** , escriba el nombre de bussniess.

    d. Haga clic en el botón **Guardar e invitar** .

    >[AZURE.NOTE]El titular AAD recibirá un correo electrónico y seguir un vínculo para confirmar su cuenta antes de que esté activa. Puede usar cualquier otros adjunto usuario cuenta herramientas de creación o API proporcionan por adjunto a cuentas de usuario AAD de aprovisionamiento.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a adjunto.
    
![Asignar usuario][200]

**Para asignar a Britta Simon a adjunto, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **adjunto**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)

3. En el menú de la parte superior, haga clic en **usuarios**.
    
    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.
    
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.
 
Al hacer clic en el mosaico adjunto en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su aplicación adjunto.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png
