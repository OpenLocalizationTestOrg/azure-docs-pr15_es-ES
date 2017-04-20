<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con el servidor de una plantilla | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y el servidor de una plantilla."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integración de Azure Active Directory con el servidor de una plantilla

El objetivo de este tutorial es mostrar cómo integrar el servidor de una plantilla con Azure Active Directory (AD Azure).

Integración de servidor de una plantilla con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso al servidor de una plantilla
- Puede habilitar los usuarios automáticamente obtener iniciado sesión al servidor de una plantilla (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con el servidor de una plantilla, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un servidor de una plantilla inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar servidor de una plantilla de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-tableau-server-from-the-gallery"></a>Agregar servidor de una plantilla de la Galería
Para configurar la integración del servidor de una plantilla en Azure AD, debe agregar el servidor de una plantilla de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar el servidor de una plantilla de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
 
    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Servidor de una plantilla**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)

7. En el panel de resultados, seleccione **El servidor de una plantilla**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Selección de la aplicación en la Galería](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con el servidor de una plantilla en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en el servidor de una plantilla para un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en el servidor de una plantilla.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en el servidor de una plantilla.

Para configurar y probar el inicio de sesión único de Azure AD con el servidor de una plantilla, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Creación de un servidor de una plantilla probar usuario](#creating-a-tableauserver-test-user)** - tener un equivalente de Britta Simon en el servidor de una plantilla que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de servidor de una plantilla.

Aplicación de servidor de una plantilla espera las afirmaciones SAML en un formato específico. La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**Para configurar el inicio de sesión único Azure AD con el servidor de una plantilla, realice los pasos siguientes:**


1. En el portal de clásico Azure, en la página de integración de aplicación de **Servidor de una plantilla** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 


1. En el cuadro de diálogo **atributos de token SAML** , realice los pasos siguientes:

    

    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 


    b. En el cuadro de texto **Nombre del atributo isErrorPage** , escriba el **nombre de usuario**.

    c. En la lista **Valor del atributo** , selsect **user.displayname**.

    d. Haga clic en **Finalizar**.  
    



1. En el menú de la parte superior, haga clic en **Inicio rápido**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  








1. Haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 



2. En la página **¿cómo le gustaría usuarios a iniciar sesión el servidor de una plantilla** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 


3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 



    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL de su servidor de una plantilla. 

    b. En el identificador del cuadro copia la 

    c. Haga clic en **siguiente**


4. En la página **configurar inicio de sesión único en el servidor de una plantilla** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 


    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


6. Para obtener el SSO configurado para la aplicación, debe iniciar sesión en su inquilino de servidor de una plantilla como administrador.

    una. En la configuración del servidor de una plantilla, haga clic en la pestaña **SAML** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 


    b. Seleccione la casilla de verificación de **SAML de uso para el inicio de sesión único**.

    c. Localizar el archivo de metadatos de federación descargado de Azure portal clásica y luego cárguelo en el **archivo de metadatos de SAML Idp**.

    d. Servidor de una plantilla devolver la dirección URL: la dirección URL que los usuarios de una plantilla Server tendrá acceso, como http://tableau_server. No se recomienda usar http://localhost. No es compatible con una dirección URL con una barra diagonal (por ejemplo, http://tableau_server/). Copiar **una plantilla Server devolver la dirección URL** y pegarla en el cuadro de texto de Azure AD **Inicio de sesión en la dirección URL** tal como se muestra en el paso 3

    e. Id. de entidad SAML: el identificador de entidad identifica la instalación de servidor de una plantilla a la IdP. Aquí puede especificar la dirección URL del servidor tablero nuevamente, si lo desea, pero no tiene que la URL del servidor de una plantilla. Copiar **Id. de entidad SAML** y pegarla en el cuadro de texto de Azure AD **identificador** tal como se muestra en el paso 3.

    f. Haga clic en el **Archivo de exportación de metadatos** y ábralo en la aplicación de edición de texto. Buscar la dirección URL del servicio de consumidores de aserción con Http Post y el índice 0 y copie la dirección URL. Ahora pegarla en el cuadro de texto de Azure AD **Respuesta URL** tal como se muestra en el paso 3. 

    g. Haga clic en **Aceptar** en la página Configiuration de servidor de una plantilla.

    > [AZURE.NOTE] Si necesita ayuda para configurar SAML en el cuadro servidor consulte este artículo [Configurar SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm) 

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

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 


4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 

    una. Como **Tipo de usuario**, seleccione **nuevo usuario de su organización**.

    b. En el cuadro de texto **Nombre de usuario** , escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 


8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-tableau-server-test-user"></a>Crear un usuario de prueba de servidor de una plantilla

Es el objetivo de esta sección crear un usuario denominado a Britta Simon de servidor de una plantilla. Debe aprovisionar a todos los usuarios en el servidor de una plantilla. Tenga en cuenta también ese nombre de usuario del usuario debe coincidir con el valor que ha configurado en el atributo personalizado de Azure AD del **nombre de usuario**. Con la asignación correcta debería funcionar la integración de [Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear un usuario de forma manual, debe ponerse en contacto con el administrador del servidor de una plantilla de su organización.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al servidor de una plantilla.

![Asignar usuario][200] 

**Para asignar a Britta Simon al servidor de una plantilla, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
 
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Servidor de una plantilla**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 


1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de servidor de una plantilla en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de servidor de una plantilla.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png
