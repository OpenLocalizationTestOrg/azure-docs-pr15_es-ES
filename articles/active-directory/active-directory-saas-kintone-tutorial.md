<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Kintone | Microsoft Azure" 
    description="Aprenda a usar Kintone con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integración de Azure Active Directory con Kintone
  
El objetivo de este tutorial es mostrar la integración de Azure y Kintone.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Kintone activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Kintone será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Kintone (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Kintone
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Escenario")
##<a name="enabling-the-application-integration-for-kintone"></a>Habilitar la integración de aplicación para Kintone
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Kintone.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Kintone, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Kintone**.

    ![Galería de aplicación] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Kintone**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Kintone con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Kintone** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Kintone** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Kintone inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*https://company.kintone.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Kintone** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa **Kintone** como administrador.

6.  Haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Configuración")

7.  Haga clic en **usuarios y la administración del sistema**.

    ![Usuarios y la administración del sistema] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Usuarios y la administración del sistema")

8.  En **administración del sistema \> seguridad** haga clic en **Login**.

    ![Inicio de sesión] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Inicio de sesión")

9.  Haga clic en **Habilitar SAML autenticación**.

    ![Autenticación de SAML] (./media/active-directory-saas-kintone-tutorial/IC785882.png "Autenticación de SAML")

10. En la sección autenticación de SAML, realice los pasos siguientes:

    ![Autenticación de SAML] (./media/active-directory-saas-kintone-tutorial/IC785883.png "Autenticación de SAML")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Kintone** , copie el valor de **Dirección URL de inicio de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de inicio de sesión** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Kintone** , copie el valor de **Dirección URL de cierre de sesión remota** y, a continuación, péguelo en el cuadro de texto **Dirección URL de cierre de sesión** .
    3.  Haga clic en **Examinar** para cargar el certificado descargado.
    4.  Haga clic en **Guardar**.

11. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurar el inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Kintone, debe aprovisionar en Kintone.  
En el caso de Kintone, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Kintone** .

2.  Haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Configuración")

3.  Haga clic en **usuarios y la administración del sistema**.

    ![Administración del sistema y usuario] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Administración del sistema y usuario")

4.  En **Administración de usuarios**, haga clic en **departamentos y usuarios**.

    ![Departamento y los usuarios] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Departamento y los usuarios")

5.  Haga clic en **nuevo usuario**.

    ![Nuevos usuarios] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Nuevos usuarios")

6.  En la sección **Nuevo usuario** , siga estos pasos:

    ![Nuevos usuarios] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Nuevos usuarios")

    1.  Escriba un **Nombre para mostrar**, **Nombre de inicio de sesión**, **Nueva contraseña**, **Confirmar contraseña**, **Dirección de correo electrónico** y otros detalles de una cuenta AAD válida que desee aprovisionar en el texboxes relacionados.
    2.  Haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros Kintone usuario cuenta herramientas de creación o API proporcionan por Kintone a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Para asignar a los usuarios a Kintone, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Kintone **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).