<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Software de recursos humanos Cezanne | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Cezanne HR Software."
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integración de Azure Active Directory con Software de recursos humanos Cezanne

El objetivo de este tutorial es mostrar cómo integrar Software de recursos humanos Cezanne con Azure Active Directory (AD Azure).

Integración de Software de recursos humanos Cezanne con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Cezanne Software de recursos humanos
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Cezanne HR Software (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Software de recursos humanos Cezanne, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un Software de recursos humanos Cezanne inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Cezanne HR Software de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Agregar Cezanne HR Software de la Galería
Para configurar la integración del Software de recursos humanos Cezanne en Azure AD, debe agregar Cezanne HR Software desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Cezanne HR Software de la galería, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
    
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Cezanne HR Software**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. En el panel de resultados, seleccione **Cezanne HR Software**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Selección de la aplicación en la Galería](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con Software de recursos humanos Cezanne en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Cezanne HR Software a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Cezanne HR Software.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en el Software de recursos humanos Cezanne establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Software de recursos humanos Cezanne, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Creación de un Software de recursos humanos Cezanne probar usuario](#creating-a-cezanne-hr-software-test-user)** - tener un equivalente de Britta Simon de Software de recursos humanos Cezanne que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación de Software de recursos humanos Cezanne.

**Para configurar el inicio de sesión único Azure AD con Software de recursos humanos Cezanne, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicación de **Software de recursos humanos Cezanne** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión Cezanne Software de recursos humanos** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba una dirección URL mediante el siguiente patrón: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. En el cuadro de texto **Dirección URL de respuesta** , escriba una dirección URL mediante el siguiente patrón: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

    c. Haga clic en **siguiente**

    > [AZURE.NOTE] Tenga en cuenta que es necesario actualizar estos valores con el inicio de sesión en dirección URL real y la dirección URL de respuesta. Para obtener estos valores, póngase en contacto con el equipo de soporte técnico de Software de recursos humanos Cezanne mediante <mailto:info@cezannehr.com>.

4. En la página **configurar inicio de sesión único en Cezanne HR Software** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.

5. En una ventana de explorador web diferente, inicio de sesión en su inquilino de Software de recursos humanos Cezanne como administrador.

6. En el panel de navegación izquierdo, haga clic en **Configuración del sistema**. Vaya a **configuración de seguridad**. Vaya a la **Configuración de inicio de sesión único**.

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. **Permitir a los usuarios inicie sesión con el servicio de inicio de sesión único (SSO) siguiente** panel Active la casilla de **SAML 2.0** y seleccione la opción de **Configuración avanzada** junto a él.

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Haga clic en el botón **Agregar nuevo** .

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Realice los pasos siguientes en la sección de **Proveedores de IDENTIDADES SAML 2.0** .

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    una. Escriba el nombre de su proveedor de identidades como el **Nombre para mostrar**.

    b. En el **Identificador de entidad** del cuadro de texto ponga el valor de **Id. de entidad** desde el Asistente para configuración de aplicación de Azure AD.

    c. Cambiar el **enlace de SAML** a 'Entrada'.

    d. En el **Extremo de servicio de Token de seguridad** del cuadro de texto coloque el valor de **URL de servicio de inicio de sesión único** desde el Asistente para configuración de aplicación de Azure AD.

    e. Escriba 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' en el **nombre del atributo ID de usuario**.

    f. Haga clic en el icono de **cargar** para cargar el certificado de Azure AD descargado.

    g. Haga clic en el botón **Aceptar** . 

10. Haga clic en el botón **Guardar** .

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

12. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el cuadro de texto **Apellido** , escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-cezanne-hr-software-test-user"></a>Crear un usuario de prueba de Software de recursos humanos Cezanne

Para permitir que los usuarios de Azure AD iniciar sesión en Cezanne Software de recursos humanos, debe aprovisionar en Cezanne HR Software. En el caso de Software de recursos humanos Cezanne, aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para crear una cuenta de usuario, siga estos pasos:

1.  Inicie sesión en el sitio de su empresa de Software de recursos humanos Cezanne como administrador.

2.  En el panel de navegación izquierdo, haga clic en **Configuración del sistema**. Vaya a **administración de usuarios**. A continuación, vaya a **Agregar un nuevo usuario**.

    ![Nuevo usuario] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuevo usuario")

3.  En la sección de **Detalles de la persona** , realice los pasos siguientes:

    ![Nuevo usuario] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuevo usuario")

    una. Establecer **usuario interno** como OFF.

    b. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    c. En el cuadro de texto **Apellido** , escriba **Simon**.

    d. En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de cuenta de Britta Simon.

4.  En la sección **Información de cuenta** , realice los pasos siguientes:

    ![Nuevo usuario] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuevo usuario")

    una. En el cuadro de texto **nombre de usuario** , escriba la dirección de correo electrónico de Britta Simon.

    b. En el cuadro de texto **contraseña** , escriba la contraseña de cuenta de Britta Simon.

    c. Seleccione **HR Professional** como **rol de seguridad**.

    d. Haga clic en **Aceptar**.

5. Vaya a la ficha **Inicio de sesión único** y seleccione **Agregar nuevo** en el área de **SAML 2.0 identificadores** .

    ![Usuario] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuario")

6. Elija su proveedor de identidades para el **Proveedor de identidades** y en el cuadro de texto del **Identificador de usuario**, escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    ![Usuario] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuario")
    
7. Haga clic en el botón **Guardar** .

    ![Usuario] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuario")


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Software de recursos humanos Cezanne.
    
![Asignar usuario][200]

**Para asignar a Britta Simon Cezanne HR software, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Cezanne HR Software**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. En el menú de la parte superior, haga clic en **usuarios**.
    
    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.
    
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.
 
Al hacer clic en el mosaico de Software de recursos humanos Cezanne en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su aplicación de Software de recursos humanos Cezanne.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png
