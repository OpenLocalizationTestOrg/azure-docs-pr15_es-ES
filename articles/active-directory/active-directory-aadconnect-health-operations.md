<properties
    pageTitle="Azure AD Connect operaciones de mantenimiento."
    description="En este artículo se describe otras acciones que pueden llevar a cabo una vez que se ha implementado el estado de conexión de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect operaciones de mantenimiento

El tema siguiente describe las distintas operaciones que se pueden realizar con el estado de conexión de Azure AD.

## <a name="enable-email-notifications"></a>Habilitar las notificaciones de correo electrónico
Puede configurar el servicio estado de conexión AD Azure para enviar notificaciones de correo electrónico cuando se generan alertas que indica que la infraestructura de identidades no es correcto. Esto ocurre cuando se genera una alerta, así como cuando se marcará como resuelto. Siga estas instrucciones para configurar notificaciones de correo electrónico.

![Azure AD Connect correo electrónico de estado notificación descubrir](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Notificaciones de correo electrónico están deshabilitadas de forma predeterminada.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para habilitar Azure AD conectar estado notificaciones de correo electrónico

1. Abra el módulo de alertas para el servicio que desea recibir una notificación de correo electrónico.
2. Haga clic en el botón "Configuración de notificación" de la barra de acciones.
3. Gire el interruptor de notificación de correo electrónico a activado.
4. Seleccione la casilla de verificación para configurar todos los administradores Global para recibir notificaciones de correo electrónico.
5. Si desea recibir notificaciones de correo electrónico en cualquier otra dirección de correo electrónico, puede especificarlos en el cuadro destinatario de correo electrónico adicional. Para quitar una dirección de correo electrónico de esta lista, haga clic con el botón secundario en la entrada y seleccione Eliminar.
6. Para finalizar los cambios, haga clic en "Guardar". Todos los cambios tardarán efectos solo después de seleccionar "Guardar".

## <a name="delete-a-server-or-service-instance"></a>Eliminar una instancia del servidor o servicio

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Eliminar un servidor de servicio de Azure AD Connect estado
En algunos casos, puede quitar un servidor de supervisión. Siga estas instrucciones para quitar un servidor de servicio de Azure AD Connect estado.

Al eliminar un servidor, tenga en cuenta lo siguiente:

- Esta acción detendrá recopilar más datos desde el servidor. Se quitará el servicio de supervisión de este servidor. Después de la acción, no podrá ver las alertas nuevas, supervisión o uso de datos de análisis de este servidor.
- Esta acción no se desinstalar o quitar al agente de mantenimiento de su servidor. Si no ha desinstalado al agente de mantenimiento antes de realizar este paso, puede ver los eventos de error en el servidor relacionado con el agente de mantenimiento.
- Esta acción no eliminará los datos recopilados ya desde este servidor. Se eliminarán los datos según la directiva de retención de datos de Microsoft Azure.
- Después de realizar esta acción, si desea empezar a supervisar el mismo servidor de nuevo, debe desinstalar y volver a instalar al agente de mantenimiento en este servidor.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Para eliminar un servidor de Azure AD conectar estado del servicio

Azure AD Connect salud de AD FS y Azure AD conectan (sincronizar):

1. Abra el módulo de servidor desde el servidor de la hoja de lista, seleccione el nombre del servidor que se eliminen.
2. En el módulo de servidor, haga clic en el botón "Eliminar" de la barra de acciones.
3. Confirmar la acción para eliminar el servidor escribiendo el nombre del servidor en el cuadro de confirmación.
4. Haga clic en el botón "Eliminar".

Azure AD Connect estado para AD DS:

1. Abra el panel controladores de dominio.
2. Seleccione el controlador de dominio que se eliminen.
3. Haga clic en el botón "Eliminar seleccionadas" de la barra de acciones.
4. Confirmar la acción para eliminar el servidor.
5. Haga clic en el botón "Eliminar".

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminar una instancia de servicio del servicio de Azure AD Connect estado

En algunos casos, puede quitar una instancia de servicio. Siga estas instrucciones para quitar una instancia de servicio de Azure AD conectar estado del servicio.

Al eliminar una instancia de servicio, tenga en cuenta lo siguiente:

- Esta acción quitará la instancia actual de servicio del servicio de supervisión.
- Esta acción no se desinstalar o quitar al agente de mantenimiento de cualquiera de los servidores que se hayan supervisar como parte de esta instancia de servicio. Si no ha desinstalado al agente de mantenimiento antes de realizar este paso, puede ver los eventos de error en los servidores relacionados con el agente de mantenimiento.
- Según la directiva de retención de datos de Microsoft Azure, se eliminarán todos los datos de esta instancia de servicio.
- Después de realizar esta acción, si desea iniciar el servicio de supervisión, desinstale y vuelva a instalar al agente de mantenimiento en todos los servidores que se va a supervisar. Después de realizar esta acción, si desea empezar a supervisar el mismo servidor de nuevo, debe desinstalar y volver a instalar al agente de mantenimiento en este servidor.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Para eliminar una instancia de servicio de Azure AD conectar estado del servicio

1. Abra el módulo de servicio desde el módulo de lista de servicio, seleccione el identificador de servicio (nombre del conjunto de servidores) que desea quitar.
2. En el módulo de servidor, haga clic en el botón "Eliminar" de la barra de acciones.
3. Confirme el nombre de servicio escribiendo en el cuadro de confirmación. (por ejemplo: sts.contoso.com)
4. Haga clic en el botón "Eliminar".
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Administrar el acceso de la función a partir del Control de acceso
### <a name="overview"></a>Información general
[Control de acceso en función de funciones](role-based-access-control-configure.md) para el estado de conexión de Azure AD proporciona acceso servicio de estado de conexión de Azure AD a usuarios o grupos fuera de los administradores globales. Esto se logra mediante la asignación de roles a los grupos de usuarios deseados y/o y proporciona un mecanismo para limitar los administradores globales en el directorio.

#### <a name="roles"></a>Roles
Estado de conexión de Azure AD es compatible con las siguientes funciones integradas.

| Función | Permisos |
| ----------- | ---------- |
| Propietario | Los propietarios pueden ***administrar el acceso*** (por ejemplo, asignar rol a un usuario o grupo), ***ver toda la información*** (por ejemplo, ver avisos) desde el portal y ***cambiar la configuración*** (por ejemplo, notificaciones de correo electrónico) dentro de estado de conexión de Azure AD. <br>De forma predeterminada, los administradores globales de Azure AD están asignados a esta función y esto no se puede cambiar.  |
|Colaborador|  Los colaboradores pueden ***ver toda la información*** (por ejemplo, ver avisos) desde el portal y ***cambiar la configuración*** (por ejemplo, notificaciones de correo electrónico) dentro de estado de conexión de Azure AD.|
|Lector| Los lectores pueden ***ver toda la información*** (por ejemplo, ver avisos) desde el portal de estado de conexión de Azure AD.|

Todos los otros roles (por ejemplo, 'Administradores de acceso de usuario' o 'DevTest prácticas usuarios'), incluso si está disponible en la experiencia de portal no afectar acceso a dentro del estado de conexión de Azure AD.

#### <a name="access-scope"></a>Ámbito de Access

Azure AD Connect es compatible con la administración del acceso en dos niveles:

- ***Todas las instancias de servicio***: este es el procedimiento recomendado para la mayoría de los clientes y controla el acceso para todas las instancias de servicio (por ejemplo, una granja de servidores ADFS) en todos los tipos de rol supervisados por estado de conexión de Azure AD.

- ***Instancia de servicio***: en algunos casos, tendrá que separar acceso basándose en los tipos de funciones o por una instancia de servicio. En este caso, puede administrar el acceso en el nivel de instancia de servicio.  

Se concede permiso si un usuario tiene acceso a nivel de directorio o la instancia de servicio.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Cómo permitir el acceso de usuarios o grupos a estado de conexión de Azure AD
#### <a name="steps-1-select-the-appropriate-access-scope"></a>Los pasos 1: Seleccione el ámbito de acceso adecuado
Para permitir el acceso de un usuario en el nivel de *todas las instancias de servicio* en el estado de conexión de Azure AD, abra el módulo principal en estado de conexión de Azure AD.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Paso 2: Agregar usuarios, grupos y asignar roles
1. Haga clic en el elemento de "Usuarios" de la sección Configurar.<br>
![Azure AD Connect estado RBAC principal placa](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Haga clic en "Agregar"
3. Seleccione el rol de"" como "Propietario"<br>
![Azure AD Connect salud RBAC Agregar usuario](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Escriba el nombre o el identificador del usuario o grupo. Puede seleccionar uno o más usuarios o grupos al mismo tiempo. Haga clic en "seleccionar".
![Azure AD Connect estado RBAC Seleccionar usuario](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Seleccione "Aceptar".<br>

6. Una vez completada la asignación de roles, los usuarios y grupos aparecerán en la lista.<br>
![Azure AD Connect lista de usuarios de salud RBAC](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Estos pasos le permitirá la lista de usuarios y el acceso de grupo según sus funciones asignadas.
>[AZURE.NOTE]
- Los administradores globales siempre tienen acceso completo a todas las operaciones, pero las cuentas de administrador global no aparecerá en la lista anterior.
- NO se admite la característica "Invitar a usuarios" dentro de estado de conexión de Azure AD.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Paso 3: Compartir la ubicación de la hoja con los usuarios o grupos
1. Después de asignar permisos, un usuario puede acceder a estado de conexión de Azure AD yendo a [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Una vez en el módulo, el usuario puede anclar el módulo o diferentes partes a panel haciendo clic en "Anclar al panel"<br>
![Módulo de Azure AD RBAC de salud conectar pin](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un usuario con la función de "Lector" asignado no podrá realizar la operación "crear" para obtener la extensión de estado de conexión de Azure AD de Azure Marketplace. Este usuario puede obtener acceso a los módulos seleccionando el vínculo anterior. Para un uso posterior, el usuario puede anclar el módulo en el panel.

### <a name="remove-users-andor-groups"></a>Quitar usuarios o grupos
Puede quitar un usuario o grupo agregado al elemento de Azure AD conectar estado rol Control de acceso, hacer clic en y seleccione Quitar.<br>
![Azure AD Connect estado RBAC quitar usuario](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Vínculos relacionados

* [Azure AD Connect estado](active-directory-aadconnect-health.md)
* [Azure AD Connect instalación de agente de estado](active-directory-aadconnect-health-agent-install.md)
* [Conecte mediante Azure AD estado con AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando el estado de conexión de Azure AD para sincronizar](active-directory-aadconnect-health-sync.md)
* [Conecte mediante Azure AD estado con AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect salud preguntas más frecuentes](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historial de versiones de estado](active-directory-aadconnect-health-version-history.md)
