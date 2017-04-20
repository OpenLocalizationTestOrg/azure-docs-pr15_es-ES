<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con eTouches | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y eTouches."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: Integración de Azure Active Directory con eTouches

En este tutorial, aprenderá a integrar eTouches con Azure Active Directory (AD Azure).

Integrar eTouches con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a eTouches
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en eTouches (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con eTouches, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un eTouches inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar eTouches de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-etouches-from-the-gallery"></a>Agregar eTouches de la Galería
Para configurar la integración de eTouches en Azure AD, debe agregar eTouches desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar eTouches de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **eTouches**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_01.png)

7. En el panel de resultados, seleccione **eTouches**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único

En esta sección, configurar y probar el inicio de sesión único Azure AD con eTouches en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en eTouches a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en eTouches.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en eTouches.

Para configurar y probar el inicio de sesión único de Azure AD con eTouches, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un eTouches probar usuario](#creating-a-predictix-price-reporting-test-user)** - tener un equivalente de Britta Simon en eTouches que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación eTouches.

aplicación de eTouches espera las afirmaciones SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la pestaña **"Atrribute"** de la aplicación. La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_07.png) 

**Para configurar el inicio de sesión único Azure AD con eTocuhes, siga estos pasos:**


1. En el portal de clásico Azure, en la página de integración de aplicación **eTouches** , en el menú de la parte superior, haga clic en **atributos**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_general_80.png) 


2. En el cuadro de diálogo **atributos de token SAML** , para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes:

  	| Nombre del atributo | Valor del atributo |
  	| --- | --- |    
  	| Correo electrónico | User.Mail |

    una. Haga clic en **Agregar usuario atributo** para abrir el cuadro de diálogo **Agregar usuario Attribure** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_general_81.png) 


    b. En el cuadro de texto **Nombre del atributo isErrorPage** , escriba el nombre del atributo que se muestra de la fila.

    c. En la lista **Valor del atributo** , selsect el valor del atributo se muestra de la fila.

    d. Haga clic en **Finalizar**.  
    

3. En el portal clásico, en la página de integración de aplicación **eTouches** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

4. En la página **¿cómo desea que los usuarios para iniciar sesión eTouches** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_03.png) 

5. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de eTouches mediante el siguiente patrón: **https://www.eiseverywhere.com/saml/accounts/?sso&accountid=\<accountid\>**.
    
    b. Haga clic en **siguiente**
 
6. En la página **configurar inicio de sesión único en eTouches** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_05.png)

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


7. Para obtener el SSO configurado para la aplicación, realice los pasos siguientes en la aplicación eTouches:

    una. Inicie sesión en la aplicación de **eTouches** con los derechos de administrador.
    
    b. Vaya a la configuración de **SAML**

    c. En la sección **Configuración General** pegar el contenido de los metadatos de federación de Azure AD en el cuadro de texto.

    d. Haga clic en el botón **Guardar y mantenerse**

    e. Haga clic en el botón de **Actualización de metadatos** en la sección de metadatos de SAML. 

    f. Esto abrirá la página y realizará SSO. Una vez que el SSO está trabajando puede configurar el nombre de usuario

    g. En el campo **nombre de usuario** , seleccione la **dirección de correo electrónico** como se muestra en la imagen siguiente. 

    h. Copiar la **SSO URL / ACS** valor y colocarlo en el cuadro de texto de inicio de sesión en la dirección URL de Azure AD aplicación configuración asistente.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png)

8. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

9. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
 
    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.


![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , siga estos pasos:  ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , siga estos pasos: ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-an-etouches-test-user"></a>Crear un usuario de prueba eTouches

En esta sección, creará un usuario llamado a Britta Simon en eTouches. Trabaje con el equipo de soporte técnico de eTouches para agregar los usuarios de la plataforma de eTouches.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso al eTouches.

![Asignar usuario][200] 

**Para asignar a Britta Simon a eTouches, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **eTouches**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de eTouches en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación eTouches.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_205.png
