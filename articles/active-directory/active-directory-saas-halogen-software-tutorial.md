<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Software de halógeno"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y halógeno Software."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integración de Azure Active Directory con Software de halógeno

El objetivo de este tutorial es mostrar cómo integrar halógeno Software con Azure Active Directory (AD Azure).

Integración del Software de halógeno con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a Software halógeno 
- Puede habilitar los usuarios automáticamente obtener firmado en Software halógenas (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con Software de halógeno, necesita lo siguiente:

- Una suscripción de Azure AD
- Un Software de halógeno inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba. 

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Software halógenas de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-halogen-software-from-the-gallery"></a>Agregar Software halógenas de la Galería
Para configurar la integración del Software de halógeno en Azure AD, debe agregar Software halógeno desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar halógeno Software de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página. 

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **software halógeno**.

    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **Halógeno Software**y, a continuación, haga clic en **completado** para agregar la aplicación.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con Software de halógeno en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en halógeno Software a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en halógeno Software.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en el Software de halógeno establecida esta relación de vínculo.
 
Para configurar y probar el inicio de sesión único de Azure AD con Software de halógeno, debe completar los siguientes bloques de creación:

1. **[Configurar Azure AD inicio de sesión único](#configuring-azure-ad-single-single-sign-on)** : para habilitar los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Creación de un Software de halógeno probar usuario](#creating-a-halogen-software-test-user)** - tener un equivalente de Britta Simon software halógeno que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurar el inicio de sesión único solo de Azure AD

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en la aplicación de Software de halógeno.


**Para configurar el inicio de sesión único Azure AD con Software halógenas, realice los pasos siguientes:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **Software de halógeno** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][8]

2. En la página **¿cómo le gustaría usuarios a iniciar sesión halógeno Software** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][9]

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:  ![configurar opciones de aplicación][10]
 
     una. en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL usada por los usuarios para iniciar sesión su aplicación de Software de halógeno utilizando el modelo siguiente: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Haga clic en **siguiente**.
 
4. En la página **configurar inicio de sesión único en halógeno Software** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.
    
    ![¿Qué es Azure AD Connect][11]

5. En una ventana de explorador diferente, inicio de sesión en su aplicación de **Software de halógeno** como administrador.

6. Haga clic en la pestaña **Opciones** . 

    ![¿Qué es Azure AD Connect][12]


7. En el panel de navegación izquierdo, haga clic en **Configuración de SAML**. 

    ![¿Qué es Azure AD Connect][13]

8. En la página **Configuración de SAML** , realice los pasos siguientes:  ![¿qué es Azure AD Connect][14]

    una. Como **Identificador único**, seleccione **NameID**.

    b. Como **Mapas de identificador único para**, seleccione el **nombre de usuario**.

    c. Para cargar el archivo descargado metadatos, haga clic en **Examinar** para seleccionar el archivo y, a continuación, en **Cargar archivo**.

    d. Para probar la configuración, haga clic en **Ejecutar prueba**. 

    > [AZURE.NOTE] Debe esperar para que el mensaje "*la SAML prueba está completa. Cierre esta ventana*". A continuación, cierre la ventana del explorador abierta. Sólo se activa la casilla de verificación **Habilitar SAML** si ha finalizado la prueba.

    e. Seleccione **Habilitar SAML**.
    
    f. Haga clic en **Guardar cambios**. 


9. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** . 

    ![¿Qué es Azure AD Connect][15]

10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![¿Qué es Azure AD Connect][16]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![¿Qué es Azure AD Connect][100] 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![¿Qué es Azure AD Connect][101] 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![¿Qué es Azure AD Connect][102] 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![¿Qué es Azure AD Connect][103] 
 
    una. Como **Tipo de usuario**, seleccione **nuevo usuario de su organización**.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en siguiente.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![¿Qué es Azure AD Connect][104] 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el **Último nombre** txtbox, tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![¿Qué es Azure AD Connect][105]  

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![¿Qué es Azure AD Connect][106]   

    una. Anote el valor de la **Nueva contraseña**.
    b. Haga clic en **Finalizar**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Crear un usuario de prueba de Software de halógeno

Es el objetivo de esta sección crear un usuario llamado a Britta Simon halógeno software.

**Para crear un usuario llamado a Britta Simon halógeno software, siga estos pasos:**

1. Inicie sesión su aplicación de **Software de halógeno** como administrador.

2. Haga clic en la pestaña **Centro de usuario** y, a continuación, haga clic en **Crear usuario**.

    ![¿Qué es Azure AD Connect][300]  

3. En la página de diálogo **Nuevo usuario** , realice los pasos siguientes:

    ![¿Qué es Azure AD Connect][301]

    una. En el cuadro de texto **nombre** , escriba **Bárbara**. 
  
    b. En el cuadro de texto **Apellido** , escriba **Simon**.
  
    c. En el cuadro de texto **nombre de usuario** , escriba el **nombre de usuario de Enrique Brita en el portal de clásico de Azure**.
  
    d. En el cuadro de texto **contraseña** , escriba una contraseña para Bárbara.
  
    e. Haga clic en **Guardar**.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a halógeno Software.

![¿Qué es Azure AD Connect][200]

**Para asignar a Britta Simon halógeno software, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![¿Qué es Azure AD Connect][201]

2. En la lista de aplicaciones, seleccione **Halógeno Software**.

    ![¿Qué es Azure AD Connect][202]

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![¿Qué es Azure AD Connect][203]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

    ![¿Qué es Azure AD Connect][204]

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![¿Qué es Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de Software halógeno en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su aplicación de Software de halógeno.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png