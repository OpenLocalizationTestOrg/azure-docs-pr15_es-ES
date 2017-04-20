<properties
    pageTitle="¿Qué es la licencia de Microsoft Azure Active Directory? | Microsoft Azure"
    description="Descripción de las licencias de Microsoft Azure Active Directory, cómo funciona, cómo empezar a trabajar y mejores prácticas, incluyendo Office 365, Microsoft Intune y Azure Active Directory Premium y las ediciones básicas"
    services="active-directory"
      keywords="Licencias de Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>¿Qué es la licencia de Microsoft Azure Active Directory?

##<a name="description"></a>Descripción
Azure Active Directory (AD Azure) es la identidad de Microsoft como una solución de servicio (IDaaS) y la plataforma. Azure AD se ofrece en un número de versiones funcionales y técnicas comprendido entre Azure libre de AD, que está disponible con los servicios de Microsoft, como Office 365, Dynamics, Microsoft Intune y Azure (Azure AD no genera los gastos de consumo en este modo), para Azure AD pagado versiones como conjunto de aplicaciones de movilidad de empresa (EMS), Azure AD Premium y Basic, así como la autenticación multifactor de Azure (AMF). Como muchos de los servicios en línea de Microsoft, versiones de AD pagado más Azure se entregarán a través de los derechos de cada usuario que aparecen en Office 365, Microsoft Intune y Azure AD. En estos casos, la compra de servicio se representa con una o más suscripciones y cada suscripción incluye un número de preventa de licencias de su inquilino. Los derechos de cada usuario se consiguen a través de la asignación de licencias, crear un vínculo entre el usuario y el producto, habilitar los componentes del servicio para el usuario y a continuación, utilizar una de las licencias de prepagadas.

[Intente premium Azure AD ahora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portal de administración de Azure AD es una parte del portal clásica Azure. Mientras usa Azure AD no requiere cualquier compras Azure, acceso a este portal requiere una suscripción activa a Azure o una [suscripción de prueba de Azure](https://azure.microsoft.com/pricing/free-trial/).

Para ver una introducción amplia de capacidades de servicio de Azure AD, consulte [¿Qué es Azure AD](active-directory-whatis.md).
[Obtenga más información sobre los niveles de servicio de Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Suscripciones de Azure retenidos son diferentes: mientras también se representan en el directorio, estas suscripciones habilitar la creación de Azure recursos y asignarlos a su método de pago. En este caso, no hay ningún recuentos de licencia asociadas con la suscripción. Asociación de los usuarios con la suscripción, acceso de los usuarios a administrar recursos de la suscripción, se logra concederles permisos para que funcione en Azure recursos asignados a la suscripción.


##<a name="how-does-azure-ad-licensing-work"></a>¿Cómo funciona Azure AD licencias?

Basada en licencias (basada en derecho) Azure AD servicios trabajo activando una suscripción de su inquilino de Azure AD o servicio de directorio. Una vez que la suscripción está activa las capacidades de servicio pueden administrar los administradores de servicio de directorio/y usadas por los usuarios con licencia.

Cuando se compra o activar Enterprise movilidad Suite, Azure AD Premium o básica de Azure AD, el directorio se actualizará con la suscripción, incluida su período de validez y licencias de prepagados. La información de la suscripción, incluido el estado, el siguiente evento de ciclo de vida y el número de licencias asignadas o disponibles está disponible a través del portal clásico Azure en la pestaña de licencias para el directorio específico. También es mejor lugar para administrar las asignaciones de licencia.

Cada suscripción consta de uno o varios planes de servicio, cada una asignación el nivel incluye funcional del tipo de servicio; Por ejemplo, Azure AD, AMF Azure, Microsoft Intune, Exchange Online o SharePoint Online. Administración de licencias de AD Azure no requiere administración nivel del plan de servicio. Esto es diferente de Office 365, que se basa en este modo de configuración avanzada para administrar el acceso a servicios incluidos. Azure AD depende de configuración del servicio, para habilitar las características y administrar permisos individuales.

En general, la información de la suscripción de Azure AD se administra a través del portal clásico Azure, en la pestaña de licencias para el directorio específico. Suscripciones a Azure AD, con la excepción de Azure AD Premium, no se muestran en el portal de Office.

> [AZURE.IMPORTANT] Azure AD Premium y Basic, así como las suscripciones de serie de movilidad para empresas, se limita a su directorio aprovisiona/inquilino. No se pueden dividir entre directorios ni usadas para dar derecho a los usuarios de otros directorios suscripciones. Mover una suscripción entre directorios es posible, pero requiere el envío de una incidencia de soporte técnico o cancelación y volver a comprar en el caso de compras directas.

> Al comprar Azure AD o serie de movilidad empresarial a través de licencias por volumen de activación de la suscripción se producirá automáticamente cuando el contrato incluye otros servicios de Microsoft Online, por ejemplo, Office 365.

Pagado Azure características de AD que abarque el alcance del directorio. Algunos ejemplos:
- Grupo asignación basada en aplicaciones, que está habilitada en la aplicación específica que se administra.
- Avanzada y las capacidades de administración de grupo de autoservicio están disponibles en la configuración del directorio o dentro del grupo específico.
- Informes de seguridad de Premium se encuentran en la pestaña informes
- Detección de aplicación de nube se muestra en el portal de Azure en la identidad.

###<a name="assigning-licenses"></a>Asignación de licencias
Mientras la obtención de una suscripción es todo lo que necesita para configurar las capacidades de pagadas, usando su Azure AD pagado características requiere distribuir licencias a las personas adecuadas. En general, cualquier usuario que debería tener acceso a o que se administra mediante un anuncio Azure pagado característica debe asignarse una licencia. Una asignación de licencias es una asignación entre un usuario y un servicio comprado, como Azure AD Premium, Basic o Enterprise movilidad Suite.

Administrar los usuarios que en el directorio deben tener una licencia es sencillo. Es posible asignar a un grupo para crear reglas de asignación a través del portal de administración de Azure AD o asignando licencias directamente a las personas a través de un portal, PowerShell o API adecuadas. Al asignar licencias a un grupo, todos los miembros del grupo se asignará una licencia. Si los usuarios agregan o quitar del grupo que va a asignar o quitan la licencia correspondiente. Asignación de grupo puede utilizar cualquier disponibles de administración de grupo y es coherente con asignación basada en el grupo para las aplicaciones. Con este método, puede establecer reglas de forma que se asignan automáticamente todos los usuarios en el directorio, asegúrese de que todos los usuarios con el puesto correspondiente tienen una licencia o incluso delegar la decisión de otros administradores de la organización.

Con la asignación de licencias basadas en grupos, cualquier usuario falta una ubicación de uso heredarán la ubicación del directorio durante la asignación. El administrador puede cambiar esta ubicación en cualquier momento. En casos donde la asignación automatizada error debido a un error, la información de usuario en ese tipo de licencia reflejará dicho estado.

##<a name="getting-started-with-azure-ad-licensing"></a>Introducción con licencia de Azure AD

Introducción a Azure AD es fácil; siempre puede crear su directorio como parte de suscribirse a una prueba de Azure gratuita. [Obtenga más información acerca de la firma como una organización](sign-up-organization.md). La siguiente puede ayudarle a Asegúrese de que el directorio se alinea mejor con otros servicios de Microsoft que puede ser consumo o va a consumir y los objetivos para obtener el servicio.

Estos son algunos de los procedimientos recomendados:
- Si ya está usando cualquiera de los servicios de organización de Microsoft, ya tiene un directorio de Azure AD. En este caso, debe continuar usar el mismo directorio para otros servicios para que se puede utilizar la administración de identidades core, incluidos el aprovisionamiento y híbrido, SSO, a través de los servicios. Los usuarios tendrán una experiencia de inicio de sesión único y le vendrá de capacidades más eficaces a través de los servicios. Como resultado, si decide comprar un anuncio Azure servicio para su plantilla de pago, se recomienda que use el mismo directorio para hacer esto.
- Si planea usar Azure AD para un conjunto diferente de los usuarios (socios, clientes y así sucesivamente), o si se desea evaluar los servicios de Azure AD y le gustaría hacerlo en aislamiento de su servicio de producción, o si está buscando para configurar un entorno de recinto de seguridad de los servicios, le recomendamos primero crear un nuevo directorio a través del portal de Azure Azure clásico. [Obtener más información sobre cómo crear un nuevo directorio de Azure AD en el portal de clásico Azure](active-directory-licensing-directory-independence.md). El nuevo directorio se creará con su cuenta como un usuario externo con permisos de administrador global. Cuando inicie sesión en el portal de Azure clásico con esta cuenta, podrá ver este directorio y tener acceso a todas las tareas de administración de directorio. Le recomendamos que cree una cuenta local con privilegios adecuados para administrar otros servicios de Microsoft (los que no puede acceder a través del portal clásico Azure). [Obtenga más información sobre cómo crear cuentas de usuario en Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD admite "usuarios externos", que son las cuentas de usuario en una instancia de Azure AD que se crearon usando una cuenta de Microsoft (MSA) o una identidad de Azure AD desde otro directorio. Mientras se está ocupados ampliar esta capacidad en todos los servicios de organización de Microsoft, ahora estas cuentas no son compatibles con algunas de las experiencias de los servicios; Por ejemplo, el portal de administración de Office 365 no admite actualmente estos usuarios. Como resultado, los usuarios externos con cuentas de Microsoft no podrá tener acceso al portal de administración de Office 365, mientras que los usuarios externos de otros directorios de Azure AD se omitirá. En este caso, solo la cuenta de usuario local, Azure AD o directorio de Office 365 donde el usuario se creó originalmente, sería accesible a través de estas experiencias.

Como se indica, Azure AD tiene diferentes versiones de pagadas. Estas versiones tienen pequeñas diferencias en su disponibilidad de compra:


| Producto   | LICENCIAS POR VOLUMEN Y EA     | Abrir  |   CSP |   Derechos de uso MPN  |   Compra directa | Versión de prueba |
|---|---|---|---|---|---|---|
| Serie de movilidad para empresas |   X | X | X | X |  |      X |
| Premium de Azure AD  | X | X | X |   | X | X |
| Basic de Azure AD    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Seleccione una o varias pruebas de licencia
 En todos los casos, puede activar una suscripción de prueba de Azure AD Premium o Enterprise movilidad Suite seleccionando la versión de prueba específico que desee en la ficha licencias en el directorio. Cualquier versión de prueba contiene una suscripción de 30 días con 100 licencias.

![Planes de licencia de prueba de Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Planes de licencia de prueba de Enterprise movilidad Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Planes de licencia de prueba activa](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Asignar licencias
Una vez que la suscripción está activa, debe asignarse una licencia y actualice el explorador para asegurarse de que está viendo todas sus características. El siguiente paso es asignar licencias a los usuarios que tendrán que tener acceso a o incluirse en características de Azure AD de pago. Como se mencionó anteriormente en "Asignar licencias", la mejor manera de hacerlo es identificar el grupo que representa la audiencia que desee y asignarlo a la licencia. de este modo, se asignarse a los usuarios que se agregan o se quitan del grupo a través de su ciclo de vida o quitados de la licencia.

Para asignar una licencia a un grupo o los usuarios individuales, seleccione el plan de licencia que desea asignar y haga clic en **asignar** en la barra de comandos.

![Planes de licencia de prueba activa](./media/active-directory-licensing-what-is/assign_licenses.png)

Una vez en el cuadro de diálogo de la asignación para el plan seleccionado, puede seleccionar usuarios y agregarlos a la columna **asignar** a la derecha. Puede desplazarse por la lista de usuarios o buscar cristal concretas mediante la búsqueda en la parte superior derecha de la cuadrícula de usuario. Para asignar a grupos, seleccione "Grupos" en el menú **Mostrar** y, a continuación, haga clic en el botón de verificación a la derecha para actualizar las asignaciones que se muestran.

![Asignar licencias a grupos](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Ahora puede buscar o desplácese por grupos y agregarlos a la columna **asignar** de la misma manera. Puede usar estas para asignar una combinación de usuarios y grupos en una única operación. Para completar el proceso de asignación, haga clic en el botón de verificación en la esquina inferior derecha de la página.

![Mensaje de progreso de asignación de licencias](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Cuando se asigna un grupo, sus miembros heredan las licencias dentro de 30 minutos, pero normalmente en unos minutos de 1 y 2.

Errores de asignación se pueden producir durante la asignación de licencias de Azure AD, pero son poco. Se limitan a posibles errores de asignación:
- Conflicto de asignación - cuando un usuario previamente se ha asignado una licencia que no es compatible con la licencia actual. En este caso, asignar la nueva licencia necesitarán quitar anterior.
- Si se excede licencias disponibles - cuando el número de usuarios en grupos asignados supera licencias disponibles, estado de la asignación de los usuarios reflejará un error al asignar debido a que faltan licencias.

###<a name="view-assigned-licenses"></a>Ver asignar licencias

Una vista de resumen de licencias asignadas, incluidos los eventos de ciclo de vida de suscripción disponibles, asignadas y siguiente se muestran en la pestaña de **licencias** .

![Ver el número de licencias asignadas](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Una lista detallada de los usuarios y grupos asignados, incluidos el estado de la asignación y la ruta de acceso (directa o heredado de uno o varios grupos) está disponible cuando desplazarse en un plan de licencia.

![Visualización de detalle de licencias asignadas para un plan de licencia](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Quitar licencias es tan fácil como asignarlos. Si el usuario se asigna directamente o para un grupo asignado, puede quitar la licencia, seleccionar el tipo de licencia, seleccionando **Quitar**, agregar el usuario o grupo a la lista de quitar y confirmar la acción. Como alternativa, puede abrir un tipo de licencia, seleccione el usuario o grupo específico y puntee en **Eliminar** en la barra de comandos. Para finalizar la herencia de un usuario de una licencia de un grupo, basta con quitar el usuario del grupo.

###<a name="extending-trials"></a>Ampliar las pruebas

Extensiones de prueba para los clientes están disponibles como autoservicio a través del portal de Office 365. Un administrador del cliente puede desplazarse hasta el [portal de Office](https://portal.office.com/#Billing) (acceso depende de permisos para el portal de Office) y seleccione la versión de prueba de Azure AD Premium. Haga clic en el vínculo **Extender prueba** y siga las instrucciones. Debe introducir una tarjeta de crédito, pero no se cargará.

![Extender una prueba de licencia en el portal de Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Los clientes también pueden solicitar una extensión de prueba al enviar una solicitud de soporte. Un administrador del cliente puede navegar a la Office 365 portal [página de soporte técnico](http://aka.ms/extendAADtrial) (acceso depende de permisos de la página de soporte técnico de Office). En esta página, seleccione "Suscripciones y ensayos" en características y "Preguntas de prueba" en síntoma. Por último, introduzca la información en las circunstancias

![Extender una prueba de licencia con una solicitud de soporte](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora es posible que listo para configurar y utilizar algunas características de Azure AD Premium.

- [Restablecimiento de contraseña de autoservicio](active-directory-manage-passwords.md)
- [Administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)
- [Estado de Azure AD Connect](active-directory-aadconnect-health.md)
- [Asignación de grupo de aplicaciones](active-directory-manage-groups.md)
- [Azure autenticación multifactor](../multi-factor-authentication/multi-factor-authentication.md)
- [Compra directa de licencias de Azure AD Premium](http://aka.ms/buyaadp)
