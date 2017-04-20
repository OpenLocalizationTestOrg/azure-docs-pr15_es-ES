<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con HR2day por Merces | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y HR2day por Merces."
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


# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutorial: Integración de Azure Active Directory con HR2day por Merces

El objetivo de este tutorial es mostrar cómo integrar HR2day por Merces con Azure Active Directory (AD Azure).  
Integración de HR2day por Merces con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a HR2day por Merces
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en HR2day por Merces (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con HR2day por Merces, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un HR2day por Merces inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
Es el objetivo de este tutorial para que pueda probar Azure AD inicio de sesión único en un entorno de prueba.  
El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar HR2day por Merces de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Agregar HR2day por Merces de la Galería
Para configurar la integración de HR2day por Merces en Azure AD, debe agregar HR2day por Merces desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar HR2day por Merces de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.
 
    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **HR2day por Merces**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. En el panel de resultados, seleccione **HR2day por Merces**y, a continuación, haga clic en **completado** para agregar la aplicación.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con HR2day por Merces en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en HR2day por Merces a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en HR2day por Merces.  
Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en HR2day por Merces.

Para configurar y probar el inicio de sesión único de Azure AD con HR2day por Merces, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
4. **[Crear un HR2day por Merces probar usuario](#creating-a-hr2day-by-merces-test-user)** - a tiene equivalente de Britta Simon en HR2day por Merces que está vinculado a la representación de Azure AD de ella.
5. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD en

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar HR2day por Merces con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Su HR2day Merces aplicación espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de atributos token de SAML. La siguiente captura de pantalla muestra un ejemplo de este. 

![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

Antes de configurar la aserción SAML, debe ponerse en contacto con el soporte técnico de HR2day a través de [servicedesk@merces.nl](mailto:servicedesk@merces.nl) y solicitar el valor del atributo identificador único para el inquilino. Se necesita este valor para completar los pasos de la siguiente sección.


**Para configurar el inicio de sesión único Azure AD con HR2day por Merces, siga estos pasos:**

1. En el portal de clásico Azure, en la página de integración de aplicación de **HR2day por Merces** , en el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** . 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes, realice los pasos siguientes: 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 


    una. Haga clic en **Agregar usuario atributo**.

    b. En el cuadro de texto **Nombre de atributo** , escriba **"ATTR_LOGINCLAIM"**.

    c. En la lista **Valor del atributo** , seleccione **Join()**. 

    d. En la lista **cadena1** , seleccione **User.mail**. 

    e. En el cuadro de texto **cadena2** , escriba el **identificador único** que se le dé el equipo HR2day. 

    f. En el cuadro de texto **separador** , escriba **@**.

    g. Haga clic en **Finalizar**.

  
3. Haga clic en **Aplicar cambios**.


1. En el menú de la parte superior, haga clic en **Inicio rápido** para abrir el cuadro de diálogo de **Inicio rápido** .

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 



1. Haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo desea que los usuarios para iniciar sesión HR2day por Merces** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes: 

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 


    una. En el cuadro de texto de inicio de sesión en la dirección URL, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su HR2day aplicación de Merces mediante el siguiente patrón: **"https://\<nombre del inquilino\>.force.com/\<nombre de instancia\>"**.

    b. Haga clic en **siguiente**.

4. En la página **configurar inicio de sesión único en HR2day por Merces** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 

    una. Haga clic en **Descargar certificado**y, a continuación, guarde el archivo en su equipo.

    b. Haga clic en **siguiente**.


5. Para obtener el SSO configurado para la aplicación, póngase en contacto con su HR2day Merces equipo de soporte técnico a través de [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) y adjunte el archivo de certificado descargado a su correo electrónico. También proporciona la dirección URL de SSO de SAML, el inicio de sesión a la dirección URL y la dirección URL del emisor para que se pueden configurar para la integración de SSO.


> [AZURE.NOTE] Por favor, mencionar al equipo de Merces que esta integración necesita Id. de entidad que se establezca este patrón **https://hr2day.force.com/INSTANCENAME**



6. En el portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.

    ![En el inicio de sesión único de Azure AD][10]

7. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  

    ![En el inicio de sesión único de Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
Es el objetivo de esta sección crear un usuario de prueba en el portal de clásico Azure denominado a Britta Simon.  

![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   



### <a name="creating-a-hr2day-by-merces-test-user"></a>Crear un HR2day por el usuario de prueba de Merces

Es el objetivo de esta sección crear un usuario llamado a Britta Simon en HR2day Merces. Trabaje con HR2day Merces equipo de soporte técnico para agregar los usuarios de la cuenta HR2day. 


> [AZURE.NOTE]Si necesita crear un usuario de forma manual, debe ponerse en contacto con el HR2day Merces equipo de soporte técnico.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

El objetivo de esta sección es habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a HR2day por Merces.

![Asignar usuario][200] 

**Para asignar a Britta Simon a HR2day por Merces, siga estos pasos:**

1. En el portal de clásico de Azure, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **HR2day por Merces**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

1. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203] 

1. En la lista de usuarios, seleccione **Bárbara Menéndez**.

2. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]



### <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

Es el objetivo de esta sección probar la Azure AD única sesión configuración mediante el Panel de acceso.  
Al hacer clic en el HR2day por Merces mosaico en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en su HR2day Merces aplicación.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png
