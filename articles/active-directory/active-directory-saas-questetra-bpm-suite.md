<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con conjunto de BPM Questetra | Microsoft Aure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y conjunto de BPM Questetra."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integración de Azure Active Directory con Questetra BPM Suite

El objetivo de este tutorial es mostrar cómo integrar el conjunto de BPM Questetra con Azure Active Directory (AD Azure).  
Integrar el conjunto de BPM Questetra con Azure AD proporciona las siguientes ventajas: 

- Puede controlar en Azure AD quién tiene acceso a Questetra BPM Suite 
- Puede habilitar los usuarios automáticamente obtener firmado en conjunto de BPM Questetra (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos 

Para configurar la integración de Azure AD con Questetra BPM Suite, necesita lo siguiente:

- Una suscripción de Azure AD
- Un [Conjunto de BPM Questetra](https://senbon-imadegawa-988.questetra.net/) inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres pilares básicos:

1. Agregar conjunto de BPM Questetra de la Galería 
2. Configurar y probar Azure AD de sesión único


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Agregar conjunto de BPM Questetra de la Galería
Para configurar la integración de conjunto de BPM Questetra en Azure AD, debe agregar conjunto de BPM Questetra de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar conjunto de BPM Questetra de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Questetra BPM Suite**.

    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **Conjunto de BPM Questetra**y, a continuación, haga clic en **completado** para agregar la aplicación.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con conjunto de BPM Questetra en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en conjunto de BPM Questetra a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario en el conjunto de BPM Questetra relacionado.  
Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en el conjunto de BPM Questetra establecida esta relación de vínculo.
 
Para configurar y probar el inicio de sesión único de Azure AD con conjunto de BPM Questetra, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un conjunto de BPM Questetra probar usuario](#creating-a-questetra-bpm-suite-test-user)** - tener un equivalente de Britta Simon en conjunto de BPM Questetra que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es habilitar inicio de sesión único de Azure AD en el portal de clásico Azure y configurar el inicio de sesión único en una aplicación de conjunto de BPM Questetra.

**Para configurar el inicio de sesión único Azure AD con conjunto de BPM Questetra, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **Conjunto de BPM Questetra** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][8]

2. En la página **¿cómo desea que los usuarios para iniciar sesión el conjunto de BPM Questetra** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][9]


3. En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa **Questetra BPM Suite** como administrador.

4. En el menú de la parte superior, haga clic en **Configuración del sistema**. 

    ![En el inicio de sesión único de Azure AD][10]

5. Para abrir la página **SingleSignOnSAML** , haga clic en **SSO (SAML)**. 

    ![En el inicio de sesión único de Azure AD][11]


6. En el portal de Azure clásico, en la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes: 

    ![Establecer la configuración de la aplicación][13]
 
    una. En **Conjunto de BPM Questetra** empresa sitio, en la sección información de SP, copie la **Dirección URL de ACS**y péguelo en el cuadro de texto de **Inicio de sesión en la dirección URL** .

    b. En **Conjunto de BPM Questetra** empresa sitio, en la sección información de SP, copie el **Id. de entidad**y péguelo en el cuadro de texto de la **Dirección URL del emisor** .

    c. En **Conjunto de BPM Questetra** empresa sitio, en la sección información de SP, copie la **Dirección URL de ACS**y péguelo en el cuadro de texto **Dirección URL de respuesta** .

    d. Haga clic en **siguiente**.

 
7. En la página **configurar inicio de sesión único en el conjunto de BPM Questetra** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único][14]


8. En el sitio de la compañía **Questetra BPM Suite** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único][15]

    una. Seleccione **Habilitar inicio de sesión único**.
     
    b. En el portal de clásico Azure, copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto **Id. de entidad** .

    c. En el portal de clásico Azure, copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto **dirección URL de la página de inicio de sesión** .

    d. En el portal de clásico Azure, copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **dirección URL de la página de cierre de sesión** .

    e. En el cuadro de texto de **formato NameID** , escriba **urn: oasis: nombres: tc: SAML:1.1:nameid-formato: dirección de correo electrónico**.


    f. Crear un archivo de base 64 codificado de certificado descargado. 

    >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido del mismo en su Portapapeles y péguelo en el cuadro de texto de **certificado de validación** . 

    h. Haga clic en **Guardar**.


9. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**. 

    ![¿Qué es Azure AD Connect][17]


10. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![¿Qué es Azure AD Connect][18]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear el usuario de prueba de Azure AD][100] 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear el usuario de prueba de Azure AD][101] 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**. 

    ![Crear el usuario de prueba de Azure AD][102] 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear el usuario de prueba de Azure AD][103]
 
    una. Como **Tipo de usuario**, seleccione **nuevo usuario de su organización**.
  
    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en siguiente.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes: 

    ![Crear el usuario de prueba de Azure AD][104] 
  
    una. En el cuadro de texto **nombre** , escriba **Bárbara**. 
 
    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear el usuario de prueba de Azure AD][105]  

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear el usuario de prueba de Azure AD][106]   
  
    una. Anote el valor de la **Nueva contraseña**.
  
    b. Haga clic en **Finalizar**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Crear un usuario de prueba Questetra BPM Suite

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en conjunto de BPM Questetra.

**Para crear un usuario llamado a Britta Simon en conjunto de BPM Questetra, siga estos pasos:**

1.  Inicio de sesión en el sitio de su empresa Questetra BPM Suite como administrador.
2.  Vaya a **Configuración del sistema > lista de usuario > nuevo usuario**. 
3.  En el cuadro de diálogo nuevo usuario, siga estos pasos: 

    ![Crear el usuario de prueba][300] 

    una. En el cuadro de texto **nombre** , escriba el nombre de usuario de Bárbara en Azure AD.

    b. En el cuadro de texto de **correo electrónico** , escriba el nombre de usuario de Bárbara en Azure AD.

    c. En el cuadro de texto **contraseña** , escriba una contraseña.

4.  Haga clic en **Agregar nuevo usuario**.



### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a conjunto de BPM Questetra.

![¿Qué es Azure AD Connect][200]

**Para asignar a Britta Simon Questetra BPM Suite, realice los pasos siguientes:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![¿Qué es Azure AD Connect][201]

2. En la lista de aplicaciones, seleccione **Conjunto de BPM Questetra**.

    ![¿Qué es Azure AD Connect][205]

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![¿Qué es Azure AD Connect][202]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

    ![¿Qué es Azure AD Connect][203]

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![¿Qué es Azure AD Connect][204]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el icono de conjunto de BPM Questetra en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación de la serie de BPM Questetra.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 