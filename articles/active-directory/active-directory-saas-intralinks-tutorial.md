<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Intralinks | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Intralinks."
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


# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Tutorial: Integración de Azure Active Directory con Intralinks

En este tutorial, aprenderá a integrar Intralinks con Azure Active Directory (AD Azure).

Integrar Intralinks con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Intralinks
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Intralinks (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Intralinks, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un Intralinks inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Intralinks de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-intralinks-from-the-gallery"></a>Agregar Intralinks de la Galería
Para configurar la integración de Intralinks en Azure AD, debe agregar Intralinks desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Intralinks de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Intralinks**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)

7. En el panel de resultados, seleccione **Intralinks**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único

En esta sección, configurar y probar el inicio de sesión único Azure AD con Intralinks en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Intralinks a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Intralinks.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Intralinks.

Para configurar y probar el inicio de sesión único de Azure AD con Intralinks, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un Intralinks probar usuario](#creating-an-intralinks-test-user)** - a tiene equivalente de Britta Simon en Intralinks que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación Intralinks.


**Para configurar el inicio de sesión único Azure AD con Intralinks, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicación **Intralinks** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión Intralinks** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , siga estos pasos:.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de Intralinks mediante el siguiente patrón: **https://\<nombre de la compañía\>.Intralinks.com/?PartnerIdpId= https://sts.windows.net/\<ID de Azure AD inquilino\>**.

    b. Haga clic en **siguiente**.
    
4. En la página **configurar inicio de sesión único en Intralinks** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para obtener el SSO configurado para la aplicación, póngase en contacto con el equipo de soporte técnico de Intralinks y el archivo de metadatos descargados adjuntar el correo electrónico.


6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.

En la lista de usuarios, seleccione **Bárbara Menéndez**.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , siga estos pasos:  ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , siga estos pasos: ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-intralinks-test-user"></a>Crear un usuario de prueba Intralinks

En esta sección, creará un usuario llamado a Britta Simon en Intralinks. Trabaje con el equipo de soporte técnico de Intralinks para agregar los usuarios de la plataforma de Intralinks.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Intralinks.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Intralinks, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Intralinks**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]

### <a name="adding-intralinks-via-or-elite-application"></a>Agregar aplicación Intralinks a través de o Elite
Intralinks utiliza la misma plataforma de inicio de sesión único en la identidad para todas las demás aplicaciones de Intralinks Excluir aplicación Nexus de negocio. Por lo que si tiene previsto usar cualquier otra aplicación Intralinks, a continuación, primero debe configurar el inicio de sesión único para una aplicación de Intralinks principal mediante el procedimiento descrito anteriormente.

Después de que puede seguir el siguiente procedimiento para agregar otra aplicación Intralinks en su inquilino que puede aprovechar esta aplicación principal para el inicio de sesión único. 

> [AZURE.NOTE] Inicie la nota que esta característica está disponible únicamente para los clientes de Azure AD Premium SKU y no está disponible de forma gratuita o clientes de SKU básica.

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. Desde la ficha izquierda, haga clic en la pestaña **personalizada**

    ![Agregar aplicación Intralinks a través de o Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)

7. Asigne el nombre adecuado para la aplicación, por ejemplo, **Intralinks Elite** y haga clic en botón de finalizar.

8. Haga clic en el botón de **Configurar inicio de sesión único**

9. Seleccione la opción **Existente inicio de sesión único**

    ![Agregar aplicación Intralinks a través de o Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)

10. Obtener el SP iniciados por SSO dirección URL de la Intralinks de grupo para la otra aplicación Intralinks e introducirlo tal y como se muestra a continuación. 

    ![Agregar aplicación Intralinks a través de o Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)

    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de Intralinks mediante el siguiente patrón: **https://\<nombredelaempresa\>.Intralinks.com/?PartnerIdpId= https://sts.windows.net/\<AzureADTenantID\> **


11. Haga clic en **siguiente**.

12. Asignar la aplicación a usuarios o grupos, como se muestra en la sección ** [asignar al usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de Intralinks en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación Intralinks.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png
