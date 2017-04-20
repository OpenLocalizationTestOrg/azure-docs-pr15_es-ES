<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con SmarterU | Microsoft Azure" 
    description="Aprenda a usar SmarterU con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: Integración de Azure Active Directory con SmarterU
  
El objetivo de este tutorial es mostrar la integración de Azure y SmarterU.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino SmarterU
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a SmarterU será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de SmarterU (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para SmarterU
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Escenario")

##<a name="enabling-the-application-integration-for-smarteru"></a>Habilitar la integración de aplicación para SmarterU
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para SmarterU.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Para habilitar la integración de aplicación para SmarterU, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **SmarterU**.

    ![Fallery de aplicación] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Fallery de aplicación")

7.  En el panel de resultados, seleccione **SmarterU**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a SmarterU con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **SmarterU** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión SmarterU** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurar el inicio de sesión único")

3.  En **configurar inicio de sesión único en SmarterU** página para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, el archivo de datos local como **c:\\SmarterUMetaData.cer**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurar el inicio de sesión único")

4.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa SmarterU como administrador.

5.  En la barra de herramientas en la parte superior, haga clic en **Configuración de la cuenta**.

    ![Configuración de la cuenta] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Configuración de la cuenta")

6.  En la página de configuración de cuenta, realice los pasos siguientes:

    ![Autorización externo] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorización externo")

    1.  Seleccione **Habilitar autorización externo**.
    2.  En la sección **Control de inicio de sesión de patrón** , seleccione la ficha **SmarterU** .
    3.  En la sección de **Inicio de sesión de usuario predeterminada** , seleccione la ficha **SmarterU** .
    4.  Seleccione **Habilitar Okta**.
    5.  Copiar el contenido del archivo de metadatos descargados y péguelo en el cuadro de texto de **Metadatos de Okta** .
    6.  Haga clic en **Guardar**.

7.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en SmarterU, debe aprovisionar en SmarterU.  
En el caso de SmarterU, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **SmarterU** .

2.  Vaya a **los usuarios**.

3.  En la sección de usuario, siga estos pasos:

    ![Nuevo usuario] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nuevo usuario")

    1.  Haga clic en **+ usuario**.
    2.  Escriba los valores de atributo relacionado de la cuenta de usuario de Azure AD en los cuadros de texto siguientes: **correo electrónico principal**, **ID de empleado**, **contraseña**, **Compruebe la contraseña**, **nombre**, **apellido**.
    3.  Haga clic en **activo**.
    4.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros SmarterU usuario cuenta herramientas de creación o API proporcionan por SmarterU a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Para asignar a los usuarios a SmarterU, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **SmarterU **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).