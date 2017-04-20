<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con SAP Business ByDesign | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y ByDesign de negocios de SAP."
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
    ms.date="09/09/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integración de Azure Active Directory con ByDesign de negocios de SAP

En este tutorial, aprenderá a integrar SAP Business ByDesign con Azure Active Directory (AD Azure).

Integración de SAP Business ByDesign con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ByDesign de negocios de SAP
- Puede habilitar los usuarios automáticamente obtener firmado en SAP Business ByDesign (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ByDesign de negocios de SAP, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un SAP Business ByDesign inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar ByDesign empresariales de SAP desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Agregar ByDesign empresariales de SAP desde la Galería
Para configurar la integración de SAP Business ByDesign en Azure AD, debe agregar ByDesign empresariales de SAP desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar ByDesign empresariales de SAP desde la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.


3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **ByDesign de negocios de SAP**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. En el panel de resultados, seleccione **ByDesign de negocios de SAP**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con ByDesign de negocios de SAP en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en SAP ByDesign de empresa a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en ByDesign de negocios de SAP.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ByDesign de negocios de SAP.

Para configurar y probar el inicio de sesión único de Azure AD con ByDesign de negocios de SAP, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear una ByDesign de negocios de SAP probar usuario](#creating-an-sap-business-bydesign-test-user)** - a tiene equivalente de Britta Simon en ByDesign de negocios de SAP que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación ByDesign de negocios de SAP.

Aplicación de empresa ByDesign SAP espera las afirmaciones SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la pestaña **"Atrribute"** de la aplicación. La siguiente captura de pantalla muestra un ejemplo de este. 


**Para configurar el inicio de sesión único Azure AD con ByDesign de negocios de SAP, siga estos pasos:**


1. En el portal de clásico Azure, en la página de integración de aplicación de **SAP Business ByDesign** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png) 


2. En la lista de atributos token SAML de atributos, seleccione el atributo nameidentifier y, a continuación, haga clic en **Editar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png) 

3. En el cuadro de diálogo Modificar el atributo de usuario, siga estos pasos:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png) 

    una. En la lista valor del atributo, seleccione la función **ExtractMailPrefix()**

    b. En la lista de correo, seleccione el atributo de usuario que desea usar para su implementación. 
    Por ejemplo, si desea usar el IdEmpleado como identificador de usuario único y ha almacenado el valor del atributo en el ExtensionAttribute2, a continuación, seleccione **user.extensionattribute2**. 

    c. Haga clic en **Finalizar**. 
    

4. En el portal clásico, en la página de integración de aplicación de **SAP Business ByDesign** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

5. En la página **¿cómo le gustaría usuarios a iniciar sesión ByDesign de negocios de SAP** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png) 

6. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de SAP Business ByDesign utilizando el modelo siguiente:`https://<servername>.sapbydesign.com`
    
    b. Haga clic en **siguiente**
 
7. En la página **configurar inicio de sesión único en ByDesign de negocios de SAP** , siga los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


8. Para obtener el SSO configurado para la aplicación, siga estos pasos:

    una. Inicie sesión el portal de SAP Business ByDesign con derechos de administrador.

    b. Vaya a la **aplicación y tareas comunes de administración de usuario** y haga clic en la pestaña **Proveedor de identidades** .

    c. Haga clic en **Nuevo proveedor de identidades** y seleccione el archivo XML de metadatos que ha descargado desde el portal de clásico de Azure. Importando los metadatos, el sistema carga automáticamente el certificado de firma requerida y el certificado de cifrado.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

    d. Para incluir la **Dirección URL del servicio de consumidores de aserción** en la convocatoria SAML, seleccione **Incluir dirección URL del servicio de consumidores de aserción**.

    e. Haga clic en **activar el inicio de sesión único**.

    f. Guarde los cambios.

    g. Haga clic en la pestaña **Mi sistema** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

    h. Copie la **Dirección URL de SSO** y pegarlo en el cuadro de texto de **Inicio de sesión de Azure AD en la dirección URL** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

    . Especificar si el empleado manualmente puede elegir entre iniciar sesión con el identificador de usuario y la contraseña o SSO seleccionando la **Selección de proveedor de identidades Manual**.

    j. En la sección **Dirección URL de SSO** , especifique la dirección URL que debe ser usada por el empleado para iniciar sesión en el sistema. 
    En la dirección URL envía a la lista de empleados de la lista desplegable, puede elegir entre las siguientes opciones:
    
    **Dirección URL no SSO**
 
    El sistema envía solo la dirección URL del sistema normal al empleado. El empleado no puede iniciar sesión con SSO y debe usar contraseña o certificados en su lugar.

    **DIRECCIÓN URL DE SSO** 

    El sistema envía solo la dirección URL de SSO al empleado. El empleado puede iniciar sesión con SSO. Solicitud de autenticación se redirige a través de la IdP.

    **Selección automática**
 
    Si SSO no está activa, el sistema envía la dirección URL del sistema normal al empleado. Si SSO está activa, el sistema comprueba si el empleado tiene una contraseña. Si una contraseña está disponible, SSO URL y dirección URL de SSO no se envían a los empleados. Sin embargo, si el empleado no tiene ninguna contraseña, solo la dirección URL de SSO se envía al empleado.

    k. Guarde los cambios.

9. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.


![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-sap-business-bydesign-test-user"></a>Crear un usuario de prueba de ByDesign de negocios de SAP

En esta sección, creará un usuario llamado a Britta Simon en ByDesign de negocios de SAP. Trabaje con SAP Business ByDesign equipo de soporte técnico para agregar los usuarios de la plataforma ByDesign de negocios de SAP. 

> [AZURE.NOTE] Asegúrese de que valor NameID debe coincidir con el campo nombre de usuario de la plataforma ByDesign de negocios de SAP.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a SAP Business ByDesign.

![Asignar usuario][200] 

**Para asignar a Britta Simon a ByDesign de negocios de SAP, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ByDesign de negocios de SAP**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de SAP Business ByDesign en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación ByDesign de negocios de SAP.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png
