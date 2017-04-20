<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Jive | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Jive."
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


# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Tutorial: Integración de Azure Active Directory con Jive

En este tutorial, aprenderá a integrar Jive con Azure Active Directory (AD Azure).

Integrar Jive con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Jive
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Jive (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Jive, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un Jive inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Jive de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-jive-from-the-gallery"></a>Agregar Jive de la Galería
Para configurar la integración de Jive en Azure AD, debe agregar Jive desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Jive de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Jive**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. En el panel de resultados, seleccione **Jive**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con Jive en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Jive a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Jive.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Jive.

Para configurar y probar el inicio de sesión único de Azure AD con Jive, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un Jive probar usuario](#creating-a-jive-test-user)** - a tiene equivalente de Britta Simon en Jive que está vinculado a la representación de Azure AD de ella.
4. **[Configuración de aprovisionamiento de usuario](#configuring-user-provisioning)** - que describen cómo habilitar el aprovisionamiento de usuario del usuario de Active Directory cuentas a Jive.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
6. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación Jive.

**Para configurar el inicio de sesión único Azure AD con Jive, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicación **Jive** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión Jive** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de Jive mediante el siguiente patrón: **https://\<nombre del cliente\>. jivecustom.com**.
    
    b. Haga clic en **siguiente**
 
4. En la página **configurar inicio de sesión único en Jive** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Inicio de sesión en su inquilino Jive como administrador.

6. En el menú de la parte superior, haga clic en "**Saml**".

    ![Configurar el inicio de sesión único en el lado de la aplicación](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    una. Seleccione **habilitada** en la ficha **Genaral** .

    b. Haga clic en el botón "**Guardar todos los valores de saml**".

7. Vaya a la pestaña "**Idp metadatos**".

    ![Configurar el inicio de sesión único en el lado de la aplicación](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

    una. Copiar el contenido del archivo XML de metadatos descargados y péguelo en el cuadro de texto de **metadatos del proveedor de identidades (IDP)** .

    b. Haga clic en el botón "**Guardar todos los valores de saml**". 

8. Vaya a la ficha "**Asignación de atributos de usuario**".

    ![Configurar el inicio de sesión único en el lado de la aplicación](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

    una. En el cuadro de texto de **correo electrónico** , copie y pegue el nombre del atributo de valor de **correo** .

    b. En el cuadro de texto **nombre** , copie y pegue el nombre del atributo de valor **givenname** .

    c. En el cuadro de texto **Apellido** , copie y pegue el nombre del atributo de valor del **apellido** .
    
9. En el portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
![En el inicio de sesión único de Azure AD][10]

10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
  ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.


![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , siga estos pasos:  ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , siga estos pasos: ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



###<a name="creating-a-jive-test-user"></a>Crear un usuario de prueba Jive

En esta sección, creará un usuario llamado a Britta Simon en Jive. Trabaje con el equipo de soporte técnico de Jive para agregar los usuarios de la plataforma de Jive.


###<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
El objetivo de esta sección es describen cómo habilitar el usuario de aprovisionamiento de cuentas de usuario de Active Directory para Jive.  
Como parte de este procedimiento, es necesario para proporcionar un token de seguridad de usuario que debe solicitar Jive.com.
  
La siguiente captura de pantalla muestra un ejemplo del cuadro de diálogo relacionado en Azure AD:

![Configurar el aprovisionamiento de usuario] (./media/active-directory-saas-jive-tutorial/IC698794.png "Configurar el aprovisionamiento de usuario")

####<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  En el Portal de administración de Azure en la página de integración de aplicación **Jive** , haga clic en **Configurar el aprovisionamiento de usuario** para abrir el cuadro de diálogo **Configurar el aprovisionamiento de usuario** .

2.  En la página **Escriba sus credenciales de Jive para habilitar el aprovisionamiento de usuario automática** , proporcione la siguiente configuración:

    1.  En el cuadro de texto **Nombre de usuario de administración de Jive** , escriba un nombre de cuenta de Jive que tiene el perfil de **Administrador del sistema** en Jive.com asignado.

    2.  En el cuadro de texto **Contraseña de administrador de Jive** , escriba la contraseña para esta cuenta.

    3.  En el cuadro de texto de la **Dirección URL del inquilino de Jive** , escriba la dirección URL del inquilino Jive.

        >[AZURE.NOTE]La dirección URL de inquilinos de Jive es que su organización utiliza para iniciar sesión en Jive.  
        Normalmente, la dirección URL tiene el siguiente formato: **www.\< organización\>. jive.com**.

    4.  Haga clic en **Validar** para comprobar la configuración.

    5.  Haga clic en el botón **siguiente** para abrir la página de **confirmación** .

3.  En la página de **confirmación** , haga clic en la marca de verificación para guardar la configuración.
  
Ahora puede crear una cuenta de prueba, espere 10 minutos y compruebe que la cuenta se ha sincronizado con Jive.com.




### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Jive.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Jive, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Jive**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de Jive en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación Jive.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png
