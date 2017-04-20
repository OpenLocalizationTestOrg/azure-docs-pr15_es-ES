<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con la plataforma de nube de SAP HANA | Microsoft Azure" 
    description="¡Descubra cómo usar la plataforma de nube de SAP HANA con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Tutorial: Integración de Azure Active Directory con la plataforma de nube de HANA de SAP
  
El objetivo de este tutorial es mostrar la integración de Azure y plataforma de nube de HANA de SAP.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Una cuenta de plataforma de nube de HANA de SAP
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a la plataforma de nube de SAP HANA podrán al inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Debe implementar su propia aplicación o suscribirse a una aplicación en su cuenta de plataforma de nube de HANA SAP para probar el inicio de sesión único en. En este tutorial, se implementa una aplicación de la cuenta.
  
El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación de plataforma de nube de HANA de SAP
2.  Configuración de inicio de sesión único
3.  Asignar un rol a un usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Escenario")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Habilitar la integración de aplicación de plataforma de nube de HANA de SAP
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación de plataforma de nube de HANA de SAP.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Para habilitar la integración de aplicación de plataforma de nube de HANA de SAP, siga estos pasos:

1.  En el Portal de administración de Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba la **Plataforma de nube de HANA de SAP**.

    ![Galería de aplicación] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Galería de aplicación")

7.  En el panel de resultados, seleccione la **Plataforma de nube de HANA de SAP**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Hana SAP] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "Hana SAP")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a la plataforma de nube de SAP HANA con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para cargar un certificado codificado de base 64 en el inquilino de plataforma de nube de HANA de SAP.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Plataforma de nube de HANA de SAP** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión plataforma de nube de HANA de SAP** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurar el inicio de sesión único")

3.  En una ventana de explorador web diferente, inicie sesión la cabina de plataforma de nube de SAP HANA en https://account. \<host horizontal\>.ondemand.com/cockpit (por ejemplo: *https://account.hanatrial.ondemand.com/cockpit*).

4.  Haga clic en la ficha **confianza** .

    ![Confiar] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Confiar")

5.  En la sección Administración de confianza, realice los pasos siguientes:

    ![Obtener metadatos] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Obtener metadatos")

    1.  Haga clic en la ficha de **Proveedor de servicios Local** .
    2.  Para descargar el archivo de metadatos de plataforma de nube de HANA de SAP, haga clic en **Obtener metadatos**.

6.  En el portal de clásica Azure Active, en la página **Configurar la dirección URL de aplicación** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurar la dirección URL de la aplicación")

    1.  En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión en la aplicación de la **Plataforma de nube de HANA de SAP** . Esta es la URL específica de la cuenta de un recurso en la aplicación de la plataforma de nube de SAP HANA protegido. La dirección URL se basa en el modelo siguiente: https:// *\<applicationName\>\<nombre de la cuenta\>.\< host horizontal\>.ondemand.com/\<ruta\_a\_protegido\_recursos\> * (por ejemplo: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Esta es la dirección URL en la aplicación de la plataforma de nube de HANA de SAP que requiere que el usuario autenticar.

    2.  Abrir el archivo descargado de metadatos de plataforma de nube de HANA de SAP y, a continuación, busque la etiqueta **ns3:AssertionConsumerService** .
    3.  Copie el valor del atributo de **ubicación** y, a continuación, péguelo en el cuadro de texto **Dirección URL de respuesta de SAP HANA nube plataforma** .

7.  En la página **configurar inicio de sesión único en la plataforma de nube de HANA de SAP** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurar el inicio de sesión único")

8.  En la cabina de plataforma de nube de HANA SAP, en la sección **Proveedor de servicios Local** , realice los pasos siguientes:

    ![Administración de la confianza] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Administración de la confianza")

    1.  Haga clic en **Editar**.
    2.  Como **Tipo de configuración**, seleccione **personalizado**.
    3.  Como **Nombre del proveedor Local**, deje el valor predeterminado.
    4.  Para generar un par de clave de **Certificado de firma** y una **Clave de firma** , haga clic en **Generar clave de par**.
    5.  Como **Propagación Principal**, seleccione **deshabilitado**.
    6.  Como **Fuerza la autenticación**, seleccione **deshabilitado**.
    7.  Haga clic en **Guardar**.

9.  Haga clic en la pestaña **Proveedor de identidades de confianza** y, a continuación, haga clic en **Agregar proveedor de identidades de confianza**.

    ![Administración de la confianza] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Administración de la confianza")

    >[AZURE.NOTE]Para administrar la lista de proveedores de identidades de confianza, debe ha elegido el tipo de configuración personalizada en la sección del proveedor de servicios Local. Para tipo de configuración de forma predeterminada, tiene una confianza implícita y no se puede modificar el servicio de Id. de SAP. Para ninguno, no tiene ninguna configuración de confianza.

10. Haga clic en la pestaña **General** y, a continuación, haga clic en **Examinar** para cargar el archivo de metadatos descargado.

    ![Administración de la confianza] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Administración de la confianza")

    >[AZURE.NOTE] Después de cargar el archivo de metadatos, los valores de la **dirección URL de inicio de sesión único**, la **Dirección URL de cierre de sesión único** y **Certificado de firma** se rellenan automáticamente.

11. Haga clic en la ficha **atributos** .

12. En la ficha **atributos** , siga estos pasos:

    ![Atributos] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Atributos")

    1.  Haciendo clic en **El atributo de Add Assertion-Based**, agregue los siguientes atributos de aserción:

        |Atributo de aserción| Atributo de capital|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenName|   nombre|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Surname|        apellido|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/EmailAddress|Correo electrónico|

    >[AZURE.NOTE]La configuración de los atributos depende de cómo las aplicaciones en HCP se desarrollan, es decir, qué atributos que esperan en la respuesta SAML y en qué nombre (atributo capital) tienen acceso a este atributo en el código.
    >  
    >una.  El **Atributo predeterminado** en la captura de pantalla es solo con fines ilustrativos. No es necesario para que el escenario de funcionar.  
    >
    >b.  Los nombres y valores para el **Atributo a Principal** se muestra en la captura de pantalla dependen de cómo se desarrolla la aplicación. Es posible que la aplicación requiere asignaciones diferentes.

13. En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único en la plataforma de nube de HANA de SAP** , seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurar el inicio de sesión único")
  
Como paso opcional, puede configurar grupos de aserción para su proveedor de identidades de Azure Active Directory

>[AZURE.NOTE]Uso de grupos en la plataforma de nube de SAP HANA le permite asignar dinámicamente uno o más usuarios a una o varias funciones en las aplicaciones de la plataforma de nube de HANA de SAP, determinadas por los valores de los atributos de la aserción SAML 2.0. Por ejemplo, si la aserción contiene el atributo "*contrato = temporal*", es conveniente afectados todos los usuarios que deben agregarse al grupo "*temporal*". El grupo "*temporal*" puede contener una o varias funciones de una o más aplicaciones implementadas en su cuenta de plataforma de nube de HANA de SAP.
>  
>Usar grupos de aserción si desea asignar masa muchos usuarios a una o varias funciones de las aplicaciones en su cuenta de plataforma de nube de HANA de SAP. Si desea asignar a un único o pequeño número de usuarios a (a) funciones específicas, se recomienda asignarlos directamente en la pestaña "**autorizaciones**" de la cabina de plataforma de nube de HANA de SAP.

##<a name="assigning-a-role-to-a-user"></a>Asignar un rol a un usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en la plataforma de nube de HANA de SAP, debe asignar roles de la plataforma de nube de HANA de SAP en ellos.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Para asignar un rol a un usuario, siga estos pasos:

1.  Inicie sesión en la cabina de **Plataforma de nube de HANA de SAP** .

2.  Realice los pasos siguientes:

    ![Autorizaciones] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorizaciones")

    1.  Haga clic en **autorización**.
    2.  Haga clic en la ficha **usuarios** .
    3.  En el cuadro de texto de **usuario** , escriba la dirección de correo electrónico del usuario.
    4.  Haga clic en **asignar** para asignar al usuario a un rol.
    5.  Haga clic en **Guardar**.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Para asignar a los usuarios a la plataforma de nube de HANA de SAP, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Plataforma de nube de HANA de SAP** , haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).