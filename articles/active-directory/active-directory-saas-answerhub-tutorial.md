<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con AnswerHub | Microsoft Azure" 
    description="Aprenda a usar AnswerHub con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integración de Azure Active Directory con AnswerHub

El objetivo de este tutorial es mostrar la integración de Azure y [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a AnswerHub será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de AnswerHub (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para AnswerHub
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Escenario")

##<a name="enabling-the-application-integration-for-answerhub"></a>Habilitar la integración de aplicación para AnswerHub

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para AnswerHub.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Para habilitar la integración de aplicación para AnswerHub, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **AnswerHub**.

    ![Galería de aplicación] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **AnswerHub**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a AnswerHub con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **AnswerHub** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión AnswerHub** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **AnswerHub inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*https://company.answerhub.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en AnswerHub** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa AnswerHub como administrador.
    >[AZURE.NOTE] Si necesita ayuda para configurar AnswerHub, póngase en contacto con el [equipo de soporte técnico de AnswerHub](mailto:success@answerhub.com. ).








6.  Vaya a **administración**.

7.  Haga clic en la pestaña **usuarios y grupos** .

8.  En el panel de navegación en el lado izquierdo, en la sección **Configuración Social** , haga clic en **Configuración de SAML**.

9.  Haga clic en **Configuración de IDP** .

10. En la pestaña **Configuración IDP** , realice los pasos siguientes:

    ![Configuración SAML] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "Configuración SAML")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en AnswerHub** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de IDP** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en AnswerHub** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de IDP** .
    3.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en AnswerHub** , copie el valor de **Nombre de formato de identificador** y péguelo en el cuadro de texto de **Formato de identificador de nombre de IDP** .
    4.  Haga clic en **certificados y claves**.

11. En la pestaña de claves y certificados, siga estos pasos:

    ![Claves y certificados] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Claves y certificados")

    1.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto **IDP clave pública (x 509 formato)** .
    3.  Haga clic en **Guardar**.

12. En la pestaña **Config IDP** , haga clic en **Guardar**.

13. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurar inicio de sesión único")

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en AnswerHub, debe aprovisionar en AnswerHub.  
En el caso de AnswerHub, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **AnswerHub** .

2.  Vaya a **administración**.

3.  Haga clic en la pestaña **usuarios y grupos** .

4.  En el panel de navegación en el lado izquierdo, en la sección **Administrar usuarios** , haga clic en **crear o importar usuarios**.

    ![Usuarios y grupos] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Usuarios y grupos")

5.  Escriba la **dirección de correo electrónico**, el **nombre de usuario** y la **contraseña** de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados y, a continuación, haga clic en **Guardar**.

>[AZURE.NOTE] Puede usar cualquier otros AnswerHub usuario cuenta herramientas de creación o API proporcionan por AnswerHub a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Para asignar a los usuarios a AnswerHub, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **AnswerHub **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
