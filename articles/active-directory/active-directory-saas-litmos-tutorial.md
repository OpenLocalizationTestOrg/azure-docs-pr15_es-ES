<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Litmos | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Litmos."
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


# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integración de Azure Active Directory con Litmos

El objetivo de este tutorial es mostrar cómo integrar Litmos con Azure Active Directory (AD Azure).  
Integrar Litmos con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a Litmos 
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Litmos (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: Azure Active Directory 

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con Litmos, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un Litmos inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres pilares básicos:

1. Agregar Litmos de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-litmos-from-the-gallery"></a>Agregar Litmos de la Galería
Para configurar la integración de Litmos en Azure AD, debe agregar Litmos desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Litmos de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Litmos**.

    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **Litmos**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Aplicaciones][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con Litmos en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Litmos a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Litmos.  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Litmos.
 
Para configurar y probar el inicio de sesión único de Azure AD con Litmos, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un Litmos probar usuario](#creating-a-halogen-software-test-user)** - a tiene equivalente de Britta Simon en Litmos que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación Litmos.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Como parte de la configuración, debe personalizar los **Atributos de Token SAML** para su aplicación Litmos.  

![En el inicio de sesión único de Azure AD][17] 

**Para configurar el inicio de sesión único Azure AD con Litmos, siga estos pasos:**

1. En el portal de Azure AD clásico, en la página de integración de aplicación **Litmos** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión Litmos** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
 
    ![En el inicio de sesión único de Azure AD][7] 


1. Inicio de sesión en el sitio de su empresa Litmos (por ejemplo: *https://azureapptest.litmos.com/account/Login*) como administrador.

    ![En el inicio de sesión único de Azure AD][21] 


1. En la barra de navegación en el lado izquierdo, haga clic en **cuentas**.

    ![En el inicio de sesión único de Azure AD][22] 


1. Haga clic en la pestaña **integraciones** .

    ![En el inicio de sesión único de Azure AD][23] 


1. En la pestaña **integraciones** , desplácese hacia abajo hasta **3ª integraciones de terceros**y, a continuación, haga clic en pestaña **SAML 2.0** .

    ![En el inicio de sesión único de Azure AD][24] 

1. Copie el valor en **es la SAML endoiint para litmos:**.

    ![En el inicio de sesión único de Azure AD][26] 


3. En el portal de Azure clásico, en la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![En el inicio de sesión único de Azure AD][8] 
 
    una. En el cuadro de texto **identificador** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación Litmos (por ejemplo: *https://azureapptest.litmos.com/account/Login*).
     
    b. En el cuadro de texto **Dirección URL de respuesta** , pegue el valor que ha copiado de la aplicación Litmos en el paso anterior.

    c. Haga clic en **siguiente**.
 
4. En la página **configurar inicio de sesión único en Litmos** , realice los pasos siguientes:

    ![En el inicio de sesión único de Azure AD][2] 

    una. Haga clic en Descargar certificado y, a continuación, guarde el archivo en su equipo.


1. En la aplicación **Litmos** , siga estos pasos:

    ![En el inicio de sesión único de Azure AD][25] 

    una. Haga clic en **Habilitar SAML**.

    b. Crear un archivo **codificado base 64** de certificado descargado.  

    >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    c. Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado X.509 de SAML** .

    d. Haga clic en **Guardar cambios**.


6. En el portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
  
    ![En el inicio de sesión único de Azure AD][11]


20. En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** . 

    ![Configurar el inicio de sesión único][12]


24. En el cuadro de diálogo **Agregar atributo de usuario** , siga estos pasos: 

    ![Configurar el inicio de sesión único][14]

  	| Nombre del atributo | Valor del atributo |
  	| ---            | ---             |
  	| Correo electrónico          | User.Mail       |
  	| Nombre      | User.givenName  |
  	| Apellido       | User.Surname    |

    Para cada fila de datos de la tabla anterior, realice los pasos siguientes:
   
    una. Haga clic en **Agregar usuario atributo**. 

    ![Configurar el inicio de sesión único][15]


    una. En el cuadro de texto **Nombre de atributo** , escriba el **Nombre del atributo** que se muestra de la fila.

    b. Seleccione el **Valor del atributo** se muestra de la fila.

    c. Haga clic en **completado** para cerrar el cuadro de diálogo **Agregar atributo de usuario** .


25. Haga clic en **Aplicar cambios**. 

    ![Configurar el inicio de sesión único][16]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de Azure clasic**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
 
4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  

    una. Como **Tipo de usuario**, seleccione **nuevo usuario de su organización**.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
 
    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.
    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
 
8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
  
    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   

  
 
### <a name="creating-a-litmos-test-user"></a>Crear un usuario de prueba Litmos

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en Litmos.  
La aplicación de Litmos admite solo en tiempo de aprovisionamiento. Esto significa, una cuenta de usuario se crea automáticamente si es necesario al intentar obtener acceso a la aplicación utilizando el Panel de acceso.

**Para crear un usuario llamado a Britta Simon en Litmos, siga estos pasos:**


1. Inicio de sesión en el sitio de su empresa Litmos (por ejemplo: *https://azureapptest.litmos.com/account/Login*) como administrador.

    ![En el inicio de sesión único de Azure AD][21] 


1. En la barra de navegación en el lado izquierdo, haga clic en **cuentas**.

    ![En el inicio de sesión único de Azure AD][22] 


1. Haga clic en la pestaña **integraciones** .

    ![En el inicio de sesión único de Azure AD][23] 


1. En la pestaña **integraciones** , desplácese hacia abajo hasta **3ª integraciones de terceros**y, a continuación, haga clic en pestaña **SAML 2.0** .

    ![En el inicio de sesión único de Azure AD][24] 

1. Seleccione **generar automáticamente usuarios:**.

    ![En el inicio de sesión único de Azure AD][27] 


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a Litmos.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Litmos, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Litmos**.

    ![Asignar usuario][202] 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de Litmos en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación Litmos.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png





