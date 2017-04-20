<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Panopto | Microsoft Azure" 
    description="Aprenda a usar Panopto con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integración de Azure Active Directory con Panopto
  
El objetivo de este tutorial es mostrar la integración de Azure y Panopto.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Panopto
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Panopto será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Panopto (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Panopto
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Escenario")
##<a name="enabling-the-application-integration-for-panopto"></a>Habilitar la integración de aplicación para Panopto
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Panopto.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Panopto, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Panopto**.

    ![Galería de Appkication] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Galería de Appkication")

7.  En el panel de resultados, seleccione **Panopto**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Panopto con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Panopto** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Panopto** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Panopto inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Panopto.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Panopto** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Panopto como administrador.

6.  En la barra de herramientas de la izquierda, haga clic en **sistema**y, a continuación, haga clic en **Proveedores de identidades**.

    ![Sistema] (./media/active-directory-saas-panopto-tutorial/IC777670.png "Sistema")

7.  Haga clic en **Agregar proveedor**.

    ![Proveedores de identidades] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Proveedores de identidades")

8.  En la sección de proveedor SAML, realice los pasos siguientes:

    ![Configuración de SaaS] (./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuración de SaaS")

    1.  En la lista **Tipo de proveedor** , seleccione **SAML20**
    2.  En el cuadro de texto **Nombre de instancia** , escriba un nombre para la instancia.
    3.  En el cuadro de texto **Descripción** , escriba una descripción.
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Panopto** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    5.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Panopto** , copie el valor de **Dirección URL de SAML SSO** y péguelo en el cuadro de texto **Dirección Url de la página de rebote** .
    6.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    7.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de la **Clave pública**
    8.  Haga clic en **Guardar**.
        ![Guardar] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Guardar")

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
No hay ningún elemento de acción para configurar Panopto de aprovisionamiento de usuarios.  
Cuando un usuario asignado intenta iniciar sesión en Panopto mediante el panel de acceso, Panopto comprueba si existe el usuario.  
Si no hay ninguna cuenta de usuario aún, se crea automáticamente por Panopto.

>[AZURE.NOTE]Puede usar cualquier otros Panopto usuario cuenta herramientas de creación o API proporcionan por Panopto proporcionando cuentas de usuario de Azure AD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Para asignar a los usuarios a Panopto, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Panopto **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).