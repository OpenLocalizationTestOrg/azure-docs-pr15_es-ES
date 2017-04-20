<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Benefitsolver | Microsoft Azure"
    description="Aprenda a usar Benefitsolver con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Tutorial: Integración de Azure Active Directory con Benefitsolver

El objetivo de este tutorial es mostrar la integración de Azure y Benefitsolver.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Benefitsolver activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Benefitsolver será capaz de inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Benefitsolver
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Escenario")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>Habilitar la integración de aplicación para Benefitsolver

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Benefitsolver.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Benefitsolver, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Benefitsolver**.

    ![Galería de aplicación] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Benefitsolver**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Benefitsolver con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La aplicación de Benefitsolver espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Benefitsolver** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Benefitsolver** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurar el inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Establecer la configuración de la aplicación] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Establecer la configuración de la aplicación")

    1.  En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba **http://azure.benefitsolver.com**.
    2.  En el cuadro de texto **Dirección URL de respuesta** , escriba **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  


    3.  Haga clic en **siguiente**.

4.  En la página **configurar inicio de sesión único en Benefitsolver** , para descargar los metadatos, haga clic en **descargar metadatos**y, a continuación, guarde el archivo de metadatos localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurar el inicio de sesión único")

5.  Enviar el archivo descargado metadatos a su equipo de soporte técnico de Benefitsolver.

    >[AZURE.NOTE] El equipo de soporte de Benefitsolver tiene que ver la configuración de SSO real.
Recibirá una notificación cuando se ha habilitado SSO para la suscripción.

6.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurar el inicio de sesión único")

7.  En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Atributos")

8.  Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    ![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

  	|Nombre del atributo|Valor del atributo|
  	|---|---|
  	|ClientID|Debe obtener este valor desde su equipo de soporte técnico de Benefitsolver.|
  	|ClientKey|Debe obtener este valor desde su equipo de soporte técnico de Benefitsolver.|
  	|LogoutURL|Debe obtener este valor desde su equipo de soporte técnico de Benefitsolver.|
  	|IdEmpleado|Debe obtener este valor desde su equipo de soporte técnico de Benefitsolver.|

    1.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    3.  En el cuadro de texto del **Valor del atributo** , seleccione el valor del atributo que se muestra de la fila.
    4.  Haga clic en **Finalizar**.

9.  Haga clic en **Aplicar cambios**.

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Benefitsolver, debe aprovisionar en Benefitsolver.  
En el caso de Benefitsolver, datos de los empleados se están en la aplicación que se rellenan a través de un archivo del censo de su sistema HRIS (normalmente por la noche).  

>[AZURE.NOTE] Puede usar cualquier otros Benefitsolver usuario cuenta herramientas de creación o API proporcionan por Benefitsolver a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Para asignar a los usuarios a Benefitsolver, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Benefitsolver **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
