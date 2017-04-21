<properties
    pageTitle="Administrar el acceso a los análisis de registro | Microsoft Azure"
    description="Administrar el acceso a análisis de registro con una gran variedad de tareas administrativas en los usuarios, cuentas, áreas de trabajo OMS y cuentas de Azure."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>

# <a name="manage-access-to-log-analytics"></a>Administrar el acceso a los análisis de registro

Para administrar el acceso a los análisis de registro, deberá usar una gran variedad de tareas administrativas en los usuarios, cuentas, áreas de trabajo OMS y cuentas de Azure. Para crear un área de trabajo en el conjunto de administración de operaciones (OMS), elija un nombre de área de trabajo, asociar con su cuenta y, a continuación, elija una ubicación geográfica. Un área de trabajo es, esencialmente, un contenedor que incluye información de cuenta y configuración simple para la cuenta. Usted u otros miembros de su organización pueden usar varias áreas de trabajo OMS para administrar diferentes conjuntos de datos que se recopilan de todas o partes de la infraestructura de TI.

El artículo [Introducción a análisis de registro](log-analytics-get-started.md) muestra cómo obtener rápidamente hacia arriba y ejecutando y el resto de este artículo se describen con más detalle algunas de las acciones que se debe administrar el acceso a OMS.

Aunque no debe llevar a cabo cada tarea de administración en primer lugar, trataremos todas las tareas utilizadas con frecuencia que puede usar en las secciones siguientes:

- Determinar el número de áreas de trabajo que necesita
- Administrar las cuentas y los usuarios
- Agregar un grupo a un área de trabajo existente
- Vincular un área de trabajo existente a una suscripción de Azure
- Actualizar un área de trabajo a un plan de datos de pago
- Cambiar un tipo de plan de datos
- Agregar una organización de Azure Active Directory a un área de trabajo existente
- Cerrar el área de trabajo OMS

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar el número de áreas de trabajo que necesita

Un área de trabajo es un recurso de Azure y un contenedor donde datos se recopilan, agregados, analizar y realizadas en el portal OMS.

Es posible crear varias áreas de trabajo de análisis de registro de OMS y los usuarios tengan acceso a una o varias áreas de trabajo. En general desea reducir el número de áreas de trabajo como esto le permitirá la consulta y relacionar a través de la mayoría de los datos. Esta sección describe cuándo puede ser útil crear varias áreas de trabajo.

En la actualidad, proporciona un área de trabajo de análisis de registro:

- Una ubicación geográfica para el almacenamiento de datos
- Detalle de facturación
- Aislamiento de datos

En función de las características anteriores, es aconsejable crear varias áreas de trabajo si:

- Es una empresa global y necesita los datos almacenados en regiones específicas por motivos de soberanía o cumplimiento de datos.
- Uso de Azure y desea evitar gastos de transferencia saliente al tener un área de trabajo de análisis de registro de la misma región como los recursos de Azure administra.
- Desea asignar los gastos a los diferentes departamentos o grupos de negocio en función de su uso. Al crear un área de trabajo para cada departamento o grupo de empresa, la instrucción de factura y el uso de Azure muestra los gastos de cada área de trabajo por separado.
- Es un proveedor de servicios administrados y necesita para mantener los datos de análisis de registro de cada cliente administrar aislados de los datos de otro cliente.
- Administrar a varios clientes y quiere que cada cliente o departamento o grupo de empresa para ver sus propios datos, pero no los datos de otros clientes o departamentos o grupos de la empresa.

Al utilizar a agentes para recopilar datos, puede configurar cada agente para informar al área de trabajo necesario.

Si está utilizando System Center Operations manager, cada grupo de administración de Operations Manager se pueden conectar con solo un área de trabajo. Puede instalar al agente de supervisión de Microsoft en equipos administrados por Operations Manager y que el informe de agente Operations Manager y otra área de trabajo de análisis de registro.

### <a name="workspace-information"></a>Información de área de trabajo

En el portal OMS, puede ver la información del área de trabajo y elija si desea recibir información de Microsoft.

#### <a name="view-workspace-information"></a>Visualizar la información del área de trabajo

1. OMS, haga clic en el icono **configuración** .
2. Haga clic en la pestaña **cuentas** .
3. Haga clic en la ficha **Información del área de trabajo** .  
  ![Información de área de trabajo](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Administrar las cuentas y los usuarios

Cada área de trabajo puede tener varias cuentas de usuario asociadas, y cada cuenta de usuario (cuenta de Microsoft o profesional) puede tener acceso a varias áreas de trabajo OMS.

De forma predeterminada, la cuenta de Microsoft o profesional utilizados para crear el área de trabajo se convierte en el administrador del área de trabajo. El administrador puede invitar a otras cuentas de Microsoft o seleccione los usuarios de Azure Active Directory.

Conceder acceso de usuarios al área de trabajo OMS está controlado en 2 decimales:

- En Azure, puede usar el control de acceso basado en roles para proporcionar acceso a la suscripción de Azure y los recursos asociados de Azure. También se usa para el acceso de PowerShell y API de REST.
- En el portal OMS, acceder a solo el portal de OMS - no la suscripción de Azure asociada.

Si se conceden a los usuarios acceso al portal de OMS pero no a la suscripción de Azure que está vinculado a, a continuación, los mosaicos de solución de automatización, copia de seguridad y recuperación del sitio no muestran los datos a los usuarios al que inicio de sesión en el portal OMS.

Para permitir que todos los usuarios ver los datos en estas soluciones, asegúrese de que tengan al menos **lector de** acceso para el depósito de cuenta de automatización, depósito de copia de seguridad y recuperación de sitios que está vinculado al área de trabajo OMS.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Administrar el acceso a los análisis de registro con el portal de Azure

Si conceder acceso a los usuarios al área de trabajo de análisis de registro con permisos de Azure, en el portal de Azure, por ejemplo, a continuación, los mismos usuarios pueden acceder al registro análisis portal. Si los usuarios en el portal de Azure, puede navegar hasta el portal OMS haciendo clic en la tarea de **OMS Portal** al ver el recurso de área de trabajo de análisis de registro.

Aspectos a tener en cuenta sobre el portal de Azure:

- No es *el Control de acceso basado en roles*. Si tiene permisos de acceso de *lectura* en el portal de Azure del área de trabajo de análisis de registro, a continuación, puede realizar cambios con el portal OMS. El portal OMS incluye el concepto de administrador, Colaborador y usuario de sólo lectura. Si la cuenta que ha iniciado sesión en con en Azure Active Directory vinculado al área de trabajo será un administrador en el portal OMS, en caso contrario, será un colaborador.

- Cuando inicie sesión en el portal OMS con http://mms.microsoft.com, a continuación, de forma predeterminada, verá la lista **Seleccionar un área de trabajo** . Solo contiene áreas de trabajo que se agregaron a través del portal OMS. Para ver las áreas de trabajo que tiene acceso con suscripciones a Azure, a continuación, debe especificar a un inquilino como parte de la dirección URL. Por ejemplo:

  `mms.microsoft.com/?tenant=contoso.com`El identificador del inquilino suele ser la última parte de la dirección de correo electrónico iniciar sesión con.

- Si la cuenta que iniciar sesión con una cuenta en el inquilino Azure Active Directory, que normalmente es el caso, a menos que esté iniciar sesión en como un CSP, será un *Administrador* en el portal OMS. Si su cuenta no está en el inquilino Azure Active Directory, será un *usuario* en el portal OMS.

- Si desea ir directamente a un portal que tiene acceso a utilizar permisos de Azure, debe especificar el recurso como parte de la dirección URL. Es posible obtener esta dirección URL con PowerShell.

  Por ejemplo, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  Tendrá un aspecto similar a la dirección URL:`https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### <a name="managing-users-in-the-oms-portal"></a>Administrar usuarios en el portal OMS

Administrar usuarios y grupos en la pestaña **Administrar usuarios** en la ficha **cuentas** en la página de configuración. Allí, puede realizar las tareas en las secciones siguientes.  

![Administrar usuarios](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Agregar un usuario a un área de trabajo existente

Use los siguientes pasos para agregar un usuario o grupo a un área de trabajo OMS. El usuario o grupo podrán ver y actuar en todas las alertas que están asociadas a esta área de trabajo.

>[AZURE.NOTE] Si desea agregar un usuario o grupo de la cuenta de la organización de Azure Active Directory, primero debe asegurarse de que su cuenta OMS ha asociado con su dominio de Active Directory. Consulte [Agregar una organización de Azure Active Directory a un área de trabajo existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. OMS, haga clic en el icono **configuración** .
2. Haga clic en la pestaña **cuentas** y, a continuación, haga clic en la pestaña **Administrar usuarios** .
3. En la sección **Administrar usuarios** , elija el tipo de cuenta para agregar: **Cuenta profesional**, **Cuenta de Microsoft**, **Soporte técnico de Microsoft**.
    - Si elige Microsoft Account, escriba la dirección de correo electrónico del usuario asociado con el Account de Microsoft.
    - Si elige cuenta profesional, introduzca parte del usuario o el alias de correo electrónico o de nombre del grupo y aparecerá una lista de usuarios y grupos. Seleccione un usuario o grupo.
    - Usar Microsoft Support para dar a un Support Microsoft ingeniería acceso temporal al área de trabajo para ayudar a solucionar problemas.

    >[AZURE.NOTE] Para obtener los mejores resultados de rendimiento, limitar el número de grupos de Active Directory asociados a una sola cuenta OMS a tres: uno para los administradores, uno para colaboradores y otro para los usuarios de solo lectura. Uso de los grupos más puede afectar al rendimiento de análisis de registro.

5. Elija el tipo de usuario o grupo: **Administrador**, **Colaborador**o **Usuario de sólo lectura** .  
6. Haga clic en **Agregar**.

  Si va a agregar una cuenta de Microsoft, se envía una invitación para participar en el área de trabajo para el correo electrónico que ha proporcionado. Después de que el usuario sigue las instrucciones de la invitación para unirse a OMS, el usuario puede ver las alertas y la información de cuenta para esta cuenta OMS y podrá ver la información de usuario en la pestaña **cuentas** de la página de **configuración** .
  Si va a agregar una cuenta de la organización, el usuario podrá acceso inmediato a análisis de registro.  
  ![correo electrónico de invitación](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>Editar un tipo de usuario existente

Puede cambiar el rol de cuenta para un usuario asociado a su cuenta OMS. Tiene las siguientes opciones de rol:

 - *Administrador*: puede administrar los usuarios, ver y actuar en todas las alertas y agregar y quitar servidores

 - *Colaborador*: puede ver y actuar en todas las alertas y agregar y quitar servidores

 - *Usuario de sólo lectura*: marcados como de solo lectura de los usuarios no podrán para:
   1. Agregar o quitar soluciones. Se oculta la Galería de soluciones.
   2. Agregar, modificar o eliminar los mosaicos en **Mi panel**.
   3. Ver las páginas de **configuración** . Las páginas están ocultas.
   4. En la búsqueda vista, PowerBI configuración, búsquedas guardadas y las alertas de las tareas están ocultas.


#### <a name="to-edit-an-account"></a>Para editar una cuenta

1. OMS, haga clic en el icono **configuración** .
2. Haga clic en la pestaña **cuentas** y, a continuación, haga clic en la pestaña **Administrar usuarios** .
3. Seleccione el rol para el usuario que desea cambiar.
2. En el cuadro de diálogo de confirmación, haga clic en **Sí**.

### <a name="remove-a-user-from-a-oms-workspace"></a>Quitar un usuario de un área de trabajo OMS

Use los siguientes pasos para quitar un usuario de un área de trabajo OMS. Tenga en cuenta que no se cierre área de trabajo del usuario. En su lugar, se quita la asociación entre ese usuario y el área de trabajo. Si un usuario está asociado a varias áreas de trabajo, ese usuario seguirá poder iniciar sesión en OMS y ver las otras áreas de trabajo.

1. OMS, haga clic en el icono **configuración** .
2. Haga clic en la pestaña **cuentas** y, a continuación, haga clic en la pestaña **Administrar usuarios** .
3. Haga clic en **Quitar** junto al nombre de usuario que desea quitar.
4. En el cuadro de diálogo de confirmación, haga clic en **Sí**.


### <a name="add-a-group-to-an-existing-workspace"></a>Agregar un grupo a un área de trabajo existente

1.  Siga los pasos 1 -4 de "To agregar un usuario a un área de trabajo existente", más arriba.
2.  En **Elija usuario o grupo**, seleccione el **grupo**.
    ![Agregar un grupo a un área de trabajo existente](./media/log-analytics-manage-access/add-group.png)
3.  Escriba la dirección de correo electrónico o de nombre para mostrar para el grupo que desea agregar.
4.  Seleccione el grupo en los resultados de la lista y, a continuación, haga clic en **Agregar**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Vincular un área de trabajo existente a una suscripción de Azure

Es posible crear un área de trabajo desde el sitio Web de [microsoft.com/oms](https://microsoft.com/oms) .  Sin embargo, determinados límites existen para estas áreas de trabajo, más notables ser un límite de 500MB por día de cargas de datos si usa una cuenta gratuita. Para realizar cambios en esta área de trabajo debe *vincular el área de trabajo existente a una suscripción de Azure*.

>[AZURE.IMPORTANT] Para vincular a un área de trabajo, su cuenta de Azure ya debe tener acceso al área de trabajo que desea vincular.  En otras palabras, la cuenta que utiliza para acceder al portal de Azure debe ser **la misma** que la cuenta que use para tener acceso a su área de trabajo OMS. Si no es el caso, vea [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Vincular un área de trabajo a una suscripción de Azure en el portal OMS

Para vincular un área de trabajo a una suscripción de Azure en el portal OMS, el usuario que ha iniciado sesión ya debe tener una cuenta de Azure pagada. El área de trabajo que actualmente está usando se vincula a la cuenta de Azure.

1. OMS, haga clic en el icono **configuración** .
2. Haga clic en la pestaña **cuentas** y, a continuación, haga clic en la pestaña **Azure suscripción y el Plan de datos** .
3. Haga clic en el plan de datos que desee usar.
4. Haga clic en **Guardar**.  
  ![planes de suscripción y datos](./media/log-analytics-manage-access/subscription-tab.png)

El nuevo plan de datos se muestra en la cinta de opciones de portal OMS en la parte superior de la página web.

![Cinta de opciones OMS](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Vincular un área de trabajo a una suscripción de Azure en el portal de Azure

1.  Iniciar sesión en el [portal de Azure](http://portal.azure.com).
2.  Busque el **Registro de análisis (OMS)** y selecciónelo.
3.  Verá su lista de áreas de trabajo existentes. Haga clic en **Agregar**.  
    ![lista de áreas de trabajo](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  En **El área de trabajo de OMS**, haga clic en **o vincular a existente**.  
    ![Vincular a existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Haga clic en **Configurar la configuración necesaria**.  
    ![configurar opciones necesarias](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Verá la lista de áreas de trabajo que no se ha vinculado a su cuenta de Azure. Seleccione un área de trabajo.  
    ![Seleccione áreas de trabajo](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Si es necesario, puede cambiar los valores de los elementos siguientes:
    - Suscripción
    - Grupo de recursos
    - Ubicación
    - Nivel de precios  
        ![cambiar los valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Haga clic en **crear**. El área de trabajo se vincula a su cuenta de Azure.

>[AZURE.NOTE] Si no ve el área de trabajo que desea vincular, la suscripción de Azure no tiene acceso al área de trabajo OMS que creó mediante el sitio Web de OMS.  Debe conceder acceso a esta cuenta desde dentro de su área de trabajo OMS mediante el sitio web OMS. Para ello, consulte [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).



## <a name="upgrade-a-workspace-to-a-paid-data-plan"></a>Actualizar un área de trabajo a un plan de datos de pago

Hay tres datos del área de trabajo plan tipos para OMS: **libre**, **estándar**y **Premium**.  Si está usando un plan *libre* , puede haber alcanzado el límite de datos de 500 MB.  Debe actualizar el área de trabajo a un ***plan de pago*** para recopilar datos excedan ese límite. En cualquier momento puede convertir al tipo de plan.  Para obtener más información sobre precios de OMS, consulte [Detalles de precios](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Sólo se pueden cambiar planes de área de trabajo si están *vinculadas* a una suscripción de Azure.  Si creó el área de trabajo en Azure o si ha había vinculado *ya* el área de trabajo, puede omitir este mensaje.  Si creó el área de trabajo el [sitio web OMS](http://www.microsoft.com/oms), debe seguir los pasos descritos en el [vínculo de un área de trabajo existente a una suscripción de Azure](#link-an-existing-workspace-to-an-azure-subscription).

### <a name="using-entitlements-from-the-oms-add-on-for-system-center"></a>Uso de los derechos desde el complemento de OMS de System Center

El complemento de OMS de System Center proporciona un derecho para el plan de Premium de análisis de registro de OMS, se describe en [OMS precios](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

Si compra el complemento OMS de System Center, el complemento OMS se agrega como un derecho en el acuerdo de System Center. Puede realizar ninguna suscripción Azure que se crea en este acuerdo de uso de los derechos. Esto le permite, por ejemplo, tiene varias áreas de trabajo OMS que usan el derecho desde el complemento OMS.

Para asegurarse de que el uso de un área de trabajo OMS se aplica a sus derechos desde el complemento OMS, tendrá que:

1. Vincular el área de trabajo OMS a una suscripción de Azure que forma parte del contrato Enterprise que incluye la compra de complemento OMS y el uso de la suscripción de Azure
2. Seleccione el plan de Premium del área de trabajo

Al revisar su uso en el portal de Azure o OMS, no verá los derechos de complemento OMS. Sin embargo, puede ver los derechos en el Portal de empresa.  

Si necesita cambiar la suscripción de Azure que está vinculado el área de trabajo OMS, puede usar el cmdlet de PowerShell de Azure [AzureRmResource mover](https://msdn.microsoft.com/library/mt652516.aspx) .

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Uso de Azure compromiso de un contrato Enterprise

Si decide usar independiente precios de componentes OMS, pagará por separado para cada componente de OMS y el uso aparecerá en su factura de Azure.

Si tiene una confirmación monetaria Azure en la inscripción de empresa a los que están vinculados sus suscripciones Azure, cualquier uso de análisis de registro se débito automáticamente contra cualquier confirmación monetario restante.

Si necesita cambiar la suscripción de Azure que el área de trabajo OMS está vinculado a los puede usar el cmdlet de PowerShell de Azure [AzureRmResource mover](https://msdn.microsoft.com/library/mt652516.aspx) .  



### <a name="to-change-a-workspace-to-a-paid-data-plan"></a>Para cambiar un área de trabajo a un plan de datos de pago

1.  Iniciar sesión en el [portal de Azure](http://portal.azure.com).
2.  Busque el **Registro de análisis (OMS)** y selecciónelo.
3.  Verá su lista de áreas de trabajo existentes. Seleccione un área de trabajo.  
    ![lista de áreas de trabajo](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  En **configuración**, haga clic en **el nivel de precios**.  
    ![nivel de precios](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  En **el nivel de precios**, seleccione un plan de datos y, a continuación, haga clic en **Seleccionar**.  
    ![Seleccione el plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Al actualizar la vista en el portal de Azure, verá el **nivel de precios** actualizada para el plan que ha seleccionado.  
    ![actualizar el nivel de precios](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Ahora puede recopilar datos más allá de la capital de datos "libres".


## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Agregar una organización de Azure Active Directory a un área de trabajo existente

Puede asociar el área de trabajo de análisis de registro (OMS) con un dominio de Active Directory de Azure. Esto le permite agregar usuarios desde Active Directory directamente en el área de trabajo OMS sin necesidad de tener una cuenta de Microsoft independiente.

Al crear el área de trabajo desde el portal de Azure o vincular el área de trabajo a una suscripción de Azure Azure Active Directory estará vinculado como su cuenta de la organización.

Al crear el área de trabajo desde el portal OMS que se le pedirá que vincular a una suscripción de Azure y una cuenta profesional.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Para agregar una organización de Azure Active Directory a un área de trabajo existente

1. En la página Configuración del OMS, haga clic en **cuentas** y, a continuación, haga clic en la ficha **Información del área de trabajo** .  
2. Revise la información sobre cuentas de la organización y, a continuación, haga clic en **Agregar organización**.  
    ![agregar organización](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Escriba la información de identidad para el Administrador de su dominio de Active Directory de Azure. Cuando termine, verá una confirmación que indica que el área de trabajo está vinculado a su dominio de Active Directory de Azure.
    ![confirmación de área de trabajo vinculado](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] Una vez que su cuenta está vinculada a una cuenta profesional, vinculación no puede quitar o cambiar.

## <a name="close-your-oms-workspace"></a>Cerrar el área de trabajo OMS

Cuando se cierra un área de trabajo OMS, se elimina todos los datos relacionados con el área de trabajo del servicio de OMS dentro de 30 días después de cerrar el área de trabajo.

Si es un administrador y hay varios usuarios asociados con el área de trabajo, se rompe la asociación entre los usuarios y el área de trabajo. Si los usuarios están asociados a otras áreas de trabajo, a continuación, puede seguir utilizando OMS con las otras áreas de trabajo. Sin embargo, si no están asociadas a otras áreas de trabajo, a continuación, necesitará crear un área de trabajo para usar OMS.

### <a name="to-close-an-oms-workspace"></a>Para cerrar un área de trabajo OMS

1. OMS, haga clic en el icono **configuración** .
2. Haga clic en la pestaña **cuentas** y, a continuación, haga clic en la ficha **Información del área de trabajo** .
3. Haga clic en **Cerrar área de trabajo**.
4. Seleccione una de las razones para cerrar el área de trabajo, o escriba un motivo diferente en el cuadro de texto.
5. Haga clic en **Cerrar área de trabajo**.

## <a name="next-steps"></a>Pasos siguientes

- Ver [equipos con Windows conectarse al análisis de registro](log-analytics-windows-agents.md) para agregar a agentes y recopilación de datos.
- [Soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad y recopilación de datos.
- [Establecer la configuración del servidor proxy y el firewall de análisis de registro](log-analytics-proxy-firewall.md) si su organización usa un servidor proxy o firewall para que los agentes puedan comunicarse con el servicio de análisis de registro.
