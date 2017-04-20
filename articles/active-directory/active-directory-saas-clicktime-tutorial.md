<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con ClickTime | Microsoft Azure" 
    description="Aprenda a usar ClickTime con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: Integración de Azure Active Directory con ClickTime

En este tutorial, aprenderá a integrar ClickTime con Azure Active Directory (AD Azure).

Integrar ClickTime con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ClickTime
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en ClickTime (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ClickTime, necesitará los elementos siguientes:

- Una suscripción de Azure AD
- Un ClickTime inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar ClickTime de la Galería
2. Configurar y probar Azure AD de sesión único

##<a name="adding-clicktime-from-the-gallery"></a>Agregar ClickTime de la Galería

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para ClickTime.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Para habilitar la integración de aplicación para ClickTime, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **ClickTime**.

    ![Galería de aplicación] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **ClickTime**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con ClickTime en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en ClickTime a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en ClickTime.

Esta relación de vínculo se establece por asignar el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ClickTime.

Para configurar y probar el inicio de sesión único de Azure AD con ClickTime, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear un ClickTime probar usuario](#creating-a-clicktime-test-user)** - a tiene equivalente de Britta Simon en ClickTime que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a ClickTime con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  


>[AZURE.IMPORTANT] Para poder configurar el inicio de sesión único en su inquilino ClickTime, debe ponerse en contacto con el soporte técnico de ClickTime para obtener esta característica está habilitada en primer lugar.

**Para configurar el inicio de sesión único Azure AD con ClickTime, siga estos pasos:**

1.  En el portal de clásico Azure, en la página de integración de aplicación **ClickTime** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Habilitar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión ClickTime** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configurar inicio de sesión único")

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    una. En el cuadro de texto **IdentifierL** , escriba la dirección URL mediante el siguiente patrón: **https://app.clicktime.com/sp/**
    
    b. En el cuadro de texto **Dirección URL de respuesta** , escriba la dirección URL mediante el siguiente patrón: **https://app.clicktime.com/Login/**

    c. Haga clic en **siguiente**

4.  En la página **configurar inicio de sesión único en ClickTime** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configurar inicio de sesión único")

4.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa ClickTime como administrador.

5.  En la barra de herramientas en la parte superior, haga clic en **Preferencias**y, a continuación, haga clic en **Configuración de seguridad**.

6.  En la sección Configuración de **Preferencias de inicio de sesión único** , realice los pasos siguientes:

    ![Configuración de seguridad] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Configuración de seguridad")

    una.  Seleccione **Permitir** inicio de sesión con el inicio de sesión único (SSO) con **Azure AD**.
    
    b.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en ClickTime** , copie el valor de **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto de **Extremo del proveedor de identidades** .

    c.  Abrir el certificado codificado de base 64 en **el Bloc de notas**, copie el contenido y péguelo en el cuadro de texto de **Certificado X.509** .
    
    d.  Haga clic en **Guardar**.

7.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en ClickTime, debe aprovisionar en ClickTime.  
En el caso de ClickTime, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **ClickTime** .

2.  En la barra de herramientas en la parte superior, haga clic en **compañía**y, a continuación, haga clic en **personas**.

    ![Personas] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Personas")

3.  Haga clic en **Agregar a persona**.

    ![Agregar persona] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Agregar persona")

4.  En la sección nueva persona, realice los pasos siguientes:

    ![Personas] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Personas")

    una.  En el cuadro de texto **dirección de correo electrónico** , escriba la dirección de correo electrónico de su cuenta de Azure AD.
    
    b.  En el cuadro de texto **nombre completo** , escriba el nombre de su cuenta de Azure AD.  

    >[AZURE.NOTE] Si lo desea, puede establecer propiedades adicionales del nuevo objeto de la persona.

    c.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros ClickTime usuario cuenta herramientas de creación o API proporcionan por ClickTime proporcionando cuentas de usuario de Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a ClickTime.

![Asignar usuario][200]

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

**Para asignar a Britta Simon a ClickTime, realice los pasos siguientes**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ClickTime**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]

## <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único
En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico de ClickTime en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación ClickTime.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png