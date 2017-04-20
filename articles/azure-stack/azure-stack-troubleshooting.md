<properties
    pageTitle="Solución de problemas de Microsoft Azure pila | Microsoft Azure"
    description="Azure pila de solución de problemas."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Solución de problemas de pila de Microsoft Azure

Este documento proporciona información de solución de problemas comunes de pila de Azure.

Para obtener la mejor experiencia, asegúrese de que su entorno de implementación cumpla todos los [requisitos](azure-stack-deploy.md) y [preparados](azure-stack-run-powershell-script.md) antes de instalar. 

Las recomendaciones para solucionar problemas que se describen en esta sección derivan de varios orígenes y pueden o no pueden resolver el problema en particular. Si está experimentando un problema no documentado, asegúrese de comprobar el [Foro de Azure pila MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para más soporte e información adicional.

Se proporcionan ejemplos de código como está y no se puede garantizar resultados esperados. Esta sección está sujeto a modificaciones frecuentes y actualizaciones cuando se implementan mejoras en el producto.

  

## <a name="known-issues"></a>Problemas conocidos

 - Es posible que vea los siguientes no terminación errores durante la implementación, que afectan de éxito de la implementación:
     - "No se reconoce el término 'C:\WinRM\Start-Logging.ps1'"
     - "EceAction invocar: no es posible indizar en una matriz null" 
     - "InvokeEceAction: no se puede enlazar el argumento al parámetro 'Mensaje' porque es una cadena vacía."
 - Es posible que vea implementación un error en el paso 60.61.93 con un error "La aplicación con identificador 'URI' no encontrado". Este comportamiento es debido a la forma en que se registran las aplicaciones de Azure Active Directory.  Si recibe este error, continuar para [volver a ejecutar la secuencia de comandos de instalación](azure-stack-rerun-deploy.md) desde el paso 60.61.93 hasta que se complete la implementación.
 - Verá que el recurso de **Conjunto de disponibilidad** en el catálogo de soluciones se muestra en la categoría **virtualMachine ARM** : esta apariencia es solo un problema cosmético.
 - Al crear una nueva máquina virtual en el portal, en el paso de **conceptos básicos** , la opción de almacenamiento predeterminado SSD.  Esta configuración se debe cambiar a la unidad de disco duro o en el paso de **tamaño** de implementación de máquina virtual, no podrá ver los tamaños de VM disponibles para seleccionar y continuar su implementación. 
 - Verá módulos AzureRM PowerShell ya no se instalan de forma predeterminada en la máquina virtual CON01 MAS (en TP1 Esto se denomina ClientVM). Este comportamiento es por diseño, porque existe un método alternativo para [instalar estos módulos y conectar](azure-stack-connect-powershell.md).  
 - Verá que el proveedor de recursos **Microsoft.Insights** no está registrado automáticamente para suscripciones de inquilinos. Si desea ver los datos supervisados para una VM implementada como un inquilino, ejecute el siguiente comando de PowerShell (después de que [instalar y conectar](azure-stack-connect-powershell.md) como un inquilino): 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Verá funcionalidad en el portal de exportación para los grupos de recursos, aunque no sea texto aparece y está disponible para exportar.      
 - Puede comenzar una implementación de recursos de almacenamiento mayores de cuota disponible.  Se produce un error de esta implementación y los recursos de cuenta será suspendidos.  Existen dos opciones de corrección disponibles:
     - Administrador del servicio puede aumentar la cuota, aunque los cambios no surten efecto inmediatamente y suele tardar hasta una hora en propagarse.
     - Administrador del servicio puede crear un plan de complemento con cuota adicional que el inquilino, a continuación, puede agregar a la suscripción.
 - Al usar el portal para crear máquinas virtuales en entornos de pila de Azure con identidad en 'Azure - China', que no podrá ver los tamaños VM disponibles para su selección en el paso de **tamaño** de implementación de máquina virtual y no puede continuar la implementación.
 - Cuando la máquina virtual realmente ha implementado correctamente, es posible que vea un error de implementación en el portal.
 - Cuando se elimina un plan, la oferta o la suscripción, no se pueden eliminar máquinas virtuales.
 - Verá las extensiones VM en el catálogo de soluciones.
 - No se puede implementar una máquina virtual de una imagen de máquina virtual guardada.
 - Es podrán que los inquilinos Vea servicios que no se incluyen en su suscripción.  Cuando los inquilinos intentan implementar estos recursos, reciben un error.  Ejemplo: Suscripción inquilino solo incluye recursos de almacenamiento.  Inquilino verá la opción para crear otros recursos como máquinas virtuales.  En este escenario, cuando un inquilino intenta implementar una máquina virtual, reciben un mensaje que indica que no se puede crear la máquina virtual. 
 - Al instalar TP2, no debe activar el host de sistema operativo en el disco duro virtual proporcionado donde ejecutar la secuencia de comandos de configuración de pila de Azure, o puede recibir un error de mensajería de Windows que indica que va a expirar pronto.


## <a name="deployment"></a>Implementación

### <a name="deployment-failure"></a>Error de implementación
Si se produce un error durante la instalación, el instalador de pila de Azure le permite seguir un error en la instalación siguiendo la [vuelva a ejecutar los pasos de implementación](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al final de la implementación, la sesión de PowerShell, todavía está abierta y no muestra ningún resultado

Este comportamiento es probable solamente el resultado del comportamiento predeterminado de una ventana de comandos de PowerShell, cuando se ha seleccionado. La implementación de prueba de concepto realmente se ha realizado correctamente pero la secuencia de comandos pausado al seleccionar la ventana. Puede comprobar que este es el caso buscando la palabra "seleccionar" en la barra de título de la ventana de comandos.  Presione la tecla ESC para cancelar la selección, y se debe mostrar el mensaje de finalización después de él.

## <a name="templates"></a>Plantillas

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure plantilla no implementa en pila de Azure

Asegúrese de:

- La plantilla debe estar usando un servicio de Microsoft Azure que ya está disponible, o en vista previa de la pila de Azure.
- Las API utilizadas para un recurso específico son compatibles con la instancia de Azure pila local y que se dirige a una ubicación válida ("local" en Azure pila Technical Preview (TP) 2, vs "Oriental Estados Unidos" o "Sur India" en Azure).
- Revise [este artículo](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) acerca de los cmdlets de prueba AzureRmResourceGroupDeployment que detectar pequeñas diferencias en sintaxis del Administrador de recursos de Azure.

También puede usar las plantillas de pila de Azure proporcionadas en el [repositorio de GitHub](http://aka.ms/AzureStackGitHub/) para ayudarle a empezar.

## <a name="virtual-machines"></a>Máquinas virtuales de Windows

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>¿Después de iniciar el host de Mi prueba de concepto de pila de Microsoft Azure, mi inquilinos máquinas virtuales pasados desde el Administrador de Hyper-V y que vuelva automáticamente después de esperar un poco?

Según el sistema vuelve el subsistema de almacenamiento y RPs necesita determinar la coherencia. El tiempo necesario depende del hardware y especificaciones que se usa, pero es posible que algún tiempo después de reiniciar el host del inquilino VM regresar y se reconocerá.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Ha eliminado algunas máquinas virtuales de Windows, pero seguir viendo los archivos de disco duro virtual en disco. ¿Es este comportamiento esperado?

Sí, esto es el comportamiento esperado. Fue diseñado así porque:

- Al eliminar una máquina virtual, VHD no se eliminan. Los discos son distintos de los recursos del grupo de recursos.
- Cuando se elimina una cuenta de almacenamiento, la eliminación es visible inmediatamente a través de Azure Administrador de recursos (portal PowerShell) pero aún se conservan los discos puede contener en almacenamiento hasta que se ejecuta recolección.

Si ve "huérfanas" VHD, es importante saber si son parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si no se ha eliminado la cuenta de almacenamiento, es normal que siguen estando allí.

Puede obtener más información acerca de cómo configurar el umbral de retención en [Administrar cuentas de almacenamiento](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Pasos de instalación
La siguiente información sobre los pasos de instalación de pila de Azure puede ser útil para solucionar problemas:  

| Índice | Nombre | Descripción|
| ----- | ----- | -----|
|0,11 | (DEP) Validar máquinas físicas | Validar el hardware y la configuración del sistema operativo en los nodos físicos. |
| 0,12 | (DEP) Configurar la red de equipos físicos de prueba de concepto | Configurar una red virtual modificadores e interfaces. |
| 0.14 | (DEP) Implementar el dominio | Implementar Active Directory en la máquina Virtual. |
| 0,15 | (DEP) Configurar el servidor de dominio | Configurar el servidor de dominio con los grupos de seguridad etcetera. |
| 0,16 | (DEP) Configurar equipo físico | Configurar el dominio de combinación de redes y administradores locales del programa de instalación. |
| 0.18 | (DETENER) Configurar el clúster de almacenamiento | Cree el clúster de almacenamiento, cree un servidor de archivos y agrupaciones de almacenamiento. |
| 0.19 | (IRC) Infraestructura de tela de instalación | Configurar los requisitos previos para la implementación de tela. |
| 0.21 | (NET) Configurar BGP y NAT | Instala BGP y NAT - sólo necesaria para un nodo. |
| 0.22 | (NET) Configurar NAT y servidor de tiempo | Sincroniza el servidor de tiempo y configura NAT entradas. |
| 40.41 | (IRC) Crear invitadas | Crear la administración de máquinas virtuales. |
| 40.42 | (FBI) Configurar JEA PowerShell | Configurar JEA PowerShell para todos los roles. |
| 40.43 | (FBI) Configurar la entidad emisora de certificados de pila de Azure | Instala pila Azure entidad emisora de certificados. |
| 40.44 | (FBI) Configurar la entidad emisora de certificados de pila de Azure | Configura la entidad emisora de certificados de pila de Azure. |
| 40.45 | (NET) Configurar NC en máquinas virtuales | NC se instala en el invitadas. |
| 40.46 | (NET) Configurar NC en máquinas virtuales | Configurar NC en la invitadas. |
| 40.47 | (NET) Configurar invitadas | Configurar la administración de máquinas virtuales con NC ACL. |
| 60.61.81 | (FBI) Implementar los servicios de llamada de tela de pila Azure - requisito previo FabricRing | Crea a VIP para FabricRing |
| 60.61.82 | (FBI) Implementar los servicios de Azure pila tela anillo: implementar tela anillo clúster | Instala y configura el clúster de Azure pila tela llamar. |
| 60.61.83 | (FBI) Implementar extensiones de administración para proveedores de recursos | Instalar las extensiones de administrador para proveedores de recursos |
| 60.61.84 | (ACS) Configurar el almacenamiento de Azure coherente en el nivel de nodo. | Instala y configura el almacenamiento de Azure coherente en el nivel de nodo. |
| 60.61.85 | (ACS) Configurar el almacenamiento de Azure coherente en el nivel de clúster. | Instala y configura el almacenamiento de Azure coherente en el nivel de clúster. |
| 60.61.86 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para InfraServiceController |
| 60.61.87 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para IRC |
| 60.61.88 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para ASAppGateway |
| 60.61.89 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para el controlador de almacenamiento |
| 60.61.90 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para HealthMonitoring |
| 60.61.91 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para CEPE |
| 60.61.92 | (FBI) Implementar servicios de controlador de pila Azure tela anillo - requisito previo | Requisitos previos para PMM |
| 60.61.93 | (Katal) Crear servicio AzureStack principales | Crear aplicaciones de Azure Graph y principales de servicio en AAD. |
| 60.61.94 | (NET) Máquinas virtuales de instalación GW | GW se instala en el invitadas. |
| 60.61.95 | (NET) Configurar máquinas virtuales de GW | Configura GW de la invitadas. |
| 60.61.96 | (NET) Implementar IDN en hosts | Implementar IDN en hosts de infraestructura |
| 60.61.97 | (NET) Configurar IDN | Configurar función IDN |
| 60.61.98 | (FBI) Configurar WSUS VM | Desconectado se instala en el invitadas. |
| 60.61.99 | (FBI) Configurar máquinas virtuales WSUS | Configura desconectado de la invitadas. |
| 60.61.100 | (FBI) Configurar máquinas virtuales SQL Azure | Instala al servidor de SQL Azure en la invitadas. |
| 60.61.101 | (Katal) Requisitos previos para máquinas virtuales fue la instalación. | Configurará los requisitos previos de Microsoft Azure pila en la invitadas. |
| 60.61.102 | (Katal) El programa de instalación fue VM | Instala Microsoft Azure pila en la invitadas. |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.121 | (FBI) Implementar proveedores de recursos y controladores | Instala proveedores de recursos y controladores |
| 60.120.122 | (FBI) Configuración del controlador | Configura los controladores |
| 60.120.123 | (Katal) Configurar se VM | Configura la pila de Microsoft Azure en la invitadas. |
| 60.120.124 | (Katal) Configuración de AAD de pila de Azure. | Configura pila Azure con Azure AD. |
| 60.120.125 | (Katal) Instalar ADFS | Instale los servicios de federación de Active Directory (ADFS) |
| 60.120.126 | (Katal) Instalar ADFS o gráfico | Gráfico de Azure pila de instalaciones |
| 60.120.127 | (Katal) Configurar ADFS | Configura servicios de federación de Active Directory (ADFS) |
| 60.140.141 | (FBI) Configurar SRP | Configura el proveedor de recursos de almacenamiento |
| 60.140.142 | (ACS) Configurar el almacenamiento de Azure coherentes. | Configura el almacenamiento de Azure coherentes. |
| 60.140.143 | (FBI) Crear cuentas de almacenamiento | Crear todas las cuentas de almacenamiento para usarlo con diferentes proveedores. |
| 60.140.144 | (FBI) Uso de registros para los SRP | Uso de registros de proveedor de almacenamiento. |
| 60.140.145 | (IRC) Migrar máquinas virtuales creadas, Hosts y clúster a IRC | Migra objetos creado máquinas virtuales, Hosts y clúster a IRC |
| 60.140.146 | (FBI) Configurar Windows Defender | Configura Windows Defender |
| 60.160.161 | (LUN) Configurar el agente de supervisión | Configura el agente de supervisión |
| 60.160.162 | (FBI) Requisitos previos NRP | Requisitos previos NRP de instalaciones |
| 60.160.163 | (FBI) Implementación de NRP | Instalaciones NRP  |
| 60.160.164 | (FBI) Configuración de NRP | Configura NRP |
| 60.160.165 | (FBI) Requisitos previos de PRC | Requisitos previos de PRC de instalaciones |
| 60.160.166 | (FBI) Implementación de PRC | PRC de instalaciones |
| 60.160.167 | (FBI) Configuración de PRC | Configura PRC |
| 60.160.168 | (FBI) Requisitos previos FRP | Requisitos previos FRP de instalaciones |
| 60.160.169 | (FBI) Implementación de FRP | Instalaciones FRP  |
| 60.160.170 | (FBI) Configuración de FRP | Configura FRP |
| 60.160.174 | (FBI) URP como requisito previo | Requisitos previos URP de instalaciones |
| 60.160.175 | (FBI) Implementación de URP | Instalaciones URP  |
| 60.160.176 | (FBI) Configuración de URP | Configura URP |
| 60.160.171 | (FBI) Requisitos previos HRP | Requisitos previos HRP de instalaciones |
| 60.160.172 | (FBI) Implementación de HRP | Instalaciones HRP  |
| 60.160.173 | (FBI) Configuración de HRP | Configura HRP |
| 60.160.177 | (KV) Requisitos previos de KeyVault | Requisitos previos de KeyVault de instalaciones |
| 60.160.178 | (KV) Implementación de KeyVault | Instalaciones KeyVault  |
| 60.160.179 | (KV) Configuración de KeyVault | Configura KeyVault | 
| 60.190.191 | (FBI) Configurar la Galería | Configurar la Galería |
| 60.190.192 | (FBI) Configurar servicios de tela anillo | Configurar servicios de tela anillo |
| 60.221 | (FBI) Máquinas virtuales de consola de instalación | Servidor de consola se instala en el invitadas. |
| 60.222 | (FBI) Máquinas virtuales de consola de instalación | Mover el contenido DVM a la consola de máquina virtual. |
| 251 | Prepararse para futura host se reinicia | Establecer directiva de reinicio |


## <a name="next-steps"></a>Pasos siguientes

[Preguntas más frecuentes](azure-stack-FAQ.md)
