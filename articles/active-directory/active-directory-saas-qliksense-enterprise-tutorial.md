<properties
    pageTitle="Tutorial: Integración de Azure Active Directory con Qlik sentido Enterprise | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Qlik sentido Enterprise."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integración de Azure Active Directory con Qlik sentido Enterprise

En este tutorial, aprenderá a integrar Qlik sentido empresarial con Azure Active Directory (AD Azure).

Integración de Qlik sentido empresarial con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a la empresa de sentido Qlik
- Puede habilitar los usuarios automáticamente obtener ha iniciado sesión en Qlik sentido empresa (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una ubicación central: el portal de clásico de Azure

Si desea conocer más detalles sobre la integración de la aplicación de SaaS con Azure AD, vea [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Qlik sentido Enterprise, necesita lo siguiente:

- Una suscripción de Azure AD
- Un Qlik sentido Enterprise inicio de sesión único de suscripción habilitado


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda utilizar un entorno de producción.


Para probar los pasos de este tutorial, debería seguir estas recomendaciones:

- No debe utilizar su entorno de producción, a menos que esto es necesario.
- Si no tiene un entorno de prueba de Azure AD, puede obtener un mes de una prueba [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, se prueba Azure AD inicio de sesión único en un entorno de prueba.

El escenario descrito en este tutorial se compone de dos bloques de creación:

1. Agregar Qlik sentido empresarial de la Galería
2. Configurar y probar Azure AD de sesión único


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Agregar Qlik sentido empresarial de la Galería
Para configurar la integración de Qlik sentido Enterprise en Azure AD, debe agregar Qlik sentido Enterprise desde la galería a la lista de aplicaciones de SaaS administradas.

**Para agregar Qlik sentido empresarial de la galería, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Active Directory][1]
2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación de la Galería**.

    ![Aplicaciones][4]

6. En el cuadro Buscar, escriba **Qlik sentido Enterprise**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. En el panel de resultados, seleccione **Qlik sentido Enterprise**y, a continuación, haga clic en **completado** para agregar la aplicación.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar y probar Azure AD de sesión único
En esta sección, configurar y probar el inicio de sesión único Azure AD con Qlik sentido Enterprise en función de un usuario de prueba denominado a "Bárbara Menéndez".

Para que el inicio de sesión único para que funcione, debe saber qué es el usuario equivalente en Qlik sentido Enterprise a un usuario en Azure AD Azure AD. En otras palabras, debe establecerse una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Qlik sentido Enterprise.

Asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** de empresa de sentido Qlik establecida esta relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Qlik sentido Enterprise, debe completar los siguientes bloques de creación:

1. **[Inicio de sesión único en configurar Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir a los usuarios usar esta característica.
2. **[Creación de un anuncio de Azure probar usuario](#creating-an-azure-ad-test-user)** - probar Azure AD inicio de sesión único con Britta Simon.
3. **[Crear una empresa de sentido Qlik probar usuario](#creating-a-qliksense-enterprise-test-user)** - tener un equivalente de Britta Simon en Enterprise de sentido Qlik que está vinculado a la representación de Azure AD de ella.
4. **[Asignación de Azure AD probar usuario](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar inicio de sesión único en Azure AD.
5. **[Pruebas de inicio de sesión único](#testing-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración de inicio de sesión único de Azure AD

En esta sección, habilitar inicio de sesión único de Azure AD en el portal de clásico y configurar el inicio de sesión único en la aplicación empresarial de sentido Qlik.


**Para configurar el inicio de sesión único Azure AD con Qlik sentido Enterprise, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicación de **Empresa de sentido Qlik** , haga clic en **configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único** .
     
    ![Configurar el inicio de sesión único][6] 

2. En la página **¿cómo le gustaría usuarios a iniciar sesión Qlik sentido Enterprise** , seleccione **Azure AD inicio de sesión único**y, a continuación, haga clic en **siguiente**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. En la página de diálogo **Configurar opciones de aplicación** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    una. En el cuadro de texto de **Inicio de sesión en la dirección URL** , escriba la dirección URL utilizada por los usuarios para el inicio de sesión en la aplicación de empresa de sentido Qlik utilizando el modelo siguiente: **https://\<Qlik sentido totalmente completo Hostname\>: 443 / < prefijo de Proxy Virtual\>/samlauthn/**.
    
    > [AZURE.NOTE] Tenga en cuenta la barra diagonal al final de este URI.  Es necesario.

    b. Haga clic en **siguiente**
 
4. En la página **configurar inicio de sesión único en Qlik sentido empresarial** , realice los pasos siguientes:

    ![Configurar el inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    una. Haga clic en **descargar metadatos**y, a continuación, guarde el archivo en su equipo.  Estar preparado para editar el archivo de metadatos antes de cargar en el servidor de detección de Qlik.

    b. Haga clic en **siguiente**.

5. Preparar el archivo XML de metadatos de federación para que pueda cargar al servidor de detección de Qlik.

    > [AZURE.NOTE] Antes de cargar los metadatos IdP en el servidor de detección de Qlik, el archivo debe modificarse para quitar la información para garantizar el funcionamiento correcto entre Azure AD y sentido Qlik server.

    ![QlikSense][qs24]

    una. Abra el archivo FederationMetaData.xml descargado de Azure en un editor de texto.

    b. Buscar el valor **RoleDescriptor**.  Habrá cuatro entradas (dos pares de apertura y de cierre de etiquetas de elementos).

    c. Eliminar entre las etiquetas de RoleDescriptor y toda la información del archivo.

    d. Guarde el archivo y mantenerlo cerca para usarlas más adelante en este documento.

6. Vaya a la consola de administración de Qlik sentido Qlik (QMC) como un usuario que puede crear configuraciones de proxy virtual.

7. En el QMC, haga clic en el elemento de menú Proxy Virtual.

    ![QlikSense][qs6] 

8. En la parte inferior de la pantalla, haga clic en el botón Crear nuevo.

    ![QlikSense][qs7]

9. Aparecerá la pantalla de edición de proxy Virtual.  En el lado derecho de la pantalla es un menú para mostrar las opciones de configuración.

    ![QlikSense][qs9]

10. Con la opción de menú de identificación activada, escriba la información de identificación para la configuración de proxy virtual Azure.

    ![QlikSense][qs8]  
    
    una. El campo Descripción es un nombre descriptivo para la configuración de proxy virtual.  Para obtener una descripción, escriba un valor.
    
    b. El campo prefijo identifica el extremo proxy virtual para conectar con sentido Qlik con Azure AD inicio de sesión único.  Escriba un nombre único prefijo de este proxy virtual.

    c. Tiempo de espera de inactividad de sesión (minutos) es el tiempo de espera para conexiones a través de este proxy virtual.

    d. El nombre del encabezado de cookie de sesión es el nombre de cookie almacenar el identificador de sesión para la sesión de sentido Qlik que recibe un usuario tras la autenticación correcta.  Este nombre debe ser único.

11. Haga clic en la opción de menú de autenticación para que sea visible.  Aparecerá la pantalla de autenticación.

    ![QlikSense][qs10]

    una. El **modo de acceso anónimo** desplegable determina si pueden obtener acceso a los usuarios anónimos Qlik sentido a través del proxy virtual.  La opción predeterminada no es ningún usuario anónimo.

    b. El **método de autenticación** de la lista desplegable determina que el esquema de autenticación del proxy virtual usará.  En la lista desplegable, seleccione SAML.  Más opciones aparecerán como resultado.

    c. En el **campo URI del host SAML**, entrada de que los usuarios hostname se escriben para tener acceso a Qlik sentido a través de este proxy virtual de SAML.  El nombre de host es el uri del servidor de detección de Qlik.

    d. **Id. de entidad SAML**, escriba el mismo valor especificado para el campo URI del host SAML.

    e. Los **metadatos de SAML IdP** es el archivo editado anteriormente en la sección **Editar los metadatos de federación de configuración de Azure AD** .  **Antes de cargar los metadatos IdP, el archivo debe modificarse** para quitar la información para garantizar el funcionamiento correcto entre Azure AD y sentido Qlik server.  **Consulte las instrucciones anteriores si aún puede editar el archivo.**  Si se ha modificado el archivo haga clic en el botón Examinar y seleccione el archivo de metadatos editado para cargarla en la configuración de proxy virtual.

    f. Escriba el nombre del atributo o referencia de esquema para el atributo SAML que representa el **identificador de usuario** Azure AD se envía al servidor Qlik sentido.  Información de referencia de esquema está disponible en la configuración de publicación de pantallas de aplicación de Azure.  Para utilizar el atributo de nombre, **escriba http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Escriba el valor para el **directorio de usuario** que se va a unir a los usuarios cuando se autentican al servidor de detección de Qlik a través de Azure AD.  Valores codificados deben ir entre **corchetes angulares []**.  Para usar un atributo enviado en la aserción SAML de Azure AD, escriba el nombre del atributo en este texto cuadro **sin** corchetes.

    h. El **algoritmo de firma de SAML** establece el certificado de proveedor (en este caso, el servidor de detección de Qlik) de servicio de firma para la configuración de proxy virtual.  Si el servidor de detección de Qlik utiliza un certificado de confianza generado mediante Microsoft RSA mejorada y AES Cryptographic Provider, cambie el algoritmo de firma de SAML a **SHA-256**.

    . La sección de asignación de atributo SAML permite atributos adicionales como grupos que se envíen a Qlik sentido para su uso en las reglas de seguridad.

12. Haga clic en la opción de menú para que sea visible de equilibrio de carga.  Aparecerá la pantalla de equilibrio de carga.

    ![QlikSense][qs11]

13. Haga clic en el botón Agregar nueva servidor nodo, nodo de seleccionar motor o nodos Qlik sentido enviará de sesiones para equilibrar la carga y haga clic en el botón Agregar.

    ![QlikSense][qs12]

14. Haga clic en la opción de menú Opciones avanzadas para que sea visible. Aparece la pantalla de opciones avanzadas.

    ![QlikSense][qs13]

    una. La lista de notas de Host identifica los nombres de host que se aceptarán al conectar con el servidor Qlik sentido.  **Escriba el nombre de host especificarán los usuarios al conectarse al servidor de detección de Qlik.** El nombre de host es el mismo valor que el uri de host SAML sin el https://.

15. Haga clic en el botón Aplicar.

    ![QlikSense][qs14]

16. Haga clic en Aceptar para aceptar el mensaje de advertencia que indica que se reinicia servidores proxy vinculados al proxy virtual.

    ![QlikSense][qs15]

17. En el lado derecho de la pantalla, aparece el menú de elementos asociados.  Haga clic en la opción de menú de servidores proxy.

    ![QlikSense][qs16]

18. Aparecerá la pantalla proxy.  Haga clic en el botón de vínculo en la parte inferior para vincular a un servidor proxy al proxy virtual.

    ![QlikSense][qs17]

19. Seleccione el nodo de proxy que admite esta conexión virtual proxy y haga clic en el botón de vínculo.  Después de vincular, el servidor proxy aparecerán en servidores proxy asociados.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Después de unos cinco y diez segundos, aparecerá el mensaje de actualización QMC.  Haga clic en el botón Actualizar QMC.

    ![QlikSense][qs20]

21. Cuando se actualiza el QMC, haga clic en el elemento de menú Virtual servidores proxy. La nueva entrada de proxy virtual SAML aparece en la tabla en la pantalla.  Solo clic en la entrada del proxy virtual.

    ![QlikSense][qs51]

22. En la parte inferior de la pantalla, se activará el botón de metadatos de SP descargar.  Haga clic en el botón de metadatos de SP descargar para guardar los metadatos a un archivo.

    ![QlikSense][qs52]

23. Abra el archivo de metadatos de sp.  Observe la entrada **entityID** y la entrada de **AssertionConsumerService** .  Estos valores son equivalentes para el **identificador** y la **dirección URL de inicio de sesión** en la configuración de la aplicación de Azure AD. Si no coinciden debe reemplazar ellos en el Asistente de configuración de aplicación de Azure AD.

    ![QlikSense][qs53]

24. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **siguiente**.
    
    ![En el inicio de sesión único de Azure AD][10]

25. En la página de **confirmación de inicio de sesión único** , haga clic en **completado**.  
 
    ![En el inicio de sesión único de Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Crear un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba en el portal de clásico denominado a Britta Simon.


![Crear usuario de Azure AD][20]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el **portal de clásica Azure**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **usuarios**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario** , en la barra de herramientas en la parte inferior, haga clic en **Agregar usuario**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Díganos sobre este usuario** , siga estos pasos:  ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    una. Como tipo de usuario, seleccione Nuevo usuario de su organización.

    b. En el **cuadro de texto**de nombre de usuario, escriba **BrittaSimon**.

    c. Haga clic en **siguiente**.

6.  En la página de diálogo de **Perfil de usuario** , siga estos pasos: ![crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    una. En el cuadro de texto **nombre** , escriba **Bárbara**.  

    b. En el último cuadro de texto **Nombre** , tipo, **Simon**.

    c. En el cuadro de texto **Nombre para mostrar** , escriba **Bárbara Menéndez**.

    d. En la lista **función** , seleccione el **usuario**.

    e. Haga clic en **siguiente**.

7. En la página de diálogo **obtener una contraseña temporal** , haga clic en **crear**.

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **obtener una contraseña temporal** , realice los pasos siguientes:

    ![Crear un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    una. Anote el valor de la **Nueva contraseña**.

    b. Haga clic en **Finalizar**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Crear un usuario de prueba Qlik sentido Enterprise

En esta sección, creará un usuario llamado a Britta Simon en Qlik sentido Enterprise. Trabaje con el equipo de soporte técnico de Qlik sentido Enterprise para agregar los usuarios de la plataforma Qlik sentido empresarial.


### <a name="assigning-the-azure-ad-test-user"></a>Asignar al usuario de prueba de Azure AD

En esta sección, habilitar Britta Simon utilizar un inicio de sesión único Azure al conceder acceso a la empresa de sentido Qlik.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Qlik sentido Enterprise, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Qlik sentido Enterprise**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. En el menú de la parte superior, haga clic en **usuarios**.

    ![Asignar usuario][203]

4. En la lista de usuarios, seleccione **Bárbara Menéndez**.

5. En la barra de herramientas en la parte inferior, haga clic en **asignar**.

    ![Asignar usuario][205]


## <a name="testing-single-sign-on"></a>Pruebas de inicio de sesión único

En esta sección, pruebe su Azure AD única sesión configuración mediante el Panel de acceso.

Al hacer clic en el mosaico Qlik sentido Enterprise en el Panel de acceso, debe obtener automáticamente ha iniciado sesión en la aplicación empresarial de sentido Qlik.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png