<properties
  pageTitle="Conjunto de IoT Azure y Azure Active Directory | Microsoft Azure"
  description="Describe cómo el conjunto de aplicaciones de Azure IoT utiliza Azure Active Directory para administrar los permisos."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permisos en el sitio de azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>¿Qué ocurre al iniciar sesión

Cuando inicie sesión primero en [azureiotsuite.com][lnk-azureiotsuite], el sitio determina los niveles de permisos tiene basado en el inquilino de Azure Active Directory (AAD) está seleccionada y la suscripción de Azure.

1.  El sitio primero encuentra de Azure qué inquilinos AAD que pertenece para rellenar la lista de los inquilinos que aparece junto a su nombre de usuario ha iniciado sesión. En este momento, el sitio sólo puede obtener tokens de usuario de un inquilino a la vez. Como resultado, cuando se pasa a un inquilino diferente con la lista desplegable situada en la esquina superior derecha, el sitio de volver a iniciar sesión en a ese inquilino para obtener los símbolos de ese inquilino.

2.  A continuación, el sitio se encuentra de Azure las suscripciones se han asociado con el inquilino seleccionado. Vea las suscripciones disponibles cuando se crea una nueva solución preconfigurada.

3.  Por último, el sitio recupera todos los recursos en las suscripciones y los grupos de recursos etiquetado como soluciones preconfiguradas y rellena los mosaicos en la página principal.

Las secciones siguientes describen las funciones que controlan el acceso a las soluciones preconfiguradas.

## <a name="aad-roles"></a>Funciones AAD

Las funciones AAD controlan las soluciones de aprovisionamiento preconfigurado capacidad y administran usuarios en una solución preconfigurada.

Puede encontrar más información acerca de los roles de administrador en AAD en [asignar roles de administrador en Azure AD][lnk-aad-admin], pero en este artículo se centra principalmente en el **Administrador Global** y los roles de **Usuario o miembro de dominio** que usa las soluciones preconfiguradas.

**Administrador global:** Puede haber muchos administradores globales por inquilino AAD. Cuando se crea un inquilino AAD, son de forma predeterminada el administrador global de ese inquilino. El administrador global puede aprovisionar una solución preconfigurada y se ha asignado un rol de **Administrador** de la aplicación dentro de su inquilino AAD. Sin embargo, si otro usuario en el mismo inquilino AAD crea una aplicación, la función predeterminada que se le ha concedido el administrador global es **Solo lectura implícitos**. Los administradores globales pueden asignar roles para aplicaciones con el [portal clásica Azure][lnk-classic-portal].

**Usuario o miembro de dominio:** Puede haber muchos usuarios o los miembros del dominio por inquilino AAD. Un usuario del dominio puede aprovisionar una solución preconfigurada mediante el [azureiotsuite.com] [ lnk-azureiotsuite] sitio. La función predeterminada que se les concede para la aplicación que aprovisionar sea **Administrador**. Puede crear una aplicación con la secuencia de comandos de build.cmd de la [supervisión una remota de iot azure] [ lnk-rm-github-repo] o [azure iot-predictiva mantenimiento] [ lnk-pm-github-repo] repositorio, pero la función predeterminada se les concede es **Implícita de sólo lectura**, como no tiene permiso para asignar roles. Si otro usuario en el inquilino AAD crea una aplicación, le asigna el rol de **Sólo lectura implícitos** para la aplicación de forma predeterminada. No tienen la capacidad de asignar roles de aplicaciones. por lo tanto la no puede agregar usuarios o funciones para los usuarios de una aplicación incluso si la habían aprovisionado.

**Invitado o usuario de invitado:** Puede haber muchos usuarios o invitados invitado por inquilino AAD. Los usuarios invitados tienen un conjunto limitado de derechos en el inquilino AAD. Como resultado, los usuarios invitados no pueden crear una solución preconfigurada en el inquilino AAD.

Para obtener más información, consulte los siguientes recursos:

- [Crear o editar usuarios en Azure AD][lnk-create-edit-users]
- [Asignar roles de aplicación en AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Roles de administrador de suscripción de Azure

Los roles de administrador de Azure controlan la capacidad para asignar una suscripción de Azure a un inquilino AD.

Puede encontrar más información acerca de los roles de administrador de compañeros de Azure, administrador y Administrador de la cuenta en el artículo [cómo agregar o cambiar el Administrador de compañeros de Azure, administrador y Administrador de la cuenta][lnk-admin-roles].

## <a name="application-roles"></a>Funciones de aplicación

Las funciones de aplicación controlan el acceso a los dispositivos de su solución preconfigurada.

Existen dos definido y una función implícita definida en la aplicación que se crea al aprovisionar una solución preconfigurada.

-   **Administrador:** Tiene control total para agregar, administrar y eliminar dispositivos

-   **De sólo lectura:** Tiene la capacidad de ver los dispositivos

-   **Sólo lectura implícito:** Es el mismo como de sólo lectura, pero se concede a todos los usuarios de su inquilino AAD. Esto se hizo para comodidad durante el desarrollo. Puede quitar este rol modificando la [RolePermissions.cs] [ lnk-resource-cs] archivo de origen.

### <a name="changing-application-roles-for-a-user"></a>Cambiar los roles de aplicación para un usuario

Puede usar el siguiente procedimiento para realizar a un usuario en Active Directory un administrador de la solución preconfigurada.

Debe ser un administrador global de AAD para cambiar los roles de un usuario:

1. Vaya al [portal clásica Azure][lnk-classic-portal].

2. Seleccione **Active Directory**.

3. Haga clic en el nombre de su inquilino AAD (es decir, el directorio que eligió en azureiotsuite.com Cuando aprovisiona la solución).

4. Haga clic en **aplicaciones**.

5. Haga clic en el nombre de la aplicación que coincida con el nombre de la solución preconfigurada. Si no ve la aplicación en la lista, cambie el menú **Mostrar** hasta **aplicaciones es propietario de mi compañía** y haga clic en la marca de verificación.

7. Haga clic en **usuarios**.

8. Seleccione el usuario que desea cambiar los roles.

9. Haga clic en **asignar** y seleccione el rol (como **Administrador**) que desea asignar al usuario, haga clic en la marca de verificación.

## <a name="faq"></a>Preguntas más frecuentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Soy un administrador de servicio y deseo cambiar la asignación del directorio entre mi suscripción y un inquilino AAD específico. ¿Cómo puedo hacer?

1. Vaya al [portal clásica Azure][lnk-classic-portal], haga clic en **configuración** en la lista de servicios en el lado izquierdo.

2. Seleccione la suscripción que desea cambiar la asignación de directorio.

3. Haga clic en **Editar directorio**.

4. Seleccione el **directorio** que desea usar en la lista desplegable. Haga clic en la flecha hacia adelante.

5. Confirmar la asignación del directorio y afectadas coadministradores. Tenga en cuenta que si se mueve desde otro directorio, se quitan todos los coadministradores desde el directorio original.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Soy un usuario o miembro de dominio en el inquilino AAD y que he creado una solución preconfigurada. ¿Cómo obtener asignar un rol para mi aplicación?

Pida al administrador global que le asigne como administrador global en el inquilino de AAD para obtener los permisos para asignar roles a los usuarios o pida a un administrador global para asignar un rol. Si desea cambiar al inquilino AAD la solución preconfigurada se ha implementado para ver la siguiente pregunta.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>¿Cómo puedo cambiar el inquilino AAD que se asignan a mi solución preconfigurada de supervisión y la aplicación?

Puede ejecutar una implementación de nube de <https://github.com/Azure/azure-iot-remote-monitoring> y volver a implementar con un inquilino AAD recién creado. Dado que es un administrador global de forma predeterminada al crear un nuevo inquilino AAD, tendrá acceso para agregar usuarios y asignar roles a esos usuarios.

1. Crear un nuevo directorio AAD en el [portal de administración de Azure][lnk-classic-portal].

2. Vaya a <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Ejecutar `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por ejemplo, `build.cmd cloud debug myRMSolution`)

4. Cuando se le pida, establezca el **tenantid** ser el inquilino recién creado en lugar de su inquilino anterior.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Deseo cambiar un administrador del servicio o un compañero administrador cuando inicie sesión con una cuenta de organización

Vea el artículo de soporte técnico [cambiar administrador del servicio y compañeros cuando inicie sesión con una cuenta de organización][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>¿Por qué estoy viendo este error? "La cuenta no tiene los permisos adecuados para crear una solución. Por favor, póngase en contacto con el Administrador de la cuenta o probar con una cuenta diferente."

Eche un vistazo al diagrama siguiente:

![][img-flowchart]

> [AZURE.NOTE] Si sigue viendo el error después de validar son un administrador global en el inquilino AAD y un administrador de coautoría en la suscripción, el Administrador de la cuenta eliminar el usuario y volver a asignar los permisos necesarios en este orden: agregar al usuario como administrador global y, a continuación, agregar usuario como administrador de coautoría en la suscripción de Azure. Si los problemas continúan, póngase en contacto con [Ayuda y soporte técnico][lnk-help-support].

**¿Por qué estoy viendo este error cuando tenga una suscripción de Azure?** *Se necesita una suscripción de Azure para crear soluciones preconfiguradas. Puede crear una cuenta de prueba gratuita de dos minutos.*

Si está seguro de que tiene una suscripción a Azure validar al inquilino de asignación para la suscripción y asegúrese de que está seleccionado el inquilino correcto en la lista desplegable. Si ha valida el inquilino deseado es correcto, siga el diagrama anterior y validar la asignación de la suscripción y este inquilino AAD.

## <a name="next-steps"></a>Pasos siguientes

Para continuar aprendiendo sobre IoT Suite, consulte cómo puede [Personalizar una solución preconfigurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
