<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Jitbit Helpdesk | Microsoft Azure" 
    description="¡Obtenga información sobre cómo utilizar Jitbit Helpdesk con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integración de Azure Active Directory con Jitbit Helpdesk
  
El objetivo de este tutorial es mostrar la integración de Azure y Jitbit Helpdesk.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inquilino Jitbit Helpdesk
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Jitbit Helpdesk podrán al inicio de sesión único en la aplicación en el sitio de su empresa de Jitbit Helpdesk (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1.  Habilitar la integración de aplicación para Jitbit Helpdesk
2.  Configurar el inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Escenario")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Habilitar la integración de aplicación para Jitbit Helpdesk
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Jitbit Helpdesk.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Jitbit Helpdesk, realice los pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Agregar una aplicación de muestra")

6.  En el **cuadro de búsqueda**, escriba **El departamento de soporte técnico Jitbit**.

    ![Galería de aplicación] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **El departamento de soporte técnico Jitbit**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Configurar el inicio de sesión único
  
El objetivo de esta sección es describen cómo habilitar los usuarios autenticar Jitbit Helpdesk con su cuenta de Azure AD mediante federación basada en el protocolo SAML. .  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Para poder configurar el inicio de sesión único en su inquilino Jitbit Helpdesk, debe ponerse en contacto con el soporte técnico de Jitbit Helpdesk para obtener esta característica está habilitada en primer lugar.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación de **Servicio de asistencia de Jitbit** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurar inicio de sesión único")

2.  En la página **¿cómo le gustaría usuarios a iniciar sesión el departamento de soporte técnico de Jitbit** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurar inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Jitbit departamento de soporte técnico de inicio de sesión en dirección URL** , escriba la dirección URL con el siguiente patrón "https://*\<nombre del inquilino\>. Jitbit.com*"y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único al departamento de soporte técnico de Jitbit** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente como **c:\\Jitbit Helpdesk.cer**.

    ![Configurar inicio de sesión único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurar inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Jitbit Helpdesk como administrador.

6.  En la barra de herramientas en la parte superior, haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administración")

7.  Haga clic en **configuración General**.

    ![Los usuarios, compañías y permisos] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Los usuarios, compañías y permisos")

8.  En la sección de configuración de la **configuración de autenticación** , realice los pasos siguientes:

    ![Configuración de autenticación] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Configuración de autenticación")

    1.  Seleccione **Habilitar SAML 2.0 solo inicio de sesión** con inicio de sesión único (SSO) **OneLogin**de inicio de sesión.
    2.  En el portal de clásico Azure, en la página de cuadro de diálogo **configurar inicio de sesión único al departamento de soporte técnico de Jitbit** , copie el valor de **extremo iniciados por el proveedor de servicio (SP)** y, a continuación, péguelo en el cuadro de texto de la **Dirección URL del extremo** .
    3.  Crear un archivo **codificado base 64** de certificado descargado.
        
        >[AZURE.TIP]Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra el certificado codificado de base 64, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto de **Certificado X.509**
    5.  Haga clic en **Guardar cambios**.

9.  En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar inicio de sesión único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurar inicio de sesión único")
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD iniciar sesión en Jitbit Helpdesk, debe aprovisionar en Jitbit Helpdesk.  
En el caso de Jitbit Helpdesk, aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para crear cuentas de usuario, siga estos pasos:

1.  Inicie sesión en su inquilino **Jitbit departamento de soporte técnico** .

2.  En el menú de la parte superior, haga clic en **administración**.

    ![Administración] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administración")

3.  Haga clic en **usuarios, compañías y permisos**.

    ![Los usuarios, compañías y permisos] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Los usuarios, compañías y permisos")

4.  Haga clic en **Agregar usuario**.

    ![Agregar usuario] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Agregar usuario")

5.  En la sección crear, escriba los datos de la cuenta de Azure AD desee aprovisionar en los cuadros de texto siguientes: **nombre de usuario**, **correo electrónico**, **nombre**, **Apellido**

    ![Crear] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Crear")

6.  Haga clic en **crear**.

>[AZURE.NOTE] Puede usar cualquier otros Jitbit Helpdesk usuario cuenta herramientas de creación o API proporcionan por Jitbit Helpdesk a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Para asignar a los usuarios a Jitbit Helpdesk, realice los pasos siguientes:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación de **Servicio de asistencia de Jitbit **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sí")
  
Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).