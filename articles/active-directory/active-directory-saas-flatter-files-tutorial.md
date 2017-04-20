<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con archivos más plana | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y archivos más planas."
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


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integración de Azure Active Directory con archivos más plana

El objetivo de este tutorial es mostrar cómo integrar archivos más plana con Azure Active Directory (AD Azure).  
Integración de archivos más plana con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a archivos más plana 
- Puede habilitar los usuarios automáticamente obtener firmado en archivos más plana (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure Active Directory

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con archivos más plana, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un archivos más plana inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar archivos más plana de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-flatter-files-from-the-gallery"></a>Agregar archivos más plana de la Galería
Para configurar la integración de archivos más plana en Azure AD, debe agregar archivos más plana de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar más plana de archivos de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Archivos más planas**.


    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. En el panel de resultados, seleccione **Archivos más plana**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con archivos más plana en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en archivos más plana a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en archivos más planas.  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en los archivos más planas.
 
Para configurar y probar el inicio de sesión único Azure AD con archivos más plana, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear archivos de más plana probar usuario](#creating-a-halogen-software-test-user)** - tener un equivalente de Britta Simon en archivos más plana que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico de Azure AD y configurar el inicio de sesión único en la aplicación de archivos más planas. Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64. Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar el inicio de sesión único para los archivos más plana, necesita un dominio registrado. Si no tiene un dominio registrado todavía, contacto archivos más plana soporte técnico a través de [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Para configurar el inicio de sesión único Azure AD con archivos más plana, realice los pasos siguientes:**

1. En el portal de Azure AD clásico, en la página de integración de aplicación **Más plana de archivos** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión archivos más plana** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
 
    ![Configurar el inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] Archivos más planas usa la misma dirección URL de inicio de sesión SSO para todos los clientes: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. En la página **configurar inicio de sesión único en archivos más plana** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


1. Inicio de sesión en la aplicación de archivos más planas como administrador.

2. Haga clic en panel. 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. Haga clic en **configuración**y, a continuación, realice los pasos siguientes en la pestaña de la **empresa** : 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    una. Seleccione **usar SAML 2.0 para la autenticación**.

    b. Haga clic en **Configurar SAML**.



2. En el cuadro de diálogo **Configuración de SAML** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    una. En el cuadro de texto dominio, escriba el dominio registrado.

    > [AZURE.NOTE] Si no tiene un dominio registrado todavía, contacto archivos más plana soporte técnico a través de [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. En la Azure portal clásico, en configurar solo inicio de sesión en el cuadro de diálogo de archivos más plana, copt la única sesión dirección URL del servicio y péguelo en el cuadro de texto de la dirección URL del proveedor de identidades.

    c.  Crear un archivo **codificado base 64** de certificado descargado.  

    >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    d.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado de proveedor de identidades FlatterFiles** .

    e. Haga clic en **Actualizar**.

6. En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-a-flatter-files-test-user"></a>Crear un usuario de prueba de archivos más plana

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en archivos más planas.

**Para crear un usuario llamado a Britta Simon en archivos más plana, realice los pasos siguientes:**

1. Inicie sesión como administrador el sitio de su empresa **Archivos más planas** .

2. En el panel de navegación de la izquierda, haga clic en **configuración**y, a continuación, haga clic en la **pestaña**de usuarios.

    ![Cfreate un usuario más plana de archivos](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Haga clic en **Agregar usuario**. 

4. En el cuadro de diálogo **Agregar usuario** , siga estos pasos:

    ![Cfreate un usuario más plana de archivos](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.

    b. En el cuadro de texto **Apellido** , escriba **Simon**. 

    c. En el cuadro de texto **Dirección de correo electrónico** , escriba la dirección de correo electrónico de Bárbara en el portal de clásico de Azure.

    d. Haga clic en **Enviar**.   


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a archivos más planas.

![Asignar usuario][200] 

**Para asignar a Britta Simon a archivos más plana, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Archivos más planas**.

    ![Asignar usuario](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de archivos más plana en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de archivos más planas.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png






