<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Veracode | Microsoft Azure" 
    description="Aprenda a usar Veracode con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integración de Azure Active Directory con Veracode
  
El objetivo de este tutorial es mostrar la integración de Azure y Veracode. El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Veracode activado suscripción
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Veracode será capaz de inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Veracode
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Escenario")

##<a name="enabling-the-application-integration-for-veracode"></a>Habilitar la integración de aplicación para Veracode
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Veracode.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Veracode, siga estos pasos:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **Veracode**.

    ![Galería de aplicación] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Veracode**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Veracode con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La aplicación de Veracode espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Atributos] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Veracode** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Veracode** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurar el inicio de sesión único")

3.  En la página **Configurar opciones de aplicación** , haga clic en **siguiente**.

    ![Establecer la configuración de la aplicación] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Establecer la configuración de la aplicación")

4.  En la página **configurar inicio de sesión único en Veracode** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Veracode como administrador.

6.  En el menú de la parte superior, haga clic en **configuración**y, a continuación, haga clic en **Administrador**.

    ![Administración] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Administración")

7.  Haga clic en la pestaña **SAML** .

8.  En la sección **Configuración de la organización SAML** , realice los pasos siguientes:

    ![Administración] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Administración")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Veracode** , copie el valor de **Dirección URL del emisor** y péguelo en el cuadro de texto del **emisor**
    2.  Para cargar el certificado descargado, haga clic en **Elegir archivo**.
    3.  Seleccione **Habilitar registro automático**.

9.  En la sección **Configuración a automática del registro** , realice los pasos siguientes y, a continuación, haga clic en **Guardar**:

    ![Administración] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Administración")

    1.  Como **La activación de usuario nuevo**, seleccione **No se requiere la activación**.
    2.  Como **Las actualizaciones de datos de usuario**, seleccione **Preferencias Veracode User Data**.
    3.  Para **SAML atributo detalles**, seleccione lo siguiente:
        -   **Roles de usuario**
        -   **Administrador de directivas**
        -   **Revisor**
        -   **Cliente potencial de seguridad**
        -   **Ejecutivo**
        -   **Remitente**
        -   **Creador**
        -   **Todos los tipos de detección**
        -   **Suscripciones de grupo**
        -   **Grupo predeterminado**

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurar el inicio de sesión único")

11. En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Atributos")

12. Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

    ![Atributos] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

  	| Nombre del atributo | Valor del atributo |
  	|:---------------|:----------------|
  	| nombre      | User.givenName  |
  	| apellido       | User.Surname    |
  	| Correo electrónico          | User.Mail       |

    1.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.
    
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.

    3.  En el cuadro de texto del **Valor del atributo** , seleccione el valor del atributo que se muestra de la fila.

    4.  Haga clic en **Finalizar**.

13. Haga clic en **Aplicar cambios**.

##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Veracode, debe aprovisionar en Veracode.  
En el caso de Veracode, aprovisionamiento es una tarea automatizada.  
No hay ningún elemento de acción para usted...
  
Los usuarios se crean automáticamente durante el primer única sesión intento si es necesario.

>[AZURE.NOTE] Puede usar cualquier otros Veracode usuario cuenta herramientas de creación o API proporcionan por Veracode a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Para asignar a los usuarios a Veracode, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Veracode **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).