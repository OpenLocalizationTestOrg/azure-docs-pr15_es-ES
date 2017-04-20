<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con elementos SD | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y los elementos de SD."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Tutorial: Integración de Azure Active Directory con elementos de SD

El objetivo de este tutorial es mostrar cómo integrar SD elementos con Azure Active Directory (AD Azure).  
Integración de elementos de SD con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a los elementos de SD
- Puede habilitar los usuarios automáticamente obtener firmado en elementos SD (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: Azure Active Directory 


Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con elementos de SD, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un SD elementos inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar elementos de SD desde la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-sd-elements-from-the-gallery"></a>Agregar elementos de SD desde la Galería
Para configurar la integración de elementos de SD en Azure AD, debe agregar SD elementos de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar elementos de SD de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **SD elementos**.
 
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)

7. En el panel de resultados, seleccione **Elementos de SD**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con elementos SD en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en SD elementos a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario en SD elementos relacionado.  
Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en los elementos de SD establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con elementos de SD, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear elementos de SD probar usuario](#creating-a-sd-elements-test-user)** - tener un equivalente de Britta Simon en elementos de SD que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en una aplicación de elementos de SD.

La aplicación de elementos de SD espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** . La siguiente captura de pantalla muestra un ejemplo de esto:

![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) 



**Para configurar el inicio de sesión único Azure AD con elementos de SD, realice los pasos siguientes:**

1. En el portal de clásico Azure, en la página de integración de aplicación **SD elementos** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión SD elementos** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , siga estos pasos:.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) 


    una. En el cuadro de texto del **emisor** , escriba la dirección URL de emisor de su inquilino utilizando el modelo siguiente: *https://\<el nombre del inquilino\>.sdelements.com/sso/saml2/metadata*
   
    b. En el cuadro de texto **Dirección URL de respuesta** , escriba la dirección URL de respuesta de su inquilino utilizando el modelo siguiente: *https://\<el nombre del inquilino\>.sdelements.com/sso/saml2/acs/*       

    > [AZURE.NOTE] Si necesita la dirección URL del emisor y dirección URL de respuesta real de su inquilino, póngase en contacto con su [equipo de soporte técnico SD elementos](mailto:support@sdelements.com).
      
    c. Haga clic en **siguiente**.


4. En la página **configurar inicio de sesión único en SD elementos** , realice los pasos siguientes:
   
    ![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


1. Para obtener habilitado para el inicio de sesión único, póngase en contacto con su [equipo de soporte técnico de los elementos de SD](mailto:support@sdelements.com) y proporcionarles el archivo descargado certificado.


5. En una ventana de explorador diferente, singn en su inquilino SD elementos como administrador.

6. En el menú de la parte superior, haga clic en sistema y, a continuación, inicio de sesión único. 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 


7. En el cuadro de diálogo **Configuración de inicio de sesión único** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 

    una. Como **Tipo de SSO**, seleccione **SAML**.

    b. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en elementos de SD** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto **ID de entidad de proveedor de identidades** .

    c. En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en elementos de SD** , copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto de **Identidad proveedor solo inicio de sesión de servicio** .

    d. Haga clic en **Guardar**.

6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]

1. En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** . 
    
    ![Configurar el inicio de sesión único][21]


2. Para cada fila de la tabla siguiente, realice los pasos siguientes:

  	| Nombre del atributo | Valor del atributo |
  	| ---            | ---             |
  	| Correo electrónico          | User.Mail       |
  	| nombre      | User.givenName  |
  	| apellido       | User.Surname    |


    una. Haga clic en **Agregar usuario atributo**. 
    
    ![Configurar el inicio de sesión único][23]

    b. En el cuadro de texto **Nombre de atributo** , escriba el **Nombre del atributo** y como **Valor del atributo**, seleccione el valor del atributo se muestra de la fila.
    
    ![Configurar el inicio de sesión único][22]

    c. Haga clic en **Agregar usuario atributo**. 
    
    ![Configurar el inicio de sesión único][23]

1. Haga clic en **Aplicar cambios**. 
    
    ![Configurar el inicio de sesión único][24]

### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-sd-elements-test-user"></a>Crear un usuario de prueba SD elementos

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en SD elementos. En el caso de SD elementos, crear elementos de SD usuarios es una tarea manual.

**Para crear a Britta Simon en SD elementos, realice los pasos siguientes:**

1.  En una ventana de explorador web, inicio de sesión en el sitio de su empresa SD elementos como administrador.

2.  En el menú de la parte superior, haga clic en administración de usuarios y, a continuación, los usuarios.
 
    ![Crear un usuario de prueba SD elementos](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 

3.  Haga clic en Agregar nuevo usuario.
 
    ![Crear un usuario de prueba SD elementos](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) 

4.  En el cuadro de diálogo Agregar nuevo usuario, siga estos pasos:

    ![Crear un usuario de prueba SD elementos](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 

    una. En el cuadro de texto de **correo electrónico** , escriba la dirección de correo electrónico de Bárbara en Azure AD.

    b. En el cuadro de texto **nombre** , escriba **Bárbara**.

    c. En el cuadro de texto **Apellido** , escriba **Simon**.

    d. Como la **función**, seleccione el **usuario**. 

    e. Haga clic en **Crear usuario**.




### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a los elementos de SD.

![Asignar usuario][200] 

**Para asignar a Britta Simon a SD elementos, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Elementos de SD**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de **usuarios** , seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de SD elementos en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de elementos de SD.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png
