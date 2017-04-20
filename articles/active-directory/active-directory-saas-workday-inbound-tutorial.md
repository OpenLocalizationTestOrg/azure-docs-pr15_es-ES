<properties 
    pageTitle="Tutorial: Configurar día laborable para la sincronización entrante | Microsoft Azure" 
    description="Aprenda a usar jornada laboral como origen de datos de identidad de Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Tutorial: Configurar día laborable para la sincronización entrante


Es el objetivo de este tutorial mostrar los pasos que debe llevar a cabo en los días laborables y Azure AD para importar contactos de jornada laboral a Azure AD. 

El escenario descrito en este tutorial se supone que ya tiene los siguientes elementos:

-   Una suscripción válida de Azure AD Premium
-   Un inquilino de día laborable
  
El escenario descrito en este tutorial consta de los siguientes elementos:

1. Habilitar la integración de aplicaciones para el día laborable 


2. Creación de un usuario de sistema de integración 


3. Crear un grupo de seguridad 


4. Asignar al usuario de sistema de integración al grupo de seguridad 


5. Configurar opciones del grupo de seguridad 


6. Activar los cambios de la directiva de seguridad 


7. Importar configuración de usuario en Azure AD 



##<a name="enabling-the-application-integration-for-workday"></a>Habilitar la integración de aplicaciones para el día laborable
  
El objetivo de esta sección es describen cómo habilitar la integración de aplicaciones para el día laborable.

### <a name="steps"></a>Pasos siguientes:

1.  En el portal clásico Azure, en el panel de navegación de la izquierda, haga clic en **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3.  Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **aplicaciones** en el menú superior.

    ![Aplicaciones] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Agregar aplicación")

  
5. En el cuadro Buscar, escriba **los días laborables**.

    ![Agregar una aplicación de gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Agregar una aplicación de muestra")

6. En el panel de resultados, seleccione los días laborables y, a continuación, haga clic en completado para agregar la aplicación.

    ![Galería de aplicación] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galería de aplicación")





## <a name="creating-an-integration-system-user"></a>Creación de un usuario de sistema de integración

### <a name="steps"></a>Pasos siguientes:


1. En el **Trabajo del día laborable**, escriba crear usuario en el cuadro de búsqueda y, a continuación, haga clic en **Crear usuario del sistema de integración**. 

    ![Crear usuario] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Crear usuario")



2. Completar la tarea de **Crear un usuario de sistema de integración** , proporcione un nombre de usuario y una contraseña para un nuevo usuario de sistema de integración.  Deje que requieren nueva contraseña en el siguiente inicio de sesión en la opción está desactivada, porque este usuario se iniciará sesión mediante programación. Deje los minutos de tiempo de espera de sesión con su valor predeterminado de 0, lo que evitará las sesiones de usuario de tiempo de espera antes de tiempo. 

    ![Crear el usuario de integración de sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Crear el usuario de integración de sistema")
 




## <a name="creating-a-security-group"></a>Crear un grupo de seguridad

Para el escenario descrito en este tutorial, debe crear un grupo de seguridad del sistema de integración sin restricciones y asignar al usuario.

### <a name="steps"></a>Pasos siguientes:

1. Escriba crear grupo de seguridad en el cuadro de búsqueda y, a continuación, haga clic en **Crear grupo de seguridad**. 

    ![Grupo de CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Grupo de CreateSecurity")
 

2. Completar la tarea de crear el grupo de seguridad.  Seleccione un grupo de seguridad de sistema de integración: no restricciones en la lista desplegable Tipo de grupo de seguridad inquilinos, para crear un grupo de seguridad a los que los miembros se agregará explícitamente. 

    ![Grupo de CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Grupo de CreateSecurity")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Asignar al usuario de sistema de integración al grupo de seguridad

### <a name="steps"></a>Pasos siguientes:


1. Escriba Editar grupo de seguridad en el cuadro de búsqueda y, a continuación, haga clic en **Editar grupo de seguridad**. 

    ![Editar grupo de seguridad] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Editar grupo de seguridad")
 
 

2. Busque y seleccione el nuevo grupo de seguridad de integración por su nombre. 

    ![Editar grupo de seguridad] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Editar grupo de seguridad")

 

3. Agregar el nuevo usuario de sistema de integración para el nuevo grupo de seguridad. 

    ![Grupo de seguridad del sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Grupo de seguridad del sistema")  




## <a name="configuring-security-group-options"></a>Configurar opciones del grupo de seguridad

En este paso, conceder a los nuevos permisos de grupo de seguridad para **obtener** y **poner** operaciones en los objetos protegidos mediante las siguientes directivas de seguridad de dominio:

- Aprovisionamiento de cuenta externa

- Datos de trabajo: Informes de trabajo pública

- Los datos de trabajo: Todas las posiciones

- Datos de trabajo: Actual información de los empleados

- Datos de trabajo: Título de empresa en el perfil de trabajo

 
### <a name="steps"></a>Pasos siguientes:

1. Escriba las directivas de seguridad de dominio en el cuadro de búsqueda y, a continuación, haga clic en el vínculo, directivas de seguridad de dominio para el área funcional.  

    ![Directivas de seguridad de dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Directivas de seguridad de dominio")  
 

2. Buscar sistema y seleccione el área funcional del **sistema** .  Haga clic en **Aceptar**.  

    ![Directivas de seguridad de dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Directivas de seguridad de dominio")  


3. En la lista de directivas de seguridad para el área funcional del sistema, expanda Administración de seguridad y seleccione la directiva de seguridad de dominio externo de aprovisionamiento de cuenta.  

    ![Directivas de seguridad de dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Directivas de seguridad de dominio")  


4. Haga clic en **Editar permisos**y, a continuación, en la página de diálogo **Editar permisos**, agregar el nuevo grupo de seguridad a la lista de grupos de seguridad con permisos de integración de **obtener** y **colocar** . 

    ![Permiso de edición] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Permiso de edición")  

 

5. Repita el paso 1 anterior, para volver a la pantalla para seleccionar áreas funcionales, y esta vez, busque personal, seleccione el área funcional de personal y haga clic en **Aceptar**.

    ![Directivas de seguridad de dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Directivas de seguridad de dominio")  
 

6. En la lista de directivas de seguridad para el área funcional personal, expanda datos de trabajo: personal y repita el paso 4 anteriores para cada uno de estos restante directivas de seguridad:

     - Datos de trabajo: Informes de trabajo pública

     - Los datos de trabajo: Todas las posiciones

     - Datos de trabajo: Actual información de los empleados

     - Datos de trabajo: Título de empresa en el perfil de trabajo


    ![Directivas de seguridad de dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Directivas de seguridad de dominio")  







## <a name="activating-security-policy-changes"></a>Activar los cambios de la directiva de seguridad

### <a name="steps"></a>Pasos siguientes:

1. Escriba activar en el cuadro de búsqueda y, a continuación, haga clic en el vínculo, activar cambios pendientes de directiva de seguridad. 

    ![Activar] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activar") 
 

2. Empezar a activar cambios de la directiva de seguridad pendientes escribiendo un comentario para fines de auditoría y, a continuación, haga clic en **Aceptar**. 

    ![Activar pendientes de seguridad] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activar pendientes de seguridad")   
 

3. Completar la tarea en la siguiente pantalla activando la casilla de verificación etiquetado confirmar y, a continuación, haga clic en **Aceptar**. 

    ![Activar pendientes de seguridad] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activar pendientes de seguridad")  





## <a name="configuring-user-import-in-azure-ad"></a>Importar configuración de usuario en Azure AD

El objetivo de esta sección es indican cómo configurar Azure AD para importar contactos desde el día laborable.


### <a name="steps"></a>Pasos siguientes:


1. En la página de integración de aplicación de **jornada laboral** , haga clic en **Configurar usuario importar** para abrir el cuadro de diálogo **Configurar aprovisionamiento** .


2. En la página de **configuración y las credenciales de administrador** , realice los pasos siguientes y, a continuación, haga clic en **siguiente**: 

    ![Configuración y las credenciales de administrador] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Configuración y las credenciales de administrador")  
 
    una. En el día laborable administración usuario cuadro de texto Nombre, escriba el nombre del usuario que haya creado en la creación de una sección de usuario del sistema de integración.

    b. En el cuadro de texto de la contraseña de administrador día laborable, escriba la contraseña del usuario que haya creado en la creación de una sección de usuario del sistema de integración.

    c. En el cuadro de texto de la dirección URL de inquilinos día laborable, escriba la dirección URL o el inquilino de jornada laboral.


3. En la página de **prueba de conexión** , haga clic en **Iniciar prueba** para confirmar la conectividad y, a continuación, haga clic en **siguiente**. 

    ![Probar conexión] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Probar conexión")  
 

4. En la página de opciones de **aprovisionamiento** , haga clic en **siguiente**. 

    ![Opciones de aprovisionamiento] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opciones de aprovisionamiento")


5. En el cuadro de diálogo **iniciar el aprovisionamiento** , haga clic en **completado**. 

    ![Iniciar el aprovisionamiento] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Iniciar el aprovisionamiento")
 

Ahora puede ir a la sección **usuarios** y comprobar si se ha importado el usuario de la jornada laboral.



## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
