<properties
   pageTitle="Control de errores de automatización Azure | Microsoft Azure"
   description="Este artículo proporciona error básico tratamiento pasos para solucionar problemas y corregir errores comunes de automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="error de automatización, control de errores"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Sugerencias para los errores comunes de Azure automatización de control de errores

En este artículo se explica algunos de los errores comunes de automatización de Azure que surjan y sugiere posibles errores de pasos.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Solucionar problemas de autenticación cuando se trabaja con runbooks automatización de Azure  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Escenario: Inicie sesión en su cuenta de Azure error

**Error:** Recibe el error "Unknown_user_type: desconocido tipo de usuario" al trabajar con los cmdlets agregar AzureAccount o AzureRmAccount de inicio de sesión.

**Razón para el error:** Este error se produce si el nombre de activos de credenciales no es válido o si el nombre de usuario y la contraseña que utilizó para configurar los activos de credenciales de automatización no son válidos.

**Sugerencias de solución de problemas:** Para determinar cuál es el problema, siga estos pasos:  

1. Asegúrese de que no tiene caracteres especiales, incluidos los **@** carácter en el nombre de activos de credenciales de automatización que va a usar para conectarse a Azure.  

2. Compruebe que puede usar el nombre de usuario y contraseña que se almacenan en la credencial de automatización de Azure en el editor de PowerShell ISE local. Puede hacerlo ejecutando los siguientes cmdlets de PowerShell ISE:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Si la autenticación falla localmente, esto significa que no ha configurado correctamente las credenciales de Azure Active Directory. Consulte entrada de blog [Authenticating a Azure con Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) para obtener la cuenta de Azure Active Directory configurada correctamente.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Escenario: No se puede encontrar la suscripción de Azure

**Error:** Recibe el error "la suscripción denominada ``<subscription name>`` no se encuentra" al trabajar con los cmdlets seleccione AzureSubscription o seleccione AzureRmSubscription.

**Razón para el error:** Este error se produce si el nombre de la suscripción no es válido o si el usuario de Azure Active Directory que está intentando obtener los detalles de la suscripción no está configurado como administrador de la suscripción.

**Sugerencias de solución de problemas:** Para determinar si se han autenticado correctamente en Azure y tener acceso a la suscripción que está intentando seleccionar, realice los pasos siguientes:  

1. Asegúrese de que ejecuta el **Complemento AzureAccount** antes de ejecutar el cmdlet **Select AzureSubscription** .  

2. Si todavía ve este mensaje de error, modifique el código agregando el cmdlet **Get-AzureSubscription** siguiendo el cmdlet **AzureAccount agregar** y, a continuación, ejecutar el código.  Ahora, compruebe si el resultado de Get-AzureSubscription contiene los detalles de la suscripción.  
    * Si no puede ver los detalles de la suscripción en el resultado, esto significa que la suscripción no inicializada todavía.  
    * Si ve los detalles de la suscripción en el resultado, confirme que está usando el nombre de suscripción correcta o el identificador con el cmdlet **Select AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Escenario: Error de autenticación en Azure porque está habilitada la autenticación multifactor

**Error:** Recibe el error "Agregar AzureAccount: AADSTS50079: se requiere la inscripción de autenticación segura (prueba hasta)" cuando se autentica a Azure con su nombre de usuario de Azure y la contraseña.

**Razón para el error:** Si tiene la autenticación multifactor en su cuenta de Azure, no puede utilizar un usuario de Azure Active Directory para autenticar a Azure.  En su lugar, debe usar un certificado o un servicio principal para autenticarse en Azure.

**Sugerencias de solución de problemas:** Para usar un certificado con los cmdlets de administración de servicios de Azure, consulte [crear y agregar un certificado para administrar los servicios de Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar una entidad de seguridad de servicio con los cmdlets del Administrador de recursos de Azure, consulte [creación de servicio principal con Azure portal](./resource-group-create-service-principal-portal.md) y [autenticar un principal de servicio con el Administrador de recursos de Azure.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Solucionar errores comunes cuando se trabaja con runbooks

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Escenario: Runbook falla debido a objeto deserializado

**Error:** Su runbook un error "no se puede enlazar el parámetro ``<ParameterName>``. No se puede convertir el ``<ParameterType>`` valor de tipo Deserialized ``<ParameterType>`` escriba ``<ParameterType>``".

**Razón para el error:** Si su runbook es un flujo de trabajo de PowerShell, almacena objetos complejos en un formato deserializado para conservar su estado runbook si se ha suspendido el flujo de trabajo.  

**Sugerencias de solución de problemas:**  
Cualquiera de las siguientes tres soluciones resolverá este problema:

1. Si se tuberías objetos complejos de un cmdlet a otro, ajuste estos cmdlets en un InlineScript.  
2. Pase el nombre o el valor que necesita de objeto complejo en lugar de pasar todo el objeto.  

3. Use un runbook de PowerShell en lugar de un runbook de flujo de trabajo de PowerShell.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Escenario: Runbook error en el trabajo porque se ha superado la cuota asignada

**Error:** Su trabajo runbook un error "se ha alcanzado la cuota para el trabajo total mensual tiempo de ejecución para esta suscripción".

**Razón para el error:** Este error se produce cuando la ejecución del trabajo supera la cuota libre de 500 minuto para su cuenta. Este valor se aplica a todos los tipos de tareas de ejecución de trabajo como pruebas un trabajo, iniciar un trabajo desde el portal, ejecutar un trabajo mediante webhooks y programar un trabajo para ejecutar mediante el portal de Azure o en el centro de datos. Para obtener más información sobre precios de automatización ven [automatización precios](https://azure.microsoft.com/pricing/details/automation/).

**Sugerencias de solución de problemas:** Si desea usar más de 500 minutos de procesamiento al mes debe cambiar su suscripción de gratuita el nivel al nivel básico. Puede actualizar al nivel básico realizando los siguientes pasos:  

1. Inicie sesión en su suscripción de Azure  
2. Seleccione la cuenta de automatización que desee actualizar  
3. Haga clic en **configuración** > **nivel de precios y el uso** > **nivel de precios**  
4. En el módulo de **Elegir el nivel de precios** , seleccione **básica**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Escenario: No se reconoce al ejecutar un runbook de Cmdlet

**Error:** Su trabajo runbook un error "``<cmdlet name>``: el término ``<cmdlet name>`` no se reconoce como el nombre de cmdlet, función, archivo de comandos o un programa ejecutable."

**Razón para el error:** Este error se produce cuando el motor de PowerShell no encuentra el cmdlet que esté usando en su runbook.  Esto puede deberse a que falta el módulo que contiene el cmdlet de la cuenta, hay un conflicto de nombres con un nombre runbook, o el cmdlet también existe en otro módulo y automatización no puede resolver el nombre.

**Sugerencias de solución de problemas:** Cualquiera de las siguientes soluciones resolverá el problema:  

- Compruebe el nombre del cmdlet.  

- Asegúrese de que el cmdlet existe en su cuenta de automatización y que no hay ningún conflicto. Para comprobar si existe el cmdlet, abra un runbook en modo de edición y busque el cmdlet que desea buscar en la biblioteca o ejecutar **Get-Command ``<CommandName>`` **.  Una vez haya valida que el cmdlet está disponible para la cuenta y que no hay ningún conflicto de nombre con otros cmdlets o runbooks, agregue al lienzo y asegúrese de que está usando un parámetro válido establecer en su runbook.  

- Si tiene un conflicto de nombre y el cmdlet está disponible en dos módulos diferentes, puede resolver mediante el nombre completo del cmdlet. Por ejemplo, puede usar **ModuleName\CmdletName**.  

- Si se están ejecutando runbook local en un grupo de trabajo híbrido, asegúrese de que el cmdlet de módulo está instalado en el equipo que hospeda el trabajo híbrido.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Escenario: Un runbook larga ejecución coherente se produce un error con la excepción: "el trabajo no puede seguir ejecutando debido a se expulsa varias veces desde el mismo punto de control".

**Razón para el error:** Este es el comportamiento de diseño debido a la supervisión de "Uso compartido justo" de los procesos de automatización de Azure, que suspende automáticamente un runbook si ejecuta más de tres horas. Sin embargo, el mensaje de error devuelto proporcionan "pasos siguientes" opciones. Un runbook se puede suspender por una serie de razones. Suspende sucederá principalmente debido a errores. Por ejemplo, una excepción no detectada en un runbook, un error de red o un bloqueo en el trabajo Runbook ejecutando runbook, hará que runbook se suspende y empezar desde el último punto de comprobación cuando reanuda.

**Sugerencias de solución de problemas:** La solución documentada para evitar este problema es utilizar los controles en un flujo de trabajo.  Para obtener que más información, consulte [Los flujos de trabajo de PowerShell de aprendizaje](automation-powershell-workflow.md#Checkpoints).  En este artículo de blog [Usando los controles en Runbooks](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/), encontrará una explicación más profunda de "Uso compartido justo" y control.


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Solucionar errores comunes al importar módulos

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Escenario: No se puede importar el módulo o cmdlets no se puede ejecutar después de importar

**Error:** Un módulo no importa o se importa correctamente, pero no se extraen cmdlets.

**Razón para el error:** Algunas causas comunes que un módulo no puede importar correctamente a la automatización de Azure son:  

- La estructura no coincide con la estructura que automatización necesita estar en.  

- El módulo depende de otro módulo que no se ha implementado en su cuenta de automatización.  

- El módulo falta sus dependencias en la carpeta.  

- El cmdlet **New-AzureRmAutomationModule** se utiliza para cargar el módulo, y no tienen la ruta de acceso completa del almacenamiento o no se ha cargado el módulo mediante una dirección URL accesible públicamente.  

**Sugerencias de solución de problemas:**  
Cualquiera de las siguientes soluciones resolverá el problema:  

- Asegúrese de que el módulo sigue el siguiente formato:  
ModuleName.Zip **->** ModuleName o número de versión **->** (ModuleName.psm1, ModuleName.psd1)

- Abra el archivo .psd1 y vea si el módulo contiene dependencias.  Si es así, cargue estos módulos en la cuenta de automatización.  

- Asegúrese de que los archivos que se hace referencia están presentes en la carpeta de módulo.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Solucionar errores comunes cuando se trabaja con la configuración de estado deseado (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Escenario: Nodo es un estado de error con un error "No encontrado"

**Error:** El nodo tiene un informe con el estado de **error** y que contiene el error "el intento de obtener la acción de servidor https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction error porque una configuración válida ``<guid>`` no se encuentra."

**Razón para el error:** Este error suele ocurrir cuando el nodo se asigna a un nombre de configuración (por ejemplo, ABC) en lugar de un nombre de configuración de nodo (por ejemplo, ABC. Servidor Web).  

**Sugerencias de solución de problemas:**  

- Asegúrese de que está asignado el nodo con "nodo configuración nombre" y no la "configuración".  

- Puede asignar una configuración de nodo a un nodo con Azure portal o con un cmdlet de PowerShell.
    - Para asignar una configuración de nodo a un nodo con Azure portal, abra el módulo de **Nodos DSC** , seleccione un nodo y haga clic en el botón **configuración de nodos de asignar** .  
    - Para asignar una configuración de nodo a un nodo mediante el cmdlet de PowerShell, use el cmdlet **Set-AzureRmAutomationDscNode**


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Escenario: No hay configuraciones de nodo (archivos MOF) se han producido cuando se compila una configuración

**Error:** El trabajo de compilación DSC suspende con el error: "compilación se completó correctamente, pero no .mofs de configuración de nodo generó".

**Razón para el error:** Cuando se producirá la siguiente expresión se evalúa como la palabra clave de **nodo** en la configuración de DSC $null, a continuación, no hay configuraciones de nodo.    

**Sugerencias de solución de problemas:**  
Cualquiera de las siguientes soluciones resolverá el problema:  

- Asegúrese de que la expresión junto a la palabra clave de **nodo** en la definición de configuración no se evalúa en $null.  
- Si va a pasar ConfigurationData durante la compilación de la configuración, asegúrese de que está pasando los valores esperados que requiere la configuración de [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Escenario: El informe de nodo DSC queda detenido estado "en curso"

**Error:** Agente de DSC no salidas "Se encontró ninguna instancia con valores de propiedad especificados."

**Razón para el error:** Se ha actualizado su versión WMF y dañados WMI.  

**Sugerencias de solución de problemas:** Siga las instrucciones de la entrada de blog [DSC problemas conocidos y limitaciones](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) para solucionar el problema.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Escenario: No se puede usar una credencial en una configuración de DSC

**Error:** Se ha suspendido su trabajo de compilación DSC con el error: "error System.InvalidOperationException procesamiento 'Credencial' de tipo de propiedad ``<some resource name>``: convertir y almacenar una contraseña cifrada como texto simple sólo se permite si PSDscAllowPlainTextPassword está establecida en true".

**Razón para el error:** Ha utilizado una credencial en una configuración pero no ha proporcionado NOMPROPIO **ConfigurationData** para establecer **PSDscAllowPlainTextPassword** en true para cada configuración de nodo.  

**Sugerencias de solución de problemas:**  
- Asegúrese de que pase la correcta **ConfigurationData** a **PSDscAllowPlainTextPassword** se establece en true para cada configuración de nodo mencionada en la configuración. Para obtener más información, consulte [activos en Azure automatización DSC](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Pasos siguientes

Si ha seguido los pasos anteriores y necesita ayuda adicional en cualquier punto de este artículo, se puede:

- Obtén ayuda de expertos de Azure. Enviar el problema a la [foros de MSDN Azure o desbordamiento de pila.](https://azure.microsoft.com/support/forums/)

- Archivo de un incidente de soporte de Azure. Vaya al [sitio de soporte de Azure](https://azure.microsoft.com/support/options/) y haga clic en **obtener soporte técnico** en **soporte técnico y de facturación**.

- Si está buscando una solución de automatización de Azure runbook o un módulo de integración, publique una solicitud de secuencia de comandos en el [Centro de secuencia de comandos](https://azure.microsoft.com/documentation/scripts/) .

- Enviar solicitudes de comentarios o característica para la automatización de Azure en [Voz de usuario](https://feedback.azure.com/forums/34192--general-feedback).
