<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con OfficeSpace Software | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar OfficeSpace Software con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integración de Azure Active Directory con OfficeSpace Software
  
El objetivo de este tutorial es mostrar la integración de Azure y OfficeSpace Software.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un OfficeSpace Software inicio de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a OfficeSpace Software será capaz de inicio de sesión único en la aplicación en el sitio de su compañía de OfficeSpace Software (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicaciones de OfficeSpace Software
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Escenario")
##<a name="enabling-the-application-integration-for-officespace-software"></a>Habilitar la integración de aplicaciones de OfficeSpace Software
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones de OfficeSpace Software.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>Para habilitar la integración de aplicaciones de OfficeSpace Software, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **OfficeSpace Software**.

    ![Galería de aplicación] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **OfficeSpace Software**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Software de OfficeSpace] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "Software de OfficeSpace")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar al OfficeSpace Software con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Configuración de inicio de sesión único para el OfficeSpace Software requiere que recuperar un valor de la huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **OfficeSpace Software** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único = en] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurar el inicio de sesión único = en")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión OfficeSpace Software** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **OfficeSpace Software de inicio de sesión en dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión su aplicación de OfficeSpace Software (por ejemplo: "*https://company.officespacesoftware.com*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en OfficeSpace Software** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa de OfficeSpace Software como administrador.

6.  Vaya a **administración \> conectores**.

    ![Administrador] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administrador")

7.  Haga clic en **autorización de SAML**.

    ![Conectores] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Conectores")

8.  En la sección **Autorización de SAML** , realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuración de SAML")

    1.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en OfficeSpace Software** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto de la **dirección url del proveedor de cierre de sesión** .
    2.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en OfficeSpace Software** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **dirección url de destino de idp de cliente** .
    3.  Copie el valor de la **huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto de la **huella digital de certificado de cliente idp** .  

        >[AZURE.TIP]
        Para obtener más detalles, consulte [cómo recuperar el valor de la huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    4.  Haga clic en **Guardar configuración**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en OfficeSpace Software, debe aprovisionar en OfficeSpace Software. En el caso de OfficeSpace Software, aprovisionamiento es una tarea automatizada.  
No hay ningún elemento de acción para usted.  
Los usuarios se crean automáticamente durante el primer única sesión intento si es necesario.

>[AZURE.NOTE]Puede usar cualquier otros OfficeSpace Software usuario cuenta herramientas de creación o API proporcionan por OfficeSpace Software de cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Para asignar a los usuarios a OfficeSpace Software, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **OfficeSpace Software **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).