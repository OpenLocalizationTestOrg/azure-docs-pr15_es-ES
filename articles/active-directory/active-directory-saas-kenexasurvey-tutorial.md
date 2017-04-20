<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con IBM Kenexa encuesta Enterprise | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y IBM Kenexa encuesta Enterprise."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integración de Azure Active Directory con IBM Kenexa encuesta Enterprise

En este tutorial, aprenderá a integrar IBM Kenexa encuesta Enterprise con Azure Active Directory (AD Azure).

Integración de IBM Kenexa encuesta Enterprise con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a la empresa de IBM Kenexa encuesta
- Puede habilitar los usuarios automáticamente obtener firmado en IBM Kenexa encuesta empresa (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con IBM Kenexa encuesta Enterprise, necesita lo siguiente:

- Una suscripción de Azure AD
- Una empresa de IBM Kenexa encuesta inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba. El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar IBM Kenexa encuesta Enterprise de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Agregar IBM Kenexa encuesta Enterprise de la Galería
Para configurar la integración de IBM Kenexa encuesta Enterprise en Azure AD, debe agregar IBM Kenexa encuesta Enterprise desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar IBM Kenexa encuesta Enterprise de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **IBM Kenexa encuesta Enterprise**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_01.png)

7. En el panel de resultados, seleccione **IBM Kenexa encuesta Enterprise**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con IBM Kenexa encuesta Enterprise en función de un usuario de prueba denominado a "Britta Simon".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en IBM Kenexa encuesta Enterprise a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en IBM Kenexa encuesta Enterprise.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** de empresa de IBM Kenexa encuesta.

Para configurar y probar el inicio de sesión único de Azure AD con IBM Kenexa encuesta Enterprise, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear una encuesta de IBM Kenexa empresa probar usuario](#creating-an-kenexasurvey-test-user)** - tener un equivalente de Britta Simon de IBM Kenexa encuesta Enterprise que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
6. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación empresarial de IBM Kenexa encuesta.


**Para configurar el inicio de sesión único Azure AD con IBM Kenexa encuesta Enterprise, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicación de **Empresa de IBM Kenexa encuesta** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión IBM Kenexa encuesta Enterprise** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_03.png)

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_04.png)

    una. En el cuadro de texto **identificador** , escriba una dirección URL utilizando el modelo siguiente:`https://surveys.kenexa.com/<company code>`

    b. En el cuadro de texto **Dirección URL de respuesta** , escriba una dirección URL mediante el siguiente patrón:`https://surveys.kenexa.com/<company code>/tools/sso.asp`

    c. Haga clic en **siguiente**.

    > [AZURE.NOTE] Tenga en cuenta que no son los valores reales. Debe actualizar estos valores con el identificador y la dirección URL de respuesta real. Póngase en contacto con el equipo de soporte técnico de IBM Kenexa encuesta Enterprise para obtener estos valores.

4. En la página **configurar inicio de sesión único en IBM Kenexa encuesta empresarial** , haga clic en **Descargar certificado** y, a continuación, guarde el archivo en su equipo:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_05.png) 

5. Para obtener el SSO configurado para la aplicación, póngase en contacto con el equipo de soporte técnico de IBM Kenexa y proporcionarles lo siguiente:

    • El archivo descargado certificado

    • La **dirección URL del emisor**

    • La **dirección URL de SAML SSO**

    • La **dirección URL del servicio de cierre de sesión único**

    > [AZURE.NOTE] Por favor, nota que NameID reclamar valor en la respuesta debe coincidir con el Id. de SSO configurado en el sistema de Kenexa. Así pues, el trabajo con Kenexa soporte técnico para asignar el identificador de usuario apropiadas en su organización como Id. de SSO De forma predeterminada Azure AD establecerá la NameIdentifier como valor UPN. Puede cambiar esta opción de la ficha de atributo, como se muestra en la siguiente captura de pantalla. La integración solo funcionará después de completar la asignación correcta. 
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_51.png)

6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
  
    ![En el inicio de sesión único de Azure AD][11]

8. En el portal de clásico Azure, en la página de integración de aplicación de **Empresa de IBM Kenexa encuesta** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_06.png)

9. En el cuadro de diálogo **atributos de token SAML** , realice los pasos siguientes:

    una. Seleccione el atributo de **NameIdentifier** y haga clic en el icono **Editar** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_07.png)
    
    b. En la lista **Valor del atributo** , escriba el valor del atributo de Id. de SSO que se configura en el sistema Kenexa.
    
    c. Haga clic en **completada**

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-ibm-kenexa-survey-enterprise-test-user"></a>Crear un usuario de prueba de IBM Kenexa encuesta Enterprise

En esta sección, creará un usuario llamado a Britta Simon de IBM Kenexa encuesta Enterprise. Trabaje con el equipo de soporte técnico de IBM Kenexa para asignar el Id. de SSO para todos los usuarios. También este valor de Id. de SSO debe asignarse al valor NameIdentifier Azure AD. Puede cambiar esta configuración predeterminada en la ficha atributo.


> [AZURE.NOTE] Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico de IBM Kenexa encuesta Enterprise.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a la empresa de IBM Kenexa encuesta.

![Asignar usuario][200] 

**Para asignar a Britta Simon a IBM Kenexa encuesta Enterprise, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **IBM Kenexa encuesta Enterprise**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.
    
    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de IBM Kenexa encuesta Enterprise en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación empresarial de IBM Kenexa encuesta.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_205.png
