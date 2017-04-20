<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Pagerduty | Microsoft Azure" 
    description="Aprenda a usar Pagerduty con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integración de Azure Active Directory con Pagerduty
  
El objetivo de este tutorial es mostrar la integración de Azure y Pagerduty.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Pagerduty
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Pagerduty será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Pagerduty (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Pagerduty
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Escenario")
##<a name="enabling-the-application-integration-for-pagerduty"></a>Habilitar la integración de aplicación para Pagerduty
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Pagerduty.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Pagerduty, siga estos pasos:

1.  En el Portal de administración de Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Pagerduty**.

    ![Galería de aplicación] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Pagerduty**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Pagerduty con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Pagerduty** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Pagerduty** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Pagerduty inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Pagerduty.com*"y, a continuación, haga clic en **siguiente**.

    ![Dirección url de aplicación de configurar] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Dirección url de aplicación de configurar")

4.  En la página **configurar inicio de sesión único en Pagerduty** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Pagerduty como administrador.

6.  En el menú de la parte superior, haga clic en **Configuración de la cuenta**.

    ![Configuración de la cuenta] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Configuración de la cuenta")

7.  Haga clic en **Inicio de sesión único**.

    ![Inicio de sesión único] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Inicio de sesión único")

8.  En la página Habilitar inicio de sesión único (SSO), realice los pasos siguientes:

    ![Habilitar inicio de sesión único] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Habilitar inicio de sesión único")

    1.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado X.509**
    3.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en Pagerduty** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    4.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en Pagerduty** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    5.  Seleccione **activar el inicio de sesión único**.
    6.  Haga clic en **Guardar cambios**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Pagerduty, debe aprovisionar en Pagerduty.  
En el caso de Pagerduty, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Pagerduty** .

2.  En el menú de la parte superior, haga clic en **usuarios**.

3.  Haga clic en **Agregar usuarios**.

    ![Agregar usuarios] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Agregar usuarios")

4.  En el cuadro de diálogo **Invitar a su equipo** , escriba el **nombre y apellido** y la dirección de **correo electrónico** del usuario de Azure AD que desea aprovisionar, haga clic en **Agregar**y, a continuación, haga clic en **Enviar invitaciones**.

    ![Invitar a su equipo] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invitar a su equipo")

    >[AZURE.NOTE] Todos los usuarios agregados recibirá una invitación para crear una cuenta de PagerDuty.

>[AZURE.NOTE] Puede usar cualquier otros Pagerduty usuario cuenta herramientas de creación o API proporcionan por Pagerduty a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Para asignar a los usuarios a Pagerduty, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Pagerduty **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).