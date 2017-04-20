<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con gris hospedado | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y hospedado gris."
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


# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integración de Azure Active Directory con hospedado gris

El objetivo de este tutorial es mostrar cómo integrar hospedado gris con Azure Active Directory (AD Azure).

Integración de hospedado gris con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a hospedado gris
- Puede habilitar los usuarios automáticamente obtener firmado en gris hospedado (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con hospedado gris, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un gris hospedado inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar hospedado gris de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-hosted-graphite-from-the-gallery"></a>Agregar hospedado gris de la Galería
Para configurar la integración de gris hospedado en Azure AD, debe agregar hospedado gris de la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar hospedado gris de la galería, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
    
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Hospedado gris**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)

7. En el panel de resultados, seleccione **Hospedado gris**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Selección de la aplicación en la Galería](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único Azure AD con gris hospedado en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en gris hospedado a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en gris hospedado.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en gris hospedado establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con hospedado gris, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un gris hospedado probar usuario](#creating-a-hosted-graphite-test-user)** - a tiene equivalente de Britta Simon en gris hospedado que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación hospedado gris.

**Para configurar el inicio de sesión único Azure AD con hospedado gris, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicación **Hospedado gris** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión gris hospedado** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)

3. En la página de diálogo **Configurar opciones de aplicación** , si desea configurar la aplicación en **IDP iniciadas modo**, realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)

    una. En el cuadro de texto **identificador** , escriba una dirección URL utilizando el modelo siguiente:`https://www.hostedgraphite.com/metadata/<user id>`

    b. En el cuadro de texto **Dirección URL de respuesta** , escriba una dirección URL mediante el siguiente patrón:`https://www.hostedgraphite.com/complete/saml/<user id>`

    c. Haga clic en **siguiente**

4. Si desea configurar la aplicación en **SP iniciados por el modo** en la página de diálogo de **Configuración de aplicación** , haga clic en **"Mostrar avanzadas configuración (opcional)"** y, a continuación, introduzca el **Inicio de sesión en la dirección URL** y haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba una dirección URL mediante el siguiente patrón:`https://www.hostedgraphite.com/login/saml/<user id>/`

    b. Haga clic en **siguiente**

    > [AZURE.NOTE] Tenga en cuenta que no son los valores reales. Debe actualizar estos valores con el inicio de sesión en la dirección URL, identificador y dirección URL de respuesta real. Para obtener estos valores, puede ir a Access -> configuración SAML en el lado de la aplicación o póngase en contacto con hospedado gris.

5. En la página **configurar inicio de sesión único en gris hospedado** , realice los pasos siguientes y haga clic en **siguiente**:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.

6. Inicio de sesión en su inquilino hospedado gris como administrador.

7. Vaya a la **página de configuración de SAML** en la barra lateral (**Access -> configuración de SAML**).

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

8. Confirmar estas direcciones URL que coincidan con la configuración en el paso 3.

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

9. Copiar **dirección URL del emisor** y **SAML SSO** de Azure AD **entidad o el identificador del emisor** y la **dirección URL de inicio de sesión SSO** en gris hospedado.

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)

9. Seleccione "**sólo lectura**" como **rol de usuario predeterminado**.

    ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

10. Copiar el contenido del archivo de certificado descargados y péguelo en el cuadro de texto de **Certificado X.509** .

     ![Configurar lado solo inicio de sesión de aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

11. Haga clic en el botón **Guardar** .

12. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

13. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
    
    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico denominado a Britta Simon.

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **Portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:
    
    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-hosted-graphite-test-user"></a>Crear un usuario de prueba hospedado gris

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en gris hospedado. Gris hospedado admite en el tiempo de aprovisionamiento, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para en esta sección. Al intentar tener acceso a gris hospedado si todavía no existe, se creará un nuevo usuario.

> [AZURE.NOTE] Si necesita crear un usuario de forma manual, debe ponerse en contacto con el equipo de soporte técnico de gris hospedado mediante <mailto:help@hostedgraphite.com>.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a hospedado gris.
    
![Asignar usuario][200]

**Para asignar a Britta Simon a hospedado gris, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
    
    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Hospedado gris**.
    
    ![Configurar el inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)

1. En el menú de la parte superior, haga clic en **usuarios**.
    
    ![Asignar usuario][203]

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.
    
    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.
 
Al hacer clic en el mosaico de gris hospedado en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su aplicación hospedado gris.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png
