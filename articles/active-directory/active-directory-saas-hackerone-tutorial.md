<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con HackerOne | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y HackerOne."
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


# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integración de Azure Active Directory con HackerOne

En este tutorial, se integre HackerOne con Azure Active Directory (AD Azure).

Integrar HackerOne con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a HackerOne
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en HackerOne (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con HackerOne, necesitará los elementos siguientes:

- Una suscripción de Azure
- Un HackerOne inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, configurar y probar el inicio de sesión único Azure AD en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar HackerOne de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-hackerone-from-the-gallery"></a>Agregar HackerOne de la Galería
Para integrar HackerOne en Azure AD, debe agregar HackerOne desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar HackerOne de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

![Aplicaciones][4]

6. En el cuadro Buscar, escriba **HackerOne**.

![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. En el panel de resultados, seleccione **HackerOne**y, a continuación, haga clic en **completado** para agregar la aplicación.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
A continuación, configurar y probar el inicio de sesión único Azure AD con HackerOne en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en HackerOne a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en HackerOne.  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en HackerOne.

Para configurar y probar el inicio de sesión único de Azure AD con HackerOne, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un HackerOne probar usuario](#creating-a-hackerone-test-user)** - a tiene equivalente de Britta Simon en certificar que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

A continuación, habilitar Azure AD inicio de sesión único en el portal de clásico y configurar el inicio de sesión único en la aplicación HackerOne.

Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar el inicio de sesión único Azure AD con HackerOne, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación **HackerOne** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión HackerOne** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 


    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de HackerOne mediante el siguiente patrón: **"https://hackerone.com/\<nombre de la compañía\>/authentication"**. 

    b. Póngase en contacto con el equipo de soporte técnico de HackerOne a través de [support@hackerone.com](mailto:support@hackerone.com) para obtener la dirección URL del inquilino si no lo sabe.

    c. En el cuadro de texto **identificador** , escriba la dirección URL del inquilino. 

    d. Haga clic en **siguiente**.


4. En la página **configurar inicio de sesión único en HackerOne** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


1. Inicio de sesión en su inquilino HackerOne como administrador.

1. En el menú de la parte superior, haga clic en la **configuración**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

1. Vaya a "**autenticación**" y haga clic en "**Agregar SAML configuración**".

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 


1. En el cuadro de diálogo **Configuración de SAML** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    una. En el cuadro de texto **Dominio de correo electrónico** , escriba un dominio registrado.

    b. En el portal de clásico Azure, copiar la **URL de servicio de inicio de sesión único**y péguelo en el cuadro de texto de inicio de sesión único en URL.

    c. Crear un archivo **codificado base 64** de certificado descargado.  

       >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    
    d. Abrir el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en la **X509 certificado** cuadro de texto.

    e. Haga clic en **Guardar**


1. En el cuadro de diálogo Configuración de autenticación, realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    una. Haga clic en **Ejecutar prueba**.

    b. Si el valor del **estado** de campo es igual a **estado de la última prueba: crea**, póngase en contacto con el soporte técnico de HackerOne a través de [support@hackerone.com](mailto:support@hackerone.com) para solicitar una revisión de la configuración.


6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD

A continuación, cree un usuario de prueba en el portal de clásico denominado a Britta Simon.  

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba seguro entregar en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   


### <a name="creating-a-hackerone-test-user"></a>Crear un usuario de prueba HackerOne

A continuación, cree un usuario llamado a Britta Simon en HackerOne. HackerOne admite en el tiempo de aprovisionamiento, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para en esta sección. Cuando se tiene acceso a HackerOne, se crea un nuevo usuario si aún no existe. [Configuración de inicio de sesión único de Azure AD en](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico para certificar.




### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

A continuación, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a HackerOne.

![Asignar usuario][200] 

**Para asignar a Britta Simon a HackerOne, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **HackerOne**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Por último, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el mosaico de HackerOne en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación HackerOne.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png