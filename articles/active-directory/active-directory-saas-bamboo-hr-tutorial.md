<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con bambú HR | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar bambú HR con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Tutorial: Integración de Azure Active Directory con bambú recursos humanos

El objetivo de este tutorial es mostrar la integración de Azure y BambooHR.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de BambooHR activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a BambooHR será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de BambooHR (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para BambooHR
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Escenario")
##<a name="enabling-the-application-integration-for-bamboohr"></a>Habilitar la integración de aplicación para BambooHR

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para BambooHR.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Para habilitar la integración de aplicación para BambooHR, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **BambooHR**.

    ![Galería de aplicación] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **BambooHR**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a BambooHR con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **BambooHR** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Escenario] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Escenario")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión BambooHR** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **BambooHR inicio de sesión en la URL** , escriba la dirección URL usada por los usuarios para iniciar sesión su aplicación BambooHR (por ejemplo: https://company.bamboohr.com) y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en BambooHR** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa BambooHR como administrador.

6.  En la página principal, siga estos pasos:

    ![Inicio de sesión único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Inicio de sesión único")

    1.  Haga clic en **aplicaciones**.
    2.  En el menú de aplicaciones de la izquierda, haga clic en **Inicio de sesión único**.
    3.  Haga clic en **Inicio de sesión único de SAML**.

7.  En la sección **Inicio de sesión único de SAML** , realice los pasos siguientes:

    ![SAML inicio de sesión único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML inicio de sesión único")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en BambooHR** , copie el valor de la **URL de servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión SSO** .
    2.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado X.509**
    4.  Haga clic en **Guardar**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en BambooHR, debe aprovisionar en BambooHR.  
En el caso de BambooHR, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador el sitio **BambooHR** .

2.  En la barra de herramientas en la parte superior, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuración")

3.  Haga clic en **información general**.

4.  En el panel de navegación izquierdo, vaya a **seguridad \> usuarios**.

5.  Escriba la dirección de correo electrónico, la contraseña y el nombre de usuario de una cuenta AAD válida que desee aprovisionar en los cuadros de texto relacionados.

6.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros BambooHR usuario cuenta herramientas de creación o API proporcionan por BambooHR a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Para asignar a los usuarios a BambooHR, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **BambooHR **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
