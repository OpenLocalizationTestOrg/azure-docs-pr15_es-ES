<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con TeamSeer | Microsoft Azure" 
    description="Aprenda a usar TeamSeer con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: Integración de Azure Active Directory con TeamSeer
  
El objetivo de este tutorial es mostrar la integración de Azure y TeamSeer.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino TeamSeer
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TeamSeer será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de TeamSeer (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para TeamSeer
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-teamseer-tutorial/IC789618.png "Escenario")

##<a name="enabling-the-application-integration-for-teamseer"></a>Habilitar la integración de aplicación para TeamSeer
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para TeamSeer.

###<a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Para habilitar la integración de aplicación para TeamSeer, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-teamseer-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-teamseer-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-teamseer-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **TeamSeer**.

    ![Galería de aplicación] (./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **TeamSeer**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![TeamSeer] (./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a TeamSeer con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **TeamSeer** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión TeamSeer** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **TeamSeer inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*http://www.teamseer.com/companyid*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en TeamSeer** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa TeamSeer como administrador.

6.  Vaya a **administración de recursos humanos**.

    ![Administración de recursos humanos] (./media/active-directory-saas-teamseer-tutorial/IC789634.png "Administración de recursos humanos")

7.  Haga clic en **configuración**.

    ![Programa de instalación] (./media/active-directory-saas-teamseer-tutorial/IC789635.png "Programa de instalación")

8.  Haga clic en **configurar los detalles del proveedor SAML**.

    ![Configuración de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789636.png "Configuración de SAML")

9.  En la sección de detalles del proveedor SAML, realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789637.png "Configuración de SAML")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en TeamSeer** , copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto de la **dirección URL** .
    2.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto **IdP público certificado** .

10. Para completar la configuración del proveedor SAML, realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789638.png "Configuración de SAML")

    1.  En las **Direcciones de correo electrónico de prueba**, escriba la dirección de correo electrónico del usuario de prueba.
    2.  En el cuadro de texto del **emisor** , escriba la dirección URL de emisor del proveedor de servicios.
    3.  Haga clic en **Guardar**.

11. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurar el inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en TeamSeer, debe aprovisionar en ShiftPlanning.  
En el caso de TeamSeer, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **TeamSeer** .

2.  Realice los pasos siguientes:

    ![Administración de recursos humanos] (./media/active-directory-saas-teamseer-tutorial/IC789640.png "Administración de recursos humanos")

    1.  Vaya a **administración de recursos humanos \> usuarios**.
    2.  Haga clic en **ejecutar al Asistente para nuevo usuario**.

3.  En la sección de **Detalles del usuario** , siga estos pasos:

    ![Detalles del usuario] (./media/active-directory-saas-teamseer-tutorial/IC789641.png "Detalles del usuario")

    1.  Escriba el **nombre**, **apellido**, **nombre de usuario (dirección de correo electrónico)** de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **siguiente**.

4.  Siga las instrucciones en pantalla para agregar un nuevo usuario y haga clic en **Finalizar**.

>[AZURE.NOTE] Puede usar cualquier otros TeamSeer usuario cuenta herramientas de creación o API proporcionan por TeamSeer proporcionando cuentas de usuario de Azure AD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Para asignar a los usuarios a TeamSeer, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **TeamSeer **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-teamseer-tutorial/IC789642.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-teamseer-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).