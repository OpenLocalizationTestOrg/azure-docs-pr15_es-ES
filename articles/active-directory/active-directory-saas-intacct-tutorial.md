<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Intacct | Microsoft Azure" 
    description="Aprenda a usar Intacct con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: Integración de Azure Active Directory con Intacct
  
El objetivo de este tutorial es mostrar la integración de Azure y Intacct.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Intacct
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Intacct será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Intacct (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Intacct
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-intacct-tutorial/IC790030.png "Escenario")
##<a name="enabling-the-application-integration-for-intacct"></a>Habilitar la integración de aplicación para Intacct
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Intacct.

###<a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Intacct, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-intacct-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-intacct-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-intacct-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Intacct**.

    ![Galería de aplicación] (./media/active-directory-saas-intacct-tutorial/IC790031.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Intacct**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Intacct] (./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Intacct con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Intacct** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Intacct** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Intacct inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*https://Intacct.com/company*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Intacct** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Intacct como administrador.

6.  Haga clic en la pestaña **empresa** y, a continuación, haga clic en **Información de la empresa**.

    ![Compañía] (./media/active-directory-saas-intacct-tutorial/IC790037.png "Compañía")

7.  Haga clic en la pestaña **seguridad** y, a continuación, haga clic en **Editar**.

    ![Seguridad] (./media/active-directory-saas-intacct-tutorial/IC790038.png "Seguridad")

8.  En la sección de **Inicio de sesión único (SSO)** , siga estos pasos:

    ![Inicio de sesión único] (./media/active-directory-saas-intacct-tutorial/IC790039.png "Inicio de sesión único")

    1.  Seleccione **Habilitar inicio de sesión único**.
    2.  Como **tipo de proveedor de identidades**, seleccione **SAML 2.0**.
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Intacct** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto de la **Dirección URL del emisor** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Intacct** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    5.  Crear un archivo **codificado base 64** de certificado descargado.
        
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **certificado**
    7.  Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Intacct, debe aprovisionar en Intacct.  
En el caso de Intacct, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Intacct** .

2.  Haga clic en la pestaña **empresa** y, a continuación, haga clic en **usuarios**.

    ![Usuarios] (./media/active-directory-saas-intacct-tutorial/IC790041.png "Usuarios")

3.  Haga clic en la ficha **Agregar**

    ![Agregar] (./media/active-directory-saas-intacct-tutorial/IC790042.png "Agregar")

4.  En la sección **Información de usuario** , siga estos pasos:

    ![Información de usuario] (./media/active-directory-saas-intacct-tutorial/IC790043.png "Información de usuario")

    1.  Escriba el **Identificador de usuario**, el **apellido**, **nombre**, la **dirección de correo electrónico**, el **título** y el **teléfono** de una cuenta de Azure AD que desee aprovisionar en los cuadros de texto relacionados.
    2.  Seleccione los **privilegios de administrador** de una cuenta de Azure AD que desee aprovisionar.
    3.  Haga clic en **Guardar**.
        
        >[AZURE.NOTE] El titular AAD recibirá un correo electrónico y seguir un vínculo para confirmar su cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Intacct usuario cuenta herramientas de creación o API proporcionan por Intacct a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Para asignar a los usuarios a Intacct, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Intacct **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-intacct-tutorial/IC790044.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-intacct-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).