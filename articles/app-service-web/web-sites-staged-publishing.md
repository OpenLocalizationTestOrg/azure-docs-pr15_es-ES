<properties
    pageTitle="Configurar entornos de web apps en el servicio de aplicación de Azure de ensayo"
    description="Obtenga información sobre cómo usar preconfigurada publicación de web apps en el servicio de aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>Configurar entornos de web apps en el servicio de aplicación de Azure de ensayo
<a name="Overview"></a>

Cuando se implementa la aplicación web en la [Aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714), puede implementar una franja de implementación independiente en lugar de la franja de producción de forma predeterminada cuando se ejecuta en el modo de plan **estándar** o **Premium** aplicación de servicio. Ranuras de implementación son aplicaciones web realmente directo con sus propios nombres de host. Elementos de contenido y las configuraciones de aplicación Web se pueden intercambiar entre dos ranuras de implementación, incluida la franja de producción. Implementar su aplicación en una franja de implementación tiene las siguientes ventajas:

- Para validar los cambios de aplicación web en una franja de implementación provisional antes de intercambiar con la franja de producción.

- Implementar una aplicación web para un espacio en primer lugar e intercambiar en producción asegura que todas las instancias de la franja se caliente antes de que se ha intercambiado en producción. Esto elimina el tiempo de inactividad cuando se implementa la aplicación web. El redireccionamiento de tráfico sea transparente y no se eliminan solicitudes como resultado de operaciones de intercambio. Este flujo de trabajo completo se puede automatizar mediante la configuración [Automática intercambiar](#configure-auto-swap-for-your-web-app) cuando no es necesario intercambiar previa validación.

- Después de un intercambio la franja con la aplicación web previamente preconfigurada ahora tiene la aplicación web de producción anterior. Si los cambios intercambiados en la franja de producción son no tal como se esperaba, puede realizar la misma intercambiar inmediatamente para obtener el "último sitio conocido" volver.

Cada modo de plan de servicio de la aplicación es compatible con un número diferente de ranuras de implementación. Para averiguar el número de ranuras es compatible con el modo de la aplicación web, vea [Tarifas de servicio de aplicación](/pricing/details/app-service/).

- Cuando la aplicación web tiene varias posiciones, no puede cambiar el modo.

- Ajuste de escala no está disponible para las ranuras que no sean de producción.

- Administración de recursos vinculados no se admite para las ranuras que no sean de producción. En el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) solo, puede evitar este impacto potencial en una franja de producción moviendo temporalmente la franja de no producción a otro modo de plan de servicio de aplicación. Tenga en cuenta que la franja de producción no nuevamente debe compartir el mismo modo con la franja de producción antes de que puede intercambiar dos ranuras.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>Agregar un espacio de implementación para una aplicación web ##

La aplicación web debe estar ejecutándose en el modo **estándar** o **Premium** en orden para poder habilitar varias ranuras de implementación.

1. En el [Portal de Azure](https://portal.azure.com/), abra el módulo de la aplicación web.
2. Haga clic en **configuración**y, a continuación, haga clic en **ranuras de implementación**. A continuación, en el módulo **ranuras de implementación** , haga clic en **Agregar franja**.

    ![Agregar un nuevo espacio de implementación][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > Si la aplicación web no está en el modo **estándar** o **Premium** , recibirá un mensaje que indica los modos compatibles para habilitar la publicación de preconfigurada. En este momento, tiene la opción para seleccionar la **actualización** y vaya a la pestaña **escala** de la aplicación web antes de continuar.

2. En el módulo de **Agregar un espacio** , escriba un nombre para la franja y seleccione si desea clonar configuración de aplicación web de otro franja de implementación existente. Haga clic en la marca de verificación para continuar.

    ![Origen de configuración][ConfigurationSource1]

    La primera vez agregue un espacio, solo tendrá dos opciones: configuración de clonar desde la franja de forma predeterminada en producción o no.

    Después de haber creado varias ranuras, podrá Clonar configuración de un espacio distinto de producción:

    ![Orígenes de configuración][MultipleConfigurationSources]

5. En el módulo **ranuras de implementación** , haga clic en la franja de implementación para abrir un módulo para la franja, con un conjunto de métricas y configuración igual que cualquier otra aplicación web. **Your-Web-App-Name-Deployment-slot-Name** aparecerá en la parte superior del módulo para recordarle que está viendo la franja de implementación.

    ![Título de la franja de implementación][StagingTitle]

5. Haga clic en la dirección URL de aplicación en el módulo de la zona. Observe la franja de implementación tiene su propio nombre de host y también es una aplicación directo. Para limitar el acceso del público a la franja de implementación, vea la [Aplicación de servicio Web App: web bloquear el acceso a las casillas de implementación de producción no](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

No hay ningún contenido después de la creación de la franja de implementación. Puede implementar en la franja de una rama diferente o un repositorio totalmente diferente. También puede cambiar la configuración de la zona. Use las credenciales de perfil o implementación de publicar asociadas a la franja de implementación de actualizaciones de contenido.  Por ejemplo, puede [publicar en esta franja con git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>Configuración para ranuras de implementación ##
Al clonar configuración desde otro franja de implementación, la configuración duplicada es modificable. Además, algunos elementos de configuración seguirá el contenido a través de un intercambio (no franja específica) mientras que otros elementos de configuración permanecerá en la misma casilla después de un intercambio (franja específica). Las siguientes listas muestran la configuración que cambiará cuando intercambiar ranuras.

**Configuración que se han intercambiado**:

- Configuración general - como la versión de framework, 32 o 64 bits, Web sockets
- Configuración de la aplicación (puede estar configurado para limitarse a un espacio)
- Cadenas de conexión (puede estar configurado para limitarse a un espacio)
- Asignaciones de controlador
- Configuración de supervisión y diagnóstico
- Contenido de WebJobs

**Configuración que no se han intercambiado**:

- Extremos de publicación
- Nombres de dominio personalizado
- Enlaces y certificados SSL
- Configuración de escala
- Programadores WebJobs

Para configurar una cadena de conexión o configuración de aplicación mantenerse dentro de un espacio (no intercambiado), tener acceso a la hoja de **Configuración de la aplicación** de un intervalo específico y luego active la casilla de **Configuración de la franja** de los elementos de configuración que debe limitarse la franja. Tenga en cuenta que marcar un elemento de configuración como franja específica tiene el efecto de establecer ese elemento como no intercambiable en todas las ranuras de implementación asociadas con la aplicación web.

![Configuración de franja][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>Intercambiar ranuras de implementación ##

>[AZURE.IMPORTANT] Antes de cambiar una aplicación web de un espacio de implementación en producción, asegúrese de que todas las opciones específicas de franja no están configuradas tal como desea que tengan en el destino de intercambio.

1. Para intercambiar ranuras de implementación, haga clic en el botón de **intercambio** en la barra de comandos de la aplicación web o en la barra de comandos de un intervalo de implementación. Asegúrese de que el origen de intercambio y de destino de intercambio están configurados correctamente. Normalmente, el destino intercambiar sería la franja de producción.  

    ![Botón intercambiar][SwapButtonBar]

3. Haga clic en **Aceptar** para completar la operación. Al finalizar la operación, se han intercambiado las ranuras de implementación.

## <a name="configure-auto-swap-for-your-web-app"></a>Configurar intercambiar automático para la aplicación web ##

Intercambiar automática simplifica escenarios DevOps donde desea implementar continuamente la aplicación web con cero inicial fría y cero el tiempo de inactividad de los clientes de finalización de la aplicación web. Cuando una franja de implementación está configurada para intercambiar automática en producción, cada vez que push la actualización de código para que se incluyen, servicio de aplicación intercambiar automáticamente la aplicación web en producción después de que ya ha caliente en la franja.

>[AZURE.IMPORTANT] Al habilitar intercambiar automática para un espacio, asegúrese de que la configuración de franja es exactamente la configuración para la franja de destino (normalmente la franja de producción).

Es fácil configurar intercambiar automática para un espacio. Siga los pasos siguientes:

1. En el módulo de **Implementación ranuras** , seleccione un intervalo de producción no, haga clic en **Toda la configuración del** módulo de la zona.  

    ![][Autoswap1]

2. Haga clic en **configuración de la aplicación**. Seleccione **activado** para **Intercambiar automática**, seleccione la franja de destino que desee en la **Franja de intercambiar automático**y haga clic en **Guardar** en la barra de comandos. Asegúrese de que la configuración para la franja es exactamente la configuración para la franja de destino.

    La ficha **notificaciones** parpadea verde **éxito** una vez completada la operación.

    ![][Autoswap2]

    >[AZURE.NOTE] Para probar intercambiar automático para la aplicación web, se puede seleccionar un espacio de no producción destino en la **Franja de intercambiar automática** para familiarizarse con la característica.  

3. Ejecutar una inserción de código a ese franja de implementación. Intercambiar automática ocurrirá poco tiempo después y la actualización se reflejarán en la dirección URL de la zona de destino.

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>Usar varias fase intercambiar para la aplicación web ##

Intercambio de varias fase está disponible para simplificar la validación en el contexto de elementos de configuración diseñados para limitarse a un espacio como cadenas de conexión. En estos casos, puede ser útil aplicar estos elementos de configuración de destino de intercambio con el origen de intercambio y validar antes de intercambiar surta efecto. Una vez que los elementos de configuración de destino de intercambio se aplican al origen de intercambio se complete el intercambio o volver a la configuración original del origen de intercambio que también tiene el efecto de cancelar el intercambio de las acciones disponibles. Ejemplos de los cmdlets de PowerShell de Azure disponibles para varias fase intercambiar se incluyen en los cmdlets de PowerShell de Azure de la sección de implementación ranuras.

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>Para deshacer una aplicación de producción después de intercambio ##

Si se identifican los errores de producción después de un intercambio de franja, deshacer las ranuras a su estado previo intercambio mediante intercambiar las mismas dos posiciones inmediatamente.

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>Preparación personalizado antes de intercambio ##

Algunas aplicaciones pueden requerir acciones de preparación personalizado. El elemento de configuración de applicationInitialization de web.config permite especificar acciones de inicialización personalizadas para llevar a cabo antes de que se recibe una solicitud. La operación de intercambio esperará esta preparación personalizado completar. Aquí tiene un fragmento de web.config de ejemplo.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>Para eliminar una casilla de implementación##

En el módulo de un intervalo de implementación, haga clic en **Eliminar** en la barra de comandos.  

![Eliminar un espacio de implementación][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Cmdlets de PowerShell Azure para ranuras de implementación

PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure a través de Windows PowerShell, incluido el soporte técnico para administrar ranuras de implementación de aplicación web de servicio de aplicaciones de Azure.

- Para obtener información de instalación y configuración de PowerShell de Azure y, en autenticación de PowerShell de Azure con su suscripción de Azure, consulte [cómo instalar y configurar Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### <a name="create-web-app"></a>Crear aplicaciones web

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>Crear un espacio de implementación para una aplicación web

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>Iniciar intercambio de varias fase y aplicar configuración de franja de destino a la franja de origen

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>Revertir la primera fase de intercambio de varias fase y restaurar la configuración de la franja de origen

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>Intercambiar ranuras de implementación

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>Eliminar la franja de implementación

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Comandos de línea de comandos de interfaz Azure Azure para ranuras de implementación

CLI Azure proporciona varias plataformas comandos para trabajar con Azure, incluido el soporte técnico para administrar ranuras de implementación de aplicación Web.

- Para obtener instrucciones sobre cómo instalar y configurar la CLI de Azure, incluida información sobre cómo conectar Azure CLI a su suscripción de Azure, vea [instalar y configurar la CLI de Azure](../xplat-cli-install.md).

-  Para obtener una lista de los comandos disponibles para el servicio de aplicación de Azure en Azure CLI, llamada `azure site -h`.

----------
### <a name="azure-site-list"></a>lista de sitios de Azure
Para obtener información acerca de las aplicaciones web de la suscripción actual, llame a **lista de sitios de azure**, como en el ejemplo siguiente.

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>crear el sitio de Azure
Para crear un espacio de implementación, llame al **crear el sitio de azure** y especifique el nombre de una aplicación web existente y el nombre de la casilla Crear, como en el ejemplo siguiente.

`azure site create webappslotstest --slot staging`

Para habilitar el control de origen para la nueva casilla, use la opción **--git** , como en el ejemplo siguiente.

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>intercambio de sitio de Azure
Para realizar la implementación actualizada ranura la aplicación de producción, use el comando **sitio azure intercambiar** para realizar una operación de intercambio, como en el ejemplo siguiente. La aplicación de producción no experimentará ningún período de inactividad ni experimentará un inicio frío.

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>Eliminar sitio de Azure
Para eliminar una franja de implementación que ya no es necesario, utilice el comando **Eliminar sitio azure** , como en el ejemplo siguiente.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="next-steps"></a>Pasos siguientes ##
[Azure servicio de aplicación Web App: web bloquear el acceso a las casillas de implementación de producción no](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Prueba gratuita de Microsoft Azure](/pricing/free-trial/)

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
