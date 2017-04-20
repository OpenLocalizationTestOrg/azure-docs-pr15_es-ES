<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con ADP eTime | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y eTime ADP."
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
    ms.date="10/17/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Tutorial: Integración de Azure Active Directory con eTime ADP

El objetivo de este tutorial es mostrar cómo integrar eTime ADP con Azure Active Directory (AD Azure).  
Integración de ADP eTime con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a eTime ADP
- Puede habilitar los usuarios automáticamente obtener firmado en eTime ADP (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure


Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con eTime ADP, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un ADP eTime inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar ADP eTime desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-adp-etime-from-the-gallery"></a>Agregar ADP eTime desde la Galería
Para configurar la integración de ADP eTime en Azure AD, debe agregar ADP eTime desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar ADP eTime de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **eTime ADP**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)

7. En el panel de resultados, seleccione **eTime ADP**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con eTime ADP en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en eTime ADP a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en eTime ADP.  
Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ADP eTime establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con ADP eTime, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un eTime ADP probar usuario](#creating-a-adpetime-test-user)** - tener un equivalente de Britta Simon en eTime ADP que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de eTime ADP.

La aplicación de eTime ADP espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de atributos token de SAML. La siguiente captura de pantalla muestra un ejemplo de este. El nombre de la notificación siempre será **"PersonImmutableID"** y el valor que hemos asignado a ExtensionAttribute2 que contiene el valor de IdEmpleado del usuario. Aquí se realizará fren de asignación de usuario Azure AD a ADP eTime en el IdEmpleado pero se puede asignar a un valor diferente también en función de la configuración de la aplicación. Así pues, trabajar con ADP eTime equipo primero use el identificador correcto de un usuario y asignar el valor con la notificación de **"PersonImmutableID"** .  

![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

Antes de configurar la aserción SAML, debe ponerse en contacto con el soporte técnico de ADP eTime y solicitar el valor del atributo identificador único para el inquilino. Necesita este valor para configurar la notificación personalizada para la aplicación.


**Para configurar el inicio de sesión único Azure AD con ADP eTime, realice los pasos siguientes:**

1. En el portal de clásico Azure, en la página de integración de aplicación **eTime ADP** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión eTime ADP** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , siga estos pasos:.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 


    una. En el cuadro de texto **Dirección URL de respuesta** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de eTime ADP utilizando el modelo siguiente: `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.

    b. Haga clic en **siguiente**.

4. En la página **configurar inicio de sesión único en eTime ADP** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png) 

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para obtener el SSO configurado para la aplicación, póngase en contacto con el soporte técnico de ADP eTime y el archivo de metadatos adjuntar descargada de correo electrónico para que se pueden configurar para la integración de SSO.

    > [AZURE.NOTE] Después de **ADP eTime** grupo configurar la instancia, obtener el valor de **RelayState** de ellos. Siga el debajo mencionado pasos para configurarla. Después de esta configuración puede probar la integración. Así que tenga en cuenta que se trata de configuración importantes para la integración de esta aplicación para que funcione.

6. Para configurar el valor de RelayState en Azure AD, realice los pasos siguientes: 
    
    una. Inicie sesión en el [Portal de administración de Azure](https://portal.azure.com) como administrador.

    b. En el panel de navegación izquierdo, haga clic en **Más servicios**. 
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png)

    c. En el cuadro de texto **Buscar** , escriba **Azure Active Directory**y, a continuación, haga clic en el vínculo relacionado.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)

    d. Haga clic en **aplicaciones empresariales**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)

    e. En la sección **Administrar** , haga clic en **Todas las aplicaciones**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png)

    f. En el cuadro de texto **Buscar** , escriba **eTime ADP**y, a continuación, haga clic en el vínculo relacionado. 
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)

    g. En la sección **Administrar** , haga clic en **Inicio de sesión único**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png)

    h. Seleccione **Mostrar avanzadas de configuración de la dirección URL**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png)
    
    . En el cuadro de texto de **Estado de la transmisión** , escriba un valor con los modelos siguientes:
    
    - Entorno de producción:`https://fed.adp.com/saml/fedlanding.html?<id>` 
    - Entorno de ensayo:`https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png)

    j. Guardar la configuración.

7. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

8. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  
En la lista de usuarios, seleccione **Bárbara Menéndez**.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png) 

    una. Como **Tipo de usuario**, seleccione **nuevo usuario de su organización**.

    b. En el cuadro de texto **Nombre de usuario** , escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-adp-etime-test-user"></a>Crear un usuario de prueba eTime ADP

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en eTime ADP. Trabaje con el equipo de soporte técnico de ADP eTime para agregar los usuarios en la cuenta de eTime ADP. 


> [AZURE.NOTE]Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico de eTime ADP.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al eTime ADP.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ADP eTime, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **eTime ADP**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de eTime ADP en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de eTime ADP.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png
