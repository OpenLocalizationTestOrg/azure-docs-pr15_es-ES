<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Clever | Microsoft Azure" 
    description="Aprenda a usar Clever con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Tutorial: Integración de Azure Active Directory con Clever

El objetivo de este tutorial es mostrar la integración de Azure y Clever. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino inteligente

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Clever será capaz de inicio de sesión único en la aplicación en el sitio de su empresa inteligente (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Clever
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-clever-tutorial/IC798977.png "Escenario")
##<a name="enabling-the-application-integration-for-clever"></a>Habilitar la integración de aplicación para Clever

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Clever.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Clever, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-clever-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-clever-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Clever**.

    ![Galería de aplicación] (./media/active-directory-saas-clever-tutorial/IC798978.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Clever**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Inteligente] (./media/active-directory-saas-clever-tutorial/IC798979.png "Inteligente")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Clever con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La aplicación inteligente espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Atributos] (./media/active-directory-saas-clever-tutorial/IC798980.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Clever** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clever-tutorial/IC784682.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Clever** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clever-tutorial/IC798981.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión inteligente en URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación inteligente (por ejemplo: *https://clever.com/in/azsandbox*) y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-clever-tutorial/IC798982.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Clever** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clever-tutorial/IC798983.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa inteligente como administrador.

6.  En la barra de herramientas, haga clic en **Inicio de sesión de instantánea**.

    ![Inicio de sesión de instantánea] (./media/active-directory-saas-clever-tutorial/IC798984.png "Inicio de sesión de instantánea")

7.  En la página de **Inicio de sesión de instantánea** , realice los pasos siguientes:

    ![Inicio de sesión de instantánea] (./media/active-directory-saas-clever-tutorial/IC798985.png "Inicio de sesión de instantánea")

    1.  Escriba la **dirección URL de inicio de sesión**.  

        >[AZURE.NOTE] La **Dirección URL de inicio de sesión** es un valor personalizado.
Puede obtener el valor real desde el equipo de soporte inteligente.

    2.  Como **Identidad del sistema**, seleccione **ADFS**.
    3.  Haga clic en **Guardar**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-clever-tutorial/IC798986.png "Configurar el inicio de sesión único")

9.  En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-clever-tutorial/IC795920.png "Atributos")

10. Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    ![atributos de token SAML] (./media/active-directory-saas-clever-tutorial/IC795921.png "atributos de token SAML")

  	|Nombre del atributo|Valor del atributo|
  	|---|---|
  	|clever.Student.Credentials.District\_nombre de usuario|User.userPrincipalName|

    1.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    3.  En el cuadro de texto del **Valor del atributo** , seleccione el valor del atributo que se muestra de la fila.
    4.  Haga clic en **Finalizar**.

11. Haga clic en **Aplicar cambios**.

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Clever, debe aprovisionar en Clever.  
En el caso de Clever, aprovisionamiento es una tarea manual que debe realizarse inteligente soporte técnico.

>[AZURE.NOTE] Puede usar otras herramientas de creación de la cuenta usuario inteligente o API proporcionan por Clever a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Para asignar a los usuarios a Clever, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Clever **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-clever-tutorial/IC798987.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-clever-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
