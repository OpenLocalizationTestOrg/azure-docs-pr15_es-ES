<properties 
    pageTitle="Proteger SQL Server con la recuperación de SQL Server y recuperación de sitio de Azure | Microsoft Azure" 
    description="Este artículo describe cómo replicar SQL Server mediante capacidades de desastres Azure sitio recuperación de SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger SQL Server con la recuperación de SQL Server y recuperación de sitio de Azure 


El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md).

 En este artículo se describe cómo proteger el servidor SQL Server de una aplicación mediante una combinación de tecnologías de SQL Server BCDR y recuperación de sitio de Azure. Debe tener una buena comprensión de capacidad de recuperación SQL Server (base de datos de conmutación, grupos de disponibilidad AlwaysOn, reflejo, envío de registro) y de recuperación de sitio de Azure, antes de implementar los escenarios descritos en este artículo.



## <a name="overview"></a>Información general

Muchas cargas de trabajo usan SQL Server como base. Aplicaciones como SharePoint, Dynamics y SAP usar SQL Server para implementar los servicios de datos.  Aplicaciones implementarán SQL Server en un número de maneras diferentes:

- **Independiente de SQL Server**: SQL Server y todas las bases de datos se hospedan en un único equipo (físico o un virtual). Cuando virtualizado, host clústeres se usa para alta disponibilidad local. No se ha implementado disponibilidad alto nivel de invitado.
- **Instancias de clúster de migración tras error de SQL Server (siempre en FCI)**: dos o más nodos de instancias de SQL server con discos compartidos están configurados en un clúster Windows. Si cualquiera de las instancias de clúster está inactivo, el clúster puede conmutar SQL Server a otra instancia. Esta configuración se usa normalmente para alta disponibilidad en un sitio principal. No protegerse falla o interrupción de la capa de almacenamiento compartido. Disco compartido se puede implementar mediante ISCSI, canal de fibra o VHDx compartido.
- **SQL siempre en disponibilidad de grupos**: en esta configuración, dos nodos están configurados en compartido nada de clúster con bases de datos de SQL Server configuradas en un grupo de disponibilidad con replicación sincrónica y migración tras error automática.

En Enterprise Edition, SQL Server también proporciona desastres nativa tecnologías de recuperación para recuperar bases de datos a un sitio remoto. En este artículo, se podrá aprovechar e intégrelo con estas tecnologías de recuperación de desastres nativas de SQL: 

- SQL siempre en disponibilidad de grupos de recuperación para SQL Server 2012 o 2014 Enterprise Edition.
- Reflejo de base de datos SQL en modo de alta seguridad de SQL Server Standard edition (cualquier versión) o de SQL Server 2008 R2.


Recuperación de sitio puede proteger SQL Server forma mostrada en la tabla.

 |**Local a local** | **Local a Azure** 
---|---|---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí 
**Servidor físico** | Sí | Sí


## <a name="support-and-integration"></a>Integración y soporte técnico

Estas versiones de SQL Server son compatibles con los escenarios de este artículo:


- Standard y Enterprise SQL Server de 2014
- Estándar y Enterprise SQL Server 2012
- Estándar y SQL Server 2008 R2 Enterprise


Recuperación de sitio se puede integrar con las tecnologías nativas de SQL Server BCDR resumidas en la tabla siguiente para proporcionar una solución de recuperación de desastres.

**Característica** |**Detalles** | **Versión de SQL Server** 
---|---|---
**Grupo de disponibilidad AlwaysOn** | Ejecutan varias instancias de independiente de SQL Server en un clúster de migración tras error que tiene varios nodos.<br/><br/>Bases de datos se pueden agrupar en grupos de migración tras error que se pueden copiar (reflejado) en las instancias de SQL Server para que no necesita espacio de almacenamiento compartido.<br/><br/>Proporciona recuperación entre un sitio principal y uno o más sitios secundarios. Dos nodos pueden configurarse en un compartido nada clúster con bases de datos de SQL Server configurado en un grupo de disponibilidad con replicación sincrónica y migración tras error automática. | SQL Server 2014 & 2012 Enterprise edition
**Conmutación (AlwaysOn FCI)** | SQL Server aprovecha Windows conmutación para alta disponibilidad de cargas de trabajo de SQL Server local.<br/><br/>Nodos de instancias de SQL Server con discos compartidos se configuran en un clúster. Si es una instancia hacia abajo el clúster conmutar por error a otro.<br/><br/>El clúster no protegerse de error o interrupciones de almacenamiento compartido. El disco compartido se puede implementar con iSCSI, canal de fibra o compartido VHDXs. | Ediciones de SQL Server Enterprise<br/><br/>SQL Server Standard edition (limitado a dos nodos solo)
**Base de datos que coinciden con (modo de alta seguridad)** | Protege una base de datos a una única copia secundaria. Disponible en ambos seguridad alta (sincrónico) y modos de replicación (asincrónica) de alto rendimiento. No requiere un clúster. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise todas las ediciones
**Independiente SQL Server** | SQL Server y base de datos se hospedan en un único servidor (físico o virtual). Host clústeres se usa para alta disponibilidad si el servidor es virtual. Sin alta disponibilidad de nivel de invitado. | Enterprise o Standard edition

## <a name="deployment-recommendations"></a>Recomendaciones de implementación


Esta tabla resume nuestras recomendaciones para la integración de las tecnologías de SQL Server BCDR con la recuperación de sitio.

**Versión** |**Edition** | **Implementación** | **Local a local** | **Local a Azure** 
---|---|---|---|---
SQL Server 2012 o 2014 | Enterprise | Instancia de clúster | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
 | Enterprise | Grupos de disponibilidad AlwaysOn para alta disponibilidad | Grupos de disponibilidad AlwaysOn | Grupos de disponibilidad AlwaysOn
 | Estándar | Instancia de clúster (FCI) | Replicación de recuperación de sitios con una réplica local | Replicación de recuperación de sitios con una réplica local
 | Enterprise o Standard | Independiente | Replicación de recuperación de sitios | Replicación de recuperación de sitios
SQL Server 2008 R2 | Enterprise o Standard | Instancia de clúster (FCI) | Replicación de recuperación de sitios con una réplica local | Replicación de recuperación de sitios con una réplica local
 | Enterprise o Standard | Independiente | Replicación de recuperación de sitios | Replicación de recuperación de sitios
SQL Server (cualquier versión) | Enterprise o Standard | Instancia de clúster: aplicación de DTC | Replicación de recuperación de sitios | No compatible


## <a name="deployment-prerequisites"></a>Requisitos previos de implementación

Esto es lo necesita antes de empezar:

- Implementación local de SQL Server ejecute una versión compatible de SQL Server. Normalmente también necesitará un Active Directory para SQL server.
- Los requisitos previos para el escenario que desea distribuir. Requisitos previos se pueden encontrar en cada artículo de la implementación. Se proporcionan vínculos a estos en la [Introducción al sitio de recuperación](site-recovery-overview.md).
- Si desea configurar recuperación en Azure, debe ejecutar la herramienta de [Evaluación de disponibilidad de la máquina Virtual de Azure](http://www.microsoft.com/download/details.aspx?id=40898) en sus máquinas virtuales de SQL Server para asegurarse de que son compatibles con Azure y recuperación del sitio.


## <a name="set-up-active-directory"></a>Configurar Active Directory

Necesitará Active Directory en el sitio de recuperación secundarios para SQL Server se ejecute correctamente. hay un par de opciones:

- **Pequeña empresa**: si tiene un pequeño número de aplicaciones y un controlador de dominio para el sitio local y desea errores en todo el sitio, se recomienda que use repication de recuperación de sitios para replicar el controlador de dominio en el centro de datos secundario o en Azure.

- **Medianos y grandes empresas**, si tiene una gran cantidad de aplicación, que está ejecutando un bosque de Active Directory y desea conmutar por aplicación o la carga de trabajo de error, se recomienda configurar un controlador de dominio en el centro de datos secundario o en Azure. Tenga en cuenta que si está usando grupos de disponibilidad AlwaysOn recuperar a un sitio remoto se recomienda que configurar otro controlador de dominio adicional en el sitio secundario o Azure, para usar para la instancia de SQL Server recuperada.

Las instrucciones de este documento por supuesto que un controlador de dominio está disponible en la ubicación secundaria. [Obtenga más información](site-recovery-active-directory.md) acerca de cómo protegerse de Active Directory con la recuperación de sitios.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Intégrelo protección Maletín de le de SQL Server (local a Azure)


Recuperación de sitio admite de forma nativa AlwaysOn de SQL. Si ha creado un grupo de disponibilidad de SQL con una máquina virtual Azure configurar como 'Secundario', a continuación, puede usar recuperación de sitio para administrar la migración tras error de los grupos de disponibilidad. 

>[AZURE.NOTE] Esta capacidad está actualmente en la vista previa y está disponible cuando administran servidores de host de Hyper-V en el centro de datos principal de nubes de VMM y configuración de VMware es administrado por un [Servidor de configuración](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Actualmente, esta función no está disponible en el nuevo portal de Azure.

#### <a name="prerequisites"></a>Requisitos previos

Aquí es lo que necesita integrar AlwaysOn SQL con la recuperación de sitio:

- Un servidor SQL local (servidor independiente o un clúster).
- Uno o varios equipos virtuales Azure con SQL Server instalado
- Un grupo de disponibilidad de SQL configurado entre en local, SQL Server y SQL Server se ejecuta en Azure
- PowerShell remoto debe estar habilitado en el equipo de SQL Server local. El servidor VMM o el servidor de configuración debería poder realizar llamadas de PowerShell remotas a SQL Server.
- Es recomendable agregar una cuenta de usuario en el servidor de SQL en local, en los grupos de usuario SQL con al menos los permisos siguientes:
    - GRUPO de disponibilidad ALTER: permisos [aquí](https://msdn.microsoft.com/library/hh231018.aspx)y [aquí](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Base de datos de ALTER - permisos[aquí](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Se debe crear una cuenta de RunAs en servidor VMM o crear una cuenta en el servidor de configuración mediante la CSPSConfigtool.exe para el usuario que se mencionan en el paso anterior 
- El módulo de SQL PS debe instalarse en servidores de SQL que se ejecutan en local y en máquinas virtuales de Windows Azure
- El agente de VM debe ser instalados máquinas virtuales que se ejecutan en Azure
- Debe tener NTAUTHORITY\System siguiendo permisos en SQL Server en máquinas virtuales de Azure:
    - MODIFICAR disponibilidad grupo - permisos [aquí](https://msdn.microsoft.com/library/hh231018.aspx)y [aquí](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Base de datos de ALTER - permisos [aquí](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Paso 1: Agregar un servidor SQL Server


1. Haga clic en **Agregar SQL** para agregar un nuevo servidor de SQL Server. 

    ![Agregar SQL](./media/site-recovery-sql/add-sql.png)

2. En **Configuración de SQL** > **nombre** proporcionar un nombre descriptivo para referirse a SQL Server.
3. **En SQL Server (FQDN)** especificar el FQDN del origen de SQL Server que desee agregar. En caso de que se instala SQL Server en un clúster, a continuación, proporcione FQDN del clúster y no de cualquiera de los nodos del clúster.  
4. En la **Instancia de SQL Server** elija la instancia predeterminada o proporcionar el nombre de la instancia personalizada.
5. En el **Servidor de administración** , seleccione un servidor VMM o configuración registrado en depósito de recuperación del sitio. Recuperación de sitio utiliza este servidor de administración para comunicarse con SQL Server.
6. En el cuadro **Ejecutar como cuenta de** proporcionar el nombre de una cuenta de RunAs que creó en el servidor VMM especificado o la cuenta que creó en el servidor Configuraaaon. Esta cuenta se usa para tener acceso a SQL Server y debe tener permisos de lectura y migración tras error en grupos de disponibilidad en el equipo de SQL Server.

    ![Agregar el cuadro de diálogo SQL](./media/site-recovery-sql/add-sql-dialog.png)

Después de agregar SQL Server aparecerá en la pestaña **Servidores SQL Server** . 

![Lista SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Paso 2: Agregar un grupo de disponibilidad de SQL

1. Después de SQL Server máquina se agrega que el siguiente paso es agregar los grupos de disponibilidad al sitio de recuperación. Para ello, explore en profundidad dentro del servidor SQL que agregó en el paso anterior y haga clic en Agregar grupo de disponibilidad de SQL. 

    ![Agregar SQL AG](./media/site-recovery-sql/add-sqlag.png)

2. Grupo de disponibilidad de SQL se pueden replicar en uno o varios equipos virtuales en Azure. Al agregar el grupo de disponibilidad de sql que se le pide que proporcione el nombre y la suscripción de la máquina virtual Azure donde desea que el grupo de disponibilidad para ser error al sitio de recuperación.

    ![Agregar el cuadro de diálogo de SQL AG](./media/site-recovery-sql/add-sqlag-dialog.png)

3. En el ejemplo anterior se convertirá en disponibilidad de grupo DB1 AG principal en máquina virtual SQLAGVM2 ejecuta dentro de suscripción DevTesting2 en un error. 

>[AZURE.NOTE] Sólo los grupos disponibilidad principal en el servidor SQL que agregó en el paso anterior están disponibles para agregarse a la recuperación de sitios. Si ha realizado una principal de grupo de disponibilidad en SQL Server o si ha agregado más grupos de disponibilidad en SQL Server después de que se ha agregado, actualice mediante la opción de actualización disponible en SQL Server.

#### <a name="step-3-create-a-recovery-plan"></a>Paso 3: Crear un Plan de recuperación

El siguiente paso es crear un plan de recuperación con máquinas virtuales y los grupos de disponibilidad. Seleccione el mismo servidor VMM o servidor de configuración que utilizó en el paso 1 como origen y Microsoft Azure como destino.

![Crear el Plan de recuperación](./media/site-recovery-sql/create-rp1.png)

![Crear el Plan de recuperación](./media/site-recovery-sql/create-rp2.png)

En el ejemplo de la aplicación Sharepoint consta de 3 máquinas virtuales que utilizar un grupo de disponibilidad de SQL como su back-end. En este plan de recuperación que podríamos seleccionamos también tanto el grupo de disponibilidad la máquina virtual que constituyen la aplicación. 

Puede personalizar aún más el plan de recuperación moviendo máquinas virtuales a los grupos de diferentes migración tras error para el orden de migración tras error de la secuencia. Grupo de disponibilidad siempre es error sobre primero se utilizará como back-end de cualquier aplicación. 

![Personalizar el Plan de recuperación](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Paso 4: Conmutar

Opciones de diferentes migración tras error están disponibles una vez que se ha agregado un grupo de disponibilidad en un Plan de recuperación.

Migración tras error | Detalles
--- | ---
**Failover planificado** | Failover planificado no implica un failover de pérdida de datos. Para lograr que el modo de disponibilidad del grupo de disponibilidad de SQL en primer lugar se establece en sincrónico y, a continuación, se activa un error para hacer que el grupo de disponibilidad principal en la máquina virtual proporcionado al agregar el grupo de disponibilidad al sitio de recuperación. Una vez completada la migración tras error, el modo de disponibilidad se establece en el mismo valor tal y como era antes de que se ha activado la migración tras error planeada.
**No planeado migración tras error** | No planeado migración tras error puede dar lugar a una pérdida de datos. Al activar planificado migración tras error no se cambia el modo de disponibilidad del grupo de disponibilidad y la TI se convierte en principal en la máquina virtual proporcionada al agregar el grupo de disponibilidad al sitio de recuperación. Una vez completo planificado migración tras error y servidor local de SQL Server vuelve a estar disponible, tiene invertir replicación se activa en el grupo de disponibilidad. Tenga en cuenta que esta acción no está disponible en el plan de recuperación y se puede realizar en el grupo de disponibilidad de SQL en la pestaña servidores SQL
**Migración tras error de prueba** | No se admite la recuperación de errores de prueba para grupo de disponibilidad de SQL. Si desencadenar migración tras error de prueba de un Plan de recuperación que contiene el grupo de disponibilidad de SQL, se podría omitir migración tras error para disponibilidad de grupo.


Tenga en cuenta estas opciones de migración tras error.

Opción | Detalles
--- | ---
**Opción 1** | 1. realice una migración tras error de prueba de la aplicación y los niveles front-end.<br/><br/>2. actualizar el nivel de aplicación para obtener acceso a la copia de réplica en modo de solo lectura y realizar una prueba de sólo lectura de la aplicación.
**Opción 2** | 1. crear una copia de la instancia de máquina virtual de SQL Server de réplica (mediante clonar VMM a sitios o copia de seguridad de Azure) y que aparezca en una red de prueba<br/><br/> 2. realice la prueba de migración tras error mediante el plan de recuperación.

Paso 5: Conmutar

Si desea hacer que el grupo de disponibilidad nuevo principal en SQL Server local puede hacerlo desencadenante planeado migración tras error en el Plan de recuperación y eligiendo la dirección de Microsoft Azure al servidor de VMM local.

>[AZURE.NOTE] Después de la una migración planificado replicación inversa tiene se activa en el grupo de disponibilidad para reanudar la replicación. Hasta que esto se realiza la replicación permanece suspendida.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Proteger equipos sin un servidor VMM o en un servidor de configuración

Para los entornos que no son administrados por un servidor de VMM o en un servidor de configuración, Azure automatización Runbooks puede usarse para configurar una migración tras error secuencia de comandos de los grupos de disponibilidad de SQL. A continuación se muestran los pasos para configurar:

1.  Crear un archivo local para el script de error en un grupo de disponibilidad. Esta secuencia de comandos de ejemplo especifica una ruta de acceso al grupo de disponibilidad en la réplica de Azure y falla sobre a esa instancia de réplica. Esta secuencia de comandos se ejecutará en la máquina virtual de réplica pasando es la extensión de script personalizado de SQL Server.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Cargar el script a un blob en una cuenta de almacenamiento de Azure. Utilice este ejemplo:

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Crear un runbook de automatización Azure para llamar a las secuencias de comandos en el equipo de virtual de réplica de SQL Server en Azure. Use este script de ejemplo para realizar esta acción. [Más información](site-recovery-runbook-automation.md) sobre cómo usar Automatización runbooks en planes de recuperación. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Al crear un plan de recuperación para la aplicación de agregar un paso de secuencias de comandos "previamente grupo 1 inicio" que invoca runbook automatización conmutar grupos de disponibilidad.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Integrar protección con SQL AlwaysOn (local a local)

Si SQL Server está usando grupos de disponibilidad de alta disponibilidad, o una instancia de clúster, se recomienda usar grupos de disponibilidad en el sitio de recuperación también. Tenga en cuenta que esta guía es para las aplicaciones que no se usan las transacciones distribuidas.

1. [Configurar bases de datos](https://msdn.microsoft.com/library/hh213078.aspx) en grupos de disponibilidad.
2. Crear una nueva red virtual en un sitio secundario.
3. Configurar una VPN de sitio a sitio entre la nueva red virtual y el sitio primario.
4. Crear una máquina virtual en el sitio de recuperación e instalar a SQL Server en él.
5. Extender los grupos de disponibilidad AlwaysOn existentes a la nueva máquina virtual de SQL Server. Configurar esta instancia de SQL Server como una copia de réplica asincrónica.
6. Crear un detector de grupo de disponibilidad, o actualizar el agente de escucha existente para incluir la máquina virtual de réplica asincrónica.
7. Asegúrese de que el conjunto de servidores de la aplicación está configurada con la escucha. Si se trata de instalar utilizando el nombre de servidor de base de datos, actualice para utilizar la escucha de modo que no necesita volver a configurar después del error.

Para las aplicaciones que utilizan transacciones distribuidas se recomendación usar [Recuperación de sitios con la replicación de SAN](site-recovery-vmm-san.md) o [replicación de sitio a otro servidor de VMWare o físico](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Consideraciones del plan de recuperación

1. Agregar esta secuencia de comandos de ejemplo en la biblioteca VMM en los sitios primario y secundarios.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Al crear un plan de recuperación para la aplicación de agregar un paso de secuencias de comandos "previamente grupo 1 inicio" que invoca el script conmutar grupos de disponibilidad.



## <a name="protect-a-standalone-sql-server"></a>Proteger una independiente de SQL Server

En esta configuración se recomienda que usar replicación de recuperación de sitio para proteger el equipo de SQL Server. Los pasos exactos dependerán si SQL Server está configurada como una máquina virtual o el servidor físico y si desea replicar en Azure o una secundaria sitio local. Obtenga instrucciones para todos los escenarios de implementación en la [Introducción al sitio de recuperación](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Proteger un clúster de SQL Server (estándar o 2008 R2)

Para un clúster que ejecuta SQL Server Standard edition o SQL Server 2008 R2, se recomienda que usar replicación de recuperación de sitio para proteger SQL Server.

### <a name="on-premises-to-on-premises"></a>Local a local

- Si la aplicación utiliza transacciones distribuidas se recomienda que implementar [Recuperación de sitios con la replicación de SAN](site-recovery-vmm-san.md) para un entorno de Hyper-V y [VMware o físico servidor VMware](site-recovery-vmware-to-vmware.md) para entorno VMware.

- Para las aplicaciones no DTC, aproveche el método anterior para recuperar el clúster como un servidor independiente mediante el uso de un local seguridad alta DB simétricos.

### <a name="on-premises-to-azure"></a>Local a Azure

Recuperación de sitio no admite la compatibilidad de clúster de invitado al replicar en Azure. SQL Server no proporciona una solución de recuperación de desastres de bajo costo para Standard edition. Se recomienda proteger el clúster de SQL Server local a un servidor SQL Server independiente y recuperar en Azure.


1. Configurar una instancia de SQL Server independiente adicionales en el sitio local.
2. Configurar esta instancia para que actúe como un reflejo para las bases de datos que necesita protección. Configurar el reflejo en el modo de alta seguridad.
3.  Configurar recuperación de sitio en el sitio de local basado en el entorno ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [servidor de VMware o físico](site-recovery-vmware-to-azure-classic.md).
4.  Utilizar la replicación de recuperación de sitios para replicar la nueva instancia de SQL Server en Azure. Es una copia de seguridad alta simétricos y se podrá sincronizar con el clúster principal, pero se podrá replicarse en Azure con replicación de recuperación del sitio.

La ilustración siguiente muestra esta configuración.

![Clúster estándar](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Consideraciones de recuperación

Para clústeres estándar de SQL, recuperación después de una migración tras error planificada se requiere una copia de seguridad SQL y restaurar desde la instancia reflejada en el clúster original y volver a establecer el reflejo.

## <a name="next-steps"></a>Pasos siguientes
[Más información](site-recovery-best-practices.md) acerca de prepararse implementar el sitio de recuperación.










 