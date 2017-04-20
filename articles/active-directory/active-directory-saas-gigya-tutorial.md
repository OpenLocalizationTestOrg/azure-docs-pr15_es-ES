<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Gigya | Microsoft Azure" 
    description="Aprenda a usar Gigya con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: Integración de Azure Active Directory con Gigya
  
El objetivo de este tutorial es mostrar la integración de Azure y Gigya.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Gigya de sesión único habilitado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Gigya será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Gigya (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Gigya
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Configurar el inicio de sesión único] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurar el inicio de sesión único")
##<a name="enabling-the-application-integration-for-gigya"></a>Habilitar la integración de aplicación para Gigya
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Gigya.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Gigya, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Gigya**.

    ![Galería de aplicación] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Gigya**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Gigya con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Gigya** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Gigya** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Gigya inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*http://company.gigya.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Gigya** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Gigya como administrador.

6.  Vaya a **configuración \> inicio de sesión de SAML**y, a continuación, haga clic en el botón **Agregar** .

    ![Inicio de sesión SAML] (./media/active-directory-saas-gigya-tutorial/IC789532.png "Inicio de sesión SAML")

7.  En la sección de **Inicio de sesión de SAML** , realice los pasos siguientes:

    ![Configuración de SAML] (./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuración de SAML")

    1.  En el cuadro de texto **nombre** , escriba un nombre para la configuración.
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Gigya** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Gigya** , copie el valor de la **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto de la **URL de servicio de inicio de sesión único** .
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Gigya** , copie el valor de **Nombre de formato de identificador** y péguelo en el cuadro de texto de **Formato de nombre de ID** .
    5.  Crear un archivo **codificado base 64** de certificado descargado.
        
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado X.509** .
    7.  Haga clic en **Guardar configuración**.

8.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Gigya, debe aprovisionar en Gigya.  
En el caso de Gigya, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Gigya** .

2.  Vaya a **administración \> administrar usuarios**y, a continuación, haga clic en **Invitar a usuarios**.

    ![Administrar usuarios] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Administrar usuarios")

3.  En el cuadro de diálogo Invitar a usuarios, realice los pasos siguientes:

    ![Invitar a usuarios] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Invitar a usuarios")

    1.  En el cuadro de texto de **correo electrónico** , escriba el alias de correo electrónico de una cuenta de Azure Active Directory válida que desee aprovisionar.
    2.  Haga clic en **Invitar a usuario**.
    
        >[AZURE.NOTE] Titular de la cuenta de Azure Active Directory recibirán un correo electrónico que incluye un vínculo para confirmar la cuenta antes de que esté activa.

>[AZURE.NOTE] Puede usar cualquier otros Gigya usuario cuenta herramientas de creación o API proporcionan por Gigya a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Para asignar a los usuarios a Gigya, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Gigya **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).