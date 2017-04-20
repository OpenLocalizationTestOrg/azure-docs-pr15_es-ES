<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Felicidades | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Felicidades con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integración de Azure Active Directory con puntos
  
El objetivo de este tutorial es mostrar la integración de Azure y Felicidades.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Felicidades
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Felicidades podrán al inicio de sesión único en la aplicación en el sitio de su empresa de Felicidades (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de la aplicación para Felicidades
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Escenario")
##<a name="enabling-the-application-integration-for-kudos"></a>Habilitar la integración de la aplicación para Felicidades
  
El objetivo de esta sección es describen cómo habilitar la integración de la aplicación para Felicidades.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>Para habilitar la integración de la aplicación para Felicidades, realice los pasos siguientes:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **¡Enhorabuena**.

    ![Galería de aplicación] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Felicidades**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![¡Enhorabuena] (./media/active-directory-saas-kudos-tutorial/IC787801.png "¡Enhorabuena")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Felicidades con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Felicidades** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurar inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Felicidades** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto de **Puntos de inicio de sesión en la URL** , escriba la dirección URL con el siguiente patrón "*https://company.kudosnow.com*" y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Felicidades** , haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Felicidades como administrador.

6.  En el menú de la parte superior, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Configuración")

7.  Haga clic en **integraciones \> SSO**.

8.  En la sección **SSO** , realice los pasos siguientes:

    ![SSO] (./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Felicidades** , copie el valor de la **URL de servicio de inicio de sesión único** y, a continuación, péguelo en el cuadro de texto **dirección URL de inicio de sesión** .
    2.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP]
        Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **certificado X.509**
    4.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Felicidades** , copie el valor de **Dirección URL del servicio de Sign-Out único** y péguelo en el cuadro de texto de **Cierre de sesión a la dirección URL** .
    5.  En el cuadro de texto de **La dirección URL de Felicidades** , escriba el nombre de su compañía.
    6.  Haga clic en **Guardar**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Felicidades, debe aprovisionar en puntos.  
En el caso de Felicidades, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Felicidades** .

2.  En el menú de la parte superior, haga clic en **configuración**.

    ![Configuración] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Configuración")

3.  Haga clic en **Administrador de usuarios**.

4.  Haga clic en la ficha **usuarios** y, a continuación, haga clic en **Agregar un usuario**.

    ![Administrador de usuarios] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Administrador de usuarios")

5.  En la sección **Agregar un usuario** , siga estos pasos:

    ![Agregar un usuario] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Agregar un usuario")

    1.  Escriba el **nombre**, **Apellidos**, **correo electrónico** y otros detalles de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Crear usuario**.

>[AZURE.NOTE]Puede usar cualquier otros Felicidades usuario cuenta herramientas de creación o API proporcionan por Felicidades a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Para asignar a los usuarios a Felicidades, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Felicidades **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).