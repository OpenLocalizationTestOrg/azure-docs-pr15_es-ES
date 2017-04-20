<properties 
    pageTitle="Tutorial: Integración de integración de Azure Active Directory con Druva | Microsoft Azure" 
    description="Aprenda a usar Druva con Azure Active Directory para habilitar el inicio de sesión único, aprovisionamiento automatizado y mucho más." 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Tutorial: Integración de integración de Azure Active Directory con Druva

El objetivo de este tutorial es mostrar la integración de Azure y Druva.  
El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure
-   Un inicio de sesión único de Druva activado suscripción

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Druva será capaz de inicio de sesión único en la aplicación en el sitio de su empresa de Druva (servicio proveedor inicia sesión) o mediante la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

El escenario descrito en este tutorial se compone de los siguientes bloques de creación:

1.  Habilitar la integración de aplicación para Druva
2.  Configuración de inicio de sesión único
3.  Configuración de aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario] (./media/active-directory-saas-druva-tutorial/IC795084.png "Escenario")
##<a name="enabling-the-application-integration-for-druva"></a>Habilitar la integración de aplicación para Druva

El objetivo de esta sección es describen cómo habilitar la integración de aplicación para Druva.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Para habilitar la integración de aplicación para Druva, siga estos pasos:

1.  En el portal de clásico Azure, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-druva-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-druva-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Agregar una aplicación de gallerry")

6.  En el **cuadro de búsqueda**, escriba **Druva**.

    ![Galería de aplicación] (./media/active-directory-saas-druva-tutorial/IC795085.png "Galería de aplicación")

7.  En el panel de resultados, seleccione **Druva**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Configuración de inicio de sesión único

El objetivo de esta sección es describen cómo habilitar los usuarios autenticar a Druva con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario para crear un archivo de certificado codificado de base 64.  
Si no está familiarizado con este procedimiento, vea [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

La aplicación de Druva espera las afirmaciones SAML en un formato específico, lo que requiere que agregue asignaciones de atributos personalizados a la configuración de **atributos de token saml** .  
La siguiente captura de pantalla muestra un ejemplo de este.

![Atributos de Token SAML] (./media/active-directory-saas-druva-tutorial/IC795087.png "Atributos de Token SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar el inicio de sesión único, realice los pasos siguientes:

1.  En el portal de clásico Azure, en la página de integración de aplicación **Druva** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-druva-tutorial/IC795027.png "Configurar el inicio de sesión único")

2.  En la página **¿cómo desea que los usuarios para iniciar sesión Druva** , seleccione **Inicio de sesión único de Microsoft Azure AD**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-druva-tutorial/IC795088.png "Configurar el inicio de sesión único")

3.  En la página **Configurar la dirección URL de aplicación** , en el cuadro de texto **Druva inicio de sesión en la URL** , escriba la dirección URL utilizada por los usuarios para iniciar sesión la aplicación Druva (por ejemplo: "*https://cloud.druva.com/home/*") y, a continuación, haga clic en **siguiente**.

    ![Configurar la dirección URL de la aplicación] (./media/active-directory-saas-druva-tutorial/IC795089.png "Configurar la dirección URL de la aplicación")

4.  En la página **configurar inicio de sesión único en Druva** , para descargar el certificado, haga clic en **Descargar certificado**y, a continuación, guarde el archivo de certificado localmente en el equipo.

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-druva-tutorial/IC795090.png "Configurar el inicio de sesión único")

5.  En una ventana de explorador web diferente, inicie sesión en el sitio de su empresa Druva como administrador.

6.  Vaya a **administrar \> configuración**.

    ![Configuración] (./media/active-directory-saas-druva-tutorial/IC795091.png "Configuración")

7.  En el cuadro de diálogo Configuración de inicio de sesión único, realice los pasos siguientes:

    ![Configuración de inicio de sesión en Mosai] (./media/active-directory-saas-druva-tutorial/IC795092.png "Configuración de inicio de sesión en Mosai")

    1.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Druva** , copie el valor de **Dirección URL de inicio de sesión remota** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de ID** .
    2.  En el portal de clásico Azure, en la página de diálogo **configurar inicio de sesión único en Druva** , copie el valor de **Dirección URL de cierre de sesión remota** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de ID** .
    3.  Crear un archivo **codificado base 64** de certificado descargado.  

        >[AZURE.TIP] Para obtener más detalles, consulte [cómo convertir un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra el certificado codificado de base 64 en el Bloc de notas, copie el contenido de la misma en el Portapapeles y, a continuación, péguela en el cuadro de texto **ID de proveedor de servicios de certificado**
    5.  Para abrir la página **configuración** , haga clic en **Guardar**.

8.  En la página **configuración** , haga clic en **Generar Token de SSO**.

    ![Configuración] (./media/active-directory-saas-druva-tutorial/IC795093.png "Configuración")

9.  En el cuadro de diálogo **Solo inicio de sesión de autenticación Token** , realice los pasos siguientes:

    ![Símbolo de SSO] (./media/active-directory-saas-druva-tutorial/IC795094.png "Símbolo de SSO")

    1.  Haga clic en **Copiar**.
    2.  Haga clic en **Cerrar**.

10. En el portal de clásico Azure, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **completado** para cerrar el cuadro de diálogo **Configurar inicio de sesión único** .

    ![Configurar el inicio de sesión único] (./media/active-directory-saas-druva-tutorial/IC795095.png "Configurar el inicio de sesión único")

11. En el menú de la parte superior, haga clic en **atributos** para abrir el cuadro de diálogo **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-druva-tutorial/IC795096.png "Atributos")

12. Para agregar las asignaciones de atributo obligatorio, realice los pasos siguientes:

  	|Nombre del atributo|Valor del atributo|
  	|---|---|
  	|sincronizada\_auth\_token|<*valor del Portapapeles*>|

    1.  Para cada fila de datos de la tabla anterior, haga clic en **Agregar usuario atributo**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre del atributo que se muestra de la fila.
    3.  En el cuadro de texto del **Valor del atributo** , escriba el valor del atributo que se muestra de la fila.
    4.  Haga clic en **Finalizar**.

13. Haga clic en **Aplicar cambios**.
##<a name="configuring-user-provisioning"></a>Configuración de aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD iniciar sesión en Druva, debe aprovisionar en Druva.  
En el caso de Druva, aprovisionamiento es una tarea manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar el aprovisionamiento de usuario, siga estos pasos:

1.  Inicie sesión como administrador en el sitio de su empresa **Druva** .

2.  Vaya a **administrar \> usuarios**.

    ![Administrar usuarios] (./media/active-directory-saas-druva-tutorial/IC795097.png "Administrar usuarios")

3.  Haga clic en **Crear nuevo**.

    ![Administrar usuarios] (./media/active-directory-saas-druva-tutorial/IC795098.png "Administrar usuarios")

4.  En el cuadro de diálogo Crear nuevo usuario, siga estos pasos:

    ![Crear NewUser] (./media/active-directory-saas-druva-tutorial/IC795099.png "Crear NewUser")

    1.  Escriba la dirección de correo electrónico y el nombre de una cuenta de usuario válida de Azure Active Directory que desee aprovisionar en los cuadros de texto relacionados.
    2.  Haga clic en **Crear usuario**.

>[AZURE.NOTE] Puede usar cualquier otros Druva usuario cuenta herramientas de creación o API proporcionan por Druva a cuentas de usuario AAD de aprovisionamiento.

##<a name="assigning-users"></a>Asignación de usuarios

Para probar la configuración, debe conceder a los usuarios de Azure AD que desea permitir el uso de la aplicación tenga acceso a ella mediante la asignación.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Para asignar a los usuarios a Druva, siga estos pasos:

1.  En el portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicación **Druva **, haga clic en **asignar a los usuarios**.

    ![Asignar usuarios] (./media/active-directory-saas-druva-tutorial/IC795100.png "Asignar usuarios")

3.  Seleccione el usuario de prueba, haga clic en **asignar**y, a continuación, haga clic en **Sí** para confirmar la asignación.

    ![Sí] (./media/active-directory-saas-druva-tutorial/IC767830.png "Sí")

Si desea comprobar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más detalles sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
