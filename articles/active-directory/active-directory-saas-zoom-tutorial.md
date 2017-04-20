<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory zoom | Microsoft Azure" 
    description="Obtenga información sobre cómo usar el Zoom con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y más!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integración de Azure Active Directory zoom
  
El objetivo de este tutorial es mostrar la integración de Azure y Zoom.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino de Zoom
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Zoom podrán solo inicio de sesión en la aplicación en el sitio de su empresa de Zoom (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de la aplicación de Zoom
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Escenario")

##<a name="enabling-the-application-integration-for-zoom"></a>Habilitar la integración de la aplicación de Zoom
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación de Zoom.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Para habilitar la integración de la aplicación de Zoom, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba el **Zoom**.

    ![Galería de aplicación] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Zoom**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Zoom] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar zoom con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Zoom** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zoom-tutorial/IC784696.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión Zoom** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zoom-tutorial/IC784697.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Zoom de inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*http://company.zoom.us*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Zoom** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zoom-tutorial/IC784699.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa de Zoom como administrador.

6.  Haga clic en la ficha **Inicio de sesión único** .

    ![Inicio de sesión único] (./media/active-directory-saas-zoom-tutorial/IC784700.png "Inicio de sesión único")

7.  Haga clic en la pestaña de **Control de seguridad** y, a continuación, vaya a la configuración de **Inicio de sesión único** .

8.  En la sección Inicio de sesión único, realice los pasos siguientes:

    ![Inicio de sesión único] (./media/active-directory-saas-zoom-tutorial/IC784701.png "Inicio de sesión único")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Zoom** , copie el valor de **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto **dirección URL de la página de inicio de sesión** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Zoom** , copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto **dirección URL de la página de cierre de sesión** .
    3.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **certificado del proveedor de identidades**
    5.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Zoom** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    6.  Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-zoom-tutorial/IC784702.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Zoom, debe aprovisionar en Zoom.  
En el caso de Zoom, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de empresa **Zoom** .

2.  Haga clic en la pestaña **Administración de cuenta** y, a continuación, haga clic en **Administración de usuarios**.

3.  En la sección Administración de usuarios, haga clic en **Agregar usuarios**.

    ![Administración de usuarios] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Administración de usuarios")

4.  En la página **Agregar usuarios** , realice los pasos siguientes:

    ![Agregar usuarios] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Agregar usuarios")

    1.  Como **Tipo de usuario**, seleccione **básica**.
    2.  En el cuadro de texto de **mensajes de correo electrónico** , escriba la dirección de correo electrónico de una cuenta AAD válida que desee aprovisionar.
    3.  Haga clic en **Agregar**.

>[AZURE.NOTE] Puede usar cualquier otros usuario cuenta creación herramientas de Zoom o API proporcionan por Zoom aprovisionar AAD cuentas de usuario.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Para asignar a los usuarios a Zoom, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Zoom **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).