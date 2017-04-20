<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con el ciclo de vida de control de código fuente | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar el ciclo de vida del control de código fuente con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integración de Azure Active Directory con el ciclo de vida de control de código fuente
  
El objetivo de este tutorial es mostrar la integración de Azure y ciclo de vida del control de código fuente.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único en el ciclo de vida de control de código fuente habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado el ciclo de vida de control de código fuente será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de ciclo de vida del control de código fuente (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación de ciclo de vida del control de código fuente
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Escenario")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Habilitar la integración de la aplicación de ciclo de vida del control de código fuente
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación de ciclo de vida del control de código fuente.

###<a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>Para habilitar la integración de la aplicación de ciclo de vida del control de código fuente, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **El ciclo de vida del control de código fuente**.

    ![Galería de aplicación] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **El ciclo de vida del control de código fuente**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Ciclo de vida del control de código fuente] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "Ciclo de vida del control de código fuente")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al ciclo de vida del control de código fuente con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Ciclo de vida del control de código fuente** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión el ciclo de vida del control de código fuente** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación de ciclo de vida del control de código fuente mediante el siguiente patrón "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en el ciclo de vida del control de código fuente** , haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurar el inicio de sesión único")

5.  Reenviar ese archivo de metadatos al equipo de soporte de ciclo de vida del control de código fuente.

    >[AZURE.NOTE]Inicio de sesión único debe estar habilitado en el equipo de soporte técnico del ciclo de vida del control de código fuente.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en el ciclo de vida del control de código fuente, debe aprovisionar en el ciclo de vida del control de código fuente.
  
No hay ningún elemento de acción para configurar el aprovisionamiento de ciclo de vida de control de código fuente de usuario.  
Cuando un usuario asignado intenta iniciar sesión en el ciclo de vida del control de código fuente, una cuenta de ciclo de vida del control de código fuente se crea automáticamente si es necesario.

>[AZURE.NOTE]Puede usar cualquier otros ciclo de vida del control de código fuente usuario cuenta herramientas de creación o API proporcionan por el ciclo de vida del control de código fuente a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>Para asignar a los usuarios a ciclo de vida del control de código fuente, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Ciclo de vida del control de código fuente **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).