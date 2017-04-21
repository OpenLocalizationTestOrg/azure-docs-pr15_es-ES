<properties
    pageTitle="Replicar local máquinas virtuales VMware o servidores físicos en un sitio secundario | Microsoft Azure"
    description="Use este artículo para replicar máquinas virtuales de VMware o Windows/Linux servidores físicos en un sitio secundario con la recuperación de sitio de Azure."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Replicar máquinas virtuales de VMware local o servidores físicos en un sitio secundario


## <a name="overview"></a>Información general

InMage Scout de recuperación de sitios de Azure proporciona replicación en tiempo real entre los sitios de VMware local. InMage Scout se incluye en las suscripciones de servicio de recuperación de sitios de Azure.


## <a name="prerequisites"></a>Requisitos previos

**Cuenta de Azure**: tendrá una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede empezar con una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) acerca de precios de recuperación del sitio.


## <a name="step-1-create-a-vault"></a>Paso 1: Crear un depósito
1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Haga clic en Nuevo > Administración > copia de seguridad y recuperación de sitios (OMS). Como alternativa, puede hacer clic en Examinar > depósito de servicios de recuperación > Agregar.
3. En **nombre** , especifique un nombre descriptivo para identificar la cámara. Si tiene más de una suscripción, seleccione uno de ellos.
4. En el **grupo de recursos** , cree un nuevo grupo de recursos o seleccione uno existente. Especificar una región Azure para completar los campos necesarios. 
5.  En **ubicación**, seleccione la región geográfica de la cámara. Para comprobar las regiones admitidas, vea [Precios de recuperación de sitio de Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Si desea tener acceso rápidamente a la cámara del panel, haga clic en Anclar al panel y, a continuación, haga clic en crear.
6. El nuevo depósito aparecerá en el panel > todos los recursos y en los servicios de recuperación principal depósitos módulo.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Paso 2: Configurar la cámara y descargar los componentes de InMage Scout
7. En el módulo de depósitos de servicios de recuperación seleccione su cámara y haga clic en configuración.
8. En la **configuración de** > **Introducción** haga clic en el **Sitio de recuperación** > paso 1: **Preparar la infraestructura** > **objetivo de protección**.
9. En el **objetivo de protección** seleccione al sitio de recuperación y haga clic en Sí, con VMware vSphere hipervisor. A continuación, haga clic en Aceptar.
10. En la **configuración de Scout**, haga clic en descargar para descargar InMage Scout 8.0.1 GA software y registro clave. Los archivos de instalación para todos los componentes necesarios están en el archivo .zip descargado.


## <a name="step-3-install-component-updates"></a>Paso 3: Instalar actualizaciones de componentes

Obtenga información sobre las últimas [actualizaciones](#updates). Deberá instalar los archivos de actualización en servidores en el siguiente orden:

1. Servidor de recepción si existe
2. Servidores de configuración
3. Servidores de proceso
3. Servidores de destino principal
4. servidores vContinuum
5. Servidor de origen (Windows y Linux Server)

Instale las actualizaciones de la siguiente manera:

1. Descargue el archivo .zip [Actualizar](https://aka.ms/asr-scout-update4) . Este archivo .zip contiene los archivos siguientes:

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - Bits de update4 UA RHEL5, OL5, OL6, SUSE 10, 11 SUSE: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Extraer los archivos .zip.<br>
3. **Servidor para la recepción**: copia **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** en el servidor de recepción y extraer de él. En la carpeta extraída, **ejecute/instalar**.<br>
4. **Para el servidor de proceso del servidor de configuración**: copia **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** para el servidor de configuración y proceso. Haga doble clic para ejecutarlo.<br>
5. **Servidor de destino de maestro para Windows**: para actualizar el agente unificado, copie **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** en el servidor de destino principal. Haga doble clic en él para ejecutarlo. Tenga en cuenta que el agente unificado también es aplicable al servidor de origen. Debe instalarlo en el servidor de origen, así como se mencionó más adelante en esta lista.<br>
7. **Para el servidor vContinuum**: copiar **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** en el servidor de vContinuum.  Asegúrese de que ha cerrado al Asistente vContinuum. Haga doble clic en el archivo para ejecutarlo.<br>
8. **Servidor de destino principal para Linux**: para actualizar el agente unificado, copiar **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** en el servidor de destino principal y extraerlo. En la carpeta extraída, **ejecute/instalar**.<br>
9. **Servidor de origen para Windows**: para actualizar el agente unificado, copie **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** en el servidor de origen. Haga doble clic en él para ejecutarlo.<br>
10. **Servidor de origen para Linux**: para actualizar el agente unificado, copiar versión correspondiente del archivo UA en el servidor Linux y extraer de él. En la carpeta extraída, **ejecute/instalar**.  Ejemplo: RHEL 6.7 64 bits servidor, copiar **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** en el servidor y extraer de él. En la carpeta extraída, **ejecute/instalar**.

## <a name="step-4-set-up-replication"></a>Paso 4: Configurar la replicación
1. Configurar la replicación entre el origen y destino VMware sitios.
2. Para obtener instrucciones, use la documentación de InMage Scout que se descarga con el producto. Como alternativa, puede acceder a la documentación como sigue:

    - [Notas de la versión](https://aka.ms/asr-scout-release-notes)
    - [Matriz de compatibilidad](https://aka.ms/asr-scout-cm)
    - [Guía del usuario](https://aka.ms/asr-scout-user-guide)
    - [Guía de usuario de recepción](https://aka.ms/asr-scout-rx-user-guide)
    - [Guía de instalación rápida](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Actualizaciones

### <a name="azure-site-recovery-scout-801-update-4"></a>Sitio de Azure recuperación Scout 8.0.1 actualización 4
Actualización de Scout 4 es una actualización acumulativa. Tiene todas las revisiones de update1 hasta update3 y siguientes nuevas correcciones de errores y mejoras.

**Nuevo soporte de plataforma** 

- Se ha agregado compatibilidad para vCenter/vSphere 6.0, 6.1 y 6.2
- Se ha agregado compatibilidad para los siguientes sistemas operativos Linux
    - Rojos sombrero Enterprise Linux (RHEL) 7.0, 7.1 y 7.2 
    - CentOS 7.0, 7.1 y 7.2
    - Rojos sombrero Enterprise Linux (RHEL) 6.8
    - CentOS 6.8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 de 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** se incluye con el paquete Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**base. Descargue el paquete de GA Scout de portal como se mencionó en el [paso 1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault).

**Mejoras y correcciones de errores** 

- Cierre mejorado tratamiento para que siguientes Linux OSes y clones para evitar problemas no deseados, vuelva a sincronizar.
    - Rojos sombrero Enterprise Linux (RHEL) 6.x
    - Oracle Linux (OL) 6.x
- Para Linux, complete el acceso a la carpeta permisos en el directorio de instalación de agente unificado ahora están restringidos solo para el usuario local.
- En Windows tiempo de espera problema al emitir marca coherencia distribuido comunes en gran medida carga aplicaciones distribuidas como clústeres SQL y punto de uso compartido.
- Registro agregado relacionados con la corrección en instalador de base de CX.
- Vínculo de descarga de VMware vCLI 6.0 se agrega al instalador de base de destino de patrón de Windows.
- Agrega más comprobaciones y registros de los cambios de las configuraciones de red durante la migración y DR taladros.
- Información de retención en ocasiones no se notifica a lo CX.  
- Volumen cambie el tamaño de la operación vContinuum Asistente para clúster físico, falla cuando ha ocurrido reducir volumen de origen.
- Error de protección de clúster con error "Error al buscar la firma de disco" cuando clúster disco es PRDM.
- cxps de transporte bloqueo en el servidor debido a excepción de fuera de rango. 
- Nombre del servidor y las columnas IP están ahora puede cambiar el tamaño de la página de instalación de inserción del Asistente para vContinuum.
- Mejoras de la API de recepción
    - Proporciona cinco más recientes disponible comunes coherencia puntos (sólo garantiza etiquetas).
    - Proporciona la capacidad y detalles de espacio libre para todos los dispositivos protegidos.
    - Proporciona el estado del controlador Scout en el servidor de origen. 
    
>[AZURE.NOTE] 
>
>- Paquete de base de **InMage_Scout_Standard_8.0.1_GA.zip** ahora actualizó a CX installer base **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** y destino de Windows patrón base installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Para la instalación nuevo todos los use nuevos bits GA CX y de destino de patrón de Windows.
>- Actualización 4 se pueden aplicar directamente en 8.0.1 Georgia.
>- Las actualizaciones de recepción y el servidor de configuración no se puede deshacer después de que se aplican en el sistema.

### <a name="azure-site-recovery-scout-801-update-3"></a>Actualización de Azure sitio recuperación Scout 8.0.1 3
Actualización 3 incluye las correcciones de errores y mejoras siguientes:

- El servidor de configuración y la recepción no se registró en depósito de recuperación de sitios cuando estén detrás del proxy.
- El número de horas que no se cumple el objetivo de punto de recuperación (RPO) no recibe se actualiza en el informe de estado.
- El servidor de configuración no se está sincronizando con recepción cuando los detalles de hardware ESX o los detalles de red contengan caracteres UTF-8.
- Controladores de dominio de Windows Server 2008 R2 no arranque después de recuperación.
- Sincronización sin conexión no funciona como se esperaba.
- Después de la migración de máquina virtual (VM), eliminación de replicación de par no sale de la UI CX por mucho tiempo, y los usuarios no pueden completar la recuperación o reanudar la operación.
- En general instantánea operaciones que se realizan mediante el trabajo de coherencia se han optimizado para ayudar a reducir la aplicación se desconecta como los clientes de SQL.
- Se ha mejorado el rendimiento de la herramienta de coherencia (VACP.exe) reduciendo el uso de memoria que se necesita para crear instantáneas en Windows.
- La inserción instalar servicio se bloquea cuando la contraseña es mayor que 16 caracteres.
- vContinuum no está comprobando y solicitar nuevas credenciales vCenter cuando se cambian las credenciales.
- En Linux, el Administrador de caché de destino principal (cachemgr) no descarga archivos desde el servidor de proceso, que da como resultado replicación limitación de par.
- Cuando el orden del disco físico migración tras error clúster (MSCS) no es el mismo en todos los nodos, replicación no está activada para algunos de los volúmenes de clúster.
<br/>Tenga en cuenta que el clúster debe ser reprotected para beneficiarse de esta revisión.  
- La funcionalidad de SMTP no funciona como se esperaba después de actualiza recepción Scout 7.1 a Scout 8.0.1.
- Se han agregado más estadísticas en el registro para seguimiento del tiempo que haya tomado para completar la operación de deshacer.
- Se ha agregado compatibilidad para sistemas operativos de Linux en el servidor de origen:
    - Actualización de red sombrero Enterprise Linux (RHEL) 6 7
    - Actualización de centOS 6 de 7
- La IU de recepción y CX ahora pueden mostrar la notificación de par, que entra en el modo de mapa de bits.
- Se han agregado las siguientes revisiones de seguridad en recepción:

**Descripción del problema**|**Procedimientos de implementación**
---|---
Omitir la autorización a través de manipulación de parámetros|Acceso restringido a los usuarios no es aplicable.
Solicitudes entre sitios|Implementa el concepto de token de página, que genera aleatoriamente para cada página. <br/>Con esto, verá: <li> Hay una única sesión instancia para el mismo usuario.</li><li>Actualice la página no funciona, se le redirigirá a panel.</li>
Carga de archivos malintencionados|Archivos restringidos a determinadas extensiones. Permitidos extensiones son: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, registro mid mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, alquitrán, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml y zip.
Persistente las secuencias de comandos | Agregar validaciones de entrada.


>[AZURE.NOTE]
>
>-  Todas las actualizaciones de recuperación de sitios son acumulativas. Actualización 3 tiene todas las revisiones de actualización 1 y 2 de actualización. Actualización 3 se puede aplicar directamente en 8.0.1 Georgia.
>-  Las actualizaciones de recepción y el servidor de configuración no se puede deshacer después de que se aplican en el sistema.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Sitio de Azure recuperación Scout 8.0.1 actualización 2 (actualizar 15 de Dic 03)

Incluyen correcciones de actualización 2:

- **Servidor de configuración**: solucionar un problema que impide que la característica de medición gratuita 31 días laborables según lo esperado cuando el servidor de configuración se registró en el sitio de recuperación.
- **Agente de unificado**: solucionar un problema en Update 1 que ha generado la actualización no están instalada en el servidor de destino principal al actualizar desde la versión 8.0 a 8.0.1.


### <a name="azure-site-recovery-scout-801-update-1"></a>Actualización de Azure sitio recuperación Scout 8.0.1 1

Actualización 1 incluye las siguientes correcciones de errores y las nuevas características:

- 31 días de protección gratuita por cada instancia del servidor. Esto le permite probar la funcionalidad o configurar una prueba de concepto.
    - Todas las operaciones en el servidor, incluidos conmutación y, son gratuitas para el 31 días, empezando desde el momento en que un servidor primero está protegido con Scout de recuperación del sitio.
    - Desde el 32nd día en adelante, cada servidor protegido se cargará a la tasa estándar de instancia para la protección de recuperación de sitios de Azure en un sitio de cliente.
    - En cualquier momento, el número de servidores protegidos que actualmente se carga está disponible en la página panel del depósito de recuperación de sitios de Azure.
- Soporte técnico había agregado para vSphere interfaz de línea de comandos (vCLI) actualización 5,5 2.
- Compatibilidad con sistemas operativos de Linux en el servidor de origen:
    - Actualización de RHEL 6 de 6
    - RHEL 5 Update 11
    - Actualización de centOS 6 6
    - Actualización de centOS 5 11
- Correcciones de errores para solucionar los problemas siguientes:
    - Se produce un error en el registro de la cámara para el servidor de configuración o el servidor de recepción.
    - Volúmenes de clúster no aparecen como se esperaba cuando se reprotected máquinas virtuales en clúster cuando reanuda.
    - Recuperación falla cuando el servidor de destino principal está alojado en un servidor de ESXi diferente de las máquinas virtuales de producción de local.
    - Permisos de archivo de configuración se cambian al actualizar a 8.0.1, lo que afecta a la protección y las operaciones.
    - El umbral de sincronización no se exige según lo esperado, lo que lleva al comportamiento de la replicación incoherente.
    - La configuración de RPO no aparecen correctamente en la interfaz de configuración de servidor. El valor de datos sin comprimir incorrectamente muestra el valor comprimido.
    -  No elimine la operación de eliminación según lo esperado en el Asistente de vContinuum y no se elimina la replicación de la interfaz de configuración de servidor.
    -  En el Asistente de vContinuum, el disco está seleccionado automáticamente al hacer clic en **Detalles** en la vista de disco durante la protección de máquinas virtuales MSCS.
    - Durante el escenario de físico a virtual (P2V), no se mueven requiere servicios de HP, como CIMnotify y CqMgHost, manual de recuperación de máquina virtual. El resultado es hora de inicio adicionales.
    - Protección de máquina virtual Linux falla cuando hay más de 26 discos en el servidor de destino principal.

## <a name="next-steps"></a>Pasos siguientes

Publicar las preguntas en el [foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
